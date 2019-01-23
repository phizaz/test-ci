---
layout: post
title: Single-use Angular Listener
date: 2015-04-07 11:19:00.000000000 +07:00
categories: dev
tags: [angular, web]
---
การสร้าง listener ที่สามารถฟังข้อความได้เพียงครั้งเดียว แล้วก็ทำลายตัวเองทิ้งเป็นความสามารถที่วิเศษเอามาก ๆ เพราะว่าบางทีการที่มันไม่ทำลายตัวเองทิ้งกลับทำให้เกิดการทำงานผิดปกติได้ ในกรณีของผมใช้ listener กับหน้า User ในการดูว่าผู้ใช้ออกจาระบบไปแล้วหรือยัง ?​ หากออกไปแล้วก็ควรจะ redirect ไปยังหน้าอื่น (เนื่องจากผู้ใช้ไม่มีสิทธิ์อยู่ในหน้านี้แล้ว) และหากไม่จัดการเรื่องนี้ดี ๆ อาจะทำให้เกิด redirect-loop ได้เลยทีเดียว 

เราจะเริ่มจาก listener ปกติใน AngularJs ซึ่งสามารถใช้งานได้ดังนี้ (สมมติว่าผูกไว้กับ rootScope)

```coffeescript
$rootScope.$on 'message-name', (event, value) ->
	 ... do something interesting here ...
```

listener ตัวนี้จะ "ฟัง" message ทุก ๆ ชิ้นที่ผ่านเข้ามาในสายตามัน และเลือกเฉพาะตัวที่เป็นชนิด "message-name" แล้วหยิบมาโยนใส่ call-back function ที่เรากำหนดไปใน .$on

แต่ในบางครั้งเราต้องการให้มันทำงานเพียงแค่ครั้งเดียว กล่าวคือหากมีการเรียก call-back function นั้นแล้วก็ให้ ถือว่า listener นี้ไม่มีตัวตนอีกต่อไป ใน AngularJS เราสามารถทำได้ดังนี้

```coffeescript
deregister = $rootScope.$on '<message>', (event, value) ->
	 deregister()
	 ... do something interesting here ...
```

เนื่องจาก .$on คืนค่าเป็น function ที่เมื่อถูกเรียกแล้วจะ deregister listener นั้นออกจากระบบ ทำให้ listener นี้ทำงานเพียงครั้งเดียว
