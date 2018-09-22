# DevOps/Jenkins World 2018

### https://devopsworldjenkinsworld2018.sched.com
[@devopsworldconf](https://twitter.com/devopsworldconf)

<!-- .slide: data-transition="zoom" -->

---
## workshop: cloudbees core
[github/cloudbees-cd-acceleration-workshop](https://github.com/cloudbees-cd-acceleration-workshop/cloudbees-cd-acceleration-workshop-exercises)
[github/kayla-cb-ws](https://github.com/kayla-cb-ws)

----

was Jenkins Enterprise 1.x now Cloudbees Core
https://go.cloudbees.com/downloads/
https://www.cloudbees.com/products/cloudbees-core
<!-- .slide: data-transition="convex" -->
---
#### custom marker file
```text
.node-app
```
https://github.com/kayla-cb-ws/helloworld-nodejs

* Can be any file, can contain properties.
* A way to split jobs
* can be a pom.xml or package.json for example
* can be ordered list, e.g. .node-app or Jenkinsfile
 
[custom-marker-files-setup](https://github.com/cloudbees-cd-acceleration-workshop/cloudbees-cd-acceleration-workshop-exercises/blob/master/exercises/intro-pipeline-cb-core.md#project-recognizers-with-custom-marker-files)
<!-- .slide: data-transition="convex" -->
---
## takeaways
* enterprise: cloudbees support, os: optional support hours
* master node is wrapped with teams management
* multi masters becomes easy, way of life
* masters are provisioned as code with templates
* supports custom marker files
* teams can use provided and/or override when ready
* blue ocean/classic views can be navigated between
* documenting custom libraries is simple text file
* event listeners can trigger jobs across entire team cluster

<!-- .slide: data-transition="convex" -->
---

###### 5 Essential Characteristics (NIST)
![5 essential characteristics (NIST)](/images/slide-cloud.jpg)

<!-- .slide: data-transition="convex" -->

---

## Dr. Nicole Forsgren Keynote
<div style="position:relative;height:0;padding-bottom:56.21%"><iframe src="https://www.youtube.com/embed/R9izKUqPCiU?ecver=2" style="position:absolute;width:100%;height:100%;left:0" width="640" height="360" frameborder="0" allow="autoplay; encrypted-media" allowfullscreen></iframe></div>

---

---

## The Basics

- Separate slides using '`---`' on a blank line
- For vertical slides use '`--`'  
- Write github flavored markdown
- Click 'Present' (top right) when you're ready to talk

---

## Quick tips

- There is also a speaker view, with notes - press '`s`'
- Press '`?`' with focus on the presentation for shortcuts
- <em>You can use html when necessary</em>
- Share the 'Present' URL with anyone you like!

Note:
- Anything after `Note:` will only appear here

---

## More markdown (fragments)

* static text
* fragment <!-- .element: class="fragment" -->
* fragment grow <!-- .element: class="fragment grow" -->
* fragment highlight-red <!-- .element: class="fragment highlight-red" -->
* press key down <!-- .element: class="fragment fade-up" -->

--

## More markdown (tables)

****

|h1|h2|h3|
|-|-|-|
|a|b|c|

****

--

## More markdown (code)

```
version: '2'
services:
  slides:
    image: msoedov/hacker-slides

    ports:
      - 8080:8080
    volumes:
      - ./slides:/app/slides
    restart: always

    environment:
     - USER=bob
     - PASSWORD=pa55

```

--

## Local images

![demoPicture](/images/demo.png)

Copy images into slides/images/ & include with MD:

```
![demoPicture](/images/demo.png)

```
or HTML:

```
<img src="/images/demo.png">

```


---

## Learn more

- [RevealJS Demo/Manual](http://lab.hakim.se/reveal-js)
- [RevealJS Project/README](https://github.com/hakimel/reveal.js)
- [GitHub Flavored Markdown](https://help.github.com/articles/github-flavored-markdown)
