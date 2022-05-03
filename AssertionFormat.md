# Assertion Block 语法规范

## 术语

以下面的简易文档为例（其中的内容不代表真实情况），其中出现了本规范中所用到的 4 个核心概念。

~~~md
## Dependency: Import

Descriptions...

### Supported pattern

```yaml                                   <--- Group Assertion
name: Import
```

### Syntax: Import declaration

```txt
ImportDeclaration:
    import xxx from xxx ;

...
```

**Examples:**

* Named import                            <--- Case Title

```java                                   <--- Case Code 0
// Foo.java
class Foo {
  /* ... */
}
```

```java                                   <--- Case Code 1
// Bar.java
import Foo
```

```yaml                                   <--- Case Assertion
name: Simple Import
entities:
    ...
dependencies:
    ...
```
~~~

> 请确保上述示例中所列的元素**均在第一层级上**，即不要有缩进，否则脚本会识别不到对应的元素

### Group Assertion

Group Assertion 是每个文件一个的块，仅包含一个指定 Group 名字的属性，应紧随 `## Supported pattern` 。

~~~md
```yaml
name: <Group Name: String>
```
~~~

其中，`Group Name` 只能是一个字符串，这个字符串将被用作存放该文件所产生的所有用例的文件夹的名字。

### Case

Case 是一个测试用例，包含一个标题，若干个自己语言的代码块和一个 Case Assertion 块。

#### Case Title

Case Title 是标识一个 Case 开始的唯一记号，格式为

~~~md
* <Case Name: String>
~~~

其中，`Case Name` 只能是一个字符串，这个字符串将被用作存放该 Case 所产生的所有语言文件的文件夹的名字。

#### Case Code

Case Code 是该测试用例的代码块部分，将被脚本抽取到另外的沙盒环境中去。Case Code 可以只有一个代码块，也可以有多个（以模拟多个文件之间发生的依赖等），格式为

~~~md
```<Lang: String>
// <File Name: String, Path Like>
<Code: String>
```
~~~

其中，
* `Lang` 是表示该代码块语言的字符串，正确的设置 `Lang` 可以在各处启用代码高亮，`Lang` 也被脚本用来识别 Case 的 Code 部分，请务必正确设置；

* `File Name` 是**可选的**设置该代码块被抽取后生成的代码文件的文件名的字符串，如 `Test.java` ，也可以是多层文件夹后的文件，如 `path/to/my/File.java`，这个路径会被脚本正确解析并在沙盒中创建相应的路径。**该项必须以双正斜杠 `//` 作为行起始，且该行不再包含其他内容**；
  > 如果不指定，会默认以 `file0.java`、`file1.java` 的格式为各个代码块命名。

* `Code` 是代码部分，可以写任何代码，**如果没有指定上面的 `File Name`，则该部分的第一行不能是以 `//` 标识的注释。**

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
entities:
    filter: <Entity Kind: String， Optional>
    exact: <Bool, Optional>
    items:
        -   name: <String>
            loc: <Loc String>
            category: <Entity Kind: String, Optional>
            negative: true (Optional)
            ... (More custom prperties)
dependencies:
    filter: <Dependency Kind: String, Optional>
    exact: <Bool, Optional>
    items:
        -   src: <Entity Ref String>
            dest: <Entity Ref String>
            loc: <Loc String>
            category: <Dependency Kind: String, Optional>
            negative: true (Optional)
            ... (More custom prperties)
```
~~~

其中：

* `name` 是该 Case 的名字，将被用作包含所有 Case Code 文件的文件夹的名字，**必须在当前文件中保持唯一**；

* `entities` 断言 Case Code 中所有**想要进行验证的实体**（不必是所有出现的实体）：

  + `filter` 是为 `items` 中所有实体快速指定类别的实体类别字符串，仅当 `items` 中所有实体的类别都一样时才可以使用；
  
  + `exact` 是指示 `items` 中所列的实体即是所有应该被抽取的实体的布尔值，对比结果多于或少于 `items` 中所列的情况均视为失败（不受 `negative` 影响）；

  + `items` 是列举想要进行验证的实体的数组，数组的每一项的第一个属性均以 `-` 开头，**注意缩进**：

    * `name` 是该实体的**短名字**；

    * `loc` 是该实体的**位置字符串**，格式为
      ```md
      <File Name: String, Optional>:<Start Line: Int>:<Start Column: Int>:<End Line: Int, Optional>:<End Column: Int, Optional>
      ```
      其中，`File Name` 是实体所在的文件，可以是文件名（带扩展名）或默认的 `file0`等，如果只有一个文件，则该项连同后面的冒号 `:` 可以省略；随后是 4 个整数， 分别代表开始行号、开始列号、结束行号（可选）、结束列号（可选），以冒号 `:` 分隔。实体结束的行列号可省略当且仅当其可以通过 `开始行列号 + name 的长度` 计算得出；

      例如：
      ```md
      file0:1:0:1:5
      ```
      ```md
      Test.java:2:1:2:8
      ```
    
    * `category` 是该实体的类别字符串，仅当未指定 `filter` 时才需要指定该值，两者同时只能出现一个；

    * `negative` 是指示该实体是一个反例的布尔值，一般不需要显式写出来，仅当需要标识反例时，设置其值为 `true` 即可。凡是成果识别该实体的工具均视为失败；

    * `...` 是其他自定义的描述实体的属性，这些属性不会被脚本解析，可以作自己的测试用。
  
* `dependencies` 断言 Case Code 中所有**想要进行验证的依赖**（不必是所有出现的依赖）：（以下仅说明和 `entities` 中意义用法不一致的属性）

  + `src` 和 `dest` 是实体索引字符串，通过**文件名+类型+下标**的形式来从代码文件中索引出想要的实体，格式为
    ```md
    <File Name: String>/<Entity Kind: String>[<Index: Int>]
    ```
    其中，`File Name` 是该实体所在的文件名，**只能使用 `file0` 格式的文件名**；`Entity Kind` 是该实体的类型名称；`Index` 是该实体在该文件的该类型实体中的索引号，**从 0 开始**。

    例如：
    ```md
    file0/Variable[0]     // 第 1 个代码块的第 1 个变量实体
    ```
    ```md
    file1/Method[3]       // 第 2 个代码块的第 4 个方法实体
    ```
  + `loc` 指示该依赖发生的位置，格式与实体的位置一样，但

    * `File Name` 必填；

    * `End Line` 和 `End Column` **必不填**

