# jangaroo-idea-migration

This IntelliJ IDEA Plugin provides menu items to help you migrate an Jangaroo application to Jangaroo Ext AS 6. 

It can be used to rewrite your usages of the API net.jangaroo:ext-as:2.0.x in your application to a specified version of Ext AS 6. Usages of the API in ActionScript and MXML classes are rewritten if known replacements are available. This plugin can also be used to rewrite usages of Jangaroo's generated properties classes to Flex-style ResourceBundle access.

The plugin is used for some of the recommended steps for migrating a Jangaroo application. You should only invoke the actions of this plugin as documented in the migration steps below.

## Installation

It is a companion plugin for the [CoreMedia/jangaroo-idea](https://github.com/CoreMedia/jangaroo-idea) plugin version 4.

You must install the "Jangaroo 4" IDEA plugin before installing this plugin.

## Migrating a Jangaroo Maven module

### Prerequistes

1. It's recommended to fix errors found by IntelliJ IDEA in your module before migration. To this end, with still having the old Jangaroo 0.9 plugin installed, select "Analyze | Inspect Code" in IDEA and fix reported errors in *.as and *.exml files as possible. Code that is red in Idea cannot be migrated correctly.

2. It's also recommended to replace untyped API usages if possible. One easy-to-replace pattern are config object constructor calls that use untyped JSON objects. In IDEA, you can use "Find In Path" with case-sensitive regular expression `new [a-z][a-zA-Z0-9]*\(\{` to find such patterns. For example, you could replace
    ```
var foo:Foo = new Foo(new foo({ bar: 'value' }));
```
with
    ```
var fooConfig:foo = new foo();
fooConfig.bar = 'value';
var foo:Foo = new Foo(fooConfig);
```

### Migration

0. Make config constructor parameter optional for MXML
   * In IDEA, use "Replace In Path" and replace the case-sensitive regular expression
   
     `(public\s+function\s+[A-Z][A-Za-z0-9_]*\s*\(\s*[A-Za-z]+\s*:\s*[a-z][a-zA-Z0-9_.]*)(\s*\))`
     
     with
     
     `$1 = null$2`
     
   * Commit these changes, e.g with `git commit -a`

0. Build your Maven module, still using the old Jangaroo version, so that the target directory contains all generated classes afterwards. Generated config and properties classes are required by the following steps.
0. Make EXML target classes that have the same name as the EXML file a baseClass of the EXML class
    
    ```
    mvn net.jangaroo:exml-maven-plugin:2.0.18:exml-target-to-base
    ```
0. Rename EXML files to MXML files

   ```
   mvn net.jangaroo:exml-maven-plugin:2.0.18:convert-to-mxml -DrenameOnly
   ```
0. Create a separate Git commit

   A separate commit is used to avoid that Git looses the history of files that have been renamed and will be modified.
   
   ```
   git add --all
   git commit
   ```
   
0. Convert EXML to MXML

   You probably want to use the latest version of ext-as in the command below.
   Make sure to adapt the path for -DextAsJar to the correct absolute path.
   
   ```
   mvn dependency:get -Dartifact=net.jangaroo:ext-as:6.2.0-6
   mvn net.jangaroo:exml-maven-plugin:2.0.18:convert-to-mxml -DalreadyRenamed -DextAsJar=/home/user/.m2/repository/net/jangaroo/ext-as/6.2.0-6/ext-as-6.2.0-6.jar
   ```
   
0. Use jangaroo-idea-migration IDEA Plugin to migrate API

   This step requires old generated config classes to be present in target/generated-sources. If you've followed all previous steps, these classes should be present, if the migrated modules contains config classes.
   
   * Open module in IDEA having "Jangaroo 4" and this plugin installed.
   * Maven Reimport
   * Select "Refactor | Migrate to Ext AS 6..."
   * Enter the version of ext-as (as in the previous step), e.g. 6.2.0-6
   * Usages of changed API elements will be displayed
   * Click "Do refactor"
   * Afterwards, check if messages "REPEAT the refactoring" show up in IDEA's event log. If so, repeat with "Refactor | Migrate to Ext AS 6..." until no such messages show up anymore.
   
0. Change your module pom.xml to use up-to-date version of jangaroo-tools and ext-as

   * Maven Reimport in IDEA
   * Make sure that IDEA really uses the new versions in its project structure

0. Migrate usages of generated properties classes

   This step requires old generated properties classes to be present in target/generated-sources. If you've followed all previous steps, these classes should be present, if the migrated module contains *.properties files.
   
   * Select "Refactor | Migrate Ext AS Properties"
   * Usages of properties files are displayed
   * Click "Do refactor"
    * Afterwards, check if messages "REPEAT the refactoring" show up in IDEA's event log. If so, repeat with "Refactor | Migrate Ext AS Properties" until no such messages show up anymore.  
   
0. Optimize Imports in AS and MXML Files

  * Install the IDEA Plugin "Optimize Imports For Scope" (https://plugins.jetbrains.com/plugin/8188)
  * Choose "Code|Optimize Imports for Scope/File Mask...", choose File Mask "*.as,*.mxml"

0. Commit the changes and start to fix remaining problems manually
