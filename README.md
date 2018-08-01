# Proxima 개발환경
proxima를 개발환경을 구축하기 위해 웹서버, DB등을 반복 설치하는 번거로움을 개선하기 위해 Docker를 이용한 개발환경 설정 방안이다.
다음에 이어지는 단계들을 수행하면 아래와 같은 개발환경이 구성된다.

- web-container
  - CentOS 7
  - Apache 2.4
  - PHP 7.2
  - Composer
  - nodejs 8.1.3 & npm
- db-container
  - PostgreSQL 9.6
  
DB의 기본 정보는 아래와 같으며 필요시 docker-compose.yml의 항목(services > db > environment)을 수정하여 변경할 수 있다.

- POSTGRESQL_DATABASE(데이터베이스 이름) : proxima_v3
- POSTGRESQL_USER(사용자) : proxima_v3
- POSTGRESQL_PASSWORD(비밀번호) : proxima_v3
- POSTGRESQL_ADMIN_PASSWORD(관리자 암호) : root

웹서비스의 기본 포트는 8080이고 필요시 docker-compose.yml의 항목(services > web > ports)을 수정하여 변경할 수 있다.

## Docker 설치

우선 Docker를 설치해야 한다. 리눅스, 윈도우, 맥 모든 OS를 지원하기 때문에 본인의 OS에 맞게 설치한다.

> https://www.docker.com/community-edition#/download

Docker가 잘 설치되었는지 확인한다.

> $ docker -v

## 프로젝트에 개발환경 적용하기

프로젝트에 개발환경을 적용하기 위해서는 proxima-dev-env를 다운로드하여 적용하거나 기존 프로젝트의 sub module로 추가하는 방법 두가지가 가능하다.

### 프로젝트 디렉터리 구조

프로젝트 디렉터리 구조는 아래와 같이 개발 프로젝트 디렉터리 하위에 proxima-dev-env 프로젝트가 자리하면 된다.
```
project-dir
└── .dev-env 
    └── httpd-php
    └── db
    └── logs
    └── docker-compose.yml
```

- .dev-env 디렉터리 : proxima-dev-env 디렉터리. 정렬 시 위로 올라오도록 하기 위해 편의 상 디렉터리명 앞에 .을 붙이고 이름도 간결하게 줄였음(기본 디렉터리 명 : proxima-dev-env)
  - http-php 디렉터리 : web-container(Apache, PHP)의 Dockerfile이 위치한 디렉터리
  - db 디렉터리 : db data 디렉터리
  - logs 디렉터리 : Apache log 디렉터리
  - docker-compose.yml 파일 : docker-compose 설정 파일

### 1. proxima-dev-env 프로젝트 다운로드

다운로드 링크[(http://geminisoft.iptime.org:10000/repositories/368/download_revision?download_format=zip&rev=master)](http://geminisoft.iptime.org:10000/repositories/368/download_revision?download_format=zip&rev=master)를 통해 최신 버전을 다운받은 후 압축을 해제한다.

### 2. 기존 프로젝트(git) 서브모듈로 포함하기

먼저, 기존 프로젝트는 저장소 초기화(git init)가 되어 있어야 한다.

저장소 초기화가 되어 있다면 기존 프로젝트 디렉터리로 이동한다.

> $ cd my-project

git submodule add 명령으로 기존 프로젝트의 서브모듈로 proxima-dev-env를 추가한다.

> [my-project] $ git submodule add ssh://git@geminisoft.iptime.org/zodiac_v3/proxima-v3/proxima-dev-env.git .dev-env 

### 3. 이미 프로젝트에 .dev-env가 서브모듈로 적용되어 있는 경우(.dev-env폴더는 있는데 폴더가 비여있는 경우)

아래 명령으로 서브 모듈을 초기화 시킨다.

> $ git submodule init

초기화가 성공하면 아래와 같음 메세지가 출력된다ㅏ.

```
Submodule '.dev-env' (ssh://git@geminisoft.iptime.org/zodiac_v3/proxima-v3/proxima-dev-env.git) registered for path '.dev-env'
```

그런 다음 아래 명령어로 submodule을 업데이트 한다.

> git submodule update

명령이 성공하면 아래와 같은 메세지가 출력된다.

```
Cloning into 'D:/dev/proxima_v3/.dev-env'...
Submodule path '.dev-env': checked out '79ad68b771ae7b0e5c03d48be31fa359c293a928'
```

## Docker Compose를 이용한 서비스 컨테이너 관리

1개 이상의 서비스 컨테이너를 다룰 때 Docker Compose를 활용한다.

모든 docker 명령어는 .dev-env 디렉터리 내에서 실행해야 한다.

### Docker 이미지 빌드

준비가 되었다면 컨테이너들을 구동하기 위해 각 컨테이너에 대한 이미지를 빌드해야 한다. 

개발환경 디렉터리로 이동한다. 

> $ cd .dev-env

아래 명령으로 컨테이너 이미지를 빌드한다. 

> $ docker-compose build

### Docker 컨테이너 생성/시작

컨테이너 이미지 빌드가 완료되면 아래 명령으로 컨테이너를 생성/시작 시킨다.

> $ docker-compose up

백그라운드로 실행하려면 -d 플래그를 추가한다.

> $ docker-compose up -d

### Docker 컨테이너 중지/제거

아래 명령을 실행하면 실행중인 컨테이너가 중지되고 삭제된다.

컨테이너가 삭제되면 컨테이너 내에서 했던 모든 작업이 초기화 된다.(라이브러리 설치, cron설정, php.ini설정 등)

> $ docker-compose down

### Docker 컨테이너 시작

컨테이너를 시작시키려면 아래 명령어를 입력한다.

> $ docker-compose start

### Docker 컨테이너 종료

컨테이너를 종료시키려면 아래 명령어를 입력한다.

> $ docker-compose stop

### Docker 컨테이너 재시작

컨테이너를 재시작하려면 아래 명령어를 입력한다.

> $ docker-compose restart

### web-container 터미널 실행

모든 개발환경은 컨테이너에 설정되어 있으므로 웹서비스에 대한 설정이나 의존성 패키지 설치는 web-container에 들어가서 해야 한다.

아래 명령으로 web-container에 진입할 수 있다.

> $ docker exec -it web-container bash

이후 composer install과 같은 명령을 수행할 수 있다.

### web-container 터미널 종료

> $ exit

## Proxima 개발 시작...

개발환경과 Proxima 소스가 준비되고 "docker-compose up"또는 "docker-compose start"명령으로 web-container를 구동시켰다면 다음과 같은 절차를 진행한다.

### 1. Extjs 라이브러리 적용

web-container에 extjs-3.4라이브러리가 탑제되어 있으므로 다음 명령어들을 실행한다.

web-container에 접속한다.
> $ docker exec -it web-container bash

/extjs 디렉터리를 프로젝트 내 lib 디렉터리로 이동시킨다.

> $ mv /extjs /var/www/html/lib

### 2. 설정파일 수정

project/lib/config.SYSTEM.xml.example 파일을 project/lib/config.SYSTEM.xml로 복사하여 자신의 환경에 맞게 수정한다.(주로 CUSTOM_NAME 부분)

### 3. Composer 설정파일 수정

project/composer.json.example 파일을 project/composer.json으로 복사하여 자신의 환경에 맞게 수정한다.(주로 ProximaCustom autoload 부분)

### 4. 의존성 패키지 설치

web-container에 접속한다.

> $ docker exec -it web-container bash

의존성 패키지를 설치한다.

> $ composer install

### 5. 서비스 확인

http://localhost:8080으로 접속하여 서비스가 정상적인지 확인한다.
