# Jangaroo Migration to Ext AS 6

The IntelliJ IDEA Plugin "Jangaroo Migration to Ext AS 6" provides menu items to help you migrate a Jangaroo 2 Ext AS 3.4 application to Jangaroo 4 FlExt AS 6.

It can be used to rewrite your usages of the API net.jangaroo:ext-as:2.0.x in your application to a specified version of Ext AS 6. Usages of the API in ActionScript and MXML classes are rewritten if known replacements are available. This plugin can also be used to rewrite usages of Jangaroo's generated properties classes to Flex-style ResourceBundle access.

The plugin is used for some of the recommended steps for migrating a Jangaroo application. You should only invoke the actions of this plugin as documented in the migration steps below.

## Installation

"Jangaroo Migration to Ext AS 6" is a companion plugin for the ["Jangaroo 4"](https://github.com/CoreMedia/jangaroo-idea) plugin. You must install the "Jangaroo 4" IDEA plugin before installing this plugin.

Please install both plugins in the usualy way, as described here:

https://www.jetbrains.com/help/idea/installing-updating-and-uninstalling-repository-plugins.html

## Usage

The whole migration process, including usage of this IDEA plugin, is documented on the Jangaroo Tool Wiki page ["Migrating your Jangaroo 2 Application to Jangaroo 4"](/CoreMedia/jangaroo-tools/wiki/Migrate-Jangaroo-2-to-4).
