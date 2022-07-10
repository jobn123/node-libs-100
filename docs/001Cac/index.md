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
持续集成 其作用就是在 Git 仓库代码发生更改时(push/accept pull request/…)，执行一系列(一般)用于 测试(Test)、构建(Build) 的命令，以此完成自动构建、自动测试的工作。

### rollup.config.js
打包配置，输出不同规范所需的包

### jest.config.js 
- jest单元测试
  
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

### option
`<>`参数必传，`[]` 参数可不传

#### 基本使用

```js
// examples/basic-usage.js
const cli = require('cac')()

cli.option('--type <type>', 'Choose a project type', {
  default: 'node',
})

const parsed = cli.parse()

console.log(JSON.stringify(parsed, null, 2))
```

```shell
# node basic-usage.js

{
  "args": [
    "build"
  ],
  "options": {
    "--": [],
    "type": "node"
  }
}
```

#### Dash in option names

```js
cli
  .command('dev', 'Start dev server')
  .option('--clear-screen', 'Clear screen')
  .action((options) => {
    console.log(options.clearScreen)
  })
```
--clear-screen 和 --clearScreen 一样 都能映射到 options.clearScreen.

#### Negated Options

```js
cli
  .command('build [project]', 'Build a project')
  .option('--no-config', 'Disable config file')
  .option('--config <path>', 'Use a custom config file')
```

默认的config是true,可通过`--no-config`将config设成false

#### Dot-nested Options
```js
const cli = require('cac')()

cli
  .command('build', 'desc')
  .option('--env <env>', 'Set envs')
  .example('--env.API_SECRET xxx')
  .action((options) => {
    console.log(options)
  })

cli.help()

cli.parse()
```

```shell
# 输入
node dot-nested-options.js build --env.b bbb --env.c ccc

# 输出
{ '--': [], env: { b: 'bbb', c: 'ccc' } }
```
以`.`方式传递的参数，将会合并到一个`option`中

### command

#### 基本使用

```js
// command-options.js
const cli = require('cac')()

cli.command('rm <dir>', 'Remove a dir')
  .option('-r, --recursive', "remove recursively")
  .action((dir, options) => {
    console.log('remove ' + dir + (options.recursive ? ' recursively' : ''))
  })

cli.parse()
```

```shell
# 调用
node command-options rm foo -r

# 输出
remove foo recursively
```

执行命令的时候会调用action，并将command和option的参数传递过去

#### default command

```js
const cli = require('cac')()

cli
  // Simply omit the command name, just brackets
  .command('[...files]', 'default')
  .option('--minimize', 'Minimize output')
  .action((files, options) => {
    console.log(files)
    console.log(options.minimize)
  })

cli.parse()
```

当命令不存在或者不输入命令时执行

#### command 事件监听

```js
const cli = require('cac')()

cli.command("build", "desc")
  .action(() => {
    console.log("build")
  })

cli.on('command:build', (ins) => {
  console.log(ins)
  // ins 是 cac 实例
  console.log("这里会先执行，然后在执行action")
})

cli.parse()
```

对事件进行监听。监听的回调会先执行，然后在执行action方法

### 错误处理
```js
try {
  // 需要在调用命令后执行parse
  cli.parse(process.argv, { run: false })
  // You only need `await` when your command action returns a Promise
  await cli.runMatchedCommand()
} catch (error) {
  // 错误处理
  // console.error(error.stack)
  // process.exit(1)
}
```

## 源码分析

```js
//src/CAC.ts  
// 继承自EventEmitter, 在调用command方法时，使用了emit,用户可在使用cac时通过on监听。
class CAC extends EventEmitter {
  /** The program name to display in help and version message */
  name: string
  // command 集合
  commands: Command[]
  // 全局命令
  globalCommand: GlobalCommand
  // 匹配到的命令
  matchedCommand?: Command
  // 匹配到命令名称
  matchedCommandName?: string
  /**
   * 未处理过的参数
   */
  rawArgs: string[]
  /**
   * 处理后的参数
   */
  args: ParsedArgv['args']
  /**
   * 处理后的options
   */
  options: ParsedArgv['options']
  // 显示帮助信息
  showHelpOnExit?: boolean
   // 显示版本信息
  showVersionOnExit?: boolean

  /**
   * @param name The program name to display in help and version message
   */
  constructor(name = '') {
    super()
    this.name = name
    this.commands = []
    this.rawArgs = []
    this.args = []
    this.options = {}
    this.globalCommand = new GlobalCommand(this)
    this.globalCommand.usage('<command> [options]')
  }

  /**
   * Add a global usage text.
   *
   * This is not used by sub-commands.
   */
  usage(text: string) {
    this.globalCommand.usage(text)
    return this
  }

  /**
   * 添加一个子命令 
   */
  command(rawName: string, description?: string, config?: CommandConfig) {
    // 创建一个command
    const command = new Command(rawName, description || '', config, this)
    // 将全局命令挂载到command中
    command.globalCommand = this.globalCommand
    // 将创建的命令放到commands数组中
    this.commands.push(command)
    // 返回创建的命令
    return command
  }

  /**
   * 将option放到全局commond的options中
   */
  option(rawName: string, description: string, config?: OptionConfig) {
    this.globalCommand.option(rawName, description, config)
    return this
  }

  /**
   * 通过-h , --help 显示帮助信息
   */
  help(callback?: HelpCallback) {
    this.globalCommand.option('-h, --help', 'Display this message')
    this.globalCommand.helpCallback = callback
    this.showHelpOnExit = true
    return this
  }

  /**
   * 通过-v , --version 显示版本信息
   */
  version(version: string, customFlags = '-v, --version') {
    this.globalCommand.version(version, customFlags)
    this.showVersionOnExit = true
    return this
  }

  /**
   * 全局命令中添加一个示例 
   */
  example(example: CommandExample) {
    this.globalCommand.example(example)
    return this
  }

  /**
   * 输出帮助信息
   */
  outputHelp() {
    // 是否匹配到子命令，匹配到输出子命令帮助信息
    if (this.matchedCommand) {
      this.matchedCommand.outputHelp()
    } else {
      // 否则输出全局命令帮助信息
      this.globalCommand.outputHelp()
    }
  }

  /**
   * 显示版本信息 
   */
  outputVersion() {
    this.globalCommand.outputVersion()
  }

  // 保存处理后的信息
  private setParsedInfo(
    { args, options }: ParsedArgv,
    matchedCommand?: Command,
    matchedCommandName?: string
  ) {
    this.args = args
    this.options = options
    if (matchedCommand) {
      this.matchedCommand = matchedCommand
    }
    if (matchedCommandName) {
      this.matchedCommandName = matchedCommandName
    }
    return this
  }

  // 重置匹配的命令
  unsetMatchedCommand() {
    this.matchedCommand = undefined
    this.matchedCommandName = undefined
  }

  /**
   * 解析参数 
   */
  parse(
    argv = processArgs,
    {
      /** Whether to run the action for matched command */
      run = true,
    } = {}
  ): ParsedArgv {
    // 未处理的参数
    this.rawArgs = argv
    // 为name赋值
    if (!this.name) {
      this.name = argv[1] ? getFileName(argv[1]) : 'cli'
    }

    // 设置解析flag默认为true
    let shouldParse = true

    // 遍历commands 寻找匹配的命令 
    for (const command of this.commands) {
      const parsed = this.mri(argv.slice(2), command)

      const commandName = parsed.args[0]
      if (command.isMatched(commandName)) {
        shouldParse = false
        const parsedInfo = {
          ...parsed,
          args: parsed.args.slice(1),
        }
        this.setParsedInfo(parsedInfo, command, commandName)
        // 触发命令，使用时可通过on监听
        this.emit(`command:${commandName}`, command)
      }
    }

    // 没匹配到命令
    if (shouldParse) {
      // 查找默认命令 
      for (const command of this.commands) {
        if (command.name === '') {
          shouldParse = false
          const parsed = this.mri(argv.slice(2), command)
          this.setParsedInfo(parsed, command)
           // 触发命令，使用时可通过on监听
          this.emit(`command:!`, command)
        }
      }
    }

    // 没匹配到命令，也没设置默认命令
    if (shouldParse) {
      const parsed = this.mri(argv.slice(2))
      this.setParsedInfo(parsed)
    }

    // 显示帮助信息
    if (this.options.help && this.showHelpOnExit) {
      this.outputHelp()
      run = false
      this.unsetMatchedCommand()
    }

    // 显示版本信息
    if (this.options.version && this.showVersionOnExit && this.matchedCommandName == null) {
      this.outputVersion()
      run = false
      this.unsetMatchedCommand()
    }

    // 解析后的参数
    const parsedArgv = { args: this.args, options: this.options }

    // 运行匹配到的命令
    if (run) {
      this.runMatchedCommand()
    }

    // 监听未知的命令
    if (!this.matchedCommand && this.args[0]) {
      this.emit('command:*')
    }

    return parsedArgv
  }

  private mri(
    argv: string[],
    /** Matched command */ command?: Command
  ): ParsedArgv {
    // 合并所有options 
    const cliOptions = [
      ...this.globalCommand.options,
      ...(command ? command.options : []),
    ]
    const mriOptions = getMriOptions(cliOptions)

    // Extract everything after `--` since mri doesn't support it
    let argsAfterDoubleDashes: string[] = []
    const doubleDashesIndex = argv.indexOf('--')
    if (doubleDashesIndex > -1) {
      argsAfterDoubleDashes = argv.slice(doubleDashesIndex + 1)
      argv = argv.slice(0, doubleDashesIndex)
    }

    let parsed = mri(argv, mriOptions)
    parsed = Object.keys(parsed).reduce(
      (res, name) => {
        return {
          ...res,
          [camelcaseOptionName(name)]: parsed[name],
        }
      },
      { _: [] }
    )

    const args = parsed._

    const options: { [k: string]: any } = {
      '--': argsAfterDoubleDashes,
    }

    // Set option default value
    const ignoreDefault =
      command && command.config.ignoreOptionDefaultValue
        ? command.config.ignoreOptionDefaultValue
        : this.globalCommand.config.ignoreOptionDefaultValue

    let transforms = Object.create(null)

    for (const cliOption of cliOptions) {
      if (!ignoreDefault && cliOption.config.default !== undefined) {
        for (const name of cliOption.names) {
          options[name] = cliOption.config.default
        }
      }

      // If options type is defined
      if (Array.isArray(cliOption.config.type)) {
        if (transforms[cliOption.name] === undefined) {
          transforms[cliOption.name] = Object.create(null)

          transforms[cliOption.name]['shouldTransform'] = true
          transforms[cliOption.name]['transformFunction'] =
            cliOption.config.type[0]
        }
      }
    }

    // Set option values (support dot-nested property name)
    for (const key of Object.keys(parsed)) {
      if (key !== '_') {
        const keys = key.split('.')
        setDotProp(options, keys, parsed[key])
        setByType(options, transforms)
      }
    }

    return {
      args,
      options,
    }
  }

  runMatchedCommand() {
    const { args, options, matchedCommand: command } = this
    // 没有匹配到命令，或命令没有action不予执行
    if (!command || !command.commandAction) return

    // 校验未知的options
    command.checkUnknownOptions()
    // 校验options值
    command.checkOptionValue()
    // 校验参数
    command.checkRequiredArgs()

    const actionArgs: any[] = []
    // 遍历参数
    command.args.forEach((arg, index) => {
      if (arg.variadic) {
        // a, b, c, d => a, [b,c,d]
        actionArgs.push(args.slice(index))
      } else {
        actionArgs.push(args[index])
      }
    })
    actionArgs.push(options)
    // 执行action
    return command.commandAction.apply(this, actionArgs)
  }
}

export default CAC
```