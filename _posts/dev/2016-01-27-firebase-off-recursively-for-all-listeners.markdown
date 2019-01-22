---
layout: post
title: Firebase.off() Recursively for All Listeners
date: 2016-01-27 11:11:00.000000000 +07:00
categories: dev
tags: web
---
หากท่านเคยใช้ firebase ท่านก็มักจะคุ้นชินกับการใช้ method `ref.on('..', callback);` ของ firebase ซึ่งเป็นวิธีการหลักในการอ่านค่าจาก database ของ firebase ซึ่งอาศัยวิธีการสร้าง listener นั่นเอง

มันมักจะมีเหตุการณ์เสมอที่เราต้องการจะ "ปิด" listener เหล่านี้ ซึ่ง firebase มีเครื่องมือสำหรับสิ่งนี้ก็คือการเรียก `ref.off()` สำหรับการปิดทุก ๆ listener ทีอยู่บน ref นี้ 

ปัญหาก็เกิดขึ้นเมื่อ firebase ไม่มี method สำหรับ "ปิดทุก ๆ listener" นั่นก็คือเราไม่สามารถ เรียก `rootRef.offRecursively()` อะไรลักษณะนี้ได้

ในทีนี้ผู้เขียนต้องการใช้ฟังก์ชันดังกล่าวอย่างมาก จึงเสนอวิธีการแก้ปัญหาดังกล่าวด้วยการเขียน wrapper function ขึ้นมาดังนี้

ไฟล์: firebaseWrapper.js
```
let allRefs = [];

// all listener must be wrapped with this function
// for properly allOff function
export function wrap (ref) {
  allRefs.push(ref);
  console.log('allRefs:', allRefs);
  return ref;
};

export function allOff () {
  allRefs.forEach((ref) => ref.off());
  allRefs = [];
};
```
ดังนั้นเมื่อใดก็ต้องที่เราต้องการ ใช้ method พวกที่จะสร้าง listener เช่น `.on('...', callback)` เราจะต้อง wrap มันด้วย wrapper function ที่เราเพิ่งเขียนขึ้นมาดังนี้

```
import {wrap, allOff} from './firebaseWrapper';
import Firebase from 'firebase';

const fireRef = new Firebase('....');

wrap(fireRef).onAuth(....);

const userRef = fireRef.child('users');

wrap(userRef).on('value', callback);

```
แต่ละครั้งที่เราเรียก `wrap` มันจะจำว่า มี `ref` ตัวไหนบ้างที่เราเรียกใช้ไปแล้ว ซึ่งจะทำให้ `allOff()` สามารถเรียก `off()` ได้ครบทุกตัวที่เกี่ยวข้อง
