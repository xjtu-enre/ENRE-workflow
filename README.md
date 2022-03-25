# Guidance on adopting ENRE to a new language

## Project Structure

You are suggested to organize the new project as the following diagram shows:

```text
/                         Project's root
|- docs                   Holding all documentations
   |- README.md           Indexing page
   |- BHVRDIFF.md         Records all differences against the comparison target
   |- ...
|- scripts                Scripts to be executed under the development environment
   |- ...
|- src                    Source files
   |- ...
|- tests                  All tests goes here
   |- cases               Test cases generated from docs
      |- ...
   |- suites              Test suites generated from docs
      |- ...
|- README.md              Project's landing page
```

> This may various depending on which language you have decided ENRE to be written with, so feel free to adjust this into a more practical version.

## Key Concepts

### Align with the latest and official specification

Specification defines how a programing language constructs, including key words, production rules, and runtime behaviors, etc.. It is the most accurate and up-to-date information you can find nowhere else. Please always referring to the specification when you are documenting, coding and discussing.

### Documentations go first

You are always encouraged to document everything you are going to build before actually coding them. This will not only help you to maintain a behavior consistency between the code and what you thought, but also for other people who are using ENRE or trying to contribute to ENRE.

For a detailed description about formats on documentation, feel free to jump right into [Specifications / Documentation](#documentation).

### Testing

A comprehensive testing is the most important quality assurance measure before ENRE is delivered to the community. **Document driven automated testing** is what we are currently exploring ,from which we have gained benefits. Thus you are also suggested to build a similar testing pipeline or migrate ours to your new ENRE project.

For a specification on how this is achieved, please refer to [Specifications / Testing](#testing-1).

## Specifications

### Code Location

If applicable, code location should **start from 1**.

### Documentation

Our current practice is to holding documents and relevant unit test cases together for a future rendering as interactive webpages.

#### Entity/Relation Document

This kind of document holds definitions about what entities and relations can ENRE capture, and details on name, location, extra properties and etc. if capturable.

##### Format

```text
|
|> Title
|
|<----------------------------╶╷
|                              |
|> Syntax                      |
|                              |
|<--------------------------╶╷ |
|                            | |
|> Example                   | |
|                            | |
|> Meta (Expected output) --╶╵╶╵
↓

// Illustrative blocks (such as discussions, syntax explanations) are free to locate at anywhere.
```

Where every blocks means respectively:

> Examples below are excerpt from `ENRE-java` specification.

* **Title**: The title of the document.
  + Example:
    ```text
    Entity: Variable
    ```

* **Syntax**: Production rules or any things describing the syntax.
  + Example:
    ```text
    LocalVariableDeclaration:
      {VariableModifier} LocalVariableType VariableDeclaratorList
    
    LocalVariableType:
      UnannType
      var
    
    ...
    ```

* **Example**: Sample code snippet derived by syntax rules. (Will be extracted for unit test)
  + Example:
    ```java
    var a = 1;            // Legal
    // var b = 2, c = 3.0;   // Illegal: multiple declarators
    ```

* **Meta (Expected output)**: Expected output that analyzers no matter which shall generate.
  + Example:
    ```yaml
    name: VariableDeclaration
    entities:
        -   name: a
            loc: [ 1, 5 ]
            ...
    ...
    ```
  + Extra: You could propose your own specification on format of this block, or refer to `ENRE-js`'s implementation [here](https://github.com/xjtu-enre/ENRE-NT.js/blob/main/scripts/md2/metaSchema.mjs).

#### Behavior Differences Document

This kind of document holds differences between `ENRE` and our comparison target (currently `Understand™`), which may include `Understand™`'s bugs that `ENRE` can handle them properly.

##### Format

This document can be the format of table, follow recommended columns if so.

* **Level**: The issue's severity.
  + Sample Values (You are highly suggested to utilizing emoji for a more intuitive understanding):
    - `❌ Error`
    - `⚠️ Discussion`

* **Description**: The short but slightly detailed description of the issue.

* **Maximum Reproducible `und` Version**: As our comparison target keeps evolving, the issue may be fixed in a future version. So it's necessary to record the max version number under which this issue is still valid and reproducible.

* **`ENRE`'s behavior**: How `ENRE` handles this issue.

* **Detail / Discussion**: May be a link to somewhere in the doc, or a link to an external blog, for a detailed illustration of this issue.

### Testing

#### Document driven automated testing

Using a script to extract code snippets and meta info from doc, and convert them into single unit test cases and test suites respectively. Then utilizing a testing framework to do those testing staffs.

See [`ENRE-js`'s implementation](https://github.com/xjtu-enre/ENRE-NT.js/blob/main/scripts/md2.mjs) for a better understanding.

#### Joint test with `Understand™`

When you adopting `ENRE` to a new language, it's also your job to:

* Write a `Python` script to interact with `Understand™`'s `Python API`, extracting all entities and relations from the `udb` database;
  See [`ENRE-marker`'s implementation](https://github.com/xjtu-enre/ENRE-marker/tree/main/input/java) on how to do that.

* Write a script to align `ENRE`'s entities and relations with `Understand™`'s.

  **Align** means to link the same entities or relations only if both tools' output are completely identical (If it's hard to achieve, at least providing a diff view on how to map one's to the other's and also a reverse mapping).

  > We are currently still working on this, so no more help could be offered temporarily. Stay watched on our project to get an early notification when we are done.
