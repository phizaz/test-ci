---
layout: post
title: Adding CSS !important flag with jQuery
date: 2015-05-09 14:19:00.000000000 +07:00
tags: web
---
แน่นอนว่าในบางทีเราอาจจะต้องการกำหนดค่าบางอย่างแบบ dynamic ด้วย jQuery เช่นความสูงของหน้า ๆ หนึ่ง ซึ่งอาจจะไม่เท่ากันในแต่ละสถานการณ์การทำทุกอย่างด้วย CSS อาจจะซับซ้อนเกินไป หรืออาจจะทำไม่ได้ แต่ก็ติดที่ว่าหากมีการกำหนด `!important` flag ในกฎ CSS แล้วแน่นอนว่าก็ต้องใช้ `!important` ด้วยกันมาหักล้าง

ปัญหาคือ jQuery ไม่สามารถใส่ flag นี้เข้าไปในการกำหนดค่าต่าง ๆ ของ CSS ได้ เราไม่สามารถ `$(somthing).css('height', '100px !important');` แล้วให้ผลลัพธ์ที่ถูกต้องได้ (อย่างน้อยก็ในช่วงเวลาที่ผมเขียนอยู่...)

เราจึงต้องใช้วิธีอื่น ... ซึ่งก็มีคนเสนอหลายวิธี ผมพบว่าวิธีที่ผมชอบที่สุด (แน่นอนว่าต้องเขียนง่ายมาก่อน ฮ่ะ ๆ) ก็คือนี่ ...

```
var elem = $("#elem");
elem[0].style.removeAttribute('height');
elem[0].style.setProperty('height', '100px', 'important');
```

หยิบมาจาก [stackoverflow.com](http://stackoverflow.com/questions/2655925/how-to-apply-important-using-css) จากคำตอบของคุณ **BettaSplendens**

