---
layout: post
title: Angular's Directive Wrapper (ES6)
date: 2015-12-03 14:16:00.000000000 +07:00
categories: dev
tags: angular
---
ความเดิมตอนที่แล้วที่ผมแนะนำวิธีการเขียน Angular Directive ด้วย ES6 นั้น ... ผมพบว่าตัวผมเอง ผิดถนัด และตอนนี้ผมได้เตรียมตัวมาแก้ตัว เอาเป็นว่าเป็นความพยายามครั้งที่สองของผมในการทำให้การใช้งาน directive มีเหตุผลมากขึ้น อ่านง่ายขึ้น เขียนง่ายขึ้น จัดการง่ายขึ้นแล้วกันนะครับ

โค้ดหลักอยู่ที่ https://github.com/phizaz/angular-directive-wrapper

วิธีการของผมก็คือผมสร้างไฟล์ directive.js ขึ้นมาเพื่อเป็น Singleton สำหรับจัดการงานเกี่ยวกับ สร้าง directive (โดยเฉพาะ isolated scope ซึ่งเป็นเป้าหมายหลักของการใช้งาน; ใครใช้ shared-scope โปรดเลือกเขียนตามใจชอบ ๕๕๕) เพราะว่าโดยปกติ directive ประกอบไปด้วย controller และ link ซึ่งไม่ได้ทำงานพร้อมกัน และไม่ได้มีเป้ามหายเหมือนกันซะทีเดียว แต่ส่วนตัวแล้วผมเห็นว่ามันเป็นการยากโดยใช่เหตุที่จะต้องแยกสองสิ่งเหล่านี้ออกจากกันโดยสิ้นเชิง และผมยังเห็นอีกว่าเมื่อ directive ของเราใหญ่ขึ้น ๆ การจัดการให้โค้ดต่าง ๆ อยู่ในร่องในรอยก็ทำได้ยากขึ้น จึงเสนอวิธีจัดการโค้ดออกมาจากใน directive.js นี้ด้วย

โดยผมจะสร้างตัวแปร private พิเศษ ไว้สำหรับเชื่อม ตัวแปร this ของ controller และ this ของ link อันที่จริงแล้วผมเชื่อมมันทุกอย่างด้วยตัวแปร this เลย ทำให้ทุก ๆ ที่สามารถเรียกของจากที่ ๆ หนึ่งได้เลย หากว่ามันมีอยู่

ผมว่าพูดไปก็คงเข้าใจได้ยาก ดูตัวอย่างเลยดีกว่ามาหน้าตา wrapper นี้เป็นอย่างไร แล้วมันจะช่วยเราได้จริง ๆ หรือไม่ ผมว่ามันรอให้ทุก ๆ คนตัดสินอยู่

## ตัวอย่างการใช้งาน

```js
import angular from 'angular';
//importing directive.js
import Directive from './directive';

angular
  .module('TestDirectiveModule', [])
  .directive('Test',
    () => {

      return Directive.new({

        controllerAs: 'my',
        template: '<test></test>',

        // this replaces the normal 'scope'
        // I personally think 'interfaces' is a better word
        interfaces: {
          // public is a special interface that let outsider to peek inside into props and methods freely!
          // like...
          // <test name="abc"></test>
          // $scope.abc.x => 0
          public: '=name',
        },

        // all the properties are here!
        props: {
          x: 0,
          y: 0,
          // element and attrs (from link) are automatically included here as well
          // be advised: link is invoked after the controller, then element and attrs
          // will not be available at the starter() and watcher() time
        },

        // all the $watch code should all be put here!
        // you shall write the watch destroyer here as well
        watcher($scope) {
          $scope.$watch('my.x', (x) => {
            console.log('x has changed! to:', x);
          });
        },

        // this block of code will be invoked first!
        // starter -> watcher; link is invoked normally according to angular's directive
        // if you like to use the scope, it is provided.
        starter($scope) {
          this.moveDiagonal();
        },

        // just the conventional link
        link($scope, element, attrs) {
          element.fadeIn(200);
        },

        // list all the methods
        methods: {

          moveDiagonal() {
            this.x += 10;
            this.y += 10;
          }

        }

      });

    });
```
