---
title: cloud functions
date: "2020-08-04T22:40:32.169Z"
description: Tạo một API call Http request sử dụng cloud functions
---


Trước tiên, các bạn tạo 1 http functions với dạng http request
```js
exports = module.expost = functions.https.onRequest((req, res) => {
  // ...
});
```
Cài đặt các gói `npm install —save express` `npm install —save cors`

Ví dụ về việc cài đặt 

```js
const express = require('express');
const cors = require('cors');

const app = express();

// Automatically allow cross-origin requests
app.use(cors({ origin: true }));

// Add middleware to authenticate requests
app.use(myMiddleware);

// build multiple CRUD interfaces:
app.get('/:id', (req, res) => res.send(Widgets.getById(req.params.id)));
app.post('/', (req, res) => res.send(Widgets.create()));
app.put('/:id', (req, res) => res.send(Widgets.update(req.params.id, req.body)));
app.delete('/:id', (req, res) => res.send(Widgets.delete(req.params.id)));
app.get('/', (req, res) => res.send(Widgets.list()));

// Expose Express API as a single Cloud Function:
exports.widgets = functions.https.onRequest(app);
```

còn đây là cài đặt của tôi: 
```js
const express = require('express');
const momentTz = require('moment-timezone');
const cors = require('cors');
const functions = require('firebase-functions');
const app = express();
const myMiddleware = require('./Actions/myMiddleware');

// Automatically allow cross-origin requests
app.use(cors({origin: true}));

const API_PREFIX = 'apiCalendar';

// // Add middleware to authenticate requests
app.use(myMiddleware);

app.use((req, res, next) => {
// read hostname to set environment for firestore
  if (req.hostname.includes('localhost') ||
      req.hostname.includes(‘test-stg.[domain]’) ||
      req.rawHeaders.includes('test-stg.[domain]')) {
    //stg
    req.CalEnv = 'stg';
  }
// remove prefix when use hosting behavior
  if (req.url.indexOf(`/${API_PREFIX}/`) === 0) {
    req.url = req.url.substring(API_PREFIX.length + 1);
  }
  next();
});

// build multiple CRUD interfaces:
app.get('/', (req, res) => getEvents(req, res));
app.post('/create', (req, res) => createEvent(req, res));
app.post('/update', (req, res) => updateEvent(req, res));
app.post('/move', (req, res) => moveEvent(req, res));
app.delete('/', (req, res) => deleteEvent(req, res));

// Expose Express API as a single Cloud Function:
exports = module.exports = functions.https.onRequest(app);

```

Ví dụ về export file từ thư mục khác trong môi trường node

```js
const decoder = require('jwt-decode');
const moment = require('moment');

function getTokenFromHeader(req) {
  if (req.headers.authorization && req.headers.authorization.split(' ')[0] ===
      'Bearer') {
    return req.headers.authorization.split(' ')[1];
  }
  return null;
}

const myMiddleware = (req, res, next) => {
  try {
    const accessToken = getTokenFromHeader(req);
    if (!accessToken)
      return res.status(400).send('AccessToken is missing');

    const userData = decoder(accessToken);
    if (moment(userData.exp * 1000).isBefore(moment().format()))
      return res.status(400).send('AccessToken is expired');

    return next();
  } catch (e) {
    console.log(e);
    return res.status(401).send('Login require!');
  }
};

exports = module.exports = myMiddleware;
```
Các request khác các bạn làm tương tự, tuỳ thuộc vào bảo mật api của bạn mà thiết kế chức năng của functions myMiddleware theo yêu cầu của bạn nhé.

Deploy api functions như bình thường dùng:
`firebase deploy --only functions:[function name]`
khi deploy thành công: chúng ta có thể truy cập http request bằng: `https://us-central1-[projectId].cloudfunctions.net/[function name]` 

Test case: sử dụng postman call functions

Có thể chạy test function trên local bằng: `firebase serve --only functions` hoặc `firebase emulators:start`

![upload successful](/images/pasted-6.png)

Oke bây giờ chúng ta có thể sử dụng api với `localhost:5000` rồi


* Configure hosting behavior:

Dưới đây là cài đặt hosting giúp chúng ta có thể truy cập api function với 1 hosting.
mục này sẽ được thêm ở source code website của bạn, không phải thêm ở trong project cloud function nhé.

`Important: Firebase Hosting supports Cloud Functions in us-central1 only. `

Full flow in firebase.json

```json
{
  "hosting": {

    "public": "dist/app",  // "public" is the only required attribute for Hosting

    "ignore": [
      "firebase.json",
      "**/.*",
      "**/node_modules/**"
    ],

    "redirects": [ {
      "source": "/foo",
      "destination": "/bar",
      "type": 301
    }, {
      "source": "/firebase/**",
      "destination": "https://www.firebase.com",
      "type": 302
    } ],

    "rewrites": [ {
      // Shows the same content for multiple URLs
      "source": "/app/**",
      "destination": "/app/index.html"
    }, {
      // Configures a custom domain for Dynamic Links
      "source": "/promos/**",
      "dynamicLinks": true
    }, {
      // Directs a request to Cloud Functions
      "source": "/bigben",
      "function": "bigben"
    }, {
      // Directs a request to a Cloud Run containerized app
      "source": "/helloworld",
      "run": {
        "serviceId": "helloworld",
        "region": "us-central1"
      }
    } ],

    "headers": [ {
      "source": "**/*.@(eot|otf|ttf|ttc|woff|font.css)",
      "headers": [ {
        "key": "Access-Control-Allow-Origin",
        "value": "*"
      } ]
    }, {
      "source": "**/*.@(jpg|jpeg|gif|png)",
      "headers": [ {
        "key": "Cache-Control",
        "value": "max-age=7200"
      } ]
    }, {
      "source": "404.html",
      "headers": [ {
        "key": "Cache-Control",
        "value": "max-age=300"
      } ]
    } ],

    "cleanUrls": true,

    "trailingSlash": false

    // Required to configure custom domains for Dynamic Links
    "appAssociation": "AUTO",

  }
}
```

Tài liệu tham khảo: 
- https://www.npmjs.com/package/express
- https://expressjs.com/en/starter/installing.html
- https://firebase.google.com/docs/functions/http-events
- https://firebase.google.com/docs/hosting/full-config
