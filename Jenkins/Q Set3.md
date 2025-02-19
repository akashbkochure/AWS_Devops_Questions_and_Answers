## 1.How is continuous integration achieved using Jenkins?
Continuous integration is a process where a developer’s code changes are constantly integrated into the main code and the same will be tested automatically and the results of the tests will decide whether the change is ready for deployment. In this process:
Developer Makes a change — commit/pull_request — in feature/dev branch
Source Control Management system generates appropriate events
SCM Specific Jenkins Plugins like Git/SVN will detect those events from the configured repositories and these events will be used to Trigger — build/dependent/test — jobs on Jenkins
After the Test/Dependent jobs are completed, the change/patch will be labeled according to the status of the test job
Based on the Status (i.e. readiness of a change to be merged with the main
branch), the Continuous Delivery or Continuous Deployment strategy/tool will take it forward.

## 2. What is Artifact Archival & how to do it in Pipelines?
Artifacts are the exportable/storable/archivable results of a specific job build. This can be configured using a plugin called — Copy artifact Plugin. Based on the configured pattern, the files/directories matching the configured patterns will be archived for a Jenkins build, which can be used for future references. In the pipeline, it can be configured as follows -

archiveArtifacts artifacts: ‘output/**/*’
## 3. How to configure inclusions & exclusions in Artifacts Archival?
Artifact archival takes in a pattern for matching target files. Similarly, it also takes in a pattern (ANT build system pattern for matching files) for exclusion as well which will be ignored while selecting the files for archival. For e.g.
archiveArtifacts artifacts: ‘output/*.txt’, excludes: ‘output/specific_file.txt’
The above command will archive all the text files from the output folder except specific_file.txt

## 4. How can we share information between different build steps or
stages in a Jenkins Job?
Every build step or stage will be running in its process and hence sharing information between two different build steps is not so direct. We can use either a File, a Database Entry, an Environment Variable, etc. to share info from one build step to another or a post-build action.

## 5. How code coverage is measured/tracked using Jenkins in a CI environment?

Using language-specific code coverage plugins like JaCoCo, CodeCov, etc or generic tools/plugins like Sonarqube which will add the code coverage data to builds with some minor tweaks in the code and the same can be displayed as a graph in Jenkins.

## 6. How can a job configuration be reset to an earlier version/state?

From the Job details page, we can use Job Config History to — See diff, Review & Revert the Job configs from the history of changes we have made to a particular job. This will be super useful when a job is misconfigured by someone by mistake, it can be reviewed and reverted easily to any of its earlier states.

## 7. How to do Global Tools Configuration in Jenkins?

Global Tools are tools that need to be installed outside the Jenkins environment and need to be controlled from within the Jenkins environment. Hence, it needs its corresponding Jenkins plugin as well. Steps to using a Global Tool generally include -

Install the tool Plugin into the Jenkins instance, to include the global tool into a list of global tools used by Jenkins.
Install the tool in the Jenkins instance or provide away (maybe a command to download and) install the tool during runtime.
Go to Manage Jenkins -> Global Tools Configuration and Scroll through the tool list and configure the global tool-specific configurations.
Make use of the installed global Tool in your job/pipeline.

## 8. How to create & use a Shared Library in Jenkins?
Basic requirements for a Jenkins shared library to be used in a Pipeline Code are -

A Repository with pipeline shared library code in SCM.
An appropriate SCM Plugin configuration for the Jenkins instance.
Global Shared Library should be configured in Jenkins Global configuration.
Include the Shared Library in the Pipeline Code and use the methods defined in the Jenkins Shared Library. E.g.
#!/urs/bin/env groovy

@Library(‘fs_jenkins_shared_library@v2.0.7’)_

## 9. How to install a Custom Jenkins Plugin or a Version of Plugin Not available in Jenkins Update Center?

Generally, it is the best practice to use the latest version of a plugin. But there are ways to install custom plugins or outdated versions of a published plugin. Jenkins Plugins are exported using a .hpi file and the same can be installed in multiple ways -

Using the Jenkins CLI

java -jar jenkins-cli.jar -s http://localhost:8080/ install-plugin SOURCE … [-deploy] [- name VAL] [-restart]

The above command Installs a plugin either from a file, an URL or from the update center.

SOURCE: If this points to a local file, that file will be installed. If this is an URL, Jenkins downloads the URL and installs that as a plugin. Otherwise, the name is assumed to be the short name of the plugin in the existing update center (like “findbugs”) and the plugin will be installed from the update center.
-deploy: Deploy plugins right away without postponing them until the reboot.
-name VAL: If specified, the plugin will be installed as this short name (whereas normally the name is inferred from the source name automatically).
-restart: Restart Jenkins upon successful installation.
Advanced Installation — via — Web UI

Assuming a .hpi file has been downloaded, a logged-in Jenkins administrator may upload the file from within the web UI:

Navigate to the Manage Jenkins > Manage Plugins page in the web UI.
Click on the Advanced tab.
Choose the .hpi file under the Upload Plugin section.
Upload the plugin file.
Restart the Jenkins instance
Advanced Installation — via — On the master

Assuming a .hpi file has been explicitly downloaded by a systems administrator, the administrator can manually place the .hpi file in a specific location on the file system.

Copy the downloaded .hpi file into the JENKINS_HOME/plugins directory on the Jenkins controller (for example, on Debian systems JENKINS_HOME is generally /var/lib/jenkins).

The master will need to be restarted before the plugin is loaded and made available in the Jenkins environment

## 10. How to download the Console log for a particular Jenkins build programmatically?

Using the Jenkins CLI — console — command

java -jar jenkins-cli.jar console JOB [BUILD] [-f] [-n N]
Produces the console output of a specific build to stdout, as if you are doing ‘cat build.log’

JOB: Name of the job
BUILD: Build number or permalink to point to the build. Defaults to the last build
-f: If the build is in progress, append console output as it comes, like tail -f
-n N: Display the last N lines.
ssh -l <ssh_username> -p <port_no> <Jenkins_URL> console <JOB_NAME>

## 11. What is Jenkins Remote Access API?

Jenkins provides remote access API to most of its functionalities (though some functionalities are programming language-dependent). Currently, it comes in three flavors -

XML
JSON with JSONP support
Python
Remote access API is offered in a REST-like style. That is, there is no single entry point for all features, and instead, they are available under the “…/api/” URL where the “…” portion is the data that it acts on.

For example, if your Jenkins installation sits at interviewbit.com, visiting /api/ will show just the top-level API features available — primarily a listing of the configured jobs for this Jenkins instance.

Or if we want to access information about a particular build, e.g.

https://ci.jenkins.io/job/Infra/job/jenkins.io/job/master/lastSuccessfulBuild/, then go to https://ci.jenkins.io/job/Infra/job/jenkins.io/job/master/lastSuccessfulBuild/api/ and you’ll see the list of functionalities for that build.

## 12. What is In-process Script Approval and how it works?

Jenkins, and several plugins, allow users to execute Groovy scripts in Jenkins. To protect Jenkins from the execution of malicious scripts, these plugins execute user- provided scripts in a Groovy Sandbox that limits what internal APIs are accessible.

This protection is provided by the Script Security plugin. As soon as an unsafe method is used in any of the scripts, the “In-process Script Approval” action should appear in “Manage Jenkins” to allow Administrators to make a decision about which unsafe methods, if any, should be allowed in the Jenkins environment.

This in-process script approval inherently improves the security of the overall Jenkins ecosystem.

## 13. Can we monitor Jenkins using common Observability tools?

Common monitoring platforms like DataDog, Prometheus, JavaMelody & few others — have their corresponding Jenkins plugin, which when configured, sends Metrics to the corresponding Monitoring platform, which can then be Observed with the latest tools & technologies. The same can be configured with Alarms & Notifications for immediate attention when something goes wrong.

## 14. What is a Ping Thread in Jenkins and how it works?

Jenkins installs “ping thread” on every remote connection, such as Controller/Agent connections, regardless of its transport mechanism (such as SSH, JNLP, etc.). The lower level of the Jenkins Remoting Protocol is a message-oriented protocol, and a ping thread periodically sends a ping message that the receiving end will reply to. The ping thread measures the time it takes for the reply to arrive, and if it’s taking excessive time (currently 4 minutes and configurable), then it assumes that the connection was
lost and initiates the formal close down.

This is to avoid an infinite hang, as some of the failure modes in the network cannot be detected otherwise. The timeout is also set to a long enough value so that a temporary surge in the load or a long garbage collection pause will not trip off the close-down.

Ping thread is installed on both controller & agent; each side pings the other and tries to detect the problem from their sides.

The ping thread time out is reported through java.util.logging. Besides, the controller will also report this exception in the agent launch log. Note that some agent launchers, most notably SSH agents, writes all stdout/stderr outputs from the agent JVM into this same log file, so you need to be careful.
