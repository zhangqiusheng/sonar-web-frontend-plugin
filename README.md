Sonar plugin for JS/CSS/HTML/SASS/AngularJS
===========================================

# Introduction

Sonarqube plugin for the Web world with various technologies and languages (JavaScript, CSS, SASS, HTML, AngularJS...).
This plugin consumes reports generated by tools that are heavily used by the Web community:

- Linters:
  - [JSHint](http://jshint.com/)
  - [CSS Lint](http://csslint.net/)
  - [SCSS Lint](https://github.com/brigade/scss-lint)
  - [HTMLHint](http://htmlhint.com/)
  - [ESLint plugin for AngularJS](https://github.com/Gillespie59/eslint-plugin-angular)
- Test results (unit tests and integration tests)
  - [Jasmine](http://jasmine.github.io/)
- Code coverage:
  - [Istanbul](https://gotwarlost.github.io/istanbul/)
- Code duplication
  - [Simian](http://www.harukizaemon.com/simian/)
  - [CPD](http://pmd.sourceforge.net/pmd-5.1.2/cpd-usage.html)


The reports are either directly produced by the tools or by our [gulp tasks](https://github.com/groupe-sii/front-end-continuous-integration).

# How to use it ?

## Build your Web project

When developping Web applications, you can use gulp to build, run tests, minify and package your project. Some tasks will already generate reports like Istanbul that generates a LCOV file.
You can also add additional tasks to run linters and produce reports. Theses tasks are already available [here](https://github.com/groupe-sii/front-end-continuous-integration).

## Configure your project for Sonar

In order to let Sonar analyze your project, you have to provide some configuration. You can define a file named sonar-project.properties at the root of your project with the following information:

```
# Required metadata
sonar.projectKey=<unique key for your project>
sonar.projectName=<human readable name for your project>
sonar.projectVersion=<project version>

# Comma-separated paths to directories with sources (required)
sonar.sources=<source folder>

# exclude some folders (typically dependencies)
sonar.exclusions=<exclusions>

# Encoding of the source files
sonar.sourceEncoding=UTF-8

```

Example:

```
# Required metadata
sonar.projectKey=my-project
sonar.projectName=My Project Name
sonar.projectVersion=0.1

# Comma-separated paths to directories with sources (required)
sonar.sources=src/app

# exclude some files and folders (typically dependencies)
sonar.exclusions=bower_components/**/*, node_modules/**/*

# Encoding of the source files
sonar.sourceEncoding=UTF-8

```

## Install the plugin

### Grab the plugin

#### Download the JAR

You can download the v2.0.0 release for Sonar 4.5.x directly [on Sonatype](https://oss.sonatype.org/content/groups/public/fr/sii/sonar/sonar-sii-plugin-parent/2.0.0/sonar-sii-plugin-parent-2.0.0.jar).


#### Generate from sources

To build from source, you need to ensure that:
- Java JDK 7+ is installed (be sure to use JDK and not JRE)
- [Maven](https://maven.apache.org/download.cgi) is installed

Clone this git repository.
Go into the cloned folder (sonar-web-frontend-plugin by default).
Switch to the branch for your Sonar version (do not use directly the master branch as it may not work). For example, for Sonar 4.5.x:
```
git checkout sonar-4.5.x
```

Generate the plugin using [Maven](https://maven.apache.org/download.cgi):

```
mvn clean install -DskipTests
```

This will generate a JAR file located at sonar-web-frontend-plugin/target/sonar-web-frontend-plugin-<version>.jar.

### Install the plugin in Sonar

Copy the JAR file into your Sonarqube server plugins directory (SONARQUBE_HOME/extensions/plugins directory) and restart the Sonarqube server.

## Run sonar

Reports are generated by gulp and Sonar is ready to be executed. You just have to follow standard [Sonarqube instructions to run Sonar analysis](http://docs.sonarqube.org/display/SONAR/Analyzing+Source+Code) (just execute the sonar-runner).

# Code analysis

The plugin provides many metrics specialized for Web and store them into Sonar.

## Code quality

The linters (JSHint, HTMLHint, CSSLint, SCSSLint) provide many information about code violations (issues) for every language or framework (JavaScript, CSS, HTML, SASS/Compass, AngularJS). These issues are integrated in Sonar and are usable in the different views of Sonar. We also provide a [dashboard widget for issues](#issueswidget).

## Test results

Jasmine can generate a JUnit report indicating the test results (which tests have failed, which have succeed and which have been ignored). See [gulp-jasmine documentation](https://www.npmjs.com/package/gulp-jasmine) to know how to generate JUnit report. The plugin consumes this JUnit file and add test results information into Sonar. The plugin can also differentiate unit testing from integration testing.

## Code coverage

If you use Istanbul in your project, it will automatically generate a LCOV file. This file provides information about code coverage of your source code covered by your tests. This file is consumed by the plugin and add this information into Sonar and is usable in different views of Sonar.

## Code duplication

If you use Simian or CPD in your project, it will generate a report for code duplication. This file is consumed by the plugin and adds this information into Sonar and is usable in different views of Sonar. We also provide a [dashboard widget for code duplications](#duplicationswidget).


## Widgets

The plugin provides two new widgets for multi-language projects. To add those widgets in Sonar, login as administrator and click on "Configure Widgets" button. A banner containing available widgets appears:

![widgets](https://cloud.githubusercontent.com/assets/645363/14705599/a798d040-07ba-11e6-8d8c-83b1bc3d32b8.png)

Just click on "Add widget" button on the widget you want on your dashboard.

### <a name="issueswidget"></a> Issues

The widget separate issues counters by language instead of only one counter for all languages that is not revelant. You can have 5000 issues on HTML because you are using a particular framework that doesn't respect HTML norms but only have 1 issue for JavaScript. Without the plugin you will only see 5001 issues and your project manager can tell you that you are a bad developper. But with this widget you can prove that your JavaScript code is really good !

![Issues](https://cloud.githubusercontent.com/assets/645363/8209254/57c3891c-150c-11e5-983e-955dbb69863e.png)

### <a name="duplicationswidget"></a> Code duplication

Same as above, the widget separates the count of duplicated code by language.

![Code duplication](https://cloud.githubusercontent.com/assets/645363/8209318/a5570a14-150c-11e5-8c4e-4aa064ef60df.png)


# Options

## Sonar options

### Reports path

You can change the path to every generated report directly in your sonar.properties file by adding the following properties:

```
# issues reports with default paths
sonar.sii.quality.js.report.path=/reports/sonar/jshint.json
sonar.sii.quality.css.report.path=/reports/sonar/csslint.json
sonar.sii.quality.html.report.path=/reports/sonar/htmlhint.json
sonar.sii.quality.scss.report.path=/reports/sonar/scsslint.json
sonar.sii.quality.angular.eslint.report.path=/reports/sonar/eslint-angular.json

# unit test results with default paths
sonar.sii.test.unit.js.report.path=/reports/sonar/js-ut.xml
sonar.sii.test.it.js.report.path=/reports/sonar/js-it.xml

# code coverage with default paths
sonar.sii.coverage.ut.js.report.path=/reports/sonar/js-ut.lcov
sonar.sii.coverage.it.js.report.path=/reports/sonar/js-it.lcov
sonar.sii.coverage.overall.js.report.path=/reports/sonar/js-overall.lcov

# code duplication with default paths
sonar.sii.duplication.js.report.path=/reports/sonar/js-duplication.xml
sonar.sii.duplication.css.report.path=/reports/sonar/css-duplication.xml
sonar.sii.duplication.html.report.path=/reports/sonar/html-duplication.xml
sonar.sii.duplication.scss.report.path=/reports/sonar/scss-duplication.xml

```

### Other options

#### Change extension of analyzed files

You can specify which files are analyzed for each language. You can change the values of the following properties in your sonar-project.properties:

```
# file suffixes with default values
sonar.sii.js.suffixes=.js
sonar.sii.html.suffixes=.html
sonar.sii.css.suffixes=.css
sonar.sii.scss.suffixes=.scss
```


#### Do not warn if report is missing

For indicating that reports are not found, the plugin logs a warning. This warning is useful when setting up your project to know if something is misconfigured. You can disable these logs using the following property in your sonar-project.properties:

```
sonar.sii.logs.report.missing.skip=true
```

#### Do not fail if a source file is missing

By default, in order to keep coherence, the analysis done by the plugin will fail if the generated report has an entry on a file that doesn't exist. Even if it is not recommended, you can disable the failure by using the one of the following properties in your sonar-project.properties:

```
# set to false to disable missing source file while analyzing code quality
sonar.sii.quality.js.file.missing.fail=false
sonar.sii.quality.scss.file.missing.fail=false
sonar.sii.quality.css.file.missing.fail=false
sonar.sii.quality.html.file.missing.fail=false
sonar.sii.quality.eslint.angular.file.missing.fail=false

# set to false to disable missing source file while analyzing test results
sonar.sii.test.unit.js.file.missing.fail=false
sonar.sii.test.it.js.file.missing.fail=false

# set to false to disable missing source file while analyzing code coverage
sonar.sii.coverage.ut.js.file.missing.fail=false
sonar.sii.coverage.it.js.file.missing.fail=false
sonar.sii.coverage.overall.js.file.missing.fail=false

# set to false to disable missing source file while analyzing code duplication
sonar.sii.duplication.js.file.missing.fail=false
sonar.sii.duplication.html.file.missing.fail=false
sonar.sii.duplication.css.file.missing.fail=false
sonar.sii.duplication.scss.file.missing.fail=false
```

#### Skip analysis

By default, the plugin also store information about the file (number of lines, number of lines of code, number of lines of comment...).

Sonar doesn't allow two plugins to store the same information. So if you are using another plugin that also store this information, the second plugin analysis will fail. By default our plugin doesn't override information stored by another plugin. But we can't force plugin execution order. So it may happen that our plugin is run before the other one. That's why we provide the way to explicitly tell our plugin to not store file information. To do this, use the following properties in your sonar-project.properties:

```
sonar.sii.quality.js.file.metrics.skip=true
sonar.sii.quality.html.file.metrics.skip=true
sonar.sii.quality.css.file.metrics.skip=true
sonar.sii.quality.scss.file.metrics.skip=true
sonar.sii.quality.eslint.angular.file.metrics.skip=true
```

Sonar also provide CPD analysis. Like described above, code duplication analysis can fail if two plugins are doing it.
You can either disable our analysis or disable Sonar CPD analysis:

```
# disable duplication analysis done by our plugin
sonar.sii.duplication.js.skip=true
sonar.sii.duplication.html.skip=true
sonar.sii.duplication.css.skip=true
sonar.sii.duplication.scss.skip=true

# disable Sonar CPD analysis
sonar.cpd.exclusions=**
```

# Supported Sonar versions

As Sonar team makes big changes on plugin APIs between two versions, it takes really long time to update the plugin for each version. So we decided to only support the LTS version (currently 4.5.x). We also try to make the last release version (currently 5.4) but some APIs have totally disappeared while other are no more working. When Sonar 5.5 will be released, we will try to make adjustments to make our plugin working on it. No effort is made for Sonar versions between 4.5.x and latest version. The plugin may work or not depending on what you are using and the version of Sonar.

# Roadmap

## Main features

- Code complexity ([Plato](https://github.com/es-analysis/plato) ...)
- Support new languages (TypeScript, Less, ...)
- Support Sonar 5.x (LTS version) when it will be available
- Add new widgets for better user experience

See https://github.com/groupe-sii/sonar-web-frontend-plugin/labels/feature for full list of features

See https://github.com/groupe-sii/sonar-web-frontend-plugin/milestones for future versions
  