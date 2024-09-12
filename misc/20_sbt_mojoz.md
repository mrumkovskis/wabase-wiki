# Sbt mojoz plugin

## Introduction

Sbt mojoz plugin integrates mojoz library with sbt build tool. 
During build process it: 

* Loads table and view definitions
* Generates DDL scripts
* Generates Scala classes for views

For details on sbt and sbt plugins see [sbt documentation](https://www.scala-sbt.org/1.x/docs/index.html)

## Instalation

Add the following line to `project/plugins.sbt`:

```sbt
addSbtPlugin("org.mojoz" % "sbt-mojoz" % "3.2.0")
```

## Usage

**TODO** identify tasks and settings that are mandatory to use mojoz plugin


To enable just dto generation, add the following line to `build.sbt`:

```sbt
enablePlugins(MojozPlugin)
```

or to enable both dto and schema generation:
```sbt
enablePlugins(MojozPlugin, MojozGenerateSchemaPlugin)
```

To add project specific marco class use `mojozTresqlMacrosClass` setting:

```sbt
mojozTresqlMacrosClass := Some(classOf[org.myproject.MyTresqlMacros])
```

Default view and table folders can be changed: 

```sbt
mojozViewMetadataFolders := Seq(baseDirectory.value / "views")
mojozTableMetadataFolders := Seq(baseDirectory .value / "tables")
```

For scala class generation package name and imports can be changed:

```sbt
  mojozDtosImports := Seq(
    "org.tresql._",
    "dto.common._",
    "org.myproject._"
  )

  mojozDtosPackage := "dto"
```

## List of all sbt-mojoz tasks and settings

**TODO** don't really know much to write sbt plugins are self documenting

Common settings and tasks:

* `mojozCompilerCacheFolder` - `File` - Mojoz compiler cache folder
* `mojozResourceLoader` - `String => InputStream` - Resource loader for compilation
* `mojozMdConventions` - `MdConventions` - Mojoz metadata conventions
* `mojozMetadataFileFilterPredicate` - `File => Boolean` - Predicate to filter files in metadata folders
* `mojozDbNaming` - `String => String` - Db naming rules. Transformation function from metadata name to database name
* `mojozCustomTypesFile` - `Option[File]` - Mojoz custom types file
* `mojozTypeDefs` - `collection.immutable.Seq[TypeDef]` - Mojoz type definitions
* `mojozDbAliasToDb` - `Map[String, String]` - Mojoz database alias to database for views
* `mojozTableMetadataFolders` - `Seq[File]` - Mojoz table metadata folders
* `mojozTableMetadataFiles` - `Seq[(File, String)]` - All table metadata files + relative paths they are kept in
* `mojozRawTableMetadata` - `Seq[YamlMd]` - Raw table metadata
* `mojozTableMetadata` - `TableMetadata` - Table metadata
* `mojozDbNames` - `Seq[String]` - Database names extracted from mojozTableMetadata, used by other tasks. May contain null for default database
* `mojozTresqlTableMetadataFileName` - `String` - File name for tresql table metadata for compiler, defaults to "tresql-table-metadata.yaml"
* `mojozGenerateTresqlTableMetadata` - `File` - Generates tresql table metadata for tresql compiler


Settings and tasks for scala generation:

* `mojozDtosPackage` - `String` - Package where all dtos should be placed in
* `mojozDtosImports` - `Seq[String]` - List of imports for Dtos.scala
* `mojozJobMetadataFolders` - `Seq[File]` - Job metadata folders for wabase projects
* `mojozJobMetadataFiles` - `Seq[(File, String)]` - All job metadata files + relative paths they are kept in
* `mojozViewMetadataFolders` - `Seq[File]` - Mojoz view metadata folders
* `mojozViewMetadataFiles` - `Seq[(File, String)]` - All view metadata files + relative paths they are kept in
* `mojozMetadataFilesForResources` - `Seq[(File, String)]` - All metadata files to be copied to resources and included in -md-files.txt
* `mojozMdFilesFileName` - `String` - File name for metadata filename list of all table and view files, defaults to "-md-files.txt"
* `mojozShouldGenerateMdFileList` - `Boolean` - Should -md-files.txt be generated, defaults to true
* `mojozGenerateMdFileList` - `Seq[File]` - Generates -md-files.txt
* `mojozRawJobMetadata` - `Seq[YamlMd]` - Raw job metadata
* `mojozRawViewMetadata` - `Seq[YamlMd]` - Raw view metadata
* `mojozRawYamlMetadata` - `collection.immutable.Seq[YamlMd]` - Raw yaml metadata - job metadata and view metadata
* `mojozViewMetadata` - `List[ViewDef]` - View metadata
* `mojozShouldCompileViews` - `Boolean` - Should views be compiled, defaults to true
* `mojozShowFailedViewQuery` - `Boolean` - Show query string if view fails to compile, defaults to false
* `mojozCompileViews` - `Seq[File]` - View compilation task. Returns view compiler cache files
* `mojozResourceGenerators` - `Seq[File]` - All mojoz resource generation tasks
* `mojozSourceGenerators` - `Seq[File]` - All mojoz source generation tasks
* `mojozAllSourceFiles` - `Seq[File]` - All mojoz source files - for source watch and view compilation
* `mojozAllCompilerMetadataFiles` - `Seq[File]` - All compiler metadata files - for mojozCompileViews cache invalidation. Customize if mojozTresqlMacrosClass is customized
* `mojozTresqlMacrosClass` - `Option[Class[_]]` - Macros class for view compilation. Defaults to org.tresql.Macros. Customization rarely needed - use tresql-macros.txt instead
* `mojozQuerease` - `Querease` - Creates an instance of Querease for view compilation etc.
* `mojozGenerateDtosScalaFileName` - `String` - File name where dtos are stored, default  Dtos.scala
* `mojozGenerateDtosViewMetadata` - `List[ViewDef]` - View metadata for dtos generation
* `mojozGenerateDtosMappingsViewMetadata` - `List[ViewDef]` - View metadata for dtos mappings generation
* `mojozShouldGenerateDtos` - `Boolean` - Should DTO-s be generated, defaults to true
* `mojozGenerateDtosScala` - `Option[File]` - Generates Dtos.scala
* `mojozGenerateDtosMappingsScala` - `String` - Generates extras (name to class mappings etc.) for Dtos.scala
* `mojozGeneratedFiles` - `Seq[String]` - Lists files generated by MojozPlugin
* `mojozScalaGenerator` - `org.mojoz.metadata.out.ScalaGenerator` - Creates scala (i.e. Dtos) generator
* `mojozUninheritableExtras` - `collection.immutable.Seq[String]` - View extensions not to be inherited

Settings and tasks for schema generation:

* `mojozSchemaSqlDirectory` - `File` - Directory for generated schema files, can be used by mojozSchemaSqlFiles
* `mojozSchemaSqlDbNames` - `Seq[String]` - Database names for schema generation, may contain null for default database
* `mojozSchemaSqlFiles` - `Seq[File]` - Files where to write schema sql, corresponding to mojozSchemaSqlDbNames
* `mojozSchemaSqlGenerators` - `Seq[DdlGenerator]` - SqlGenerators (per db) used to generate schema, see org.mojoz.metadata.out.DdlGenerator for available generators
* `mojozGenerateSchemaSqlFiles` - `Seq[File]` - Generates schema sql
* `mojozPrintSchemaSql` - `InputTask[Unit]` - Prints schema sql string for (space-delimited) table name(s)


# Tresql compiler macro 

It is advisable to enable tresql macro in the project. It will compile tresql statements in scala code.

```sbt
scalacOptions := Seq("-unchecked", "-deprecation", "-feature", "-encoding", "utf8",
"-Xmacro-settings:metadataFactoryClass=org.mojoz.querease.TresqlMetadataFactory" +
", tableMetadataFile=" + mojozGenerateTresqlTableMetadata.value.getCanonicalPath +
", functions=uniso.app.FunctionSignatures" +
", macros=uniso.app.Macros"),
```
