# electron-bug-securewebviewcrash

## To Reproduce

Install and start the app:

```bash
# Clone this repository
git clone https://github.com/pfrazee/electron-bug-securewebviewcrash.git
# Go into the repository
cd electron-bug-securewebviewcrash
# Install dependencies and run the app
npm install && npm start
```

Click the "Open Devtools" button, then click the "Application" tab of devtools.
The webview will crash.

## The Bug

The webview opens a custom protocol, which is registered as follows:

```js
protocol.registerStandardSchemes(['custom'])
app.on('ready', () => {
  protocol.registerHttpProtocol('custom', (req, cb) => {
    cb({ method: 'GET', url: 'http://localhost:12345' })
  }, () => console.log('Registered "custom" protocol'))
})
http.createServer((req, res) => res.end('Hello, world. This is the webview')).listen(12345)
```

The webview has a preload script which registers it as a secure protocol:

```js
var { webFrame } = require('electron')
webFrame.registerURLSchemeAsPrivileged('custom')
```

For some reason, this combination causes devtools to crash the webview when the Application tab is opened.
I used the more fine-grained controls in https://github.com/electron/electron/pull/7665 to isolate this specifically to the "secure" flag.

