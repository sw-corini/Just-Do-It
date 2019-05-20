# Electron 공부

Electron 공부 겸 아무거나 만들어보기

---

## Electron 에 대하여

HTML, CSS, 자바스크립트를 사용해 크로스 플랫폼 데스크탑 애플리케이션을 만들기 위해 GitHub에서 개발한 오픈 소스 라이브러리. Electron은 크로스 플랫폼을 지원하기 위해 Chromium과 Node.js를 1개의 런타임으로 통합했고, Electron을 이용해 작성한 앱은 Mac, Windows, 리눅스용으로 패키지할 수 있다.

## Chromium

모든 인터넷 사용자가 웹을 경험할 수 있도록보다 안전하고 빠르고 안정적인 방법을 구축하는 것을 목표로하는 오픈 소스 브라우저 프로젝트. 

---

# 일단해봄

## 1. 기본 설정 하기

먼저 위의 유튜브를 가볍게 보고 나서 다음 코드 처럼  study-electron 이라는 폴더를 만들고 필요한 것들을 설치했다.

~~~
mkdir study-electron
cd study-electron

npm init
npm install --save-dev electron
npm install --dev electron-packager
~~~
