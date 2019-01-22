---
layout: post
title: Angular's Directive ES6 Era
date: 2015-11-10 14:18:00.000000000 +07:00
categories: dev
tags: angular
---
ปกติ directive ที่เขียนอยู่ใน AngularJS official documentation จะแนะนำให้เราเขียนแบบนี้

```
var app = angular.module('app', [])
app.directive('directiveName', 
  function () {
    return {
      restrict: 'E',
      // isolate scope
      scope: {},
      controller: function () {
        ...
      },
      controllerAs: 'loginBox',
      link: function ($scope, element, attrs) {
        ...
      },
      template: '<div>template here</div>',
    };
  });
```

แต่นั่นก็คือวิธีการเขียนแบบ es5 ซึ่งตอนนี้ยุคของ es6 กำลังมาถึง ก็อาจจะถึงเวลาที่เราจะปรับตัวเองไปสู่สิ่งที่ดีกว่า แน่นอนว่า es6 จะช่วยให้เราเขียนได้งดงามขึ้น และสมเหตุสมผลมากขึ้น โดยอาศัย `class` เข้าช่วย

```
import _ from 'lodash';
import angular from 'angular';

class SomeDirective {
  constructor() {
    _.extend(this, {
      restrict: 'E',
      // create its isolate scope that will not interfere with
      // the outside world
      // scope is equivalent to `this` in the class
      scope: {},
      // always use bindToController
      // so that the code will work as expected
      bindToController: true,
      // this is var's name to be used in template
      // to talk about controller's `this` or scope
      controllerAs: 'ctrl',
      template: loginBoxTemplate,
    });
  }

  controller() {
    ...
  }

  link($scope, element, attr) {
    ...
  }
}

let app = angular.module('app', []);
app.directive('directiveName', () => new SomeDirective());
```

โดยภาพรวมแล้วโค้ดอันใหม่ทำงานเหมือนโค้ดอันเก่าทุกประการ แต่ว่าตอนนี้โค้ดของ directive ทั้งก้อนรวมเป็นก้อนเดียวกันแล้ว ในรูปที่เป็นระเบียบมากขึ้น คือ `class` 

ที่ต้อง new SomeDirective() ด้านล่างก็เพราะว่า directive คาดหวังว่าจะได้ function ที่จะถูกรันด้วย การ execution ไม่ใช่การ `new` ดังนั้นก็ต้อง `new` ให้เสียก่อน
