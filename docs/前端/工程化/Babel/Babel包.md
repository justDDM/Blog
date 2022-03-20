# Babel包

## Parser

> [@babel/parse](https://babeljs.io/docs/en/babel-parser)

Parse 可以将源码转换为 AST

- `parse`：返回整个 AST（根节点为 `File`）

- `parseExpression`：返回 AST （根节点 `Expression`）

### options

`parse` 和 `parseExpression` 都接受一个 option 配置。

常用的配置项

```js
  require("@babel/parser").parse("code", {
    // 指定是否支持解析语法模块
    // module: 解析 esm
    // script: 不解析 esm
    // unambiguous: 根据是否有 import/export 来确定是否解析 esm
    sourceType: "module",
    // 指定插件来解析语法
    plugins: [
      "jsx",
      "typescript"
    ]
  })
```

## traverse

> [@babel/tarverse](https://babeljs.io/docs/en/babel-traverse)

parse 产生的 AST 由 `@babel/traverse` 进行遍历处理，在遍历 AST 时候会调用相应的 `visitor` 函数。

```js
tarverse(parent, opts)
```

- `parent`: 指定要遍历的 AST 节点

- `opts`: 指定 `vistor` 函数。`visitor` 可以为函数/对象

  - 函数：enter 时调用的函数
  
  - 对象：可以指明是 enter 或 exit 时处理函数

```js
visitor: {
  Identifier (path, state) {},
  StringLiteral: {
    enter(path, state) {
      // 遍历当前节点的子节点前调用
    },
    exit(path, state) {
      // 遍历完当前节点的子节点后调用
    }
  }
}
```

```js
  tarverse(ast, {
    // 函数形式等于enter时调用
    FunctionDeclaration(path, state) {},
    // 对象形式可以指定
    FunctionDeclaration: {
      enter(path, state) {},
      exit(path, state) {}
    }
  })

  tarverse(ast, {
    // 多个节点通过 | 连接
    'FunctionDeclaration|VariiableDeclaration'(path, state) {}
  })

  tarverse(ast, {
    // 通过别名指定节点
    Declaration: {
      exit(path, state) {}
    }
  })
```

[别名](https://github.com/babel/babel/blob/main/packages/babel-types/src/ast-types/generated/index.ts#L2489-L2535)

### path 参数

path 参数是遍历过程的路径，保留上下文信息，提供一系列属性和方法

- `path.node`: 当前 AST 节点

- `path.get`、`path.set`: 获取/设置当前节点属性的 path

- `path.parent`: 指向父级 AST

- `path.getSibling`、`getNextSibling`、`getPrevSibling`: 获取兄弟节点 AST

- `path.scope`: 获取当前节点 **作用域** 信息

- ...

### state 参数

插件会通过 state 传递 option 和 file 信息，也可通过 state 存储一些遍历过程共享的数据

### @babel/types

[api](https://babeljs.io/docs/en/babel-types)

提供创建 AST 和判断 AST 类型的方法。

```js
t.ifStatement(test, consequent, alternate) // 创建 ifStatement

t.isIfStatement(node, opts) // 判断是否为ifStatement，返回boolean值表示结果

t.assertIfStatement(node, opts) // 判断是否为ifStatement，断言为什么类型如果不一致会抛出异常
```

### @babel/template

批量创建 AST

- `template(code, [opts])(args)`: 根据模板创建 AST，（会被包括一层 `ExpressionStatement` 结点，会当成表达式语句来 parse）

- `template.ast(code, [opts])`: 根据模板创建 AST，返回的根节点是 `Program`

- `template.expression`、`template.statement`、`template.statements`: 根据具体需要创建的 AST 类型创建AST

## generate

AST 处理完后需要将处理后的 AST 转换为目标代码

```js
generate(ast, opts, code)
```

- `ast`: 要打印的 AST 结点

- `opts`: 打印的配置，可以配置是否生成 `sourceMaps` 等

- `code`: 多文档合并打印时使用

### @babel/code-frame

提供打印错误信息

## @babel/core

其他包主要是完成某一部分功能，babel 的核心包基于其他包完成整个编译流程，从源代码生成目标代码，生成sourcemap

```js
transformSync(code, options) // 从源代码开始处理
transfromAsync(code, options) // 异步版本

transformFileSync(code, options) // 从源代码文件开始处理
transformFileAsync(code, options).then(result => {})

transformFromAstSync(parsedAst, sourceCode, options) // 从源代码 AST 开始处理
transformFromAstAsync(parentAst, souceCode, options).then(result => {})

createConfigItem(value, optopns) // 用于 plugin 和 preset 封装
```