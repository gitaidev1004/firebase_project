# 1. Firebase 프로젝트

## 1-1. Firebase 프로젝트 생성

1. Firebase Console에 접속합니다.
2. 우측 상단의 "프로젝트 추가" 버튼을 클릭하여 새 프로젝트를 생성합니다.
3. 프로젝트 이름을 입력하고, "계속" 버튼을 클릭하여 Google Analytics를 설정하거나 비활성화합니다.
4. "프로젝트 만들기" 버튼을 클릭하고, 완료되면 **"프로젝트 대시보드"**로 이동합니다.

<br><br><br>

## 1-2. Firebase CLI 설치

- Firebase CLI는 Firebase와 상호작용하기 위한 도구입니다. Node.js가 설치되어 있어야 합니다.
- Node.js가 설치되어 있는지 확인하고, 설치되지 않은 경우 Node.js 공식 웹사이트에서 설치합니다.

**Firebase CLI를 전역으로 설치합니다.**

```bash
npm install -g firebase-tools
```

<br>

**설치 후, Firebase CLI 버전을 확인하여 제대로 설치되었는지 확인합니다.**

```bash
firebase --version
```

<br><br><br>

## 1-3. Firebase 초기화

### 1-3-1. 프로젝트 폴더를 생성하고 이동

```bash
mkdir my-firebase-app
cd my-firebase-app
```

<br><br>

### 1-3-2. Firebase에 로그인

```bash
firebase login
```

<br><br>

### 1-3-3. Firebase 프로젝트 초기화

```bash
firebase init
```

<br>

**초기화 과정**

Hosting: Firebase Hosting을 선택합니다.
Functions: 서버리스 함수도 필요하다면 선택합니다.
Firestore / Realtime Database: 데이터베이스가 필요하다면 선택합니다.

<br><br>

### 1-3-4. 기본 설정 진행

**Hosting 설정**

public 디렉토리로 public을 입력합니다 (정적 파일이 위치할 폴더).
Single Page Application(SPA)인 경우 rewrite all URLs to /index.html을 선택합니다.

<br>

**Functions 설정 (선택 사항)**

Functions에 사용할 JavaScript를 선택합니다.
디렉토리 이름으로 functions를 사용합니다.

<br>

**Firestore / Realtime Database 설정 (선택 사항)**

원하는 데이터베이스 옵션을 선택하고, 규칙을 설정합니다.

<br><br><br>

## 1-4. 애플리케이션 개발

### 1-4-1. 프로젝트 파일 트리 구조

```lua
my-firebase-app/
│
├── public/                # 웹 애플리케이션 파일이 위치하는 폴더
│   ├── index.html
│   ├── styles.css
│   └── main.js
├── functions/             # Firebase Functions 코드
│   ├── index.js
│   └── package.json
├── firebase.json          # Firebase 설정 파일
└── .firebaserc            # Firebase 프로젝트 설정 파일
```

<br><br>

### 1-4-2. 예제 파일 내용

**public/index.html**

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Firebase Hosting Demo</title>
  <link rel="stylesheet" href="styles.css">
</head>
<body>
  <h1>Welcome to Firebase Hosting</h1>
  <script src="main.js"></script>
</body>
</html>
```

<br>

**public/styles.css**

```css
body {
  font-family: Arial, sans-serif;
  text-align: center;
  margin-top: 50px;
}
```

<br>

**public/main.js**

```javascript
console.log('Hello from Firebase Hosting!');
```

<br><br><br>

## 1-5. 로컬 테스트

Firebase CLI를 사용하여 로컬에서 애플리케이션을 테스트할 수 있습니다:

```bash
firebase serve
```

또는

```bash
firebase emulators:start
```

<br><br><br>

## 1-6. 배포

Firebase Hosting에 애플리케이션을 배포합니다:

```bash
firebase deploy
```

배포 후, Firebase Console의 Hosting 섹션에서 배포된 웹사이트 URL을 확인할 수 있습니다.

<br><br><br>

## 1-7. 서버리스 함수 작성 및 연동

서버리스 함수는 Firebase Cloud Functions를 사용하여 작성합니다.

### 1-7-1. functions/index.js

```javascript
const functions = require('firebase-functions');
const admin = require('firebase-admin');
admin.initializeApp();

// HTTP 함수 예제
exports.helloWorld = functions.https.onRequest((req, res) => {
  res.send('Hello from Firebase!');
});

// Firestore 예제 함수
exports.addMessage = functions.https.onRequest(async (req, res) => {
  const original = req.query.text;
  const writeResult = await admin.firestore().collection('messages').add({ original: original });
  res.json({ result: `Message with ID: ${writeResult.id} added.` });
});
```

<br><br>

### 1-7-2. functions/package.json

```json
{
  "name": "functions",
  "description": "Firebase Functions",
  "dependencies": {
    "firebase-admin": "^11.0.0",
    "firebase-functions": "^4.0.0"
  },
  "engines": {
    "node": "18"
  }
}
```

배포:

```bash
firebase deploy --only functions
```

<br><br><br>

## 1-8. Firebase Realtime Database / Firestore 연동

### 1-8-1. Realtime Database 예제

**데이터베이스에 데이터를 추가하는 함수**

```javascript
const admin = require('firebase-admin');
admin.initializeApp();

exports.addToDatabase = functions.https.onRequest(async (req, res) => {
  const db = admin.database();
  const ref = db.ref('messages');
  await ref.push({ text: 'Hello World!' });
  res.send('Data added to Realtime Database');
});
```

<br>

**Firebase Console에서 Realtime Database 규칙 설정**

```json
{
  "rules": {
    ".read": "auth != null",
    ".write": "auth != null"
  }
}
```

<br><br>

### 1-8-2. Firestore 예제

**Firestore에 데이터를 추가하는 함수**

```javascript
const admin = require('firebase-admin');
admin.initializeApp();

exports.addToFirestore = functions.https.onRequest(async (req, res) => {
  const firestore = admin.firestore();
  await firestore.collection('messages').add({ text: 'Hello Firestore!' });
  res.send('Data added to Firestore');
});
```

<br>

**Firebase Console에서 Firestore 규칙 설정**

```json
service cloud.firestore {
  match /databases/{database}/documents {
    allow read, write: if request.auth != null;
  }
}
```