---
layout: post
title: Using ES6 Promises in Angular Apps
date: 2015-09-18 16:21:00.000000000 +07:00
categories: dev
tags: js angular
---
ที่จริงคอนเซ็ปต์เรื่อง promise ไม่ใช่เรื่องใหม่ ([อ่านเพิ่มเติม](http://www.2ality.com/2014/09/es6-promises-foundations.html)เรื่อง promise) และ Angular เองก็ได้รับเอาคอนเซ็ปต์นี้เข้ามาเป็นส่วนหนึ่งของ framework ซึ่งเราจะเรียกใช้งานได้จากการ inject $q (วิธีการใช้ [$q](https://docs.angularjs.org/api/ng/service/$q)) โดยผมพบด้วยตัวผมเองว่าการใช้ promise ทำให้ผมสามารถทำงานกับ Angular ได้อย่างมีความสุขขึ้นอย่างมาก โค้ดที่เขียนออกมานั้นอ่านและเข้าใจได้ง่ายกว่าเดิมมาก จากเดิมต้องเขียนด้วยการใช้ callback ซึ่งก็เข้าใจยากด้วยตัวเองอยู่แล้ว ยังยากต่อการอ่านให้รู้เรื่องด้วยเนื่องจากการเขียน callback ไม่ได้ถูกออกแบบมาให้อ่านด้วยมนุษย์ได้ง่าย ๆ

แต่สำหรับคนที่ไม่เคยได้ยินคำว่า promise มาก่อน ผมจะอธิบยไว้ตรงนี้เสียหน่อยว่ามันก็เหมือนกับ "สัญญา" ดังความหมายของมันนั่นแหละ มันคือสิ่งที่จะมี "ค่า" ในอนาคต แม้ว่ายังไม่ใช่ตอนนี้ ลักษณะการใช้งานทั่ว ๆ ไปที่เราจะเห็นได้ก็เช่น 

```
let a = some promise;
a.then(
	function afterA(promisedValue) {
		... do something here ...
	});
```

จากตัวอย่างนี้ ... ฟังก์ชัน afterA จะถูกเรียกเมื่อ "สัญญา" ได้กลายเป็นความจริงแล้ว (ไม่เกิดขั้นทันที) และจะถูกส่งค่าที่ได้สัญญาเอาไว้ใส่ไปใน promisedValue ด้วย ถ้าดูตรงนี้แค่นี้ก็อาจจะคิดว่ามันก็ไม่ได้ต่างกับ callback หนิ แค่มีตัวอักษร .then ซึ่งก็จริง เพราะว่า .then เองก็ยังต้องใช้ callback เลย แต่เชื่อหรือไม่ว่าแค่มี .then ก็ทำให้อ่านโค้ดได้ง่ายขึ้นเป็นโขแล้ว

อีกหนึ่งตัวอย่างที่แสดงว่า promise มันน่าจะดีกว่า callback แน่ ๆ ก็เช่น

```
let a = some promise;
a
.then(
	function afterA(promisedValue) {
		... do something here ...
		let b = some promise;
		return b;
	})
.then(
	function afterB(promisedValue) {
		... do something else here ...
	});
```

ตอนนี้เรามีสองฟังก์ชัยคือ afterA กับ afterB โดย afterA จะถูกเรียกก่อน ก็คือตอนที่ "สัญญา" ถูกทำให้เป็นจริงแล้ว และถ้า afterA ยัง return เป็น **"สัญญา"** ใหม่ เราก็จะสามารถ **"เชื่อม"** (chain) สำหรับ .then ตัวถัดไปได้ อย่างในตัวอย่างด้านบน afterA ทำงานหลังจาก **สัญญา a** เสร็จสิ้น และ afterB ก็จะทำงานเมื่อ **สัญญาที่ return มาจาก afterA นั่นคือสัญญา b** ทำงานเสร็จสิ้นนั่นเอง 

จะเห็นว่า afterB จะทำงานหลังจาก afterA ซึ่งหากเราต้องการให้ มีการทำงานของทั้งสัญญา a และ b พร้อม ๆ แบบไม่ต้องรอกัน แต่เราต้องการดูเพียงว่า ทั้งสองทำงานเสร็จแล้วจึงเรียกอะไรบางอย่างมาทำงาน เราจะเขียนได้แบบนี้

```
let a = some promise;
let b = some other promise;
Promise
.all([a, b])
.then(
	function afterAB(values) {
		let promisedValueFromA = values[0];
		let promisedValueFromB = values[1];
		... do something after a, b are finished ...
	});
```

การเชื่อมโดยใช้ .then() ต่อ ๆ กันไปเรื่อย ๆ ก็น่าสนใจดี แต่ว่าบางครั้ง เราต้องการเชื่อมอย่างมีเงื่อนไข  เช่นว่าถ้าในเงื่อนไขแบบหนึ่งเราก็จะเชื่อมแบบหนึ่ง หากภายใต้เงื่อนไขอีกแบบหนึ่งเราอจจะเชื่อมอีกแบบ สามารถเขียนได้ดังนี้

```
let a = some proimes;
let promise = a;

promise = promise
	.then(
		function first() {
			... do something ...
			return some promise;
		});
		
if (condition) {
	promise = promise
		.then(
			function conditionIsTrue() {
				... do something ...
				return some promise;
			});
} else {
	promise = promise
		.then(
			function conditionIsFalse() {
				... do something ...
				return some promise;
			});
}

promise
.then(
	function last() {
		... do something ...
	});
```

สิ่งที่เราเพิ่มขึ้นมาเพื่อการแยกการเชื่อม ก็คือการยัดค่าใส่ตัวแปร ชื่อว่า `promise` ตัวแปรนี้ช่วยให้เราสามารถเชื่อม promise ต่อจากจุดเก่าได้ ดังตัวอย่างด้านบน first() จะถูกเรียกเสมอและเป็นอันดับแรก ต่อมาด้วย conditionIsTrue() ถ้า condition เป็นจริง และ conditionIsFalse() ถ้า condition ไม่เป็นจริง แล้วจบด้วย last() เสมอ

จบแล้วเรื่อง promise ตอนนี้ทุกคนน่าจะรู้จักวิธีการใช้งานของมันแล้ว ต่อไปผมจะเริ่มพูดถึงวิธีการใช้ promise กับ Angular ของเรา ซึ่งส่วนสำคัญที่สุดคือเราจะพยายาม **"ใช้งานข้อมูลเหมือนตัวแปรที่อยู่ local ให้มากที่สุด"** คือเราพยายาามใส่ abstraction บางอย่างเพื่อให้เราสามารถเรียกใช้งานตัวแปรได้ราวกับว่ามันก็คือตัวแปรธรรมดา ๆ ตัวนึง วิธีนี้จะทำให้โค้ดอ่านง่ายกว่าการเรียกเพื่อเอาค่า (resolve) ตัวแปร ณ​ เวลาที่ต้องใช้ได้อย่างมาก 

หมายเหตุ: ตัวอย่างต่อไปนี้ผมจะใช้ร่วมกับ Angular UI Router และใช้การแนวทางการเขียนแบบ [gocardless](https://github.com/gocardless/angularjs-style-guide)
สมมติว่าเราต้องการสร้าง controller สำหรับหน้า dashboard ซึ่งจะแสดงข้อมูลส่วนตัวของผู้ใช้ไว้ในหน้านี้ และผูกไว้กับ `url: /dashboard`

หลักการคือเราต้องการใช้ดึกข้อมูลผู้ใช้มา และนำเอาไปใช้ใน controller ราวกับว่าเป็นตัวแปรธรรมดา ๆ ตัวหนึ่ง เราสามารถทำได้ด้วยการใช้งาน `resolve` ของ angular ui router ซึ่งคาดหวังว่าเราจะ return เป็น promise อยู่แล้ว ดังต่อไปนี้

**ไฟล์ : dashboard.route.js**
เราจะระบุการ routing ของ ส่วนนี้ไว้ที่นี่ ประกอบกับข้อมูลที่จำเป็นต้องได้ก่อนที่จะเข้าสู่ state นั้น ๆ ได้

```
import angular from `angular';
import 'angular-ui-router';

import {backendServiceModule} from './backend.service';
import {dashboardControllerModule} from './dashboard.controller';

let partial = angular.module('dashboardRouteModule', 
	[
		'ui.router',
		backendServiceModule.name,
		dashboardControllerModule.name,
	]);

partial.config(
	($stateProvider) => {
		$stateProvider
			.state({
				url: '/dashboard',
				controller: 'dashboardController',
				resolve: {
					userInfo: (Backend) => {
						return Backend.getUserInfo();
					}
				}
			});
	});

export var dashboardRouteModule = partial;
```

**ไฟล์ : backend.service.js**
ซึ่งเราจะเขียน abstraction สำหรับการเรียก request ไปยัง server ไว้ที่นี่

```
import angular from 'angular';

class Backend {
	constructor($http) {
		this.$http = $http;
	}
	
	getUserInfo() {
		return this.$http.get('...get user info...');
	}
}

let partial = angular.module('backendServiceModule', []);

partial.service('Backend', Backend);

export var backendServiceModule = partial;
```

method getUserInfo() นั้น return เป็น promise เนื่องจาก $http.get() ของ Angular return เป็น promise อยู่แล้ว

**ไฟล์ : dashboard.controller.js**
ซึ่งเราจะเขียน controller และใช้งานข้อมูลของผู้ใช้ที่เราได้ดึงมาจาก backend ที่นี่

```
import angular from 'angular';

let partial = angular.module('dashboardControllerModule', []);

partial.controller('dashboardController', 
	(userInfo) => {
		... now we can use userInfo like we have it locally ...
	});
	
export var dashboardControllerModule = partial;
```

ตอนนี้เราสามารถใช้ข้อมูลจาก backend ราวกับว่ามันอยู่ที่ local ของ frontend ผ่านตัวแปรชื่อ userInfo โดยที่ controller ไม่ต้องรู้เลยว่าตัวแปรนี้มาได้ยังไง รู้แค่ว่ามันมีความหมายว่าอะไรก็พอ (แต่เรารู้ชัดว่ามันมาจากไหนเนื่องจากเรารู้ว่ามันไม่ใช่ serverices แสดงว่ามันมาจาก resolve ของ route แน่ ๆ ตรงนี้ทำให้เราแก้ปัญหาได้ง่ายหากเกิดอะไรขึ้น)
