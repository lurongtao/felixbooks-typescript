# 安装TypeScript
## 1、在全局环境里安装TS
```
npm install -g typescript
```

## 2、用 tsc 命令编译 .ts 文件
app.ts 文件：
```
let title: string = '千锋教育'
```
在命令行里输入以下命令都可以将.ts文件编译为.js文件：
```
tsc ./src/app.ts --outFile ./dist/app.js
tsc ./src/* --outDir ./dist --watch
```

## 3、tsconfig.json 配置文件
在命令行里输入 `tsc --init`命令，创建一个 tsconfig.json 文件，在此配置文件里修改：
```
"outDir": "./dist",
"rootDir": "./src"
```