## 版本说明

Node.js: `16.13.1`

## 创建项目

创建如下目录结构

```
project
├── src
│   └── server.ts
├── package.json
└── tsconfig.json
```

`package.json` 可以使用 `yarn init -y` 生成

`tsconfig.json` 可以使用 `tsc --init` 生成（需要全局或在项目中安装 `typescript` 包才可以使用 `tsc` 命令）

## 安装依赖

注意：

1. `@tsconfig/node16` 包需要根据 `Node.js` 的版本变化，我电脑上安装的是 `16.x.x` 的版本，所以用的是 `@tsconfig/node16`，具体看 [tsconfig/bases](https://github.com/tsconfig/bases) 中的说明，当然也可以完全不用安装这个包，这个包优点是公用性和主流推荐配置
2. `typescript` 如果已经全局安装过了，就从下面的命令中移除它
3. [concurrently](https://github.com/open-cli-tools/concurrently) 是一个并发执行多个命令的工具包
4. [nodemon](https://github.com/remy/nodemon) 是一个监听文件变化自动重启程序的工具包
```
yarn add koa
yarn add typescript @tsconfig/node16 @types/node @types/koa concurrently nodemon -D
```

## 填充内容

### src/server.ts

```typescript
import Koa from 'koa';

const server: Koa = new Koa();
const port: number = 3000;

server.use((ctx: Koa.DefaultContext) => {
    ctx.body = 'hi koa';
});

server.listen(port, () => {
    console.log(`Node.js v${process.versions.node}`);
});
```

### tsconfig.json

注意：`extends` 字段的值根据你安装的包名 `@tsconfig/node**` 替换

```json
{
  "extends": "@tsconfig/node16/tsconfig.json",
  "compilerOptions": {
    "baseUrl": ".",
    "rootDir": "src",
    "outDir": "dist",
    "noImplicitAny": true,
  },
  "include": [
    "src/**/*"
  ]
}
```

### package.json

```json
"scripts": {
  "build-ts": "tsc",
  "build": "yarn build-ts",
  "debug": "yarn build && yarn watch-debug",
  "serve-debug": "nodemon --inspect dist/server.js",
  "serve": "node dist/server.js",
  "start": "yarn serve",
  "watch-debug": "concurrently -k -p \"[{name}]\" -n \"TypeScript,Node\" -c \"yellow.bold,cyan.bold,green.bold\" \"npm:watch-ts\" \"npm:serve-debug\"",
  "watch-node": "nodemon dist/server.js",
  "watch-ts": "tsc -w",
  "watch": "concurrently -k -p \"[{name}]\" -n \"TypeScript,Node\" -c \"yellow.bold,cyan.bold,green.bold\" \"npm:watch-ts\" \"npm:watch-node\""
}
```

## 运行

我们的所有源码在 `src` 目录下，`tsc` 编译后的 `js` 文件在 `dist` 目录下，这是在 `tsconfig.json` 文件中指定的路径

本地开发：执行 `yarn debug`

部署生产：顺序执行 `yarn build`、`yarn serve` 或 `yarn start`（serve 和 start 是相同的命令）

代码已上传到 [github](https://github.com/myesn/nodejs-integration-typescript-with-koa)

## 参考资料

1. [microsoft/TypeScript-Node-Starter](https://github.com/microsoft/TypeScript-Node-Starter)
