---
layout: post
title: "Getting Started with Apache Avro 1.11.0 (Java)"
date: 2026-03-08 11:00:00 +0800
categories: Avro Data Serialization
---

This article references the original English documentation:
> [Apache Avro™ 1.11.0 Getting Started (Java)](https://avro.apache.org/docs/current/gettingstartedjava.html)

This is a short guide to getting started with Apache Avro™ using Java. This guide only covers using Avro for data serialization; for information on using Avro for RPC, see Patrick Hunt's [Avro RPC Quick Start](https://github.com/phunt/avro-rpc-quickstart)

## 1. Download

Avro implementations for C, C++, C#, Java, PHP, Python, and Ruby can be downloaded from the [Apache Avro™ releases page](https://avro.apache.org/releases.html). This guide uses the latest version available at the time of writing, `Avro 1.11.0`. For the examples in this guide, download `avro-1.11.0.jar` and `avro-tools-1.11.0.jar`

Alternatively, if you're using Maven, add the following dependency to your POM:

```xml
<dependency>
  <groupId>org.apache.avro</groupId>
  <artifactId>avro</artifactId>
  <version>1.11.0</version>
</dependency>
```

And the Avro Maven plugin (for executing code generation):

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

You can also build the required Avro jar files from source. However, building Avro is beyond the scope of this guide; for more information, see the [Build Documentation page](https://cwiki.apache.org/AVRO/Build+Documentation) in the wiki.

## 2. Defining a Schema

Avro schemas are defined using JSON. Schemas are composed of [primitive types](https://avro.apache.org/docs/current/spec.html#schema_primitive) (null, boolean, int, long, float, double, bytes, and string) and [complex types](https://avro.apache.org/docs/current/spec.html#schema_complex) (record, enum, array, map, union, and fixed). You can learn more about Avro schemas and types from the specification documentation, but for now let's start with a simple schema example `user.avsc`:

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

This schema defines a record representing a hypothetical user. (Note that a schema file can only contain a single schema definition.) A record definition must include at minimum its type (`"type":"record"`), a name (`"name":"User"`), and fields (in this case `name`, `favorite_number`, and `favorite_color`). We also defined a namespace (`"namespace":"example.avro"`), which together with the `name` attribute defines the schema's "full name" (in this case `example.avro.User`).

Fields are defined through an array of objects, with each object defining a name and type (other attributes are optional, see the [record specification documentation](https://avro.apache.org/docs/current/spec.html#schema_record) for more details). The field's type attribute is another schema object, which can be a primitive type or a complex type. For example, our `User` schema's `name` field is the primitive type string, while the `favorite_number` and `favorite_color` fields are both unions, represented by JSON arrays. `unions` is a complex type that can be any of the types listed in the array; for example, `favorite_number` can be `int` or `null`, essentially making it an optional field.

## 3. Serialization and Deserialization with Code Generation

### 3.1 Compiling the Schema

Code generation allows us to automatically create classes based on the schema we defined earlier. Once we have defined the relevant classes, we don't need to use schemas directly in our programs. We use the avro-tools jar package to generate code as follows:

```bash
java -jar /path/to/avro-tools-1.11.0.jar compile schema <schema file> <destination>
```

This generates appropriate source files in a package based on the schema's namespace in the provided target directory. For example, to generate a User class in the package `example.avro` from the schema defined above, run:

```bash
java -jar /path/to/avro-tools-1.11.0.jar compile schema user.avsc .
```

Note that if you're using the Avro Maven plugin, you don't need to manually invoke the schema compiler; the plugin automatically performs code generation on any `.avsc` files present in the configured source directory.

### 3.2 Creating Users

Now that we've completed code generation, let's create some users, serialize them to a data file on disk, and then read back the file and deserialize the user objects.

First, let's create some users and set their fields.

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

As shown in this example, Avro objects can be created by directly calling constructors or using builders. Unlike constructors, builders automatically set any default values specified in the schema. Additionally, builders validate data as it's set, while directly constructed objects won't cause errors until the object is serialized. However, using constructors directly typically provides better performance, as constructors create copies of data structures before writing to them.

Note that we didn't set user1's favorite color. Since the type of this record is `["string", "null"]`, we can set it to a string or leave it as `null`; it's essentially optional. Similarly, we set user3's favorite number to `null` (using builders requires setting all fields, even if they're `null`).

### 3.3 Serializing

Now let's serialize the users to disk.

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

We created a `DatumWriter` that converts Java objects to in-memory serialized format. The `SpecificDatumWriter` class works with generated classes and extracts the schema from the specified generated type.

Next we create a `DataFileWriter` that writes serialized records along with the schema to the file specified in the `dataFileWriter.create` call. We write users to the file by calling the `dataFileWriter.append` method. After writing is complete, we close the data file.

### 3.4 Deserializing

Finally, let's deserialize the data file we just created.

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

This code snippet will output:

```java
{"name": "Alyssa", "favorite_number": 256, "favorite_color": null}
{"name": "Ben", "favorite_number": 7, "favorite_color": "red"}
{"name": "Charlie", "favorite_number": null, "favorite_color": "blue"}
```

Deserialization is very similar to serialization. We created a `SpecificDatumReader`, similar to the `SpecificDatumWriter` we used in serialization, which converts in-memory serialized items to instances of our generated class, in this case `User`. We pass the `DatumReader` and the previously created `File` to `DataFileReader`. Similar to `DataFileWriter`, it reads the schema used by the `writer` and the data from the file on disk. Data is read using the writer's schema contained in the file and the reader's schema provided (in this case the `User` class). The writer's schema needs to know the order of fields written, while the reader's schema needs to know which fields are required and how to fill in default values for fields added since the file was written. If there are differences between the two schemas, they are resolved according to the schema resolution specification.

Next we use `DataFileReader` to iterate through the serialized users and print the deserialized objects to standard output. Note how we perform the iteration: we create a single user object where we store the currently deserialized user, and pass this record object to each call to `dataFileReader.next`. This is a performance optimization that allows `DataFileReader` to reuse the same user object instead of allocating a new user for each iteration, which can be very expensive in terms of object allocation and garbage collection if we're deserializing a large data file. While this technique is the standard way to iterate through data files, if performance isn't an issue, you can also use `for (User user : dataFileReader)`

### 3.5 Compiling and Running the Example Code

This example code is included as a Maven project in the Avro documentation's `examples/java-example` directory. In this directory, run the following commands to build and run the example:

```bash
$ mvn compile # includes code generation via Avro Maven plugin
$ mvn -q exec:java -Dexec.mainClass=example.SpecificMain
```

### 3.6 Beta Feature: Generating Faster Code

In this release, we've introduced a new code generation method that can improve object decoding speed by over 10% and encoding speed by over 30% (future performance enhancements are in progress). To ensure this change is smoothly introduced into production systems, this feature is controlled by a feature flag, the system property `org.apache.avro.specific.use_custom_coders`. In the first release, this feature is off by default. To turn it on, set the system flag to `true` at runtime. For example, in the example above, you can enable the "fatter" encoder as follows:

```bash
$ mvn -q exec:java -Dexec.mainClass=example.SpecificMain \
    -Dorg.apache.avro.specific.use_custom_coders=true
```

Note that you don't need to recompile Avro schemas to access this feature. The feature is compiled and built into your code, and you can turn it on and off at runtime using the feature flag. Therefore, for example, you can turn it on during testing and then turn it off in production. Or you can turn it on in production and quickly turn it off if issues arise.

We encourage the Avro community to use this new feature early to help build confidence. (For those paying for cloud computing resources once, it can lead to meaningful cost savings.) As confidence builds, we will enable this feature by default and eventually eliminate the feature flag (and old code).

## 4. Serialization and Deserialization Without Code Generation

Data in Avro is always stored along with its corresponding schema, which means we can read serialized items at any time, whether or not we know the schema in advance. This allows us to perform serialization and deserialization without generating code.

Let's review the same example as the previous section, but without using code generation: we'll create some users, serialize them to a data file on disk, and then read back the file and deserialize the user objects.

### 4.1 Creating Users

First, we use a Parser to read our schema definition and create a Schema object.

```java
Schema schema = new Schema.Parser().parse(new File("user.avsc"));
```

Using this schema, let's create some users.

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

Since we're not using code generation, we use `GenericRecords` to represent users. `GenericRecord` uses the schema to validate that we only specify valid fields. If we try to set a non-existent field (for example, `user1.put("favorite_animal", "cat")`), we'll receive an `AvroRuntimeException` when running the program.

Note that we didn't set user1's favorite color. Since the type of this record is `["string", "null"]`, we can set it to a string or leave it as `null`; it's essentially optional.

### 4.2 Serializing

Now that we've created the user objects, serializing and deserializing them is almost the same as the example using code generation above. The main difference is that we use generic rather than specific readers and writers.

First, let's serialize our users to a data file on disk.

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

We created a `DatumWriter` that converts Java objects to in-memory serialized format. Since we're not using code generation, we created a `GenericDatumWriter`. It needs the schema to determine how to write `GenericRecords` and to verify that all non-nullable fields are present.

As with the code generation example, we also created a `DataFileWriter` that writes serialized records along with the schema to the file specified in the `dataFileWriter.create` call. We write users to the file by calling the `dataFileWriter.append` method. After writing is complete, we close the data file.

### 4.3 Deserializing

Finally, let's deserialize the data file we just created.

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

This will output:

```java
{"name": "Alyssa", "favorite_number": 256, "favorite_color": null}
{"name": "Ben", "favorite_number": 7, "favorite_color": "red"}
```

Deserialization is very similar to serialization. We create a `GenericDatumReader`, similar to the `GenericDatumWriter` we used in serialization, which converts in-memory serialized items to `GenericRecords`. We pass the `DatumReader` and the previously created `File` to `DataFileReader`. Similar to `DataFileWriter`, it reads the schema used by the `writer` and the data from the file on disk. Data is read using the writer's schema contained in the file, and the reader's schema is provided to `GenericDatumReader`. The writer's schema needs to know the order of fields written, while the reader's schema needs to know which fields are required and how to fill in default values for fields added since the file was written. If there are differences between the two schemas, they are resolved according to the schema resolution specification.

Next, we use `DataFileReader` to iterate through the serialized users and print the deserialized objects to standard output. Note how we perform the iteration: we create a `GenericRecord` object where we store the currently deserialized user, and pass this record object to each call to `dataFileReader.next`. This is a performance optimization that allows `DataFileReader` to reuse the same record object instead of allocating a new `GenericRecord` for each iteration, which can be very expensive in terms of object allocation and garbage collection if we're deserializing a large data file. While this technique is the standard way to iterate through data files, if performance isn't an issue, you can also use `for (GenericRecord user : dataFileReader)`

### 4.4 Compiling and Running the Example Code

This example code is included as a Maven project in the Avro documentation's `examples/java-example` directory. In this directory, run the following commands to build and run the example:

```bash
$ mvn compile
$ mvn -q exec:java -Dexec.mainClass=example.GenericMain
```
