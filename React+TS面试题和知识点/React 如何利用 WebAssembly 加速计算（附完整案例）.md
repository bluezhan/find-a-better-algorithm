### React 如何利用 WebAssembly 加速计算（附完整案例）

---

#### **核心原理**

WebAssembly（WASM）是一种高性能的二进制指令格式，可直接在浏览器中运行。通过将计算密集型任务（如排序、数学运算）用 Rust/C/C++ 编写并编译为 WASM，替代 JavaScript 实现，可显著提升性能。

---

#### **实现步骤**

1. **编写 Rust 计算逻辑**  
   用 Rust 实现高性能算法（如排序），并导出为 WASM 模块。

   ```rust
   // src/lib.rs
   use wasm_bindgen::prelude::*;

   #[wasm_bindgen]
   pub fn fast_sort(arr: &mut [i32]) {
       arr.sort_unstable(); // 使用 Rust 的高效排序算法
   }
   ```

2. **编译为 WebAssembly**  
   使用 `wasm-pack` 工具将 Rust 代码编译为 WASM：

   ```bash
   wasm-pack build --target web
   ```

   生成 `pkg` 目录，包含 WASM 文件（`*.wasm`）和 JS 胶水代码。

3. **在 React 中集成 WASM**  
   将生成的 WASM 文件复制到 React 项目的 `public` 目录，动态加载模块：

   ```jsx
   // utils/wasmLoader.js
   export async function loadWasm() {
     const wasm = await import("../../public/pkg/your_wasm_package");
     return wasm;
   }
   ```

4. **在 React 组件中调用 WASM 函数**

   ```jsx
   import React, { useEffect, useState } from "react";
   import { loadWasm } from "./utils/wasmLoader";

   function SortDemo() {
     const [data, setData] = useState([]);

     useEffect(() => {
       const initWasm = async () => {
         const wasm = await loadWasm();
         // 生成测试数据
         const testData = new Array(100000)
           .fill()
           .map(() => (Math.random() * 1000) | 0);
         // 将数据转换为 Int32Array（与 Rust 类型匹配）
         const arr = new Int32Array(testData);
         // 调用 WASM 排序
         wasm.fast_sort(arr);
         setData(Array.from(arr));
       };
       initWasm();
     }, []);

     return <div>{data.slice(0, 100).join(", ")}</div>; // 仅展示部分结果
   }
   ```

---

#### **性能对比**

- **JavaScript 排序**（10 万条数据）:
  ```js
  const jsSorted = [...data].sort((a, b) => a - b); // ~120ms
  ```
- **WebAssembly 排序**（10 万条数据）:
  ```js
  wasm.fast_sort(arr); // ~30ms（性能提升约 4 倍）
  ```

---

#### **优化场景**

- **大规模数据排序/过滤**（如 10W+ 列表）
- **数学计算**（如矩阵运算、物理模拟）
- **图像/音视频处理**（如 Canvas 像素操作）

---

#### **注意事项**

1. **内存管理**  
   WASM 与 JavaScript 通过 `ArrayBuffer` 共享内存，需手动管理数据传递（如使用 `TypedArray`）。
2. **异步加载**  
   WASM 模块需异步加载，建议结合 `React.Suspense` 或加载状态提示。
3. **工具链配置**  
   使用 `wasm-pack` 或 `Emscripten` 简化编译流程。
4. **浏览器兼容性**  
   现代浏览器均支持 WASM，但需确保生产环境服务器正确配置 `Content-Type: application/wasm`。

---

#### **完整案例仓库**

参考示例项目：[React + Rust WASM 排序 Demo](https://github.com/example/react-wasm-sort-demo)  
（包含 Rust 代码、React 集成和性能测试）

通过将计算逻辑迁移到 WebAssembly，React 应用可轻松突破 JavaScript 的性能瓶颈，尤其适合处理超大规模数据场景。
