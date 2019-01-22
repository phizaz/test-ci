---
layout: post
title: Adding babel-polyfill with Browserify
date: 2015-09-20 14:19:00.000000000 +07:00
categories: dev
tags: js web
---
ปกติเราใช้ babel เมื่อเราต้องการใช้ฟีเจอร์บางอย่างของ es6 ([อ่านเพิ่ม](https://github.com/lukehoban/es6features)) ซึ่งวิธีการทำงานของ babel ก็คล้าย ๆ กับ coffeescript ซึ่งก็คือแปลงนั้น ๆ กลับมาเป็น javascript ES5 ให้สามารถรันได้ทาง web browser ทั่ว ๆ ไป (ไม่ใช่ทุก browser จะรองรับ es6 ในปริมาณที่เท่ากัน) ซึ่งในเว็บไซต์ documentation ของ babel เองก็จะระบุไว้ว่าแต่ละฟีเจอร์นั้นจะต้อง setup babel ไม่เหมือนกันบางฟีเจอร์อาจจะต้องการการติดตั้งมากกว่าอันอื่นเล็กน้อย บางอันอาจจะต้องอาศัยโปรแกรมอื่นช่วยเช่น คำสั่ง import ที่ต้องอาศัยเครื่องมือตัวกลางมาคอย require ไฟล์ต่าง ๆ ให้เพราะว่า browser babel ไม่ได้ใส่เครื่องมือพวกนี้มาให้

สถานการณ์เป็นแบบนี้ครับ ผมติดตั้ง babel (babelify) ร่วมกับ browserify ที่จริงก็มี watchify (แต่ว่าไม่สำคัญในที่นี้เท่าไหร่) และเพราะการตั้งค่าไม่ครบถ้วนของผม**ทำให้ babel แปลโค้ดให้ผมไม่ได้เป็นภาษา es5 ที่รองรับทุก ๆ browser** และส่งผมให้โค้ดของผมบึ้มในหลาย ๆ browser บึ้มแบบไม่รู้ว่าผิดตรงไหนเลยล่ะ ฮ่ะ ๆ 

ผมลองใช้ for .. of loop ซึ่งมาพร้อมกับ es6 มีลักษณะแบบนี้

```
let schools = [... list of schools ...];
for (let school of schools) {
	console.log('school name:', school);
}
```

แล้ว error ที่ผมเห็นถ้าเปิดด้วย browser เช่น safari จะพบแบบนี้

```
Error: Can't find variable: Symbol
```

ซึ่งตอนแรกผมก็ไม่เข้าใจว่าหมายถึงอะไรแน่ แต่สุดท้ายมันก็คือบอกว่ามันไม่รู้จัก Symbol ซึ่งเป็น class ที่มีใน es6 (for .. of จะถูกแปลงด้วย babel ไปใช้ Symbol.iterate แทน ซึ่งก็ไม่มีใน es5 อยู่ดี) ซึ่งการจะเสริมความสามารถ Symbol ให้ browser ทั่ว ๆ ไปก็คือต้องมีการใช้ **polyfill** ที่จริงก็คือโค้ด javascript ที่จะช่วยเสริมความสามารถของ es5 ให้มี class ต่าง ๆ ของ es6 นั่นเอง

โดยการเพิ่ม polyfill เข้าไปพร้อมกับ babel นั้น ในกรณีนี้ต้องเพิ่ม config ให้กับ browserify

ตอนแรกนั้นเราลง babelify ไว้แล้ว (เพราะว่าเราต้องใช้กับ browserify) แต่ว่าเราอาจจะไม่ต้องลง babel ก็ได้ (babelify ไม่ได้บังคับ) ดังนั้นเราก็ต้องลง babel ก่อน (เพราะว่าเราจะเรียกใช้ `babel/polyfill`)

```
npm install --save-dev babel
```

หลังจากนั้นเราก็เพิ่ม `babel/polyfill` ไปเป็นหนึี่งใน entry points ของเราใน browserify เพื่อให้โค้ดส่วนนี้เป็นส่วนหนึ่งของ bundle เรา

```
let entryPoints = [... application entrypoints ...];

// add polyfill to be the first in the entrypoints
entryPoints.unshift(require.resolve("babel/polyfill"));
```

นี่ก็คือการเพิ่ม `babel/polyfill` เข้าไปเป็นส่วนหนึ่งของ sourcecode เรา ซึ่งจะทำให้ class แปลก ๆ ของ es6 สามารถทำงานได้บน es6 แล้ว :D
