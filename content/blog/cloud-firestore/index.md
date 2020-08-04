---
title: Cloud Firestore
date: "2020-05-06T23:46:37.121Z"
description: Firebase Cloud Firestore - Giới thiệu và hướng dẫn Cloud Firestore
---

Cloud Firestore là một Database được phát triển từ Firebase và Google Cloud Platform. Nó giúp cho việc đồng bộ dữ liệu giữa các ứng dụng phía Client một cách nhanh chóng (Realtime) và hỗ trợ lưu offline data trong ứng dụng của bạn.

Cloud Firestore lưu dữ liệu theo mô hình dữ liệu NoSQL. Dữ liệu được lưu trữ trong các file tài liệu chứa các trường được ánh xạ vào các giá trị, gồm 3 thành phần chính là: Collection, document, field. Các file tài liệu này được lưu trữ có thể sử dụng nó để tổ chức và truy vấn. Cloud Firestore hỗ trợ nhiều kiểu dữ liệu: string, array, date-time, ...

 Thêm dữ liệu vào Database:
Đầu tiên cần tạo Database:
- Ở Firebase project Click: 
	
    Database → Create database → Start collection → Add document (auto ID) → Add field
    
Ví dụ:

	firestore.collection("users").add({
    	first: "Nguyen",
    	last: "Tung",
    	born: 1994
	})
	.then(function(docRef) {
    console.log("Document written with ID: ", docRef.id);
	})
	.catch(function(error) {
    console.error("Error adding document: ", error);
	});


Ở ví dụ trên chúng ta đã truy cập vào Collection ‘users’ với Document bất kỳ và thêm dữ liệu:

	First: ‘Nguyen’,
	Last: ‘Tung’,
	Born: 1994

Chúng ta cũng có thể thêm dữ liệu trong các Collection lồng nhau
Ví dụ:

	firestore.collection("users’).doc(‘idUser’)
	.collection(‘nickname’).add({
   	 	first: "Nguyen",
    		last: "Hoang",
    		street: 6
		})
	.then(function(docRef) {
 	   	console.log("Document written with ID: ", docRef.id);
	})
	.catch(function(error) {
    		console.error("Error adding document: ", error);
	});

Ở ví dụ trên ta đã thêm dữ liệu vào Collection ‘nickname’ thuộc phần nhánh của Collection ‘users’
