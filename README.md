# phpstorm-inspect
This package enables you to comfortably use PhpStorm as a CLI tool for static analysis.

Basically, it is a wrapper for PhpStorm's `inspect.sh` script that does some extra work:
* waits for PhpStorm to finish already running inspections (because PhpStorm cannot run more instances of `inspect.sh` in simultaneously; see [IDEA-150272](https://youtrack.jetbrains.com/issue/IDEA-150272))
* clears PhpStorm's cache before every run in order to prevent stale cache issues (otherwise PhpStorm sometimes fails to see new files when switching GIT branches)
* parses XML output generated by PhpStorm and presents it in a readable form (similar to tools like PHP CodeSniffer, PHPMD or PHP CS Fixer)

## Usage
```bash
php bin/phpstorm-inspect <inspectShExecutableFilepath> <phpstormSystemPath> <projectPath> <inspectionProfileFilepath> <inspectedDirectory> [<format>]
```

### Arguments description
* `inspectShExecutableFilepath` - path to `inspect.sh` script
* `phpstormSystemPath` - path to `.WebIde*/system` directory
* `projectPath` - path to project directory (that contains `.idea` directory)
* `inspectionProfileFilepath` - path to inspection profile XML file
* `inspectedDirectory` - path in which are the inspected sources
* `format (optional)` - format of output result, accepted values: "text" (default value) / "checkstyle"

## Example (text format)
```bash
php bin/phpstorm-inspect \
  /opt/PhpStorm-139.1348/bin/inspect.sh ~/.WebIde80/system \
  . .idea/inspectionProfiles/Project_Default.xml ./src
```

### Output
```
File: /home/user/project/src/MyBundle/Controller/UserController.php
--------------------------------------------------------------------------------
Found 1 problems
--------------------------------------------------------------------------------
Line 17: Undefined field: Field 'usre' not found in class
--------------------------------------------------------------------------------

File: /home/user/project/src/MyBundle/Controller/ArticleController.php
--------------------------------------------------------------------------------
Found 2 problems
--------------------------------------------------------------------------------
Line 26: Unused local variable: Unused local variable $articleId
Line 32: Unreachable statement: Unreachable statement
--------------------------------------------------------------------------------
```

## Example (checkstyle format)
```bash
php bin/phpstorm-inspect \
  /opt/PhpStorm-139.1348/bin/inspect.sh ~/.WebIde80/system \
  . .idea/inspectionProfiles/Project_Default.xml ./src checkstyle > report.xml
```

## Content of report.xml 
```
<?xml version="1.0"?>
<checkstyle version="1.0.0">
    <file name="/home/user/project/src/MyBundle/Controller/UserController.php">
        <error line="17" column="0" severity="warning" message="Undefined field: Field 'usre' not found in class" />
    </file>
    <file name="/home/user/project/src/MyBundle/Controller/ArticleController.php">
        <error line="26" column="0" severity="warning" message="Unused local variable: Unused local variable $articleId" />
        <error line="32" column="0" severity="warning" message="Unreachable statement: Unreachable statement" />
    </file>
</checkstyle>
```

## FAQ / Issues
### Why should I use this package when I can simply run the inspections from IDE?
This package comes handy in environments where you do not have graphical interface.

For example, you can run static code analysis on your Continuous Integration server (eg. Jenkins).

### Does CLI inspections work when PhpStorm IDE is running?
Unfortunately not. PhpStorm currently does not support running `inspect.sh` script when IDE is running. You need to close the IDE when you want to run inspections from CLI.

### What PhpStorm versions are supported?
The package was tested with PhpStorm 8.0 (139.732) but the newer versions will probably work too.

## Need help
Contact us on our Slack [http://slack.shopsys-framework.com/](http://slack.shopsys-framework.com/).
