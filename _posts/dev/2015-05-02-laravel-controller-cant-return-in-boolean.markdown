---
layout: post
title: Laravel Controller Can't Return in Boolean
date: 2015-05-02 10:35:00.000000000 +07:00
categories: dev
tags: [php, laravel]
---
**"รีบ ๆ เรียน ดูแค่ตัวอย่างแล้วก็เอาไปทำเองบ้าง"** เป็นวิธีที่ผมใช้เรียนการทำเว็บและเขียนโปรแกรมมาตลอด เพราะว่าตัวเองก็ค่อนข้างรีบ จะให้มานั่งอ่านละเอียดจน **ซึ้ง** แล้วค่อยมาเริ่มเขียนก็อาจจะทนไม่ไหวซะก่อน

แต่ถ้าพูดถึงเรื่อคงความประหยัดเวลาในการเรียนรู้แล้ว แม้วิธีนี้จะทำให้เราสามารถทำผลงานชิ้นแรกออกมาได้เร็ว แต่เราอาจจะต้องเสี่ยงต่อการสูญเสียเวลากับการแก้บัคที่ไม่ควรเกิดขึ้นอีกมาก

หลาย ๆ ครั้งที่ติดปัญหาและก็ต้อง google เพื่อหาวิธีแก้อยู่เป็นแรมหลายชั่วโมงเทียว สุดท้ายปัญหาจริง ๆ ก็คือผมไม่ได้อ่าน Documentation ส่วนที่เป็นเงื่อนไขที่เขียนอยู่ข้างล่างนั่นเอง เหตุการณ์เหล่านี้ไม่เกิดขึ้นครั้งเดียว แต่หากผมใช้เวลากับการอ่านเงื่อนไขด้านล่างบ้าง ก็คงประหยัด **น้ำตา** ไปมากทีเดียว

ปัญหาที่พบและกว่าจะหาต้นเหตุเจอได้กับเป็นข้อจำกัดง่าย ๆ ของ Laravel เอง ที่ผมควรจะรู้ (ผมไม่มั่นใจว่าได้เขียนไว้ใน Documentation หรือเปล่า) นั่นคือ **ฟังก์ชัน Controller ใน Laravel ไม่สามารถรีเทิร์นเป็น Boolean ได้ !**

###เหตุการณ์
โดยปกติก็คงไม่มีใครเขียนให้ `controller` รีเทิร์นเป็น `boolean` หรอก (โดยปกติผมก็ไม่) แต่ตอนนั้นผมแค่ต้องการให้มันตัดจบที่ตรงนี้ ไม่มีทำคำสั่งส่วนข้างล่างต่อ เช่น

```php
public function home() {
	$a = 10;
	$b = $a + 5;
	
	var_dump($a);
	return true;
	
	return Response::json([ 'a' => $a, 'b' => $b ]);
}
```

ผมแค่ต้องการดูว่า ตัวแปร `$a` มีค่าเท่าไหร่ด้วย `var_dump` แต่ก็ต้องไม่ต้องการให้เกิดการรีเทิร์นให้ผลลัพธ์มาบดบัง `var_dump` ด้านบน ผมจึงตัดสินใจ `return true;` ก่อน ทั้ง ๆ ที่การเขียน `return;` แค่นี้ก็สามารถทำงานตามที่ผมต้องการได้อย่างไม่มีปัญหา เพียงแต่ผมรู้สึกไม่ดีในการ return แล้วไม่มีค่าออกไปเท่านั้นเอง

###ผลที่ตามมาก็คือ...
![](/content/images/2016/07/laravel-return-boolean.png)
**The Resonse content must be a string or object implementing __toString(), "boolean" given.**  

ซึ่งจะเกิด error อยู่บนไฟล์ ```Response.php:358``` (ไม่ได้เกี่ยวกับโค้ดกรูเล้ยยย) ซึ่งมันไม่ได้บอกตรง ๆ กับผมว่า **"เอ็ง return ผิดนะเห้ยที่ function นี้ ๆ นะ"** อีกอย่างผมก็ไม่รู้หรอกว่า **Response content** ที่มันกล่าวถึงคืออะไร

เชื่อหรือไม่ว่าผมต้องเสียเวลา google กว่า 1 ชม. หรือมากกว่านั้นเพื่อแก้ปัญหานี้ (แต่ก็หาไม่เจอใน google หรอก กรณีที่ตรงกันเด๊ะ ๆ) สุดท้ายค่อยระลึกเองได้ว่า หรือว่า Response content คือ ค่าที่เรา return ออกไปก็เลยลองเปลี่ยนเป็น ```return;``` เฉย ๆ แล้วก็ ได้! 

โถ่ ... เสียเวลามาตั้งนาน



