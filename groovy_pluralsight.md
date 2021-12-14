Jenkins with Groovy - Pluralsight


#### Practice info
```
Folder: Documents/jenkinsWithGroovy
start command: $ java -jar jenkins.war 
others: jim & 123
```


# Executing Groovy Script on Startup

- Configuration as code: This makes configuration portable in a way that it can be applied to multiple instances 
- init.groovy.d: directory contains the groovy scripts that will be executed during the startup
    - The files will be executed in alphabetical order
    -  this folder should be in JENKINS_HOME directory 

# check logs
- `<host>:<port>/log/all`
# restart
- `<host>:<port>/restart`

# Working with Exceptions in Groovy
- Infrastructure as code - configuration in script
- Better to handle exceptions at high level


# Creating builds with groovy
- Where build configuration is stored
- Get a grasp on how the rest of the information is stored
- Content is in version control

## where the build configuration is stored
1. `cd $JENKINS_HOME/jobs/`
2. go to the folder of a job you want to check
3. you will find a file called `config.xml`

### check the build information
1. `cd $JENKINS_HOME/jobs/`
2. go to the folder of a job you want to check
3. `cd builds`
4. then you can go to any of build number and check the information such as logs, changelog, build.xml


# The Jenkinsfile
- Jenkinsfiles are Groovy scripts
- Jenkinsfiles are in two types: `Declarative` & `Scripted`

## A Sample Jenkinsfile

```jenkins

node {  // if you have multiple nodes you can specify which node to execute.
	
    stage('Build') {
    	echo 'Building'
    }
    
    stage('Test') {
    	echo 'Testing'
    }
    
    stage('Deploy') {
    	echo 'Deploying'
    }
}
```


### Add java project to jenkins pipeline

```jenkins

node {
	stage('SCM') {
    	echo "Gathering code from version control"
        git branch: "${branch}", url: 'https://github.com/Butare/payment'
    }
	
    stage('Build') {
    	echo "Building..."
        sh 'gradlew build'
        echo 'Building new feature'
    }
    
   	stage('Test') {
    	echo 'Testing...'
    }
    
    stage('Deploy') {
    	echo 'Deploying...'
    }
}

```

### check the pipeline info

- `cd $JENKINS_HOME/workspace/`
- `cd [pipeline-name]@script` folder


## Working with Shared Libraries and Plug-Ins

### Reusing Groovy Code in Jenkins

Why bother with these advanced language feature?
- The more complex you get, the deeper you go in the stack
- Global pipeline libraries are trusted libraries, code which executes with a higher degree of trust than everything else. Must secure global pipeline libraries.
- Global Pipeline Library repo must be secure

### our demo scenario
- Next step after build is to push to a deployment server
- QA can review the history to troubleshoot problems
- QA doesn't need to log into Jenkins, so, the logs will be saved in the file named `releasenotes.txt`
- In order to that we, need a shared library that will generate logs and save it to a file
 - Details of files: Name, Size 

#### Steps to implement our demo
1. Develop groovy script
2. Lists directory contents
3. Persists that information to a file `releasenotes.xtx`
4. Commit that script to version control
5. Configure the library in Jenkins
6. Execute the library in our pipeline script
7. Find a problem

Notes:
- For your library to be called by Jenkins by default, you need to place it in a directory called VARS. however, you can place the library in any location but you will need to tell Jenkins the location.
- You can use this library in any pipeline

#### Configure Jenkins to use your Global library 
1. Navigate to ` Manage Jenkins -> Configure System` on browser
2. Scroll down to `Global Pipeline Libraries` section, then
3. Add the library name


### Demo: Upgrading Our ReleaseNotes
#### Shared Pipelines
All your builds have the same phases
- `SCM -> Build -> Test -> Deploy`
we would implement the shared pipelines for two reasons
- To save time
- To enforce conventions

A shared pipeline is just another shared script
Demo:
- Look at existing Jenkinsfile pipeline
- Copy it to our SharedFunction repo
- Modify it to reflect the conventions of shared libraries in Jenkins
- Configure it as a shared library in Jenkins
- Modify our original Jenkinsfile to call it


#### Working with Plug-ins
- Startup scripts enforce configuration
- Especially important with containers
- Use scripts to enforce plug-in state


#### Plugin whitelist

- Create a .txt file contains a whitelisted plugins
- Upload a file in a jenkins "userContent", `http://localhost:8080/userContent` or `$JENKINS_HOME/userContent/`



# Managing Users and Credentials

## Credentials & Users
- A user has a logon and permissions into Jenkins
- Credentials are used by Jenkins to access something outside jenkins.


## Plug-ins

- Actice Choices: https://plugins.jenkins.io/uno-choice/
- Scriptler Plugin for Jenkins: https://plugins.jenkins.io/scriptler/
Scriptler allows you to store/edit groovy scripts and execute it on any of the agents/nodes... no need to copy/paste groovy code anymore.

