[![codecov](https://codecov.io/gh/intersystems/TestCoverage/branch/master/graph/badge.svg)](https://codecov.io/gh/intersystems/TestCoverage)
[![Quality Gate Status](https://community.objectscriptquality.com/api/project_badges/measure?project=intersystems_iris_community%2FTestCoverage&metric=alert_status)](https://community.objectscriptquality.com/dashboard?id=intersystems_iris_community%2FTestCoverage) 

# Unit Test Coverage for InterSystems ObjectScript

Run your typical ObjectScript %UnitTest tests and see which lines of your code are executed. Includes Cobertura-style reporting for use in continuous integration tools.

## Getting Started

Note: a minimum platform version of InterSystems IRIS® data platform 2019.1 is required.

### Installation: ZPM

If you already have the [InterSystems Package Manager](https://openexchange.intersystems.com/package/InterSystems-Package-Manager-1), installation is as easy as:
```
zpm "install testcoverage"
```

### Installation: of Release

Download an XML file from [Releases](https://github.com/intersystems/TestCoverage/releases), then run:
```
Set releaseFile = "<path on filesystem to xml file>"
Do $System.OBJ.Load(releaseFile,"ck")
```

### Installation: from Terminal

First, clone or download the repository. Then run the following commands:

```
Set root = "<path on filesystem to which repository was cloned/downloaded>"
Do $System.OBJ.ImportDir(root,"*.inc;*.cls","ck",,1)
```

### Installation: from Atelier

Note: this assumes you have the Git plugin for Atelier installed.

1. Right-click in the Atelier Explorer and select Import...
2. Select the "Projects from Git" wizard under the "Git" folder, then click "Next"
3. In the "Select Repository Source" dialog, select "Clone URI" and enter this repository's URI, then click "Next"
4. Select the branch(es) you intend to build from, then click "Next"
5. Select a local destination in which to store the code, then click "Next"
6. Choose "import existing Eclipse projects" and select the TestCoverage project, then click "Next"
7. Configure a connection for the project
8. Synchronize the project with your selected namespace

### Security
Note that, depending on your security settings, SQL privileges may be required for access to test coverage data. The relevant permissions may be granted by running:

```
zw ##class(TestCoverage.Utils).GrantSQLReadPermissions("<username or role that should have read permissions>")
```

For example:

```
zw ##class(TestCoverage.Utils).GrantSQLReadPermissions("_PUBLIC")
```

## User Guide

### Running Tests with Coverage
Generally speaking, set `^UnitTestRoot`, and then call `##class(TestCoverage.Manager).RunTest()` the same you would call `##class(%UnitTest.Manager).RunTest()`. For more information on InterSystems' %UnitTest framework, see the [tutorial](https://docs.intersystems.com/irislatest/csp/docbook/DocBook.UI.Page.cls?KEY=TUNT) and/or the [class reference for %UnitTest.Manager](https://docs.intersystems.com/irislatest/csp/documatic/%25CSP.Documatic.cls?PAGE=CLASS&LIBRARY=%25SYS&CLASSNAME=%25UnitTest.Manager).

The "userparam" argument can be used to pass information about code coverage data collection. For example:

```
Set tCoverageParams("CoverageClasses") = <$ListBuild list or %DynamicArray of class names for which code coverage data should be collected>
Set tCoverageParams("CoverageRoutines") = <$ListBuild list or %DynamicArray of routine names for which code coverage data should be collected>
Set tCoverageParams("CoverageDetail") = <0 to track code coverage overall; 1 to track it per test suite (the default); 2 to track it per test class; 3 to track it per test method.>
Do ##class(TestCoverage.Manager).RunTest(,,.tCoverageParams)
```

The first two arguments to `TestCoverage.Manager:RunTest` are the same as `%UnitTest.Manager`.

At the selected level of granularity (before all tests or a test suite/case/method is run), there will be a search for a file named "coverage.list" within the directory for the test suite and parent directories, stopping at the first such file found. This file may contain a list of classes, packages, and routines for which code coverage will be measured. For .MAC routines only (not classes/packages), the coverage list also supports the * wildcard. It is also possible to exclude classes/packages by prefixing the line with "-". For example, to track coverage for all classes in the `MyApplication` package (except those in the `MyApplication.UI` subpackage), and all routines with names starting with "MyApplication":

```
// Include all application code
MyApplication.PKG
MyApplication*.MAC

// Exclude Zen Pages
-MyApplication.UI.PKG
```

As an alternative approach, with unit test classes that have already been loaded and compiled (and which will not be deleted after running tests) and a known list of classes and routines for which code coverage should be collected, use:

```
Do ##class(TestCoverage.Manager).RunAllTests(tPackage,tLogFile,tCoverageClasses,tCoverageRoutines,tCoverageLevel,.tLogIndex,tSourceNamespace,tProcessIDs,tTiming)
```

Where:

* `tPackage` has the top-level package containing all the unit test classes to run. These must already be loaded.
* `tLogFile` (optional) may specify a file to log all output to.
* `tCoverageClasses` (optional) has a $ListBuild list of class names within which to track code coverage. By default, none are tracked.
* `tCoverageRoutines` (optional) has a $ListBuild list of routine names within which to track code coverage. By default, none are tracked.
* `tCoverageLevel` (optional) is 0 to track code coverage overall; 1 to track it per test suite (the default); 2 to track it per test class; 3 to track it per test method.
* `tLogIndex` (optional) allows for aggregation of code coverage results across unit test runs. To use this, get it back as output from the first test run, then pass it to the next.
* `tSourceNamespace` (optional) specifies the namespace in which classes were compiled, defaulting to the current namespace. This may be required to retrieve some metadata.
* `tPIDList` (optional) has a $ListBuild list of process IDs to monitor. If this is empty, all processes are monitored. By default, only the current process is monitored.
* `tTiming` (optional) is 1 to capture execution time data for monitored classes/routines as well, or 0 (the default) to not capture this data.

### Viewing Results
After running the tests, a URL is shown in the output at which you can view test coverage results. If the hostname/IP address in this URL is incorrect, you can fix it by changing the "WebServerName" setting in the management portal, at System Administration > Configuration > Additional Settings > Startup.

### Reporting on results in Cobertura format
The `RunTest()` method reports back a log index in the "userparam" argument. This can be used to generate a report in the same format as Cobertura, a popular Java code coverage tool. For example:

```
Set userParams("CoverageDetail") = 0
Do ##class(TestCoverage.Manager).RunTest(,"/nodelete",.userParams)
Set reportFile = "C:\Temp\Reports\"_tUserParams("LogIndex")_"\coverage.xml"
Do ##class(TestCoverage.Report.Cobertura.ReportGenerator).GenerateReport(userParams("LogIndex"),reportFile)
```

This exports both the coverage results themselves and the associated source code (in UDL format) for correlation/display, and has been verified with [the Cobertura plugin for Jenkins](https://wiki.jenkins.io/display/JENKINS/Cobertura+Plugin).

## Support

If you find a bug or would like to request an enhancement, [report an issue](https://github.com/intersystems/TestCoverage/issues/new). If you have a question, post it on the [InterSystems Developer Community](https://community.intersystems.com/) - consider using the "Testing" or "Continuous Integration" tags as appropriate.

## Contributing

Please read [contributing](CONTRIBUTING.md) for details on our code of conduct, and the process for submitting pull requests to us.

## Versioning

We use [SemVer](http://semver.org/) for versioning. For the versions available, see the [tags on this repository](https://github.com/intersystems/TestCoverage/tags).

## Authors

* **Tim Leavitt** - *Initial implementation* - [timleavitt](http://github.com/timleavitt) / [isc-tleavitt](http://github.com/isc-tleavitt)

See also the list of [contributors](https://github.com/intersystems/TestCoverage/contributors) who participated in this project.

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
