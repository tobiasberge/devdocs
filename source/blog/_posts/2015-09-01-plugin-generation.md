---
title: Generating plugins with the CLI tools
tags:
    - cli tools
    - plugin generation
    - boilerplate

categories:
- dev
indexed: true
github_link: blog/_posts/2015-09-01-plugin-generation.md

authors: [dn]
---
Creating plugins can be quite repetitive - if there was just a way to generate the base structure automatically... Well - there is a tool for that...

# The CLI tools
A year ago we [announced](https://developers.shopware.com/blog/2014/11/27/shopware-cli-tools/) the [Shopware CLI tools](https://github.com/shopwareLabs/sw-cli-tools) - a console application to help you with your daily Shopware work, e.g. install Shopware from GitHub or a release package.
It also supports installing plugins on the fly, which makes the daily development work a lot easier and fluent. With the
plugin code generator, there is also a (little known) tool to generate whole plugins.

# Create!
After [installing the CLI tools from GitHub](https://github.com/shopwareLabs/sw-cli-tools) you can just run `sw` in you
command line. With the new command `sw plugin:create` the code generator is triggered.

## Creating a base plugin
A very simple example can be generated by just running `sw plugin:create SwagTest`. This will create a plugin called `SwagTest`
in the current working directory.

```
SwagTest/
├── Bootstrap.php
├── LICENSE
├── phpunit.xml.dist
├── plugin.json
├── Readme.md
├── Subscriber
│   └── Frontend.php
└── tests
    └── Test.php
```

It has all you need, to start developing: a working `Bootstrap.php` file, an event subscriber `Frontend.php` with an
example event, a `phpunit.xml.dist` and `test/Test.php` unit test for unit test and a `plugin.json` for your meta data.

So if you want to change some template block in the frontend, you now just need to create the corresponding template file
on your own - the rest works out of the box.

## Creating a backend plugin

If you want to write a plugin with a backend module, you can just run `sw plugin:create --haveBackend SwagBackendTest`. It will ask you for
the model you want to build the backend module for - and then generate this structure:

```
SwagBackendTest/
├── Bootstrap.php
├── Controllers
│   └── Backend
│       └── SwagBackendTest.php
├── LICENSE
├── phpunit.xml.dist
├── plugin.json
├── Readme.md
├── Subscriber
│   ├── ControllerPath.php
│   └── Frontend.php
├── tests
│   └── Test.php
└── Views
    └── backend
        └── swag_backend_test
            ├── app.js
            ├── controller
            │   └── main.js
            ├── model
            │   └── main.js
            ├── store
            │   └── main.js
            └── view
                ├── detail
                │   ├── container.js
                │   └── window.js
                └── list
                    ├── list.js
                    └── window.js

```

As you can see, we now additionally have backend templates, a backend controller and an additional `ControllerPath` subscriber.
Of course you will need to modify `Views/backend/swag_backend_test/model/main.js` and the view templates to your need - as
the code generator can only choose some defaults here. But the general plugin will - again - work out of the box.

![Backend example](/blog/img/backend_create.png)

## Create filters for the new store front bundle
With Shopware 5, the new `StoreFrontBundle` was introduced. It will allow you to easily modify the whole product data aggregation.
This way, adding additional filters, sorters and conditions is very easy - even though the changed mindset behind this
components might seem confusing at first.

By running `sw plugin:create --haveFilters SwagFilterTest` you can easily create the boilerplate code for a simple example
which you can then extend to your needs:

```
SwagFilterTest/
├── Bootstrap.php
├── Components
│   └── SearchBundleDBAL
│       ├── Condition
│       │   ├── SwagFilterTestConditionHandler.php
│       │   └── SwagFilterTestCondition.php
│       ├── Facet
│       │   ├── SwagFilterTestFacetHandler.php
│       │   └── SwagFilterTestFacet.php
│       └── SwagFilterTestCriteriaRequestHandler.php
├── LICENSE
├── phpunit.xml.dist
├── plugin.json
├── Readme.md
├── Subscriber
│   ├── Frontend.php
│   └── SearchBundle.php
└── tests
    └── Test.php
```

Together with our [documentation](https://developers.shopware.com/developers-guide/shopware-5-search-bundle/) it should be
easy to modify.

## Commands
The symfony console commands allow you to provide additional commands for Shopware, which can then be executed from the shell.
This is ideal for long-running tasks or for tasks that you want to trigger with a native cron job.

`sw plugin:create --haveCommands SwagCommandTest` will create a command for you:

```
SwagCommandTest/
├── Bootstrap.php
├── Commands
│   └── CommandTest.php
├── LICENSE
├── phpunit.xml.dist
├── plugin.json
├── Readme.md
├── Subscriber
│   └── Frontend.php
└── tests
    └── Test.php
```

As in most other cases, the plugin creation tool will figure out the name from the command by your plugin name: `SwagCommandTest`
will become `CommandTest` as a command class name - a more realistic example would be `SwagAboCommerce` would generate an
`AboCommerce` command.

## Backend widgets
`sw plugin:create --haveWidget SwagWidgetTest` will create a simple plugin with a backend widget for you:

![Created widget](/blog/img/widget-created.png)

```
SwagWidgetTest/
├── Bootstrap.php
├── Controllers
│   └── Backend
│       └── SwagWidgetTest.php
├── LICENSE
├── phpunit.xml.dist
├── plugin.json
├── Readme.md
├── Snippets
│   └── backend
│       └── widget
│           └── labels.ini
├── Subscriber
│   ├── ControllerPath.php
│   └── Frontend.php
├── tests
│   └── Test.php
└── Views
    └── backend
        └── swag_widget_test
            └── widgets
                └── swag_widget_test.js
```

## API
It's quite easy to add new resources to the Shopware API and make them available via REST. With `sw plugin:create --haveApi SwagApiTest` this gets even more simple:

```
SwagApiTest/
├── Bootstrap.php
├── Components
│   └── Api
│       └── Resource
│           └── Article.php
├── Controllers
│   └── Api
│       └── Article.php
├── LICENSE
├── phpunit.xml.dist
├── plugin.json
├── Readme.md
├── Subscriber
│   ├── ControllerPath.php
│   └── Frontend.php
└── tests
    └── Test.php
```

## Models

`sw plugin:create --haveModels --backendModel=TestName` will create a plugin with a model called `SwagTest` and wire it up
for Shopware:

```
SwagModelTest/
├── Bootstrap.php
├── LICENSE
├── Models
│   └── SwagModelTest
│       ├── Repository
│       └── TestName.p
├── phpunit.xml.dist
├── plugin.json
├── Readme.md
├── Subscriber
│   └── Frontend.php
└── tests
    └── Test.php
```

## Putting it all together
Of course you can also chain all these commands:

`sw plugin:create --haveBackend --haveFilter --haveFrontend --haveModels --haveCommands --haveWidget --haveApi SwagAllTest`

Will create this structure:

```
SwagAllTest/
├── Bootstrap.php
├── Commands
│   └── AllTest.php
├── Components
│   ├── Api
│   │   └── Resource
│   │       └── AllTest.php
│   └── SearchBundleDBAL
│       ├── Condition
│       │   ├── SwagAllTestConditionHandler.php
│       │   └── SwagAllTestCondition.php
│       ├── Facet
│       │   ├── SwagAllTestFacetHandler.php
│       │   └── SwagAllTestFacet.php
│       └── SwagAllTestCriteriaRequestHandler.php
├── Controllers
│   ├── Api
│   │   └── AllTest.php
│   ├── Backend
│   │   └── SwagAllTest.php
│   └── Frontend
│       └── SwagAllTest.php
├── LICENSE
├── Models
│   └── SwagAllTest
│       ├── AllTest.php
│       └── Repository.php
├── phpunit.xml.dist
├── plugin.json
├── Readme.md
├── Snippets
│   └── backend
│       └── widget
│           └── labels.ini
├── Subscriber
│   ├── ControllerPath.php
│   ├── Frontend.php
│   └── SearchBundle.php
├── tests
│   └── Test.php
└── Views
    ├── backend
    │   └── swag_all_test
    │       ├── app.js
    │       ├── controller
    │       │   └── main.js
    │       ├── model
    │       │   └── main.js
    │       ├── store
    │       │   └── main.js
    │       ├── view
    │       │   ├── detail
    │       │   │   ├── container.js
    │       │   │   └── window.js
    │       │   └── list
    │       │       ├── list.js
    │       │       └── window.js
    │       └── widgets
    │           └── swag_all_test.js
    └── frontend
        └── swag_all_test
            └── index.tpl
```

# Additional options
In addition to the options mentioned above, there are also these two flags:

* `--namespace`: Namespace of the plugin (Default: `frontend`, possible: `frontend`, `core`, `backend`)
* `--licenseHeader`: File you want to include in each file as license header

# Why should I use it?
Of course writing plugins by hand is perfectly fine. The plugin generator just makes generating the files and folders
much easier and faster and takes care of the correct naming convention. So even when not using it, you might take a peek,
how e.g. store front filters are created.
Furthermore, it's also frequently updated to reflect best practice approaches, so it might also provide nice insights into
how plugins are currently written at Shopware itself. Of course, you still have to make sure that everything works as you
intended it to - so please be aware that there is no official support for this tool.

In the future there might be even more helpful flags, as "create shopping world", "register attributes" or "create newsletter element".
If you have any idea for additional generation templates - pull requests on [GitHub](https://github.com/shopwareLabs/sw-cli-tools) are appreciated.
