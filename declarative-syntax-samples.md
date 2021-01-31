# Jenkins Declarative Pipeline

## First Declarative Pipeline

```groovy
pipeline {
    agent any
    stages {
        stage('Welcome Step') {
            steps { 
                echo 'Welcome to LambdaTest'
            }
        }
    }
}
```

## pipeline

```groovy
pipeline {
}
```

## Agent

### agent Any

```groovy
pipeline {
     agent any
}
```

### agent none

```groovy
pipeline {
     agent none
}
```

### agent label

```groovy
pipeline {
       agent {
           label 'linux-machine'
       }
}
```

## stages

```groovy
pipeline {
       agent {
           label 'linux-machine'
       }
     stages {
     }
}
```

## stage

```groovy
pipeline {
       agent {
           label 'linux-machine'
       }
     stages {
         stage('build step') {
         }
     }
}
```

## steps

```groovy
pipeline {
     agent any
     stages {
         stage('build step') {
              steps {
                 echo "Build stage is running"
              }
         }
     }
}
```

## Environment Variables

### Referring Env Variables from Jenkins configuration

```groovy
pipeline {
    agent any
    stages {
        stage('Initialization') {
            steps {
                echo "${JAVA_INSTALLATION_PATH}"
            }
        }
   }
}
```

### Creating & Referring Environment variable in Jenkinsfile

```groovy
pipeline {
    agent any
    environment {
        DEPLOY_TO = 'production'
    }
    stages {
        stage('Initialization') {
            steps {
                echo "${DEPLOY_TO}"
            }
        }
    }
}
```

### Initialize Env variables using sh commands

```groovy
pipeline {
    agent any
    stages {
        stage('Initialization') {
            environment {
                   JOB_TIME = sh (returnStdout: true, script: "date '+%A %W %Y %X'").trim()
            }
            steps {
                sh 'echo $JOB_TIME'
            }
        }
    }
}
```

## credentials

### Loading credentials in declarative pipeline

```groovy
pipeline {
    agent any
    environment {
        MY_CRED = credentials('MY_SECRET')
    }
    stages {
        stage('Load Credentials') {
            steps {
                echo "Username is $MY_CRED_USR"
                echo "Password is $MY_CRED_PSW"
            }
        }
    }
}
```

## when

### Simple when block

```groovy
pipeline {
     agent any
     stages {
         stage('build') {
              when {
                  branch 'dev'
              }
              steps {
                 echo "Working on dev branch"
              }
         }
     }
}
```

### when block using Groovy expression

```groovy
pipeline {
     agent any
     stages {
         stage('build') {
              when {
                  expression {
                     return env.BRANCH_NAME == 'dev';
                  }
              }
              steps {
                 echo "Working on dev branch"
              }
         }
     }
}
```

### when block with environment variables

```groovy
pipeline {
    agent any
    environment {
        DEPLOY_TO = 'production'
    }
    stages {
        stage('Welcome Step') {
            	when {
    environment name: 'DEPLOY_TO', value: 'production'
}
            steps {
                echo 'Welcome to LambdaTest'
            }
        }
    }
}
```

### when block with multiple conditions & all conditions should be satisfied

```groovy
pipeline {
    agent any
    environment {
        DEPLOY_TO = 'production'
    }
    stages {
        stage('Welcome Step') {
            when {
                allOf {
                    branch 'master';
                    environment name: 'DEPLOY_TO', value: 'production'
                }
            }
            steps {
                echo 'Welcome to LambdaTest'
            }
        }
    }
}
```

### when block with multiple conditions & any of the given conditions should be satisfied

```groovy
pipeline {
    agent any
    environment {
        DEPLOY_TO = 'production'
    }
    stages {
        stage('Welcome Step') {
            when {
                anyOf {
                    branch 'master';
                    branch 'staging'
                }
            }
            steps {
                echo 'Welcome to LambdaTest'
            }
        }
    }
}
```

## tools

### Loading maven using tools

```groovy
pipeline {
    agent any
    tools {
        maven 'MAVEN_PATH'
    }
    stages {
         stage('Load Tools') {
              steps {
                 sh "mvn -version"
              }
         }
     }
}
```

## parallel

```groovy
pipeline {
    agent any
    stages {
        stage('Parallel Stage') {
            parallel {
                stage('windows script') {
                    agent {
                        label "windows"
                    }
                    steps {
                        	echo "Running in windows agent"
		                    bat 'echo %PATH%'
                    }
                }
                stage('linux script') {
                    agent {
                        label "linux"
                    }
                    steps {
                       sh "Running in Linux agent"
                    }
                }
             }
        }
    }
}
```

## post

### post with multiple conditional blocks

```groovy
pipeline {
     agent any
     stages {
         stage('build step') {
              steps {
                 echo "Build stage is running"
              }
         }
     }
     post {
         always {
             echo "You can always see me"
         }
         success {
              echo "I am running because the job ran successfully"
         }
         unstable {
              echo "Gear up ! The build is unstable. Try fix it"
         }
         failure {
             echo "OMG ! The build failed"
         }
     }
}
```

## Marking build as UNSTABLE

### Mark build as UNSTABLE based on Code Coverage

```groovy
pipeline {
    agent any
    tools {
        maven 'MAVEN_PATH'
        jdk 'jdk8'
    }
    stages {
        stage("Tools initialization") {
            steps {
                sh "mvn --version"
                sh "java -version"
            }
        }
        stage("Checkout Code") {
            steps {
                git branch: 'master',
                url: "https://github.com/iamvickyav/spring-boot-data-H2-embedded.git"
            }
        }
        stage("Building Application") {
            steps {
               sh "mvn clean package"
            }
        }
        stage("Code coverage") {
           steps {
               jacoco(
                    execPattern: '**/target/**.exec',
                    classPattern: '**/target/classes',
                    sourcePattern: '**/src',
                    inclusionPattern: 'com/iamvickyav/**',
                    changeBuildStatus: true,
                    minimumInstructionCoverage: '30',
                    maximumInstructionCoverage: '80')
               }
           }
        }
}
```

## triggers

### triggers sample

```groovy
pipeline {
    agent any
    triggers {
        cron('H/15 * * * *')
    }
    stages {
        stage('Example') {
            steps {
                echo 'Hello World'
            }
        }
    }
}
```
