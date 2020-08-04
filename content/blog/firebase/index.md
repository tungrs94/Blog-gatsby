---
title: Firebase
date: "2020-05-01T22:12:03.284Z"
description: Add Firebase to JavaScript
---

Thêm Firebase vào JavaScript project của bạn

1.Tạo Firebase project.
Trước khi bạn có thể thêm Firebase vào ứng dụng JavaScript của bạn, bạn cần tạo 1 Firebase project để kết nối tới ứng dụng của bạn

Truy cập 

    https://console.firebase.google.com/
    và Click (+ Add project)
    
•Nhập tên project của bạn →Continue

•Continue

•Select an account → Default Account for Firebase → Create project

2.Đăng ký ứng dụng của bạn với Firebase.
Sau khi bạn có Firebase project, bạn có thể thêm ứng dụng web của bạn đến nó
Tại project bạn vừa tạo:

•Click Web icon (</>) để khởi chạy quy trình thiết lập

•Nhập APP nickname

•Continue to console

3.Thêm Firebase SDKs và khởi tạo Firebase.
Cài đặt Firebase JavaScript SDK:

•Nếu bạn chưa có tệp package.json, tạo bằng cách chạy lệnh sau từ thư mục gốc của JavaScript project:

~$  npm init

•Cài đặt gói firebase và lưu nó vào tệp package.json của bạn bằng cách chạy:

~$ npm install --save firebase

Sử dụng một trong các tuỳ chọn sau để sử dụng module Firebase trong ứng dụng của bạn:
Bạn có thể yêu cầu các module từ bất kỳ tệp JavaScript nào
```js
	var firebase = require('firebase/app')
	require('firebase/auth')
	require('firebase/firestore')
```
và các sản phẩm Firebase mà bạn muốn sử dụng

Khởi tạo Firebase trong ứng dụng của bạn:
```js
	var firebaseConfig = {
  		apiKey: "api-key",
 		 authDomain: "project-id.firebaseapp.com",
  		databaseURL:"https://projectid.firebaseio.com",
  		projectId: "project-id",
  		storageBucket: "project-id.appspot.com",
  		messagingSenderId: "sender-id",
  		appId: "app-id",
  		measurementId: "G-measurement-id",
        };

	firebase.initializeApp(firebaseConfig);
```

Thay thế phần tương ứng bằng cấu hình dự án Firebase của ứng dụng của bạn trong:

`Project Setting → Your apps → Config`



4.Cài đặt CLI và triển khai Firebase Hosting
Nếu bạn đã liên kết ứng dụng web Firebase của mình với trang web Firebase Hosting, bạn có thể triển khai nội dung và cấu hình trang web của mình.
Chạy lệnh sau từ thư mục gốc của thư mục ứng dụng cục bộ của bạn:

```sh
firebase init
```

Triển khai nội dung và cấu hình lưu trữ của bạn lên Firebase Hosting

```sh
firebase deploy
```
