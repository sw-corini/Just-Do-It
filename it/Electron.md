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

~~~js
mkdir study-electron
cd study-electron

npm init

npm install --save-dev electron
npm install --dev electron-packager
~~~

## 2. package.json 설정

처음에는 유튜브 내용대로 electron-packager로 build했는데 하다 보니 가끔 오류를 토해내면서 오락가락 하기에 구글에 검색. 다음과 같이  electron-builder 를 설치해서 추가 해줬다.

~~~js
"scripts": {
  "start": "electron .",
  "build": "electron-packager . --out=dist --asar --overwrite --all",
  "pack": "electron-builder --dir",
  "dist": "electron-builder"
}
~~~

이제 yarn dist라는 명령으로 앱을 build 할 수 있다.

## 3. Electron 개발 해보기

### 개발자 도구, 창 닫기 처리, macOS에서 사용자가 dock의 app 아이콘을 클릭 했을 때 창 재 생성하기 등의 기능

~~~js
const { app, BrowserWindow } = require('electron')

// window 객체는 전역 변수로 유지. 이렇게 하지 않으면, 
// 자바스크립트 객체가 가비지 콜렉트될 때 자동으로 창이 닫힐 것입니다.
let win

function createWindow () {
  // 브라우저 창을 생성합니다.
  win = new BrowserWindow({ width: 800, height: 600 })

  // 앱의 index.html 파일을 로드합니다.
  win.loadFile('index.html')

  // 개발자 도구를 엽니다.
  win.webContents.openDevTools()

  // 창이 닫힐 때 발생합니다
  win.on('closed', () => {
    // window 객체에 대한 참조해제. 여러 개의 창을 지원하는 앱이라면 
    // 창을 배열에 저장할 수 있습니다. 이곳은 관련 요소를 삭제하기에 좋은 장소입니다.
    win = null
  })
}

// 이 메서드는 Electron이 초기화를 마치고 
// 브라우저 창을 생성할 준비가 되었을 때  호출될 것입니다.
// 어떤 API는 이 이벤트가 나타난 이후에만 사용할 수 있습니다.
app.on('ready', createWindow)

// 모든 창이 닫혔을 때 종료.
app.on('window-all-closed', () => {
  // macOS에서는 사용자가 명확하게 Cmd + Q를 누르기 전까지는
  // 애플리케이션이나 메뉴 바가 활성화된 상태로 머물러 있는 것이 일반적입니다.
  if (process.platform !== 'darwin') {
    app.quit()
  }
})

app.on('activate', () => {
  // macOS에서는 dock 아이콘이 클릭되고 다른 윈도우가 열려있지 않았다면
  // 앱에서 새로운 창을 다시 여는 것이 일반적입니다.
  if (win === null) {
    createWindow()
  }
})
~~~

### macOS 커스텀 dock menu

~~~js
const { app, Menu } = require('electron')

const dockMenu = Menu.buildFromTemplate([
  {
    label: 'New Window',
    click () { console.log('New Window') }
  }, {
    label: 'New Window with Settings',
    submenu: [
      { label: 'Basic' },
      { label: 'Pro' }
    ]
  },
  { label: 'New Command...' }
])

app.dock.setMenu(dockMenu)
~~~

### 라이브리로드

~~~js
// npm install --dev electron-reload
require('electron-reload')(__dirname);

win.loadURL(`file://${__dirname}/index.html`);
~~~

### 모하비 다크 모드

독바에서 아이콘 우클릭 - Finder에서 보기 - 프로그램 우클릭 - 패키지 내용 보기 - Contents - Info.plist

~~~js
<plist>
<dict>
  ...
  <key>NSRequiresAquaSystemAppearance</key>
  <false />
  ...
</dict>
</plist>
~~~

### Notification

우선 당장 알림을 보낼 만한 게 없어서 임시로 알림 이벤트를 발생할 텍스트 div 를 만들어 두고 거기에 이벤트를 줬다.

~~~js
document.body.querySelector('#event-noti').addEventListener('click', function() {
	let myNotification = new Notification('알림을 보냅니다.', {
		body: '뭔 알림을 보내야 할지 아직 모르겠어요'
	});

	myNotification.onclick = () => {
		alert('알림 눌렀다');
	};
});
~~~

맥북에서 잘 됐는데 윈10에서 실행시켜 보니 알림이 안 왔다. 구글 검색을 해보고 여러 시도를 해보다가 아래 소스를 package.json와 index.js 에 아래 코드를 추가하니 성공했다.

~~~js
// package.json

"build": {
	"appId": "co.corini.coci"
}

// index.js
app.setAppUserModelId('co.corini.coci'); // createWindow 함수 안 첫 줄에 추가하였다.
~~~

