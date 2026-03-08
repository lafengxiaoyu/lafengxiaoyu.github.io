---
layout: post
title: "Apache Avro 1.11.0 入门 (Java 版本)"
date: 2026-03-08 11:00:00 +0800
categories: Avro 数据序列化
---

本文参考原版英文文档：
> [Apache Avro™ 1.11.0 Getting Started (Java)](https://avro.apache.org/docs/current/gettingstartedjava.html)

这是使用 Java 开始使用 Apache Avro™ 的简短指南。 本指南仅涵盖使用 Avro 进行数据序列化；请参阅 Patrick Hunt 的 [Avro RPC快速入门](https://github.com/phunt/avro-rpc-quickstart)，了解如何使用 Avro 进行 RPC

## 1. 下载

可以从 [Apache Avro™ 发布页面](https://avro.apache.org/releases.html)下载 C、C++、C#、Java、PHP、Python 和 Ruby 的 Avro 实现。 本指南使用撰写本文时的最新版本 `Avro 1.11.0`。 对于本指南中的示例，请下载 `avro-1.11.0.jar` 和 `avro-tools-1.11.0.jar`

或者，如果您使用的是 Maven，请将以下依赖项添加到您的 POM：

```xml
<dependency>
  <groupId>org.apache.avro</groupId>
  <artifactId>avro</artifactId>
  <version>1.11.0</version>
</dependency>
```

以及 Avro Maven 插件（用于执行代码生成）：

```xml
<plugin>
  <groupId>org.apache.avro</groupId>
  <artifactId>avro-maven-plugin</artifactId>
  <version>1.11.0</version>
  <executions>
    <execution>
      <phase>generate-sources</phase>
      <goals>
        <goal>schema</goal>
      </goals>
      <configuration>
        <sourceDirectory>${project.basedir}/src/main/avro/</sourceDirectory>
        <outputDirectory>${project.basedir}/src/main/java/</outputDirectory>
      </configuration>
    </execution>
  </executions>
</plugin>
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-compiler-plugin</artifactId>
  <configuration>
    <source>1.8</source>
    <target>1.8</target>
  </configuration>
</plugin>
```

您还可以从源代码构建所需的 Avro jar包。不过构建 Avro 超出了本指南的范围；有关更多信息，请参阅 wiki 中的[构建文档页面](https://cwiki.apache.org/AVRO/Build+Documentation)

## 2. 定义模板（schema）

Avro 模板是使用 JSON 定义的。模板由[基本类型](https://avro.apache.org/docs/current/spec.html#schema_primitive)（null、boolean、int、long、float、double、bytes 和 string）和[复杂类型](https://avro.apache.org/docs/current/spec.html#schema_complex)（record、enum、array、map、union 和 fixed）组成。您可以从规范文档中了解有关 Avro模板和类型的更多信息，但现在让我们从一个简单的schema示例 `user.avsc` 开始：

```json
{"namespace": "example.avro",
 "type": "record",
 "name": "User",
 "fields": [
     {"name": "name", "type": "string"},
     {"name": "favorite_number",  "type": ["int", "null"]},
     {"name": "favorite_color", "type": ["string", "null"]}
 ]
}
```

此模式定义了代表假设用户的记录。 （请注意，模式文件只能包含单个模式定义）记录定义至少必须包括其类型（`"type":"record"`）、一个名称（`"name":"User"`）和字段（在本例中为 `name`、`favorite_number` 和 `favorite_color`）。我们还定义了一个命名空间（`"namespace":"example.avro"`），它与 `name` 属性一起定义了模式的 "full name" 也就是"全名"（在本例中为 `example.avro.User`）。

字段是通过对象数组定义的，每个对象定义一个名称和类型（其他属性是可选的，请参阅[记录规范文档](https://avro.apache.org/docs/current/spec.html#schema_record)以获取更多详细信息）。字段的类型属性是另一个模式对象，它可以是原始类型或复杂类型。例如，我们的 `User` schema 的 `name` 字段是原始类型字符串，而 `favorite_number` 和 `favorite_color` 字段都是联合字符串(`unions`)，由 JSON 数组表示。 `unions` 是一种复杂类型，可以是数组中列出的任何类型；例如， `favorite_number` 可以是 `int` 或 `null`，本质上它是一个可选字段。

## 3. 使用代码生成进行序列化和反序列化

### 3.1 编译Schema(模板)

代码生成允许我们根据之前定义的模式自动创建类。一旦我们定义了相关的类，就不需要在我们的程序中直接使用模式。我们使用 avro-tools jar包生成代码如下：

```bash
java -jar /path/to/avro-tools-1.11.0.jar compile schema <schema file> <destination>
```

这将根据提供的目标文件夹中架构的命名空间在包中生成适当的源文件。例如，要从上面定义的模式中生成包 `example.avro` 中的用户类，运行

```bash
java -jar /path/to/avro-tools-1.11.0.jar compile schema user.avsc .
```

请注意，如果您使用 Avro Maven 插件，则无需手动调用模式编译器；该插件会自动对配置的源目录中存在的任何 `.avsc` 文件执行代码生成

### 3.2 创建Users(用户)

现在我们已经完成了代码生成，让我们创建一些用户，将它们序列化为磁盘上的数据文件，然后读回文件并反序列化用户对象。

首先让我们创建一些用户并设置他们的字段。

```java
User user1 = new User();
user1.setName("Alyssa");
user1.setFavoriteNumber(256);
// Leave favorite color null

// Alternate constructor
User user2 = new User("Ben", 7, "red");

// Construct via builder
User user3 = User.newBuilder()
             .setName("Charlie")
             .setFavoriteColor("blue")
             .setFavoriteNumber(null)
             .build();
```

如本例所示，可以通过直接调用构造函数或使用构建器来创建 Avro 对象。与构造函数不同，生成器将自动设置模式中指定的任何默认值。此外，构建器会按设置验证数据，而直接构造的对象在对象被序列化之前不会导致错误。但是，直接使用构造函数通常会提供更好的性能，因为构造函数会在写入数据结构之前创建数据结构的副本。

请注意，我们没有设置 `user1` 最喜欢的颜色。由于该记录的类型为 `["string", "null"]`，我们可以将其设置为字符串或将其保留为 `null`；它本质上是可选的。同样，我们将 `user3` 的最喜欢的数字设置为 `null`（使用构建器需要设置所有字段，即它们为 `null`）

### 3.3 Serializing(序列化)

现在让我们将用户`Users`序列化到磁盘

```java
// Serialize user1, user2 and user3 to disk
DatumWriter<User> userDatumWriter = new SpecificDatumWriter<User>(User.class);
DataFileWriter<User> dataFileWriter = new DataFileWriter<User>(userDatumWriter);
dataFileWriter.create(user1.getSchema(), new File("users.avro"));
dataFileWriter.append(user1);
dataFileWriter.append(user2);
dataFileWriter.append(user3);
dataFileWriter.close();
```

我们创建了一个 `DatumWriter`，它将 Java 对象转换为内存中的序列化格式。 `SpecificDatumWriter` 类与生成的类一起使用，并从指定的生成类型中提取模式。

接下来我们创建一个 `DataFileWriter`，它将序列化的记录以及模式写入 `dataFileWriter.create` 调用中指定的文件。我们通过调用 `dataFileWriter.append` 方法将用户写入文件。完成写入后，我们关闭数据文件

### 3.4 Deserializing(反序列化)

最后，让我们反序列化我们刚刚创建的数据文件

```java
// Deserialize Users from disk
DatumReader<User> userDatumReader = new SpecificDatumReader<User>(User.class);
DataFileReader<User> dataFileReader = new DataFileReader<User>(file, userDatumReader);
User user = null;
while (dataFileReader.hasNext()) {
  // Reuse user object by passing it to next(). This saves us from
  // allocating and garbage collecting many objects for files with
  // many items.
  user = dataFileReader.next(user);
  System.out.println(user);
}
```

此代码段将输出：

```java
{"name": "Alyssa", "favorite_number": 256, "favorite_color": null}
{"name": "Ben", "favorite_number": 7, "favorite_color": "red"}
{"name": "Charlie", "favorite_number": null, "favorite_color": "blue"}
```

反序列化与序列化非常相似。我们创建了一个`SpecificDatumReader`，类似于我们在序列化中使用的`SpecificDatumWriter`，它将内存中的序列化项转换为我们生成的类的实例，在本例中为`User`。我们将 `DatumReader `和之前创建的 `File` 传递给 `DataFileReader`，类似于 `DataFileWriter`，它读取 `writer` 使用的模式以及磁盘上文件中的数据。将使用文件中包含的写入者模式和读取器提供的模式（在本例中为 `User` 类）读取数据。编写者的模式需要知道写入字段的顺序，而读者的模式需要知道需要哪些字段以及如何填写自文件写入以来添加的字段的默认值。如果两个架构之间存在差异，则根据架构解析规范对其进行解析。

接下来我们使用 `DataFileReader` 遍历序列化的用户并将反序列化的对象打印到标准输出。注意我们如何执行迭代：我们创建了一个单独的用户对象，我们将当前反序列化的用户存储在其中，并将这个记录对象传递给 `dataFileReader.next` 的每个调用。这是一种性能优化，允许 `DataFileReader` 重用相同的用户对象，而不是为每次迭代分配一个新用户，如果我们反序列化一个大数据文件，这在对象分配和垃圾收集方面可能非常昂贵。虽然这种技术是遍历数据文件的标准方法，但如果性能不是问题，也可以使用 `for (User user : dataFileReader)`

### 3.5 编译并运行示例代码

此示例代码作为 Maven 项目包含在 Avro 文档的`examples/java-example` 目录中。在此目录中，执行以下命令来构建和运行示例：

```bash
$ mvn compile # includes code generation via Avro Maven plugin
$ mvn -q exec:java -Dexec.mainClass=example.SpecificMain
```

### 3.6 Beta 功能：生成更快的代码

在此版本中，我们引入了一种新的代码生成方法，可将对象的解码速度提高 10% 以上，并将编码速度提高 30% 以上（未来的性能增强正在进行中）。为了确保将此更改顺利引入生产系统，此功能由功能标志控制，系统属性 `org.apache.avro.specific.use_custom_coders`。在第一个版本中，此功能默认关闭。要打开它，请在运行时将系统标志设置为 `true`。例如，在上面的示例中，您可以启用更"胖"的编码器，如下所示：

```bash
$ mvn -q exec:java -Dexec.mainClass=example.SpecificMain \
    -Dorg.apache.avro.specific.use_custom_coders=true
```

请注意，您不必重新编译 Avro 模式即可访问此功能。该功能被编译并内置到您的代码中，您可以在运行时使用功能标志打开和关闭它。因此，例如，您可以在测试期间将其打开，然后在生产中关闭。或者，您可以在生产中将其打开，如果出现问题，请迅速将其关闭。

我们鼓励 Avro 社区尽早使用这一新功能，以帮助建立信心。 （对于那些为云计算资源一次性付费的人来说，它可以带来有意义的成本节约。）随着信心的建立，我们将默认启用此功能，并最终消除功能标志（和旧代码）

## 4. 无需代码生成的序列化和反序列化

Avro 中的数据始终与其对应的模式一起存储，这意味着无论我们是否提前知道模式，我们都可以随时读取序列化项目。这允许我们在不生成代码的情况下执行序列化和反序列化。

让我们回顾与上一节相同的示例，但不使用代码生成：我们将创建一些用户，将它们序列化为磁盘上的数据文件，然后读回文件并反序列化用户对象

### 4.1 创建Users(用户)

首先，我们使用 Parser 来读取我们的模式定义并创建一个 Schema 对象。

```java
Schema schema = new Schema.Parser().parse(new File("user.avsc"));
```

使用这个Schema(模式)，让我们创建一些用户

```java
GenericRecord user1 = new GenericData.Record(schema);
user1.put("name", "Alyssa");
user1.put("favorite_number", 256);
// Leave favorite color null

GenericRecord user2 = new GenericData.Record(schema);
user2.put("name", "Ben");
user2.put("favorite_number", 7);
user2.put("favorite_color", "red");
```

由于我们不使用代码生成，我们使用 `GenericRecords` 来表示用户。 `GenericRecord` 使用模式来验证我们是否只指定了有效字段。如果我们尝试设置一个不存在的字段（例如，`user1.put("favorite_animal", "cat")`），我们将在运行程序时收到 `AvroRuntimeException`。

请注意，我们没有设置 user1 最喜欢的颜色。由于该记录的类型为 `["string", "null"]`，我们可以将其设置为字符串或将其保留为 `null`；它本质上是可选的

### 4.2 Serializing(序列化)

现在我们已经创建了用户对象，对它们进行序列化和反序列化几乎与上面使用代码生成的示例相同。主要区别在于我们使用通用而不是特定的读取器和写入器。

首先，我们将我们的用户序列化到磁盘上的数据文件

```java
// Serialize user1 and user2 to disk
File file = new File("users.avro");
DatumWriter<GenericRecord> datumWriter = new GenericDatumWriter<GenericRecord>(schema);
DataFileWriter<GenericRecord> dataFileWriter = new DataFileWriter<GenericRecord>(datumWriter);
dataFileWriter.create(schema, file);
dataFileWriter.append(user1);
dataFileWriter.append(user2);
dataFileWriter.close();
```

我们创建了一个 `DatumWriter`，它将 Java 对象转换为内存中的序列化格式。由于我们不使用代码生成，我们创建了一个 `GenericDatumWriter`。它需要模式来确定如何编写 `GenericRecords` 并验证是否存在所有不可为空的字段。

与代码生成示例一样，我们还创建了一个 `DataFileWriter`，它将序列化的记录以及模式写入 `dataFileWriter.create` 调用中指定的文件。我们通过调用 `dataFileWriter.append` 方法将用户写入文件。完成写入后，我们关闭数据文件。

### 4.3 Deserializing(反序列化)

最后，我们将反序列化刚刚创建的数据文件

```java
// Deserialize users from disk
DatumReader<GenericRecord> datumReader = new GenericDatumReader<GenericRecord>(schema);
DataFileReader<GenericRecord> dataFileReader = new DataFileReader<GenericRecord>(file, datumReader);
GenericRecord user = null;
while (dataFileReader.hasNext()) {
  // Reuse user object by passing it to next(). This saves us from
  // allocating and garbage collecting many objects for files with
  // many items.
  user = dataFileReader.next(user);
  System.out.println(user);
}
```

这将会输出：

```java
{"name": "Alyssa", "favorite_number": 256, "favorite_color": null}
{"name": "Ben", "favorite_number": 7, "favorite_color": "red"}
```

反序列化与序列化非常相似。我们创建一个 `GenericDatumReader`，类似于我们在序列化中使用的 `GenericDatumWriter`，它将内存中的序列化项转换为 `GenericRecords`。我们将 `DatumReader`和之前创建的 `File` 传递给 `DataFileReader`，类似于 `DataFileWriter`，它读取 `writer` 使用的模式以及磁盘上文件中的数据。将使用文件中包含的写入者模式读取数据，并将读取器模式提供给 `GenericDatumReader`。编写者的模式需要知道写入字段的顺序，而读者的模式需要知道需要哪些字段以及如何填写自文件写入以来添加的字段的默认值。如果两个架构之间存在差异，则根据架构解析规范对其进行解析。

接下来，我们使用 `DataFileReader` 遍历序列化的用户并将反序列化的对象打印到标准输出。请注意我们如何执行迭代：我们创建一个 `GenericRecord` 对象，我们将当前反序列化的用户存储在其中，并将此记录对象传递给 `dataFileReader.next` 的每个调用。这是一种性能优化，允许 `DataFileReader` 重用相同的记录对象，而不是为每次迭代分配一个新的 `GenericRecord`，如果我们反序列化大型数据文件，这在对象分配和垃圾收集方面可能非常昂贵。虽然这种技术是遍历数据文件的标准方法，但如果性能不是问题，也可以使用 `for (GenericRecord user : dataFileReader)`

### 4.4 编译并运行示例代码

此示例代码作为 Maven 项目包含在 Avro 文档的`examples/java-example` 目录中。在此目录中，执行以下命令来构建和运行示例：

```bash
$ mvn compile
$ mvn -q exec:java -Dexec.mainClass=example.GenericMain
```
