# CAC

`CAC` 是为创造命令行工具而生的库。它轻量、易学、功能强大、并对开发者友好。

下面我们就从`README`文件入手，一步步的分析这个库。

## 一个好的README应该什么样
`README`,对于一个库来说是非常重要的，他能决定用户会不会使用这个库。可以看到`cac`的README写的非常好，我们想获取的信息全都有。我们从该库中可以学到如何写好README.

- LOGO
- 数据牌-徽章(一些icon) 
  诸如测试覆盖率等等
- 项目背景
- 简单介绍
- 安装
- 基本使用API
- 相关项目(成功案例)
- FAQ
- 项目状态
- 如何参与贡献
- 国际化
- 开源协议

还可以加入版本变化等相关内容。

### 快速生成README模板
- [readme-md-generator](https://github.com/kefranabg/readme-md-generator)
  - npx readme-md-generator

## 项目目录

```
├── LICENSE
├── README.md
├── circle.yml
├── examples
│   ├── basic-usage.js
│   ├── command-examples.js
│   ├── command-options.js
│   ├── dot-nested-options.js
│   ├── help.js
│   ├── ignore-default-value.js
│   ├── negated-option.js
│   ├── sub-command.js
│   └── variadic-arguments.js
├── index-compat.js
├── jest.config.js
├── mod.js
├── mod.ts
├── mod_test.ts
├── package.json
├── rollup.config.js
├── scripts
│   └── build-deno.ts
├── src
│   ├── CAC.ts
│   ├── Command.ts
│   ├── Option.ts
│   ├── __test__
│   │   ├── __snapshots__
│   │   │   └── index.test.ts.snap
│   │   └── index.test.ts
│   ├── deno.ts
│   ├── index.ts
│   ├── node.ts
│   └── utils.ts
├── tsconfig.json
└── yarn.lock
```
### editorconfig 
EditorConfig帮助开发人员在不同的编辑器和IDE之间定义和维护一致的编码样式。
[editorconfig介绍](https://zhuanlan.zhihu.com/p/349063996)
  
### .gitattributes 是干嘛的
[.gitattributes](https://zhuanlan.zhihu.com/p/108266134)

### circle.yml
持续集成

### package.json

### rollup.config.js

### jest.config.js 
- 单元测试搭建
  
### tsconfig
```json
{
  "compilerOptions": {
    "target": "es2015",
    "declaration": true,
    "declarationDir": "types",
    "esModuleInterop": true,
    "pretty": true,
    "moduleResolution": "node",
    "lib": ["es2015", "es2016.array.include"],
    "allowSyntheticDefaultImports": true,
    "stripInternal": true,
    "noImplicitAny": true,
    "noImplicitReturns": true,
    "noImplicitThis": true,
    "noUnusedLocals": true,
    "noUnusedParameters": true,
    "noFallthroughCasesInSwitch": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "strictPropertyInitialization": true,
    "alwaysStrict": true,
    "module": "commonjs",
    "outDir": "lib"
  },
  "include": ["src", "declarations.d.ts"],
  "exclude": ["src/deno.ts"]
}
```
## 使用该库

## 流程图