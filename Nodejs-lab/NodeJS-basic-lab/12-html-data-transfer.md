---
title: node.js筆記-數據傳輸到index.html中的做法

---

# node.js筆記-數據傳輸到index.html中的做法

## 前情提要

因為瀏覽器會阻擋非同domain的ajax，於是爬蟲就需要後端用node.js來寫axios，當我使用完畢得到一串圖片網址，
我想要藉由node server來顯示到畫面上，於是就來研究這個傳輸的做法有哪些？

大致上有模板引擎、API接口等等。

## 方法整理列表


1. 使用模板引擎：使用 Node.js 的模板引擎（如 EJS、Pug、Handlebars 等）来渲染 index.html 页面，并将数据传递给模板引擎进行动态渲染。在服务器端，将获取到的数据注入到模板中，生成包含数据的 HTML 页面，然后将该页面发送给客户端进行展示。

2. 使用 API 接口：在 Node.js 服务器端创建一个 API 接口，客户端通过 AJAX、fetch 或其他 HTTP 请求方式向该接口发送请求，服务器端在接收到请求后，将获取到的数据作为 JSON 或其他数据格式返回给客户端。客户端可以通过 JavaScript 获取到返回的数据，并在 index.html 页面中使用 JavaScript 代码将数据渲染到相应的位置。

3. 使用 WebSocket：使用 WebSocket 技术，在 Node.js 服务器端建立一个 WebSocket 连接，并将获取到的数据通过 WebSocket 实时传递给客户端。客户端使用 JavaScript 监听 WebSocket 连接，一旦接收到数据，就可以在 index.html 页面中进行相应的处理和展示。

4. 使用服务器端渲染（SSR）：使用框架如 Next.js、Nuxt.js 等进行服务器端渲染，将 Node.js 获取到的数据直接注入到服务器端渲染的页面中，生成包含数据的 HTML 页面，并将该页面发送给客户端展示。这样客户端在首次加载页面时就能获取到带有数据的页面内容。

以上方法提供了不同的方式将数据传递到 index.html 页面并进行使用，具体的选择取决于你的需求和项目架构。你可以根据具体情况选择适合的方法来实现数据传递和展示。


## 範例說明

当使用 Node.js 获取一串数据后，将其传递到 index.html 页面并使用的示例代码如下：

**使用模板引擎（以 EJS 为例）：**

```javascript
// 服务器端代码
const express = require("express");
const app = express();
const ejs = require("ejs");

app.set("view engine", "ejs");
app.set("views", __dirname + "/views");

app.get("/", (req, res) => {
  // 获取数据
  const data = ["item1", "item2", "item3"];

  // 渲染 index.html 页面并传递数据
  res.render("index", { data });
});

app.listen(3000, () => {
  console.log("Server is running on port 3000");
});
```

```html
<!-- views/index.ejs -->
<!DOCTYPE html>
<html>
<head>
  <title>My App</title>
</head>
<body>
  <h1>Data:</h1>
  <ul>
    <% for(let item of data) { %>
      <li><%= item %></li>
    <% } %>
  </ul>
</body>
</html>
```

**使用 API 接口：**

```javascript
// 服务器端代码
const express = require("express");
const app = express();

app.get("/data", (req, res) => {
  // 获取数据
  const data = ["item1", "item2", "item3"];

  // 返回数据
  res.json(data);
});

app.listen(3000, () => {
  console.log("Server is running on port 3000");
});
```

```html
<!-- index.html -->
<!DOCTYPE html>
<html>
<head>
  <title>My App</title>
  <script src="https://cdn.jsdelivr.net/npm/axios/dist/axios.min.js"></script>
  <script>
    axios.get("/data")
      .then(response => {
        const data = response.data;
        // 使用数据进行操作
        console.log(data);
      })
      .catch(error => {
        console.error(error);
      });
  </script>
</head>
<body>
  <!-- 页面内容 -->
</body>
</html>
```

以上示例代码分别展示了使用模板引擎和 API 接口两种方式将数据传递到 index.html 页面并进行使用。你可以根据你的具体需求选择适合的方法来实现数据传递和展示。

## WebSocket的做法範例

若要使用 WebSocket 在 Node.js 中将数据传递到 index.html 页面并使用，可以采用以下步骤：

**服务器端代码：**

```javascript
const express = require("express");
const http = require("http");
const WebSocket = require("ws");

const app = express();
const server = http.createServer(app);
const wss = new WebSocket.Server({ server });

wss.on("connection", (ws) => {
  // 当 WebSocket 连接建立时执行的操作
  // 获取数据
  const data = ["item1", "item2", "item3"];

  // 发送数据给客户端
  ws.send(JSON.stringify(data));
});

server.listen(3000, () => {
  console.log("Server is running on port 3000");
});
```

**客户端代码：**

```html
<!-- index.html -->
<!DOCTYPE html>
<html>
<head>
  <title>My App</title>
</head>
<body>
  <ul id="data-list"></ul>

  <script>
    const ws = new WebSocket("ws://localhost:3000");

    ws.onmessage = (event) => {
      const data = JSON.parse(event.data);
      // 使用数据进行操作
      const dataList = document.getElementById("data-list");
      data.forEach((item) => {
        const li = document.createElement("li");
        li.textContent = item;
        dataList.appendChild(li);
      });
    };

    ws.onclose = () => {
      console.log("WebSocket connection closed");
    };
  </script>
</body>
</html>
```

上述代码创建了一个 WebSocket 服务器，当有新的 WebSocket 连接建立时，服务器将发送数据给客户端。在客户端的 index.html 页面中，通过 WebSocket 连接服务器，接收并使用服务器发送的数据。

请确保在使用 WebSocket 时正确安装和引入相关的库，例如在 Node.js 环境中安装 `ws` 库，并在代码中引入 WebSocket 相关模块。



## Next.js用法示範
下面是使用 Next.js 框架进行服务器端渲染的示例：

**首先，安装 Next.js：**
```bash
npm install next react react-dom
```

**创建页面文件：**
在项目根目录下创建一个名为 `pages` 的文件夹，并在该文件夹中创建一个名为 `index.js` 的文件。

**index.js：**
```javascript
import React from 'react';

function HomePage({ data }) {
  return (
    <div>
      <h1>Server-Side Rendering Example</h1>
      <ul>
        {data.map((item) => (
          <li key={item.id}>{item.title}</li>
        ))}
      </ul>
    </div>
  );
}

export async function getServerSideProps() {
  // 模拟获取数据
  const data = [
    { id: 1, title: 'Item 1' },
    { id: 2, title: 'Item 2' },
    { id: 3, title: 'Item 3' },
  ];

  return {
    props: {
      data,
    },
  };
}

export default HomePage;
```

在上述代码中，我们定义了一个名为 `HomePage` 的组件，该组件接收 `data` 属性，并根据 `data` 渲染页面内容。我们还定义了 `getServerSideProps` 函数，它用于在服务器端获取数据并将数据作为 `props` 传递给页面组件。

**创建启动文件：**
在项目根目录下创建一个名为 `server.js` 的文件。

**server.js：**
```javascript
const { createServer } = require('http');
const { parse } = require('url');
const next = require('next');

const dev = process.env.NODE_ENV !== 'production';
const app = next({ dev });
const handle = app.getRequestHandler();

app.prepare().then(() => {
  createServer((req, res) => {
    const parsedUrl = parse(req.url, true);
    const { pathname, query } = parsedUrl;

    handle(req, res, parsedUrl);
  }).listen(3000, (err) => {
    if (err) throw err;
    console.log('> Ready on http://localhost:3000');
  });
});
```

在上述代码中，我们使用 `next` 模块创建了一个服务器，并在 `createServer` 回调函数中使用 `handle` 方法处理请求。最后，我们监听 3000 端口并启动服务器。

**运行应用：**
在命令行中运行以下命令启动应用：
```bash
node server.js
```

现在，您可以在浏览器中访问 `http://localhost:3000`，将会看到使用服务器端渲染的页面，页面内容由 `getServerSideProps` 函数返回的数据进行渲染。

请注意，这只是 Next.js 的一个简单示例，您可以根据需要进行定制和扩展。类似地，您也可以使用 Nuxt.js 等其他服务器端渲染框架进行类似的操作。

## 心得

這個做法超級有趣的，認真沒想到有這麼多方法欸（๑ • ‿ • ๑ ）
未來有機會每一個都來玩看看！
目前最喜歡API的做法，畢竟也最熟悉。