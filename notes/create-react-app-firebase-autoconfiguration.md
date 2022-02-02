# Getting a web app created with `create-react-app` to use Firebase autoconfiguration

When using Firebase Hosting, firebase will serve the configuration JSON blob (what you normally pass to `initializeApp()`) at `/__/firebase/init.json`. By utilizing this, you don't need to keep the contents of that JSON in your codebase or do any gymnastics to switch between, say, prod and staging environments. This feature is described in [a Firebase blog post](https://firebase.googleblog.com/2017/04/easier-configuration-for-firebase-on-web.html). 

When developing locally, this feature still works when you're using the Firebase emulator to serve the app (`firebase serve`). The problem is that using the firebase emulator doesn't offer hot reloading and some other features of the development server in [`create-react-app`](https://create-react-app.dev/). To make these work together you: 

* Run both the Firebase emulator and the CRA development server.
* Configure the CRA development server to proxy URLs it doesn't understand to the Firebase emulator. 
* Load the app from the CRA development server and be happy. 

To accomplish this:

1. Make your start script in `package.json` start both servers (requires [`npm-run-all`](https://github.com/mysticatea/npm-run-all): 

```json 
{
  ...
  "scripts": {
    "start:react": "react-scripts start",
    "start:firebase": "firebase serve",
    "start": "npm-run-all --parallel start:react start:firebase",
  },
}
```

2. Configure the CRA development server to proxy URLs it can't serve by putting a `proxy` entry in `package.json`:

```json 
{
  ...
  "proxy": "http://localhost:5000/"
}
```
The value of the `proxy` field is the URL that the Firebase emulator is serving from. This only has an effect in local development. See the [CRA docs](https://create-react-app.dev/docs/proxying-api-requests-in-development/#configuring-the-proxy-manually) for more details and info on how to make proxying more configurable.

Now you should be able to just load the app from whereever the CRA dev server serves it, say http://localhost:3000, and be good to go.

Learned some of this from [this blog post](https://peterhrynkow.com/firebase/2018/08/01/firebase-with-create-react-app.html).
