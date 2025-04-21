在 TypeScript 项目中结合运行时类型校验库（如 Zod、Yup 或 io-ts）可以实现 **编译时类型安全 + 运行时数据校验** 的双重保障。以下是具体实现方案和代码示例：

---

## 一、技术选型对比

| 特性            | Zod              | Yup               | io-ts            |
| --------------- | ---------------- | ----------------- | ---------------- |
| TypeScript 支持 | ⭐️ 原生类型推断 | ⚠️ 需手动声明类型 | ⭐️ 强类型编解码 |
| 功能性          | 中等             | 丰富（表单优化）  | 强大（复杂类型） |
| 包体积          | 小 (8kb)         | 中 (15kb)         | 小 + fp-ts 依赖  |
| 错误处理        | 结构化错误信息   | 链式 API          | Either 模式      |
| 推荐场景        | 通用数据校验     | 表单验证          | 复杂类型编解码   |

---

## 二、核心实现模式（以 Zod 为例）

### 1. 定义 Schema 并生成 TypeScript 类型

```typescript
import { z } from "zod";

// 定义运行时 Schema
const UserSchema = z.object({
  id: z.string().uuid(),
  name: z.string().min(2),
  email: z.string().email(),
  age: z.number().int().positive().optional(),
  posts: z
    .array(
      z.object({
        title: z.string(),
        content: z.string(),
      })
    )
    .optional(),
});

// 导出编译时类型
export type User = z.infer<typeof UserSchema>;
```

### 2. 实现类型安全的校验函数

```typescript
const validateUser = (data: unknown): User => {
  const result = UserSchema.safeParse(data);

  if (!result.success) {
    // 处理结构化错误
    const errors = result.error.issues.map((issue) => ({
      path: issue.path.join("."),
      message: issue.message,
    }));
    throw new Error(JSON.stringify(errors));
  }

  return result.data; // 返回经过校验的强类型数据
};
```

### 3. 在 React 组件中的使用

```tsx
const UserProfile = () => {
  const [userData, setUserData] = useState<User | null>(null);

  useEffect(() => {
    fetch("/api/user")
      .then((res) => res.json())
      .then((data) => {
        try {
          const validData = validateUser(data);
          setUserData(validData);
        } catch (error) {
          console.error("Invalid user data:", error);
        }
      });
  }, []);

  if (!userData) return <div>Loading...</div>;

  return (
    <div>
      <h1>{userData.name}</h1>
      <p>Email: {userData.email}</p>
    </div>
  );
};
```

---

## 三、进阶实现方案

### 1. 组合 Schema 实现 DRY 原则

```typescript
// 基础 Schema
const BaseEntitySchema = z.object({
  id: z.string().uuid(),
  createdAt: z.date(),
});

// 扩展 Schema
const ProductSchema = BaseEntitySchema.extend({
  name: z.string(),
  price: z.number().positive(),
  sku: z.string().regex(/^[A-Z]{3}-d{4}$/),
});

type Product = z.infer<typeof ProductSchema>;
```

### 2. 实现类型安全的表单验证（React Hook Form + Zod）

```tsx
import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";

const FormSchema = z.object({
  username: z.string().min(3),
  password: z.string().min(8),
});

type FormValues = z.infer<typeof FormSchema>;

const LoginForm = () => {
  const {
    register,
    handleSubmit,
    formState: { errors },
  } = useForm<FormValues>({
    resolver: zodResolver(FormSchema),
  });

  return (
    <form onSubmit={handleSubmit((data) => console.log(data))}>
      <input {...register("username")} />
      {errors.username?.message}

      <input type="password" {...register("password")} />
      {errors.password?.message}

      <button type="submit">Submit</button>
    </form>
  );
};
```

### 3. 实现 API 响应类型守卫

```typescript
import { Result } from "./types";

const fetchApi = async <T extends z.ZodTypeAny>(
  schema: T,
  url: string
): Promise<Result<z.infer<T>>> => {
  try {
    const response = await fetch(url);
    const data = await response.json();
    const parsed = schema.safeParse(data);

    return parsed.success
      ? { success: true, data: parsed.data }
      : { success: false, error: parsed.error };
  } catch (error) {
    return { success: false, error };
  }
};

// 使用
const result = await fetchApi(UserSchema, "/api/user");
if (result.success) {
  // 安全访问 result.data
}
```

---

## 四、错误处理最佳实践

### 1. 结构化错误处理

```typescript
// Zod 错误转换工具函数
const formatZodErrors = (error: z.ZodError) => {
  return error.issues.map((issue) => ({
    field: issue.path.join("."),
    code: issue.code,
    message: issue.message,
  }));
};

// 使用
try {
  validateUser(invalidData);
} catch (error) {
  if (error instanceof z.ZodError) {
    const errors = formatZodErrors(error);
    // 显示给用户或记录日志
  }
}
```

### 2. 错误边界处理（React Error Boundary）

```tsx
class ErrorBoundary extends React.Component {
  state = { hasError: false };

  static getDerivedStateFromError(error) {
    if (error instanceof z.ZodError) {
      return { hasError: true, errorType: "VALIDATION_ERROR" };
    }
    return { hasError: true };
  }

  render() {
    if (this.state.hasError) {
      return <ErrorFallback />;
    }
    return this.props.children;
  }
}
```

---

## 五、各方案特色实现

### 1. Yup 表单优化方案

```typescript
import * as yup from "yup";

const schema = yup.object().shape({
  email: yup.string().email().required(),
  age: yup.number().min(18).required(),
});

// 异步校验
const validateAsync = async (data: unknown) => {
  try {
    return await schema.validate(data, { abortEarly: false });
  } catch (error) {
    if (error instanceof yup.ValidationError) {
      const errors = error.inner.map((err) => ({
        path: err.path,
        message: err.message,
      }));
      throw new Error(JSON.stringify(errors));
    }
    throw error;
  }
};
```

### 2. io-ts 函数式方案

```typescript
import * as t from "io-ts";
import { pipe } from "fp-ts/function";
import { fold } from "fp-ts/Either";

const UserCodec = t.type({
  id: t.string,
  name: t.string,
  email: t.string,
});

type User = t.TypeOf<typeof UserCodec>;

const decodeUser = (input: unknown) =>
  pipe(
    UserCodec.decode(input),
    fold(
      (errors) => {
        throw new Error(JSON.stringify(errors));
      },
      (user) => user
    )
  );
```

---

## 六、工程化建议

1. **统一校验层**：创建 `src/validators` 目录集中管理所有 Schema
2. **自动化类型生成**：使用 Zod 的 `z.infer` 或 io-ts 的 `t.TypeOf`
3. **测试覆盖**：编写针对各种边界条件的校验测试
4. **性能优化**：对高频校验场景使用 memoization 技术
5. **错误监控**：将校验错误上报到监控系统（如 Sentry）

通过合理选择校验库并实施这些方案，可以在 TypeScript 项目中构建起完善的运行时类型安全防护体系，有效防止脏数据进入系统核心逻辑。
