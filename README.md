# devops/jenkinsworld 2018 notes
## About this repo
This is a collection of notes from devops/jenkinsworld 2018 using a tool called
hackerslides. 

* More info: https://github.com/msoedov/hacker-slides
* hacker-slides info: https://hackmd.io/s/slide-example#First-slide
* github markdown doc:
* https://help.github.com/articles/basic-writing-and-formatting-syntax/#quoting-text
* reveal.js docs: https://github.com/hakimel/reveal.js/
* useful markdwon snippets:
https://raw.githubusercontent.com/jeffcole/well-designed-phoenix-apps-talk/master/PITCHME.md

## run locally
```
docker run -it -p 8081:8080 -v $(pwd)/slides:/app/slides msoedov/hacker-slides
```

## run docs locally
building
```
 docker build -t devopsworld-notes:latest .
```

run local
```
docker run -it --rm -p 4000:4000 --name devopsworld-notes -v "$PWD":/usr/src/app -w /usr/src/app devopsworld-notes:latest
```
or background
```
docker run -t -d --rm -p 4000:4000 -h localhost --name devopsworld-notes -v "$PWD":/usr/src/app -w /usr/src/app devopsworld-notes:latest
```

run locally without building (slower starts)
```
docker run -it --rm -p 4000:4000 --name devopsworld-notes -v "$PWD":/usr/src/devopsworld-notes -w /usr/src/devopsworld-notes ruby:2.5 bundle install && bundle exec jekyll serve
```
