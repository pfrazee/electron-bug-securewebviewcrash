# electron-bug-notifications

## To Reproduce

Install and start the app:

```bash
# Clone this repository
git clone https://github.com/pfrazee/electron-bug-notifications.git
# Go into the repository
cd electron-bug-notifications
# Install dependencies and run the app
npm install && npm start
```

Scroll the webview down to the "Notify Me" button, and click it.

You will wait 5 seconds, and then the app will crash.

## The Bug

This is the code that causes the issue:

```js
app.on('ready', () => {
  session.defaultSession.setPermissionRequestHandler(onPermissionRequestHandler)
})

function onPermissionRequestHandler (webContents, permission, cb) {
  console.log('waiting 5 seconds', permission)
  setTimeout(() => { console.log('allowing', permission); cb(true) }, 5e3)
}
```

It appears the crash is caused by the permission handler being given a response after time has passed.

I've only experienced the bug with the `notifications` permission.
