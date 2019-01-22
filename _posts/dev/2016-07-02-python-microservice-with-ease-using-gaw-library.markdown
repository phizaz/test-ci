---
layout: post
title: Python Microservice with Ease Using "Gaw" Library
date: 2016-07-02 15:20:51.000000000 +07:00
tags: python
---
การออกแบบระบบหรือโครงสร้างโปรแกรมให้เป็น "ก้อนเดียว" (เรียกว่า Monolithic system) นั้นมีข้อด้อยหลายประการ ยกตัวอย่างก็เช่น หากเราต้องการพัฒนาระบบขนาดใหญ่ ซึ่งที่จริงแล้วต้องทำงานหลาย ๆ อย่าง ...​ ซึ่งจะต้องทำให้เราพัฒนาระบบนี้ให้ตอบโจทย์หลาย ๆ ข้อ หากเราตัดสินใจพัฒนารวดเดียวให้เสร็จทั้งหมดแล้วค่อยเปิดให้ใช้บริการ ปัญหาที่จะตามมาก็คือ "ช้า" ไม่ทันการ ... หากเรารีบพัฒนาให้ออกมา ปัญหาที่จะตามมาก็คือ "บักเยอะ" เพราะว่าเป็นเรื่องปกติที่ซอฟต์แวร์หรือระบบขนาดใหญ่จะพัฒนาได้ยากกว่าระบบขนาดเล็กอยู่แล้ว

ซ้ำการออกแบบระบบซอฟต์แวร์แบบนั้นยังขัดต่อปรัญชาของระบบ Unix เสียด้วยที่ว่า 

> ทำเพียงอย่างเดียว และทำสิ่งนั้นให้ดี

หากคุณเชื่อในปรัชญานี้ คุณอาจจะสนใจการออกแบบระบบในแนวทางแบบ "ก้อนย่อย ๆ" มากกว่า ซึ่งก็เรียกแบบเป็นทางการว่า Microservices โดยหวังว่าจะแยกความสามารถที่ต้องอยู่ในระบบ ๆ เดียวออกมาเป็นส่วนย่อย ๆ และให้มันทำงานเป็นระบบแยกไปเลย ราวกับว่าเป็นคนละโปรแกรม แล้วก็ติดต่อกันผ่านช่องทางเดียวนั่นคือ "การรับ-ส่งข้อความ" (Messaging) ด้วยการส่งข้อมูลแบบนี้รับประกันว่าไม่ว่า "ก้อนย่อย ๆ" นั้นจะพัฒนาบนพื้นฐานของเทคโนโลยีใด ก็ยังสามารถคุยกันได้ผ่าน interface บางชนิดซึ่งไม่ขึ้นกับเทคโนโลยีที่ระบบนั้นถูกพัฒนาขึ้น

ในที่นี้สมมติว่าคุณสนใจแล้วนะว่าจะพัฒนาระบบเป็นแบบ "ก้อนย่อย ๆ" ผมจะเสนอวิธีตัวอย่างที่พัฒนาด้วยภาษา Python โดยอาศัยไลบรารีชื่อว่า ["กาว" (Gaw)](https://www.github.com/phizaz/gaw) ใช้เป็นตัวช่วยให้เราพัฒนาสิ่งเหล่านี้ได้โดยใช้แรงน้อยลง

## เริ่ม

สมมติว่าเราต้องการพัฒนาระบบ "บวกเลข" ซึ่งเมื่อใครก็ต้องที่ต้องการการบวกเลขจะต้องเรียกระบบนี้ขึ้นมา ... แล้วสมมติว่าการบวกเลขนี่ใช้พลังงานเยอะมาก เราก็เลยไม่อยากจะบวกเลขบ่อย ๆ โดยไม่จำเป็นเราจะสร้างระบบ "จดจำ" ผลการบวกเลขที่เคยถูกเรียกไว้ก่อนหน้าด้วย ซึ่งหากเคยบวกเลขชุดดังกล่าวมาแล้วก็จะไม่บวกอีก จะเรียกใช้จากแคชมาตอบเลย

ดังนั้นตอนนี้เราจะมี 2 ระบบย่อย ๆ ดังนี้

1. ระบบบวกเลข เรียกว่า MathService แล้วกัน
2. ระบบแคช เรียกว่า CacheService แล้วกัน

## ติดตั้งไลบรารี

สามารถติดตั้งไลบรารีด้วยการพิมพ์ `pip install gaw` ได้เลย

## Math Service

เราสามารถเขียนไฟล์ `math_service.py` ได้ดังนี้

```
from gaw import entrypoint

class MathService(object):

	name = 'math_service'

	@entrypoint
	def plus(self, a, b):
		return a + b
```

ซึ่งก็สามารถบวกเลขได้ตามปกติ ตอนนี้เราจะยังไม่ใส่ระบบแคชเข้าไปตอนนี้ แต่เราจะลองใช้ก่อนว่าเราสามารถเรียกใช้ ฟังก์ชันการบวกเลขของระบบ MathService ได้

ทดลองรัน MathService ด้วยคำสั่งผ่าน command-line ดังนี้ ซึ่งจะรัน MathService ไว้บน localhost พอร์ต 5555 โดยหากใครต้องการจะตัดต่อกับ MathService ก็ต้องติดต่อผ่าน TCP socket มาทางที่อยู่ดังกล่าว

```
$ gaw math_service --port=5555
```

แล้วเราก็จะเขียนไฟล์ `test_math_service.py` มาลองยิง request ไปถามดูว่ามันทำงานได้หรือเปล่า ?

```
from gaw import GawClient
client = GawClient('127.0.0.1', 5555)
rpc = client.math_service
print(rpc.plus(1,2))
```

ซึ่งก็จะได้ผลลัพธ์ `3` ตามที่เราคาดหวัง ถึงจุดนี้เราก็อาจจะไปต่อเองได้แล้ว แต่เพื่อจะทำให้เรื่องนี้จบอย่างสมบูรณ์ เราก็จะพัฒนาในส่วนของระบบจดจำผลการบวกด้วย

## Cache Service

เราจะเขียนไฟล์ `cache_service.py` ได้ดังนี้

```
from gaw import entrypoint

class CacheCore(object):

	def __init__(self):
		self.db = dict()

	def get(self, key):
		return self.db[key]

	def set(self, key, val):
		self.db[key] = val


class CacheService(object):

	name = 'cache_service'
	core = CacheCore()

	@entrypoint
	def get(self, key):
		print('reading from cache key:', key)
		return self.db[key]

	@entrypoint
	def set(self, key, val):
		self.core.set(key, val)

```

จะเห็นว่าเราสร้างคลาสพิเศษขึ้นมาเรียกว่า `CacheCore` และให้มันผูกกับ `CacheService` โดยให้มันเป็น attribute ที่ติดอยู่กับ `CacheService` ทุก ๆ instance ไม่ว่าจะมี `CacheService` ถูกสร้างขึ้นมากี่ instance ก็ตาม 

หาไม่ทำเช่นนี้ ทุก ๆ ครั้งที่มีการเรียก request มายัง Cache Service; Gaw จะทำการสร้าง instance ใหม่ของ class `CacheService` ขึ้นมา แล้วก็จะทำให้ฐานข้อมูลที่เราเก็บไว้ไม่แชร์กันระหว่าง request ซึ่งไม่ใช่สิ่งที่เราต้องการ

และเราก็ต้องรัน Cache Service ด้วยคำสั่งนี้

```
$ gaw cache_service --port=5556
```

และเราจะทดลอบมันด้วยไฟล์ `test_cache_service.py` ดังนี้

```
from gaw import GawClient

client = GawClient('127.0.0.1', 5556)
rpc = client.cache_service

rpc.set('a', 10)
print(rpc.get('a'))
```

ซึ่งก็จะได้ผลลัพธ์ `10` ดังที่หวังไว้ ...​ และหากเราลองเรียก `rpc.get('b')` หรืออันที่เรายังไม่ได้ `rpc.set(..)` เอาไว้ มันจะโยน Exception กลับมา ซึ่ง Exception ตัวนี้จะมี attribute `name` ซึ่งจะบอกว่ามันเคยเป็น Exception ชนิดไหนในต้นทาง เช่นในกรณีนี้ก็จะได้ `name` เป็น `KeyError` ซึ่งเกิดขึ้นเมื่อ เรียกใช้คีย์ที่ไม่มีอยู่จริง

## Math Service + Cache Service

เราจะเอาสองระบบนี้มารวมกันเพื่อให้มันเป็นระบบที่สมบูรณ์ได้ด้วยการแก้ไขโค้ดของ `math_service.py` อีกเล็กน้อย เป็นดังนี้

```
from gaw import entrypoint, GawClient, JsonSocketException

cache_service = GawClient('127.0.0.1', 5556).cache_service

class MathService(object):

	name = 'math_service'

	@entrypoint
	def plus_fresh(self, a, b):
		key = '{}|{}'.format(a, b)
		val = a + b
		cache_service.set(key, val)
		return val

	@entrypoint
	def plus(self, a, b):
		key = '{}|{}'.format(a, b)
		try:
			val = cache_service.get(key)
		except JsonSocketException as err:
			if err.name == 'KeyError':
				val = self.plus_fresh(a, b)
			else:
				raise err
		return val
```

เราทำให้ Math Service สามารถติดต่อกับ Cache Service ได้ และก็ยังเขียน method ใหม่ชือว่า `plus_fresh` ซึ่งสำหรับทำการบวกเลขแบบที่ไม่ถาม Cache Service เลย แล้วเราก็เปลี่ยนให้ method `plus` ถาม Cache Service ก่อนทุกครั้ง หากมีใน Cache แล้วก็ไม่ต้องบวกอีก หากไม่มีก็ค่อยเรียก `plus_fresh` เพื่อบวกเลข

ทดลองการบวกเลขของเราได้ที่ไฟล์ `test_math_service.py`

```
from gaw import GawClient
client = GawClient('127.0.0.1', 5555)
rpc = client.math_service
print(rpc.plus(1,2))
print(rpc.plus(1,2))
print(rpc.plus(2,1))
```

แล้วเรารันไฟล์นี้ก็จะได้ผลลัพธ์เป็น

```
3
3
3
```

อย่างที่เราคาดหวังไว้ หากเราสงสัยว่ามันใช้ Cache Service จริง ๆ หรือเปล่าเราสามารถไปดูที่หน้าที่เรารัน Cache Service ค้างไว้ว่ามี request เข้ามาหรือเปล่า ?​ ซึ่งก็จะเห็นเป็นจริงอย่างนั้นแล

## สุดท้ายนี้

ผมได้แสดงตัวอย่างการสร้าง Microservice สองตัวซึ่งทำงานด้วยกันผ่านไลบรารี "กาว" ซึ่งแม้ว่าโจทย์ของการทำจะไม่ค่อยสมจริงเท่าไหร่ เพราะว่าที่จริงแล้วราคาการบวกเลขนั้นน้อยมาก ๆ และไม่จำเป็นต้องใช้ microservice เสียด้วยซ้ำ -_-"

แต่ก็พยายามแสดงให้เห็นแบบสั้น ๆ ว่ามันทำงานอย่างไร มันประกอบเข้าไปด้วยกันได้ อย่างไรก็ดี "กาว" ไม่ได้รองรับการติดต่อผ่านช่องทางที่มีการรักษาความปลอดภัยอย่างดี ดังนั้นก็ควรใช้ในวงจำกัดไม่ควรใช้ผ่าน internet ณ ตอนนี้
