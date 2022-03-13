# Babel

> Babel是一个工具链，用于将ES5+语法编写的代码转换为向后兼容的JS语法。[Babel文档](https://www.babeljs.cn/docs/)

**Babel 能用于**

- 语法转换（esnext、ts等转换为js）

- 通过Polyfill方式在目标环境中添加缺失的特性（引入三方Polyfill模块）

- 源码转换（codemods）

- ...

## Babel编译

Babel 是一个JavaScript转译器，将高级的JS特性转换为低级的JS特性。本质上还是将代码转换成代码。

**Babel 的编译流程**：

1. parse：通过 parse 将源码转换为 AST （抽象语法树）

2. tarnsform：遍历处理 AST

3. generate：将转换处理过的 AST 打印生成目标代码（并生成 soucemap）

> 人能理解源码，计算机能理解AST

**即将源码转换成AST然后通过 对AST进行增删改转换为目标代码的AST结构，然后将AST打印生成对应目标代码。**

## Babebl工具

- @babel/core：用于完成整个编译流程，从源码到目标代码，且可生成sourcemap

- @babel/parse：对源码进行parse生成AST。可以配置 Plugins(jsx、ts...)、sourceType(module、script、unambiguous) 等指定语法

- @babel/traverse：对parse后的AST进行遍历和修改

- @bable/generate：打印AST生成目标代码

- @babel/types：创建AST节点，提供判断节点方法

- @babel/template：批量创建结点，

- @babel/code-frame：创建报错信息

