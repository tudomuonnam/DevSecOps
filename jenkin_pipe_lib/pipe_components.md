## Section
- Stages: define Series of stage
- Steps: define steps instructions
- Post: define series of step that run at the end of pipeline build
- Agent: where the pipeline take place (with label): in docker, in kubernetes, 

## Direction
- Trigger: Push, check out, timer...
- Options: Specific options: timeout, retry ...
- Environment: Define some variable during the buid
- Parameter: User input 
- Stage: Logical grouping stage
...
## Steps
Use: sh, echo, script 

## Commit
### Check out

```vi
pipeline {
    agent any
    stages {
        stage("Checkout") {
        steps {
            git url: 'https://github.com/leszko/calculator.git', branch: 'main'
        }
    }
    }
}
```

### Compile
Command to run program
```vi
stage("Compile") {
    steps {
        sh "./gradlew compileJava"
    }
}
```

### Unit test

```vi
stage("Unit test") {
    steps {
        sh "./gradlew test"
    }
}
```

### Code quality Stage
1. Add Jacoco to Grade

```vi
plugins{
    id 'jacoco'
}
...
jacocoTestCoverageVerification {
    violationRules {
        rule {
            limit {
                minimum = 0.2
            }
        }
    }
}
```

2. Add Code Coverage stage
```vi
stage("Code coverage") {
    steps {
        sh "./gradlew jacocoTestReport"
        sh "./gradlew jacocoTestCoverageVerification"
    }
}
```

Public Code Coverage report
```vi
stage("Code coverage") {
    steps {
        sh "./gradlew jacocoTestReport"
        publishHTML (target: [
            reportDir: 'build/reports/jacoco/test/html',
            reportFiles: 'index.html',
            reportName: "JaCoCo Report"
        ])
        sh "./gradlew jacocoTestCoverageVerification"
    }
}
```
This stage copies the generated JaCoCo report to the Jenkins output. When we run the
build again, we should see a link to the code coverage reports (in the menu on the left-
hand side, below Build Now)

To perform the publishHTML step, you need to have the HTML Publisher
plugin installed in Jenkins. You can read more about the plugin at https://
www.jenkins.io/doc/pipeline/steps/htmlpublisher/.
Note also that if the report is generated but not displayed properly in Jenkins,
you may need to configure Jenkins Security, as described here: https://
www.jenkins.io/doc/book/security/configuring-
content-security-policy/.

3. Adding a Static code analysis stage and Publishing static code analysis reports
```vi
stage("Static code analysis") {
    steps {
        sh "./gradlew checkstyleMain"
    }
}   
```
### SonarQube

## Trigger and Notification

### Triger
- External (from github)
- Polling Souce Control Manager Github, gitlab ... (query to )
- A schedule buid

### Notification

- Email 
- Group chat
- Team Space
