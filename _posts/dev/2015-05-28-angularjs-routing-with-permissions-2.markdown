---
layout: post
title: AngularJS Routing with Permissions
date: 2015-05-28 11:18:00.000000000 +07:00
categories: dev
tags: [angular, web]
---
ไม่ว่าจะเขียนเว็บด้วยเทคนิคอะไรก็ตาม สิ่งที่เราต้องทำก็ไม่พ้นระบบ Routing และยิ่งมากกว่านั้นคือจำกัดว่าใครสามารถเข้าไปชมหน้าไหนได้บ้าง (authorization)

ปัญหานี้ไม่ใช่เรื่องใหญ่เลยเมื่อเราเขียนเว็บไซต์ที่จัดการเรื่องนี้จากทาง backend กล่าวคือหากเราใช้ PHP Framework เช่น Laravel เราก็สามารถจัดการเรื่องนี้ใน *routes.php* โดยอาจจะเขียน *filter* กำกับไว้ (ไม่ว่าจะเขียนไว้ที่ใดก็ตาม) ด้วยว่าใครสามารถเข้า route นี้ได้บ้าง ถ้าไม่ได้จะต้องทำอย่างไรต่อไป เราก็แค่เขียนฟังก์ชันกำกับไว้เท่านั้นเอง

แต่เมื่อเราเสกสรรค์เว็บไซต์ด้วยเทคโนโลยีทาง frontend อย่าง AnuglarJS แล้วปัญหานี้มันนุ่มลึกกว่านั้นอยู่พอสมควร **ระลึกไว้เสมอว่าเรามาใช้ AngularJS เพื่อต้องการการตอบสนองอย่างทันที ราบรื่น สวยงาม** 

### Basic approach
ผมจัดการ routing ใน AngularJS ด้วย angular-ui-router ซึ่งก็เป็น pagkage ที่หลายคนแนะนำว่าดีมีความสามารถหลากหลายกว่าตัวหลักของ AngularJS เอง ผมอาจจะเร่ิมเขียน หน้า *routes.coffee* ดังนี้

```
angular.module 'routes', []
.config [
  '$stateProvider'
  (stateProvider) ->
  
    stateProvider
    .state 'user', {
      url: '^/user'
      templateUrl: "public/user.html"
      controller: 'userCtrl'
    }
]
```
ตอนนี้ผมได้กฎการ route โดยกำหนด state *user* ขึ้นมาโดยผูก state นี้กับ url */user* (การเขียน ^ นำหน้าทำให้เป็น absolute url ไม่ขึ้นกับว่า state นี้เป็นรองจาก state ไหน)


ผมอาจจะมีไฟล์ *user.coffee* ไว้เขียนรายละเอียดของ *userCtrl* ด้วย โดยผมอยากให้ *userCtlr* เป็น controller เกี่ยวกับหน้าส่วนตัวของ user แต่ละคนในระบบ ดังนั้นหน้านี้จะต้องเป็นหน้าที่เฉพาะคนที่ login แล้วเท่านั้นจึงเข้าได้ หากเค้ายังไม่ก็จะถูกเตะออกไปที่อื่นเช่นหน้าแรก 

โดยสมมติว่าผมมี userService ไว้สำหรับทำงานเช่น เช็คว่าผู้ใช้ login อยู่หรือเปล่า หรืองานอื่น ๆ 

```
angular.module 'user', []
.controller [
  '$scope'
  '$state'
  'userService'
  ( s,
    state,
    userService) ->

    if not userService.isLogin()
      # user has not logged in yet
      # redirect him to homepage
      state.go '/'
    else
      ... do something interesting here ...
]

```

ถ้าผมไม่ได้เขียนผิดอะไร แค่นี้เราก็สามารถทำงานได้แล้ว เพราะว่าคนที่จะทำงานในส่วน else ได้ก็คือคนที่ login เรียบร้อยแล้วเท่านั้น 

**แต่...** จะเห็นในส่วนของ module *routes* ที่เราเขียนไว้ก่อนหน้า state แต่ละ state จะเรียกสองส่วนพร้อม ๆ กันคือส่วนของ template และส่วนของ controller แปลว่าหาก template ถูกวางลงไปที่หน้า browser ของผู้ใช้แล้วจึงมีการเรียก *state.go()* จะส่งผลให้เกิดการกระพริบของหน้าจอแน่นอนเหมือน **เข้าไปได้ครึ่งตัวแล้วถูกเตะออกมา** ไม่รู้ว่าคุณจะรู้สึกอย่างไร แต่ผมรู้สึกแย่... ผมจึงต้องหาวิธีแก้ปัญหานี้

### The better one
หากเราต้องการจะเช็คและไม่ต้องการให้เกิดการกระพริบแน่ ๆ เราต้องเช็คก่อนหน้านั้น อันที่จริงผมเลือก **เช็คตั้งแต่เร่ิมมีการเปลี่ยน url เลย** วิธีการนี้ทำได้ด้วยการ ฟัง message *$stateChangeStart* ที่ *rootScope* ซึ่ง angular-ui-router จะส่งมาเมื่อ**เร่ิม**มีการเปลี่ยน state ดังนั้นหากเราจัดการตั้งแต่ตอนนี้ ก็จะยังไม่มีการโหลด template ใด ๆ

ผมเขียนโค้ดส่วนนี้ไว้ที่ไฟล์หลัก สมมติเป็น *app.coffee* ในส่วน *.run()* แต่มันก็จะมีปัญหาตามมาอยู่ที่ว่าในเมื่อเราเขียนครอบไว้หมด เราจะรู้ได้อย่างไรว่าหน้าที่กำลังจะไปเนี่ย **ใครเข้าได้บ้าง?**  

ดังนั้นผมจึงต้องระบุข้อมูลบางอย่างเพิ่มเติมในแต่ละ route ว่า "ใครเข้าได้บ้าง?" ผมเห็นคนแก้ปัญหานี้ด้วยการเลียนแบบ mode ในระบบปฎิบัติการพวก unix และ unix-like

#### เพิ่มไฟล์ constant.coffee
```
angular.module 'constant', []

.constant 'USER_ROLES',
  all: parseInt '111', 2
  guest: parseInt '001', 2
  member: parseInt '010', 2
  admin: parseInt '100', 2

```
ไฟล์นี้จะกำหนดว่า 001 คือ ใครก็เข้าได้ (guest) หรือ 010 คือ member เข้าได้ แต่ว่าถ้าเป็น 011 หรือ guest | member ก็แสดงว่าเข้าได้ทั้งคู่


#### แก้ routes.coffee
เราต้องการให้หน้า user เข้าได้แค่ member (ในที่นี้ admin ก็อาจจะเข้าได้ แต่ว่าไม่ใช่ประเด็นอะไร)

```
angular.module 'routes', []
.config [
  '$stateProvider'
  (stateProvider) ->
  
    stateProvider
    .state 'user', {
      url: '^/user'
      templateUrl: "public/user.html"
      controller: 'userCtrl'

      data: {
        roles: [
        { authorized: USER_ROLES.member | USER_ROLES.admin
          no: 'login'
          yes: null }
        ]
      }
    }
]
```
ตอนนี้เราเพิ่มข้อมูลเล็กน้อยคือส่วน data ซึ่งจะมี roles อยู่ด้านใน (ซึ่ง roles เป็น array) ผมตั้งใจเอาไว้ว่า   
1. **authorized** คือ รายการของผู้ที่เราสนใจ (ไม่ได้แปลว่าเราต้อนรับ)  
2. **no** ระบุว่าหาก ไม่ใช่คนเหล่านี้แล้วเราจะ **เตะ** เขาไปที่ไหน (ในที่นี้เราเตะคนที่ยังไม่ login ไป login)  
3. **yes** ระบุว่าหาก ใช่คนเหล่านี้แล้วเราจะ **เตะ** เขาไปที่ไหน ซึ่งผมตั้งไว้เป็น null แปลว่าไม่เตะ อันที่จริง yes กับ no ก็ไม่ควรมีค่าในเวลาเดียวกันไม่งั้นหน้านี้ก็ไม่น่าจะมีคนเข้าได้

เนื่องจาก roles เป็น array เราก็จะเช็คทีละสมาชิกของ roles หากเช็คแล้วไม่มีการ **เตะ** เกิดขึ้นก็จะไปเช็ค roles ตัวต่อไปเรื่อย ๆ จนกว่าจะครบ คนที่เข้าหน้านี้ได้ก็คือที่ไม่ถูกเตะเลย

ที่เหลือก็แค่เขียนโค้ดเพื่อบังคับใช้กฎเหล่านี้

#### เขียนส่วนบังคับใช้กฎ ในหน้า app.coffee
ปกติผมใช้ app.coffee เป็นหน้าเร่ิมต้นของ project ซึ่งก็จะมี injector ต่าง ๆ มากมายในหน้านี้ แต่ตอนนี้เราจะสนใจแค่ส่วน *.run()* เท่านั้น ซึ่งจะเป็นส่วนที่เราจะเขียนตัวฟัง event *$stateChangeStart* 

ผมสมมติว่ามี method *userService.getRoles()* ซึ่งจะบอกว่าผู้ใช้เป็นใคร เช่น 001 คือเป็นคนธรรมดา หรือ 010 เป็น member ที่ login แล้ว

ผมจะพยายามเขียน comment ให้ละเอียดเพื่อจะได้อ่านแล้วเข้าใจได้ง่ายขึ้น

```
angular.module 'app', [ ... many injectors ...]
.run [
  '$rootScope'
  'userService'
  'USER_ROLES'
  '$state'
  ( rootScope,
    userService,
    USER_ROLES,
    state ) ->

    # this var is needed to determine whether we should check 
    # for the user's role or not
    intendedState = null
    rootScope.$on '$stateChangeStart', (event, next, current) ->
      # the target route hasn't been specified its mode
      # so, anyone can get in
      if not next.data? or not next.data.roles?
        # update intendedState
        intendedState = next.name
        return

      # the user is there!
      # because intendedState is where he's now going to
      # no permission checking this time (we've checked him last time)
      if next.name is intendedState
        # update intendedState
        intendedState = next.name
        return

      # since, authorization takes time!
      # stop the page from normal redirection
      # because, we have to authorize the user first
      event.preventDefault()

      # get the user's role
      # we use callback function since `getRole` is not always fast
      userService.getRole (myRole) ->
        # get the target page's mode
        roles = next.data.roles

        redirection = {}

        # check user's role with each page's restriction
        for each in roles
          if (each.authorized & myRole) isnt 0
            # the user is authorized
            if each.yes?
              # follow the YES seciton, if exists
              redirection =
                target: each.yes
              break
          else
            # the user isn't authorized
            if each.no?
              # follow the NO section, if exists
              redirection =
                target: each.no
              break

        if not redirection.target?
          # the user is good, no need to kick him
          # redirect user to where he intended to go
          # according to 'next.name'
          intendedState = next.name
          # attach 'current' parameters to the redirection
          state.go next.name, current
        else
          # the user is not quailified, he has to be kicked somewhere
          # as described in 'redirection.target'
          intendedState = redirection.target
          # attach 'current' parameters to the redirection
          state.go redirection.target, current
]
```

วิธีนี้ยังมีปัญหาอยู่ที่ต้องมีการ redirect ถึง 2 ครั้ง กว่าจะเข้าไปยังหน้าที่มีการกำหนด permission ไว้ได้ เพราะว่า redirect ครั้งแรกเราก็จะตรวจสอบสิทธิ์การเข้าหน้านั้นก่อนซึ่งอาจจะต้องใช้เวลา เนื่องจาก *userService.getRoles()* อาจจะไม่เร็วในบางกรณี เมื่อตรวจสอบเสร็จแล้วก็ต้อง redirect อีกครั้งเพื่อไปยังหน้าที่ต้องการจริง ๆ ปัญหาของตรงนี้อยู่ที่ว่า

1. เราไม่มีทางทำให้ *userService.getRoles()* เร็วเสมอต้นเสมอปลายได้ (ต่อให้มีการ cache ผลลัพธ์ไว้) เพราะว่าในกรณีแย่ ๆ เราก็ต้องเรียก http request ไปยัง server อยู่ดี
2. เมื่อเราเรียก *event.preventDefault* แล้วแต่ไม่มี method สำหรับ resumeDefault ทำให้ต่อให้เราเช็คแล้วว่า user คนนี้เข้าหน้านี้ได้ แต่ก็ไม่สามารถให้เค้าเข้าไปต่อได้ (เนื่องจากเราหยุดกระบวนการไปแล้ว) เราต้องเริ่มกระบวนการใหม่อีกครั้งอยู่ดี

อย่างไรก็ดีนี่เป็นวิธีที่ผมใช้จริง ๆ และแก้ปัญหาการกระพริบได้จริง ๆ แต่ผมก็ยังหวังว่าจะมีวิธีที่ดีกว่านี้ที่จะไม่ต้อง redirect ถึง 2 ครั้ง และหากมีใครแนะนำผมก็ยินดียิ่ง :D
