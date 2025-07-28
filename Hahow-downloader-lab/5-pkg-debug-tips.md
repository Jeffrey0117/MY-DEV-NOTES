---
title: JS筆記-解雷指南：pkg的技巧整理、坑點

---

# JS筆記-解雷指南：pkg的技巧整理、坑點


[JS筆記-打包pkg教學以及相關知識(一)](https://hackmd.io/aw6DFGZoQG2KJp--fcbc9A#JS%E7%AD%86%E8%A8%98-%E6%89%93%E5%8C%85pkg%E6%95%99%E5%AD%B8%E4%BB%A5%E5%8F%8A%E7%9B%B8%E9%97%9C%E7%9F%A5%E8%AD%98%E4%B8%80)

# 各種問題一覽表


## fetch會出現錯誤

要改成axios，否則會有絕對的錯誤。


## 不支援axios
雖然說改成axios可能還是有問題，但這是可以解決的！

首先要先注意axios接收數據的寫法跟fetch不太一樣，這bug也卡我很久：
```
      try {
        const response = await axios.get(url, {
          headers: headers, // 你的 headers 变量
          withCredentials: true, // 包含凭据信息，类似于 credentials: "include"
        });

        // axios 会自动解析 JSON 数据
        const data = response.data;

        // 在这里进行你的逻辑处理
        thenCallback(data);
      } catch (error) {
        console.error("Error:", error);
      }
```

可以參考上面示範。

[It seems that pkg is not compatible with Axios!](https://github.com/vercel/pkg/issues/1936)

[PKG build file](https://github.com/axios/axios/issues/5195)

### 解法一:更改引入寫法(注意別用ESM)

![image](https://hackmd.io/_uploads/ByQNpGd2T.png)


![image](https://hackmd.io/_uploads/r17dF4d2T.png)

這樣改原本成功了，後來又不知道為什麼重開就不能用，好像有動到。
沒關係下面有另一個可以用的解法。

### 解法二: 以NCC先打包一次
![image](https://hackmd.io/_uploads/rk2Vm9Ona.png)

[pkg打包时提示找不到axios等任意模块时万能解决办法
](https://blog.oioweb.cn/139.html)


```
ncc build script.js -o dist /名稱改成自己要打包的.js名稱
```
再把dist的index.js拉到目錄底下，然後去修改設定、就可以用pkg打包了。


### 解法三: 改用node-fetch或cross-fetch(?)

似乎都沒有用，參考一下就好。

https://www.npmjs.com/package/node-fetch



## 打包出現版本錯誤

說明：無論查出怎樣的執行命令，都沒辦法成功打包。
寫找不到node20或node21之類的雞掰東西。

### 解法：統一打包的流程、版本設定好

有很多方法可以執行，這邊我使用先去package設定script，然後執行它的方法。

package設定如下：
![image](https://hackmd.io/_uploads/BkiJ49uhT.png)


```
  "scripts": {
    "build": "pkg index.js -t node16-win-x64 -o my-app",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```

這邊有個大坑，node16-win-x64這個是沒問題的，原本設置是node20之類的都會出錯！
所以要小心設置的版本，為了這個bug我找了一狗票方法==

![image](https://hackmd.io/_uploads/Sk8-Vcu2p.png)

因為用了方便的這個script，未來要打包就不用再寫全部的變數設置，
使用自定義的build去執行，就可以做出執行打包的程序：

![image](https://hackmd.io/_uploads/r1NPtcOha.png)


目錄下出現了exe檔案，成功！

## 不支援ESM
ESM 指的是 ECMAScript Modules，它是 ECMAScript（JavaScript 的规范）中的模块化系统。在过去，JavaScript 主要使用 CommonJS 或 AMD（Asynchronous Module Definition）等模块系统。但在 ECMAScript 6（ES2015）中引入了原生的模块系统，即 ESM。

主要特点包括：

1. **导入和导出语法：** ESM 提供了 `import` 和 `export` 关键字，用于在不同模块之间导入和导出功能。这种语法更简洁，易读，并且在语言层面支持模块化开发。

   ```javascript
   // 导入模块
   import { functionName } from './module';

   // 导出功能
   export function myFunction() {
     // 一些代码
   }
   ```

2. **静态导入：** ESM 使用静态导入，这意味着模块的依赖关系在代码执行之前就已经确定。这种方式有助于更好的性能和工具分析。

3. **模块级作用域：** 每个模块都有自己的作用域，不会污染全局作用域。只有通过 `export` 显式导出的内容才能在其他模块中访问。

4. **异步加载：** ESM 支持动态导入，使得在运行时异步加载模块成为可能。这对于按需加载或延迟加载模块非常有用。

   ```javascript
   // 动态导入
   const module = await import('./module');
   ```

5. **文件扩展名和相对路径：** ESM 需要在导入时指定文件的扩展名（例如 `.js`）和相对路径（如果是相对路径）。这是为了提高解析模块的准确性。

   ```javascript
   // 正确的导入方式
   import { functionName } from './module.js';
   ```

ESM 在现代 JavaScript 开发中变得越来越常见，许多新的项目和库都采用 ESM 作为首选的模块系统。然而，它与 CommonJS 等其他模块系统之间存在一些不同，因此在迁移或混合使用时需要注意。 Node.js 也支持 ESM，但有时需要在项目中配置或使用适当的标志来启用它。

![image](https://hackmd.io/_uploads/HyAdP5sna.png)


### 解法

改成CommonJS 寫法就可以。