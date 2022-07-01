---
title:  "스프링 프젝트 시작하기"
header:
  teaser: "/assets/images/500x300.png"
categories: 
  - Jekyll
tags:
  - update
---
<!-- 
minimal-mistakes
├── _data                      # data files for customizing the theme
|  ├── navigation.yml          # main navigation links
|  └── ui-text.yml             # text used throughout the theme's UI 

-->

Spring Boot 기본 구조

```bash

{projectName}                  # 프로젝트명
├── .gradle                    
├── .idea                      # IntelliJ에서 사용하는 설정파일
├── gradle                     # gradle관련 사용
├── src                        
|   ├── main
|   |  ├── java
|   |  |  └── {projectName}    # package
|   |  |     └── {projectName}spring
|   |  |        └── {projectName}SpringApplication # sorce파일
|   |  └── resources           # 실제 자바코드파일을 제외한 XML, propertis 등 나머지 파일들이 들어가 있다.
|   └── test                   # TEST 코드.
|      └── java
|         └── {projectName}
|            └── {projectName}spring
├── .gitignore                 # git의 sorce파일 관리시 사용되는 파일로, 기본적인것은 SpringBoot에서 관리 해준다.
├── build.gradle               # 빌드 정보가 들어있음.
├── gradlew
├── gradlew.bat
├── HELP.md
└── settings.gradle

```

InteliJ에서 빌드시 InteliJ IDEA로 Build & Run되도록 변경

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/SpringProjectSetUp1.png){: .align-center}

Path : Preferences -> Build, Execution, Deployment -> Build Tools -> Gradle

Build and run using : (Gradle) -> InteliJ IDEA
Run tests using : (Gradle) -> InteliJ IDEA

================================================

Gradle로 Build & Run시 생겼던 문제

start.spring.io에서 프로젝트 생성시 Java 버전을 17로 세팅하여 생성후 세팅을 확인하는데 생긴 오류.
![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/SpringProjectSetUp4.png){: .align-center}

```bash
Cause: error: invalid source release: 17
```

해결 방법1

현재 PC에 설치되어있는 자바 버전을 확인후 build.gradle에서 자바 버전을 수정

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/SpringProjectSetUp2.png){: .align-center}


[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/