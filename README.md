# Interview Management

<img src="https://github.com/ENelyubova/interview-management/blob/master/app.png">

## Tech Stack

- **Vue 3**
- **TS**
- **Pinia**

# Getting Started

This application is using api of firebase, so before using it you have to create an api from <a  target="_blank" href="https://console.firebase.google.com/">firebase</a> and generate an API and apply it to this application, follow the below step to connect api with this app.

First go to <a target="_blank" href="https://console.firebase.google.com/">https://console.firebase.google.com/</a>, create a project and follow the steps, you will get the API Code.

- go to `main.ts`
- replace `firebaseConfig` to your API, like this

```dart
const firebaseConfig = {
    apiKey: 'your_api_code_here',
    authDomain: 'your_api_code_here',
    projectId: 'your_api_code_here',
    storageBucket: 'your_api_code_here',
    messagingSenderId: 'your_api_code_here',
    appId: 'your_api_code_here'
}
```

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Type-Check, Compile and Minify for Production

```sh
npm run build
```

### Lint with [ESLint](https://eslint.org/)

```sh
npm run lint
```
