---
layout: post
title: Poor scrolling performance using bootstrap-select on a large list
date: 2015-09-13 11:13:00.000000000 +07:00
categories: dev
tags: web
---
**Bootstrap select** ([เว็บไซต์](http://silviomoreto.github.io/bootstrap-select/)) เป็น plugin ที่ทำให้เราสามารถ "รับประกัน" ประสบการณ์การใช้งาน dropdown list ของผู้ใช้ได้ เนื่องจากโดยปกติแล้ว dropdown list หรือ `<select>` มีความแตกต่างขึ้นอยู่กับ browser และก็ยากต่อการกำหนดค่าต่าง ๆ เนื่องจากสามารถกำหนดค่า CSS ได้อย่างจำกัดอย่างยิ่ง จึงมีคนคิด bootstrap select ขึ้นมาโดยอาศัยการจำลอง "ปุ่มกด" ประกอบกับเทคนิคเล็กน้อย เพื่อใช้แทน `<select>`

หากเราท่านติดตั้ง bootstrap select และใช้งานมันอย่างที่ได้เขียนกำกับไว้แล้วนั้น ผลลัพธ์ก็จะงดงามอย่างไม่น่ามีปัญหาอะไร 

![](/content/images/2016/07/bootstrap-select-ex.png)

ทว่าปัญหาจะเกิดขึ้นก็ต่อเมื่อ เราเริ่มใช้ list ที่มีขนาด "ใหญ่" นี่ที่นี้ผมกล่าวถึง ราว ๆ 60-70 ขึ้นไป เช่น จังหวัดในประเทศไทย หรือรายชื่อมหาวิทยาลัยในประเทศไทย ซึ่งผมได้ใช้ bootstrap select กับข้อมูลเหล่านี้ แล้วพบว่าในบางกรณีมีปัญหาการ "scroll หน้าเว็บที่ช้าเอามาก ๆ" และเป็นไปอย่าง**ผิดปกติ** กล่าวคือเมื่อเรา **คลี่** `select` ออกมาการ scroll ก็ลื่นปรื้ดตามปกติ แต่เมื่อเรา **หุบ** `select` เข้าไปการ scroll กับช้าราวกับเต่าคลาน (framerate ที่ผมพบไม่น่าจะเกิน 5 ต่อวินาที)

เมื่อผมลองดูหน้าต่าง performance ของ google chrome ก็จะได้ภาพดังต่อไปนี้

![](/content/images/2016/07/bootstrap-select-problem.png)

จะเห็นว่า framerate น้อยมาก ๆ ตอนที่มีการ scroll เกิดขึ้นจะเห็นกราฟสีเขียวดรอปลงในช่วงนี้

ที่แปลกคือปัญหาการ scroll เกิดขึ้นเมื่อ **หุบ** list เข้าไปแล้วซึ่งหมายความมว่า `display: none` ซึ่งตามความเข้าใจของผม browser ก็ควรจะทำงานน้อยลง แม้ว่า list จะใหญ่มี items เยอะ แต่มันก็สามารถมองข้ามไปด้วยเพราะว่ามันไม่ต้อง render เลยแม้แต่น้อย (ซึ่งผมอาจจะผิด) 

หากมองลึกลงไปหน่อยจะเห็นว่าส่วนที่ช้าที่สุด ที่ทำให้การ render ในภาพรวมช้าลงคือการ Layout ของ browser ผมพยายามหาวิธีแก้ปัญหาตรงนี้ ก็ยังไม่พบวิธีแก้ แต่ผมก็พบบทความดี ๆ เอาไว้อ่านเล่น ๆ นะครับ เกี่ยวกับการลดภาระการ Layout [Avoid large, complex layouts and layout thrashing](https://developers.google.com/web/fundamentals/performance/rendering/avoid-large-complex-layouts-and-layout-thrashing?hl=en)

ผมได้โพสปัญหานี้ไว้ที่ [github.com/bootstrap-select](https://github.com/silviomoreto/bootstrap-select/issues/1144) แต่ว่ายังไม่ได้รับการตอบกลับแต่อย่างใด

ผมเลยคิดว่าอาจจะต้องแก้ปัญหาด้วยวิธีการที่ **hack** ซักหน่อย ตอนนี้ผมรู้ว่า `display:none` ทำให้ช้า ไม่ว่าจะช้าด้วยอะไรก็แล้วแต่ และผมลองแล้วว่าถ้าเปลี่ยนจาก `display:none` เป็น `visibility: hidden` ปัญหานี้จะไม่เกิดขึ้น เพราะฉะนั้นผมจะแก้ปัญหานี้ด้วย **การเปลี่นยวิธีการซ่อน** ซึ่งแน่นอนว่าแม้ทั้่งคู่จะทำให้มองไม่เห็นวัตถุเหมือนกันแต่ไม่ได้ให้ผลลัพธ์ที่เหมือนกันซะทีเดียว เนื่องจาก `display:none` จะไม่มีการ render เลย แต่ว่า `visibility: hidden` จะมีการ render อยู่ คือมีการจองพื้นที่ใน `document` แต่ว่าไม่แสดงออกมาให้เห็นเฉย ๆ นั่นแปลว่า ถ้าเรามีวัตถุใหญ่ ๆ แต่ว่า `visibility: hidden` ไว้ แม้เราจะมองไม่เห็นแต่ว่าเราจะเห็นว่าหน้านี้วัตถุขนาดใหญ่เนื่องจากจะมี scroll bar ให้เห็น

และในที่นี้เรากำลังพูดถึง list ขนาดใหญ่ ซึ่งจะกินพื้นที่ใหญ่มาก ๆ ถ้าเราซ่อนมันไว้ด้วยวิธี `visibilty: hidden` มันจะทำให้เว็บไซต์เรามีพื้นที่ scroll ที่ว่างเปล่าจำนวนมาก

แต่ก็ด้วยความจำเพาะของ bootstrap select อีกนั่นแหละทีว่า ถ้าหากเรากด **คลี่** `<select>` ออกมาแล้ว มันจะพยายามปรับให้ ขนาดของ dropdown list ไม่เกินขนาดของหน้าจอ นั่นแปลว่าถ้าเราคลีออกมาแล้ว ไม่ว่า list เราจะใหญ่ขนาดไหน มันก็จะปรับให้มีขนาดไม่เกินหน้าจอ 

ด้วยทั้งหมดทั้งมวลนี้ผมจึงคิดได้ว่า ถ้าผมบังคับให้ bootstrap select คลี่ีตั้งแต่ต้น (จะได้มีขนาดไม่เกินหน้าจอ) แต่ว่าซ่อนเอาไว้ ผมก็จะสามารถแก้ปัญหานี้ได้ชงัดนัก

ผมจะยกตัวอย่างโค้ดที่ผมใช้ implement ตัวแก้ปัญหานี้ ในรูปแบบของ angular (ใช้ es6) ผมสร้าง directive ใหม่ชื่อว่า **selectpicker-fatser**

```
// this is only for large options scrolling performance fix

import angular from 'angular';

import './selectpicker-faster.css';

let partial = angular.module('selectpickerFaster', []);

partial.directive('selectpickerFaster',
  () => {
    return {
      restrict: 'EA',
      link: link,
    };
  });

function link($scope, element, attrs) {
  let $element = $(element);
  $element
    .siblings('.bootstrap-select')
    .click(
      function () {
        $(this).addClass('faster');
      });
}

export var selectpickerFasterComponentModule = partial;

```

โดยในไฟล์ selectpicker-faster.sass (ซึ่งจะถูก compile เป็น .css) กำหนดไว้ว่า

```
.bootstrap-select.faster
  .dropdown-menu
    display: block
    visibility: hidden
.bootstrap-select.faster.open
  .dropdown-menu
    visibility: visible
```

กล่าวคือใช้ `visibility: hidden` แทน `display:block` นั่นเอง

เวลาเราใช้งานใน angular ถ้าหากเราใช้ [angular-bootstrap-select](https://github.com/joaoneto/angular-bootstrap-select) ด้วยแล้ว จะทำให้เราใช้ directive "selectpicker" ในการสร้าง bootstrap select สำหรับ `<select>` ที่ต้องการได้ จะได้โค้ดการใช้งานเป็นแบบนี้ ในภาษา html

```
<select selectpicker
        selectpicker-faster
        ng-options="....">
  <option value="">โปรดเลือก</option>
</select>
```

เท่านี้ปัญหาการ scroll ก็จะหมดไป โดยที่เราไม่รู้เสียด้วยซ้ำว่าต้นเหตุจริง ๆ แล้วมันเกิดขึ้นมาได้ยังไง (-_-")
