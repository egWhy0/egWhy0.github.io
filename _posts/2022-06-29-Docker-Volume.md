---
title:  "도커 볼륨"
header:
  teaser: "/assets/images/500x300.png"
categories: 
  - Jekyll
tags:
  - update
---
테이블 프로젝트를 하는 중 개발환경을 구축하기 위해 도커를 사용하고 있다.

내가 그린 그림은 프론트단과 백단과 DB단을 도커 이미지를 사용하여 운영과 개발 환경을 동일하게 하는것이다. 
도커를 이용하여 이미지화를 하면 어떤 환경이든 도커에서 이미지만 다운받고 컨테이너로 올리면 손쉽게 환경을 구축할 수 있어 편할것 같았다.

그리하여 DB 설계를 하고, MySql에서 테이블 작업중 한 사람이 DB작업을 하면 다른 한사람은 그것을 공유 받아 사용하는 것을 그렸는데 
그 공유 하는 방법을 찾는것이 일이였고 알아본 것을 정리 한다.

---

도커 컨테이너의 데이터 관리
--

도커의 컨테이너는 이미지Layer에 읽기/쓰기(Read-Write)Layer를 추가하여 생성/실행 되고, 컨테이너는 생성될 때 각 컨테이너 마다 "Scratch Space"가 생긴다.

2개의 ubuntu컨테이너를 생성, 중지, 재시작, 삭제 하며 비교를 해보자.

-- 생성

1.이름이 ubuntu1인 컨테이너를 생성한다. ubuntu1 컨테이너에는 1~10000의 난수로 data.txt파일을 생성한다.
```bash
$ docker run -d -it --name ubuntu1 ubuntu bash -c "shuf -i 1-10000 -n 1 -o /data.txt && tail -f /dev/null"
```

2.이름이 ubuntu2인 컨테이너를 생성한다. ubuntu2 컨테이너는 data.txt 파일생성을 하지 않았다.
```bash
$ docker run -d -it --name ubuntu2 ubuntu
```

![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/docker-volume-test1.png){: .align-center}

3.비교할 각 컨테이너 ubuntu1, ubuntu2의 bash를 실행해본다.
```bash
$ docker exec -it ubuntu1 /bin/bash
```
```bash
$ docker exec -it ubuntu2 /bin/bash
```
![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/docker-volume-test2.png){: .align-center}

두 컨테이너의 root를 확인해 보면 ubuntu1에는 난수가 생성된 data.txt 파일이 생성되었고, ubuntu2에는 파일을 생성하지 않았다.

-- 중지 & 재시작
1.ubuntu1, ubuntu2를 중지한다.
```bash
$ docker stop ubuntu1 ubuntu2
```
![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/docker-volume-test3.png){: .align-center}

2.중지한 ubuntu1, ubuntu2를 재실행 한다.
```bash
$ docker start ubuntu1 ubuntu2
```
![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/docker-volume-test4.png){: .align-center}

3.재실행한 ubuntu1, ubuntu2의 bash를 실행해본다.
```bash
$ docker exec -it ubuntu1 /bin/bash
```
```bash
$ docker exec -it ubuntu2 /bin/bash
```
![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/docker-volume-test5.png){: .align-center}

ubuntu1의 출력된 난수의 변화 빼고, ubuntu1와 ubuntu2의 데이터는 생성 당시와 동일하다.

-- 삭제 & 생성

1.ubuntu1의 COMMAND를 ubuntu2에, ubuntu2의 COMMAND를 ubuntu1로 컨테이너를 생성해본다.
```bash
$ docker run -d -it --name ubuntu1 ubuntu
```
```bash
$ docker run -d -it --name ubuntu2 ubuntu bash -c "shuf -i 1-10000 -n 1 -o /data.txt && tail -f /dev/null"
```
![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/docker-volume-test6.png){: .align-center}

기존 ubuntu1, ubuntu2의 이름은 사용중이여서 삭제 또는 이름을 변경하여 생성해야한다.

삭제전 2개의 컨테이너 정보를 확인
```bash
CONTAINER ID   IMAGE     COMMAND                  CREATED       STATUS          PORTS     NAMES
90b7539512d6   ubuntu    "bash"                   2 hours ago   Up 54 minutes             ubuntu2
1403327c406e   ubuntu    "bash -c 'shuf -i 1-…"   2 hours ago   Up 54 minutes             ubuntu1
```

2.실행중인 ubuntu1, ubuntu2 컨테이너 강제 삭제
```bash
$ docker rm -f ubuntu1 ubuntu2
```
![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/docker-volume-test7.png){: .align-center}

3.COMMAND를 바꾼 ubuntu1, ubuntu2 컨테이너 생성
```bash
$ docker run -d -it --name ubuntu1 ubuntu
$ docker run -d -it --name ubuntu2 ubuntu bash -c "shuf -i 1-10000 -n 1 -o /data.txt && tail -f /dev/null"
```
![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/docker-volume-test8.png){: .align-center}

4.새로운 컨테이너 실행
```bash
$ docker run -d -it --name ubuntu1 ubuntu
$ docker run -d -it --name ubuntu2 ubuntu bash -c "shuf -i 1-10000 -n 1 -o /data.txt && tail -f /dev/null"
```
![image-center]({{ site.url }}{{ site.baseurl }}/assets/images/docker-volume-test8.png){: .align-center}

삭제전 2개의 컨테이너와 새로운 컨테이너의 ubuntu1, ubuntu2라는 이름은 같아도 데이터가 다른것을 확인해 볼 수 있다. 

삭제전 컨테이너의 정보와 삭제후 재생성한 컨테이너의 정보를 확인해보자.

- 삭제전
```bash
CONTAINER ID   IMAGE     COMMAND                  CREATED       STATUS          PORTS     NAMES
90b7539512d6   ubuntu    "bash"                   2 hours ago   Up 54 minutes             ubuntu2
1403327c406e   ubuntu    "bash -c 'shuf -i 1-…"   2 hours ago   Up 54 minutes             ubuntu1
```

- 재생성
```bash
CONTAINER ID   IMAGE     COMMAND                  CREATED        STATUS         PORTS     NAMES
8daeb5ecbeb4   ubuntu    "bash -c 'shuf -i 1-…"   17 hours ago   Up 6 minutes             ubuntu2
282aa796a7a3   ubuntu    "bash"                   17 hours ago   Up 6 minutes             ubuntu1
```

삭제전 ubuntu1과 재생성한 ubuntu1의 NAMES는 같지만 CONTAINER ID가 다를 것을 확인해 볼 수 있다.

도커에서는 컨테이너를 생성하면 CONTAINER ID가 해시코드로 생성되고, CONTAINER ID는 중복되지 않는다. 
이는 하나의 이미지로 여러 컨테이너를 생성하지만 생성된 각 컨테이너는 독립된 공간이라고 볼수 있다.

그리고 컨테이너 안에서 일어난 데이터의 추가 및 변경 사항은 컨테이너가 삭제되면 모두 삭제가 된다.

---






[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/