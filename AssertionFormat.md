# Assertion Block 语法规范

## 术语

以下面的简易文档为例（其中的内容不代表真实情况），其中出现了本规范中所用到的 4 个核心概念。

> 标题级号、代码块语言标识符等须严格遵照

~~~md
## Relation: Import                       <--- File Title

Descriptions...

### Supported Patterns                    <--- Pattern Title

```yaml                                   <--- Group Assertion
name: Import
```

#### Syntax: Import declaration           <--- Syntax / Semantic / Runtime / Supplemental

```text
ImportDeclaration :
    import xxx from xxx ;

...
```

##### Examples                            <--- Example Title

###### Named import                       <--- Case Title

```java                                   <--- Case Code 0
//// Foo.java
class Foo {
  /* ... */
}
```

```java                                   <--- Case Code 1
//// path/to/Bar.java
import Foo
```

```yaml                                   <--- Case Assertion
name: Simple Import
entity:
    ...
relation:
    ...
```
~~~

> 请确保上述示例中所列的元素**均在第一层级上**，即不要有缩进，否则脚本会识别不到对应的元素

### Group Assertion

Group Assertion 是每个文件仅一个的块，仅包含一个指定 Group 名字的属性，应紧随 `## Supported Patterns` 。

~~~md
```yaml
name: <Group Name: String>
```
~~~

其中，`Group Name` 只能是一个字符串，这个字符串将被用作存放该文件所产生的所有用例的文件夹的名字。

字符串中可以使用`空格`、`数字`和`其他标点符号`，在生成文件夹时除字母数字外的字符均被替换为`-`。

### Case

Case 是一个测试用例，包含一个标题，若干个自己语言的代码块和一个 Case Assertion 块。

#### Case Title

Case Title 是标识一个 Case 开始的唯一记号，格式为

~~~md
###### <Case Name: String>
~~~

其中，`Case Name` 只能是一个字符串，可以使用任意字符，仅供阅读使用。

#### Case Code

Case Code 是该测试用例的代码块部分，将被脚本抽取到另外的沙盒环境中去。Case Code 可以只有一个代码块，也可以有多个（以模拟多个文件之间发生的依赖等），格式为

~~~md
```<Lang: String>
[//// FenceMeta]
<Code: String>
```
~~~

其中，
* `Lang` 是表示该代码块语言的字符串，正确的设置 `Lang` 可以在各处启用代码高亮，`Lang` 也被脚本用来识别 Case 的 Code 部分，请务必正确设置；

* `FenceMeta` 是配置该测试用例行为的元信息，参考[这个文件的注释部分](https://github.com/xjtu-enre/ENRE-ts/blob/main/packages/enre-doc-meta-parser/src/fence-meta/index.ts)了解全部语法；

* `Code` 是代码部分，可以写任何代码。

> Case Code 的一般性原则：
> * Case Code 中的代码请务必保持简洁，仅体现想要测试的语法点，**不要有非必要的其他语法点出现；**
> * 体现同一测试点的实体或依赖不需要很多，建议不超过 2 个；但体现不同测试点的实体或依赖一定要尽可能覆盖全语言的 Spec；
> * 对于没有反例的代码块，应确保其**是最小可编译可执行的；**
> * 不要直接截取真实项目中的一段代码，这样的代码中的变量命名和注释等会误导读者。推荐的变量名是如 Foo、Bar、Baz 等；
> * 推荐在需要额外说明的实体前后用注释指明特殊之处或需要注意的地方。

#### Case Assertion

Case Assertion 是该测试用例的断言部分，以 `YAML` 格式编写，将被脚本解析以用于对各工具的输出结果进行校验，格式和所有可用的属性为

~~~md
```yaml
name: <String>
entity:
    [type: <Entity Type: String>]
    [extra: <Bool, Default true>]
    items:
        -   name: <String>
            [qualified: <String>]
            loc: <Loc String>
            [type: <Entity Type: String>]
            [negative: <Bool, Default false>]
            ... (More custom prperties)
relation:
    [type: <Relation Type: String, Optional>]
    [extra: <Bool, Optional>]
    items:
        -   src: <Entity Query String>
            dest: <Entity Query String>
            loc: <Loc String>
            [type: <Relation Type: String>]
            [negative: <Bool, Default false>]
            ... (More custom prperties)
```
~~~

其中：

* `name` 是该 Case 的名字，将被用作包含所有 Case Code 文件的文件夹的名字，**必须在当前文件中保持唯一**；

* `entity` 断言 Case Code 中所有**想要进行验证的实体**（不必是所有出现的实体）：

  + `type` 是为 `items` 中所有实体快速指定类别的实体类别字符串；
  
  + `extra` 是指示 `items` 中所列的实体即是所有应该被抽取的实体的布尔值，对比结果多于或少于 `items` 中所列的情况均视为失败（不受 `negative` 影响，默认 `true`，同时指定 `type` 时仅对该类型的实体进行检验）；

  + `items` 是列举想要进行验证的实体的数组，数组的每一项的第一个属性均以 `-` 开头，**注意缩进**：

    * `name` 是该实体的**短名字**；

    * `qualified` 是该实体的**全名**；

    * `loc` 是该实体的**位置字符串**，参考[这个文件的注释部分](https://github.com/xjtu-enre/ENRE-ts/blob/main/packages/enre-doc-meta-parser/src/loc-meta/index.ts)了解全部语法；
    
    * `type` 是该实体的类别字符串；如省略会直接继承上一级的 type 值；如果本级和上级都有，本级的值会覆盖上级的值；两级需至少有一级指定该属性；

    * `negative` 是指示该实体是一个反例的布尔值，一般不需要显式写出来，仅当需要标识反例时，设置其值为 `true` 即可。凡是识别该实体的工具均视为失败；

    * `...` 是其他自定义的描述实体的属性，这些属性不会被脚本解析，可以作自己的测试用。
  
* `relation` 断言 Case Code 中所有**想要进行验证的依赖**（不必是所有出现的依赖）：（以下仅说明和 `entities` 中意义用法不一致的属性）

  + `src` 和 `dest` 是实体索引字符串，通过**类型+名字+位置**的形式来从代码文件中索引出想要的实体，格式为
    ```md
    <Entity Type: String>:<Entity Name: String>[[Entity Predicates]]]
    ```
    其中，`Entity Type` 是该实体的类型名称；`Entity Name` 是**以单引号括起来的**该实体的名字，有 `.` 存在的被解析为**全名**，反之被解析为**短名字**；`Entity Predicates` 是可选的当通过 type 和 name 也无法唯一确定实体时可额外附加的信息，格式请参考[这个文件的注释部分](https://github.com/xjtu-enre/ENRE-ts/blob/main/packages/enre-doc-meta-parser/src/entity-ref-meta/index.ts)。

    例如：
    ```md
    class:'Foo'                     // class 类型的短名字为 Foo 的实体
    ```
    ```md
    class:'Foo.Bar'                 // class 类型的全名为 Foo.Bar 的实体
    ```

    ```md
    parameter:'a'[@loc=1]           // parameter 类型的短名字为 a 的起始行为 1 的实体
    ```

    ```md
    parameter:'a'[@loc=file1:2:1]   // parameter 类型的短名字为 a 的在第 1 个代码块中被定义的起始行为 1 的实体
    ```

    ```md
    function:'foo'[@async=true @generator=true]  // function 类型的短名字为 foo 的实体，且存在属性 async 和 generator 为 true
    ```

  + `loc` 指示该依赖发生的位置，格式与实体的位置一样，但

    * `End Line` 和 `End Column` **必不填**
