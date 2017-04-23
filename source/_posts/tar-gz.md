---
title: "리눅스 .tar.gz 사용법 정리"
catalog: false
date: 2017-03-18 10:51:24
subtitle: "맨날 헷갈리는 .tar압축 푸는 법 정리"
tags:
- linux
- tar
catagories:
- Linux
---

# 리눅스 .tar.gz .tar
마침 블로그를 날려먹고 백업자료를 뒤지고 있었는데 백업파일 압축이 tar.gz로 되어있었다.
막상 압축을 풀려고 하니 또 생각이 안난다. 주기적으로 찾게 되는 자료일 듯

출처 : [[리눅스] 리눅스 tar, gz 압축 및 해제](http://nota.tistory.com/53)

1. tar 압축하기
```shell
$tar -cvf [파일명.tar] [폴더명]
```
2. tar 압축 풀기
```shell
$tar -xvf [파일명.tar]
```
3. tar.gz로 압축하기
```shell
$tar -zcvf [파일명.tar.gz] [폴더명]
```
4. tar.gz 압축 풀기
```shell
$tar -zxvf [파일명.tar.gz]
```


주로 `wget`과 `tar -zxvf` 를 같이 쓰게 되는것 같다