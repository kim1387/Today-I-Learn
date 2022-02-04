# linux 환경에서 파일 다운 받는 방법

## 서론

이번에 sv ai 프로그램에 참여하면서 많은 것을 배웠다. LatteGan 오픈 소스와 Dalle-mini를 dockerizing 하면서 알게 된 툴들과 알고 있던 툴들을 정리해봤다.

혼자라면 이 모든 걸 다 알 수 없겠지만 같이 도움을 주었던 친구 덕분에 많은 걸 알게 되었다.(**[DPS0340](https://github.com/DPS0340)**) 땡큐땡큐

## 일반적으로 사용하는 다운로드 도구(feat. curl , wget)

wget와 curl을 통해서 많이 다운 받는다.

사용 방법

curl $(url 주소)

wget $(url 주소)

## 구글 드라이브에서 다운로드 (feat. gdown, wget)

구글 드라이브에서 파일을 다운로드 하는 방법이다.

대표적인 라이브러리에는 gdown가 있다. gdown는 사용해봤는데 잘 되지 않아서 wget을 사용하여 받았다.

기존에는 url 주소만 쓰면 다운로드가 간단하게 되었는데 2~3년 전 즈음에 보안 상의 이유로 패치가 된 것 같다. 

```java
wget --load-cookies ~/cookies.txt "https://docs.google.com/uc?export=download&confirm=$(wget --quiet --save-cookies ~/cookies.txt --keep-session-cookies --no-check-certificate 'https://docs.google.com/uc?export=download&id=$(파일id)' -O- | sed -rn 's/.*confirm=([0-9A-Za-z_]+).*/\1\n/p')&id=$(파일id)" -O $(파일명) && rm -rf ~/cookies.txt
```

## 깃 환경에서 다운로드 하는 방법 (feat. git clone, git-lfs)

git 클론은 너무 많이 사용하는 방법이니 설명은 생략한다.

git에 있는 ai 모델을 다운 받을 때는 용량이 커서 git clone으로 받으려고 하면 시간이 오래 걸리거나 오류가 난다. 이럴 때 사용할 수 있는 방법이 git에서 만든 large file storage를 다운 받기 위해 만든 라이브러리가 있다. 

아래 링크 참조 

[https://git-lfs.github.com/](https://git-lfs.github.com/)

## 빠르게 다운로드 하는 방법 (feat. axel)

여러 개의 파일을 동시에 다운로드

axel을 활용하면 용량이 큰 파일을 빠르게 다운로드할 수 있다.

## cli에서 gui로 다운로드 하기?!? (feat. w3m)

latteGan 오픈 소스였나.. 정확히 기억나지는 않는데 오픈 소스를  찾다가 shell script에 적혀있던 걸 발견한 것 같다. 신기한 점은 text를 활용해 gui를 표현하여 인터넷 링크 접속 파일 다운로드가 가능하다는 점이다.

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/b224cf3d-e3f2-4cc3-ba40-61e9029ce96f/Untitled.png)

생각 보다 정교하다..

이걸로 받으면 장점이 있다. 일부 홈페이지에서 wget로 다운로드하려고 하면 내가 로봇인지 확인하여 복잡한 인증 과정을 거처야 한다. (크롤링을 해봤으면 많이 경험 해봤을 것이다..) 

하지만 이것은 별도의 인증 없이 다운 받을 수 있다는 장점이 있다.