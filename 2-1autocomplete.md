a. 该方案实现查询语句的自动补全主要包含两个模块：
    1. 通过[antlr4ts](https://github.com/tunnelvisionlabs/antlr4ts)进行词法分析及语法解析生成parse tree（AST跟parse tree其实有区别，不过作者文中并未深究），识别用户正在输入的是key、value还是其他情况；
    2. 通过[CodeMirror](https://github.com/codemirror/codemirror5)实现用户查询语句输入的文本框及智能提示弹窗。

简单来说，该方案就是参考编译原理，在parse tree生成后，遍历其节点确定用户当前输入所处的节点类型，利用fetchSuggestions函数向后端发送请求（key或value），然后将返回值存放于组件suggestionsInfo状态。并通过第三方组件[CodeMirror](https://github.com/codemirror/codemirror5)及其[showHint Addon](https://codemirror.net/5/doc/manual.html#addon_show-hint)达到给用户输入自动补全的目的。

注：关于词法分析(Lexer)及语法解析(Parser)写法相关内容，参见[文档](https://github.com/antlr/antlr4/blob/master/doc/index.md)。另外作者提供的[源码](https://github.com/slepowronski/autocomplete/blob/master/src/services/suggestionsNetwork.service.ts)中，fetchSuggestions仅在前端进行模拟，并未实际发送请求，但其表达的意思如此。


b. 如a中所述，模块二仅为前端展示，替代与否于整体影响不大。故下述调研结果主要针对模块一：
    1. 如果查询语句的语法满足[ESTree spec](https://github.com/estree/estree)，大可采用[acorn](https://github.com/acornjs/acorn/tree/master/acorn/)或[babel-parser](https://github.com/babel/babel/tree/main/packages/babel-parser)等现成的解析器，通过解析出的AST来判断所在位置及发送请求；
    2. 记录用户查询历史，搜索查询的历史推荐用户查询的提示；
    3. 针对所收集的用户成功发起查询的语句记录，训练模型，参见[文章](https://medium.com/geekculture/auto-code-generation-using-gpt-2-4e81cb05430)。
    