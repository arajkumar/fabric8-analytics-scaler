[![Build Status](https://ci.centos.org/buildStatus/icon?job=devtools-fabric8-analytics-scaler-f8a-build-master)](https://ci.centos.org/job/devtools-fabric8-analytics-scaler-f8a-build-master/)


# fabric8-analytics-worker-scaler
OpenShift service that scales fabric8-analytics workers based on number of messages in SQS.
This service directly uses `oc` command-line tool to scale the deployment.
This requires to have mounted service account inside the container.
`workerscaler` service account for that purpose by default. Definitions and role binding of the service account
are available in the [openshift/](openshift/) directory.


## Configuration

The service needs to be configured via environment variables.
The list of variables recognized by the service follows.

`DC_NAME`

Name of the deployment config to scale. Default value: `bayesian-worker-ingestion`

`DEFAULT_REPLICAS`

Default (minimum) number of replicas. Default value: `1`

`MAX_REPLICAS`

Max number of replicas. Default value: `2`

`SQS_QUEUE_NAME`

Name of the queue to monitor (without deployment prefix). Default value: `ingestion_bayesianFlow_v0,ingestion_bayesianPackageFlow_v0`

`OC_PROJECT`

Name of the project in OpenShift where to apply the changes. Default value: `bayesian-preview`

`SCALE_COEF`

Number of messages that singe worker is able to process, i.e. no need to spawn additional pods. Default value: `100`

`SLEEP_INTERVAL`

Interval, in minutes, how often to run the scaler. Default value: `5`

`DRY_RUN`

Run in dry-run mode, do not scale anything. Default value: `false`


The default values can be tweaked directly in the [template](openshift/template.yaml).
The values for specific deployments (staging, production) can be set
in [openshiftio/saas-analytics](https://github.com/openshiftio/saas-analytics/blob/master/bay-services/worker-scaler.yaml) repository.


### Deployment on openshift

OpenShift deployment information is located at [openshift](openshift/) directory.

### Footnotes

#### Check for all possible issues

The script named `check-all.sh` is to be used to check the sources for all detectable errors and issues. This script can be run w/o any arguments:

```
./check-all.sh
```

Expected script output:

```
Running all tests and checkers
  Check all BASH scripts
    OK
  Check documentation strings in all Python source file
    OK
  Detect common errors in all Python source file
    OK
  Detect dead code in all Python source file
    OK
  Run Python linter for Python source file
    OK
  Unit tests for this project
    OK
Done

Overal result
  OK
```

An example of script output when one error is detected:

```
Running all tests and checkers
  Check all BASH scripts
    Error: please look into files check-bashscripts.log and check-bashscripts.err for possible causes
  Check documentation strings in all Python source file
    OK
  Detect common errors in all Python source file
    OK
  Detect dead code in all Python source file
    OK
  Run Python linter for Python source file
    OK
  Unit tests for this project
    OK
Done

Overal result
  One error detected!
```

Please note that the script creates bunch of `*.log` and `*.err` files that are temporary and won't be commited into the project repository.

#### Coding standards

- Local setup instructions and files can be found at `local-setup` directory, in case anyone wants local DynamoDB support. Note that, the container images used in the backup directory are not the same that are used to host the project in OpenShift. This directory should be removed once the local development support for DynamoDB makes its way in the current master branch.

- For any queries related to building of container images on registry.centos.org, or if some modifications are required in the Dockerfiles, then the CentOS Container Pipeline team can be contacted on the `container-build` channel under CentOS on Mattermost.

- For any queries regarding deployment of the application on OpenShift, contact the Deploy team at devtools-deploy@redhat.com or at `devtools-deploy` channel under RH-DevTools on Mattermost.

- You can use scripts `run-linter.sh` and `check-docstyle.sh` to check if the code follows [PEP 8](https://www.python.org/dev/peps/pep-0008/) and [PEP 257](https://www.python.org/dev/peps/pep-0257/) coding standards. These scripts can be run w/o any arguments:

```
./run-linter.sh
./check-docstyle.sh
```

The first script checks the indentation, line lengths, variable names, white space around operators etc. The second
script checks all documentation strings - its presence and format. Please fix any warnings and errors reported by these
scripts.

List of directories containing source code, that needs to be checked, are stored in a file `directories.txt`

#### Code complexity measurement

The scripts `measure-cyclomatic-complexity.sh` and `measure-maintainability-index.sh` are used to measure code complexity. These scripts can be run w/o any arguments:

```
./measure-cyclomatic-complexity.sh
./measure-maintainability-index.sh
```

The first script measures cyclomatic complexity of all Python sources found in the repository. Please see [this table](https://radon.readthedocs.io/en/latest/commandline.html#the-cc-command) for further explanation how to comprehend the results.

The second script measures maintainability index of all Python sources found in the repository. Please see [the following link](https://radon.readthedocs.io/en/latest/commandline.html#the-mi-command) with explanation of this measurement.

You can specify command line option `--fail-on-error` if you need to check and use the exit code in your workflow. In this case the script returns 0 when no failures has been found and non zero value instead.

#### Dead code detection

The script `detect-dead-code.sh` can be used to detect dead code in the repository. This script can be run w/o any arguments:

```
./detect-dead-code.sh
```

Please note that due to Python's dynamic nature, static code analyzers are likely to miss some dead code. Also, code that is only called implicitly may be reported as unused.

Because of this potential problems, only code detected with more than 90% of confidence is reported.

List of directories containing source code, that needs to be checked, are stored in a file `directories.txt`

#### Common issues detection

The script `detect-common-errors.sh` can be used to detect common errors in the repository. This script can be run w/o any arguments:

```
./detect-common-errors.sh
```

Please note that only semantical problems are reported.

List of directories containing source code, that needs to be checked, are stored in a file `directories.txt`

#### Check for scripts written in BASH

The script named `check-bashscripts.sh` can be used to check all BASH scripts (in fact: all files with the `.sh` extension) for various possible issues, incompatibilities, and caveats. This script can be run w/o any arguments:

```
./check-bashscripts.sh
```

Please see [the following link](https://github.com/koalaman/shellcheck) for further explanation about, how the ShellCheck works and which issues can be detected.

