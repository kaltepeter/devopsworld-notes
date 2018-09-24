![Jenkins World 2018](https://github.com/kaltepeter/devopsworld-notes/raw/master/slides/images/jenkins-world.jpeg)
## DevOps/Jenkins World 2018
### https://devopsworldjenkinsworld2018.sched.com
[@devopsworldconf](https://twitter.com/devopsworldconf)

<!-- .slide: data-transition="zoom" -->

---
#### workshop: cloudbees core
[github/cloudbees-cd-acceleration-workshop](https://github.com/cloudbees-cd-acceleration-workshop/cloudbees-cd-acceleration-workshop-exercises)
[github/kayla-cb-ws](https://github.com/kayla-cb-ws)


was Jenkins Enterprise 1.x now Cloudbees Core
https://go.cloudbees.com/downloads/
https://www.cloudbees.com/products/cloudbees-core
<!-- .slide: data-transition="convex" -->
---
#### custom marker file
https://github.com/kayla-cb-ws/helloworld-nodejs
```text
.node-app
```
https://github.com/kayla-cb-ws/custom-marker-pipelines
```text
nodejs-app
└── Jenkinsfile.template
```
* Can be any file, can contain properties.
* can be any file, even an app file like pom.xml
* can be ordered list, e.g. .node-app or Jenkinsfile
 
[custom-marker-files-setup](https://github.com/cloudbees-cd-acceleration-workshop/cloudbees-cd-acceleration-workshop-exercises/blob/master/exercises/intro-pipeline-cb-core.md#project-recognizers-with-custom-marker-files)
<!-- .slide: data-transition="convex" -->
---
#### stash/unstash
* very useful for multi-agent builds
* introduces a lot of network I/O to master
* pluggable storage like https://github.com/jenkinsci/artifact-manager-s3-plugin really helps performance
* enables restarting from stages (with preserveStashes)

 [approvals-artifacts-cb-core.md#stash-and-unstash](https://github.com/cloudbees-cd-acceleration-workshop/cloudbees-cd-acceleration-workshop-exercises/blob/master/exercises/approvals-artifacts-cb-core.md#stash-and-unstash)
<!-- .slide: data-transition="convex" -->
---
#### parallel vs. sequential stages
> We are duplicating the agent section and the post sections for capturing test results with the junit step. We are also executing the nodejs steps twice. That doesn't seem efficient - but it is because the parallel block for Declarative syntax does not allow you to define an agent section and a parallel block at the same level. More specifically, you can have ONLY ONE of either agent, parallel or stages as a child of the stage directive.

--
> What we really want to do in the Test stage is set-up the helloworld-nodejs just once - to incluce only running one Kubernetes Pod. We want to define the agent once for the entire Test stage, and then run the nodejs container block once but in the same workspace as what will be used by the testcafe containers. And we want to capture all of the test results. Unfortunatley this is not possible with Delcarative syntax - but it would be possible with scripted syntax.
--

* parallel can be slower, I/O bottlenecks
* parallel only works with separate containers
* if done correctly parallel can improve performance
* declartive is limited with parallel

https://github.com/cloudbees-cd-acceleration-workshop/cloudbees-cd-acceleration-workshop-exercises/blob/master/exercises/parallel-sequential-cb-core.md
<!-- .slide: data-transition="convex" -->
---
#### shared libraries

```groovy
// vars/defineProps.groovy
def call(String file, Map defaults) {
  //use the Pipeline Utility Steps plugin readProperties step to read the file
  def props = readProperties defaults: defaults, file: file
  for ( e in props ) {
    env.setProperty(e.key, e.value)
  }
}
```

The first comment may serve a purpose
--

```html
<h2>defineProps step</h2>
<p>
A custom step for using the <pre>readProperties</pre> step from the Pipeline Utilities plugin specifically from a Declarative Pipeline. 
</p>
<h3>Configuration</h3>
<dl>
	<dt>name</dt>
	<dd><pre>String</pre><b>REQUIRED</b> the file, including path, to be read from the workspace as the properties file</dd>
	<dt>defaults</dt>
	<dd><pre>Map</pre><b>OPTIONAL</b> default values for passed in properties file</dd>
</dl>

<h3>Example:</h3>
<pre>
	defineProps('.nodejs-app', [npmPackages: 'express pug'])
</pre>
```

provides documentation to the syntax generators
--
syntax matters

branch override
```groovy
library 'cd-accel@completed'
library 'cd-accel'
```
import everything
```groovy
@Library('cd-accel') _
```
--
* wildcard is expensive. it will load entire repo everytime
* wildcard breaks replay
* no wildcard with explicit imports is light

https://github.com/cloudbees-cd-acceleration-workshop/cloudbees-cd-acceleration-workshop-exercises/blob/master/exercises/advanced-pipeline-cb-core.md#pipeline-shared-libraries
<!-- .slide: data-transition="convex" -->
---
#### custom listeners

```groovy
triggers {
    eventTrigger simpleMatch('hello-api-push-event')
}
```
event listens for a job/event to fire

[advanced-pipeline-cb-core.md#cross-team-collaboration](https://github.com/cloudbees-cd-acceleration-workshop/cloudbees-cd-acceleration-workshop-exercises/blob/master/exercises/advanced-pipeline-cb-core.md#cross-team-collaboration)

https://go.cloudbees.com/docs/cloudbees-core/cloud-admin-guide/cross-team-collaboration/

<!-- .slide: data-transition="convex" -->
---
## Scaling Network Connections from the Jenkins Master
http://sched.co/F9NP

--

#### agent <==> master

Build logs:
* All logs go through master
* streamed in chunks
* access through master disk
* master load:
    * network
    * disk I/O
    * memory buffers

Beware of parallel, parallel logging can bring it down

--
#### master
* small masters
* config as code
* cloud native jenkins
* Use multiple storage systems
* system data and build data should be separate
* external storage or separate mounts
* use remote storage Optimize /tmp
* don’t put on remote storage
* separate network interfaces
    * master <—> storage
        * high traffic, low latency req
    * master <—> agents
        * high traffic, low latency

--
#### agent
* master <—> agents
* agent <—> maven
* master <—> cli (deprecated)
* massive class loading may kill instances: cloud api with heavy parallel
* avoid tool installers, maven, docker, java
    * solution: package tools in docker, tools from volume, custom tools plugin
* do not use ssh plugin at scale, each connection, tcp and buffer
    * solution: jnlp, command launcher, clobbers NIO SSH, new arch
--

#### Externalizing data
* get rid of data over remoting
* consumers
    * big artifacts (built in system)
    * long build logs
    * remote class loading (only after cache warms up)
    * misc, test/coverage/analysis results, long tail of plugins
* pluggable storage
    * cloud native: jenkins.io/sigs/cloud-native/pluggable-storage
        * artifacts, logs, config, crews, builds, jobs, test results, code coverage, static analysis, sys logs, task logs, fingerprints
        
--

* send artifacts to s3
* send logs to cloud logger
* reduce class loading
    * agent images with preloaded cache
    * no out of box solution now
    * more plugins => sometime more loading

detailed logging hudson.computer.SlaveComputer

support plugin: support core
* gives list of jars, bytes in/out
* second run sent no jars, just meta

--

#### cloud native
* general purpose, CICD engine, cube as platform, pluggable storage
* networking will change
* infinite scalabiliity
* standard communication layers
* remoting over kafka
    * google summer of code
* jenkins/remoting-kafka-agent (docker)

--

#### single shot masters
1. externalize data
2. reduce traffic to external storage
    1. tmp data in ram or local dirs
3. reduce traffic master to agents
    1. pre-init class loading
    2. bundle tools

--
## links
* [2016-jenkins-world-soyouwanttobuildtheworldslargestjenkinscluster_final.pdf](https://www.cloudbees.com/sites/default/files/2016-jenkins-world-soyouwanttobuildtheworldslargestjenkinscluster_final.pdf)
* [Scaling Jenkins to Hundreds of Nodes](https://www.google.com/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=2ahUKEwilyayhoMXdAhWLjVQKHdAeAG8QwqsBMAB6BAgGEAQ&url=https%3A%2F%2Fwww.youtube.com%2Fwatch%3Fv%3D9-DUVroz7yk&usg=AOvVaw19OotdwbdU7HTmKRRdMjQ-)
* [Scaling Network Connections from the Jenkins Master ](https://jenkins.io/blog/2018/09/10/scaling-network-connections/?utm_source=feedburner&utm_medium=twitter&utm_campaign=Feed%3A+ContinuousBlog+%28Jenkins%29)
* https://jenkins.io/sigs/cloud-native/
* https://jenkins.io/blog/2018/08/31/shifting-gears/#cloud-native-jenkins-mvp

<!-- .slide: data-transition="convex" -->
---

## Cloud Native
https://jenkins.io/blog/2018/08/31/shifting-gears/
> Admins today are unable to meet that heightened expectation using Jenkins easily enough. A Jenkins instance, especially a large one, requires too much overhead just to keep it running. It’s not unheard of that somebody restarts Jenkins every day

> As CI/CD has gone mainstream, this is no longer OK. People want something that works out of the box, something that gets people to productivity within 5 clicks in 5 minutes. Too many choices are confusing users, and we are not helping them toward “the lit path.” Everyone feels uncertain if they are doing the right thing, contributors are spread thin, and the whole thing feels a bit like a Frankenstein.

-- 
#### pluggable storage
* everything in $JENKINS_HOME
* many plugin/workaround
* limited support
* network share == performance problems

--

#### jenkinsfile runner. binary and base docker image 
* https://github.com/jenkinsci/jenkinsfile-runner
* https://github.com/jenkinsci/custom-war-packager
* https://docs.google.com/presentation/d/1T5WabYBwvgoUgf13fm4gzFmunOjjHrXH0JOaynhUT5g/edit#slide=id.p1

<!-- .slide: data-transition="convex" -->
---


[![Docker for Development](https://github.com/kaltepeter/devopsworld-notes/raw/master/slides/images/jenkinsworlddevopsworld-docker_for_dev.png)](https://www.slideshare.net/rheinwein/using-docker-for-development)

<!-- .slide: data-transition="convex" -->
---

<!-- .slide: data-transition="convex" -->
---

#### Pipeline performance
http://sched.co/F9Nb

* default: max durability
* less durable, bit faster : removes atomic writes
* perf-optimized: batch writes *recommended

https://jenkins.io/blog/2018/02/22/cheetah/

--
#### no more:
* reducing shell steps (unless shell or bash)
* storing large variables in memory (i.e. files)
* max io performance (no spinning rust)

https://jenkins.io/doc/book/pipeline/scaling-pipeline/

--

#### GC tuning!!!!
http://gceasy.io

guide: https://jenkins.io/blog/2016/11/21/gc-tuning/

* set max and min heap sizes (2-4g)
* sometimes tweak region size. don’t go too big, perf will go down
* NFS v4 is slower than v3. perf-optimized mode plays better but still is not a fix

https://www.youtube.com/watch?v=p0qX409wwPw

--

#### new pipeline changes
* CPS is going away
* decouple from running code on monolith master
* debugger coming
* no more script security
* isolation to prevent master failures

if we think outside master: can’t touch jenkins internals 

<!-- .slide: data-transition="convex" -->
---

## Spock unit testing
maven:
* jenkins uses maven
* gmaven plugin
* automatically mocks deps
* pom has list of every plugin

https://github.com/homeaway/jenkins-spock

<!-- .slide: data-transition="convex" -->
---

## Jenkins: Evergreen
http://sched.co/F9Nn

https://github.com/jenkins-infra/evergreen

GitHub/sentry: telementary

https://jenkins.io/projects/evergreen/

plugins are provided
can i implement my own list: no

<!-- .slide: data-transition="convex" -->
---

###### 5 Essential Characteristics (NIST)
![5 essential characteristics (NIST)](https://github.com/kaltepeter/devopsworld-notes/raw/master/slides/images/slide-cloud.jpg)

<!-- .slide: data-transition="convex" -->
---
[![Accelerate](https://github.com/kaltepeter/devopsworld-notes/raw/master/slides/images/accelerate-book.png)](https://www.amazon.com/Accelerate-Software-Performing-Technology-Organizations/dp/1942788339/ref=pd_lpo_sbs_14_t_0?_encoding=UTF8&psc=1&refRID=NJD8PNXHAKKDE2BPQ45P "Accelerate Book")

Devops book
<!-- .slide: data-transition="onvex" -->
---

## Dr. Nicole Forsgren Keynote
[![Dr. Nicole Forsgren](https://github.com/kaltepeter/devopsworld-notes/raw/master/slides/images/R9izKUqPCiU.png)](https://www.youtube-nocookie.com/embed/R9izKUqPCiU?start=1019 "Devops/JenkinsWorld Closing Keynote")

---
