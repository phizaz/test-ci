---
layout: post
title: Git Automatic Deployment
date: 2015-03-28 11:10:00.000000000 +07:00
categories: dev
tags: devops
---
ผมเพิ่งให้ความสนใจกับการ deploy ไม่นานมานี้ เนื่องจากเร่ิมรู้สึกว่าน่าจะมีวิธีดีกว่าที่เราทำอยู่ แต่ก่อนที่ FTP ลาก ๆ วาง ๆ ว่าเจ๋งแล้ว ก็ได้รู้จักกับ Git ที่ก็แค่กด push แล้วก็ไปกด pull ที่เครื่อง server แต่ดูเหมือนว่าที่จริงมันก็มีวิธีที่ง่ายกว่านี้ **ก็จริงที่ว่าทำไมเราต้องไปกด pull เองที่ server ด้วยหล่ะ ?** ทำไมเราไม่ให้มัน pull ให้เราเอง หรือไม่ **เราก็ push โดยตรงไปที่ server เครื่องนั้นเลย โดยไม่ต้องผ่านบริการ third-party เช่น github.com หรือ bitbucket**

หลังจาก google อยู่ซักพักผมก็ได้คำตอบจากโพสของ [DigitalOcean](https://www.digitalocean.com/community/tutorials/how-to-set-up-automatic-deployment-with-git-with-a-vps) วิธีการที่เค้าเสนอคือทำให้ server เครื่องที่เราใช้เป็น remote repository ไปเลย หากเราต้องการ deploy ขึ้นไป ก็โยนไปที่นี่เลย ไฟล์ก็จะไปอยู่ในที่ ๆ เรากำหนดใน server เราเองอย่างง่ายดาย (เนื่องจากผมใช้บริการ VPS อยู่แล้ว)

**หมายเหตุ** ผมไม่ได้เข้าใจอย่างถ่องแท้ ก็แค่พิมพ์ ๆ ตามเค้าแล้วก็พยายามหาคำอธิบายเท่าที่ความรู้ตัวเองพึงจะอธิบายได้ 

###เริ่มด้วยการเตรียม remote repository บน VPS
ที่จริงแล้วเราสามารถสร้าง remote repository ด้วยตัวเองได้ไม่ต้องพึ่ง github หรือ bitbucket แต่อย่างใด และวิธีการก็ไม่ได้ยากไปกว่าการสร้าง directory ใหม่เท่านั้นเอง แต่ไม่ได้แปลว่าเราจะโยน repository เก่าเราทิ้งไปนะ เราใช้ควบคู่กันไปได้

สมมติว่าผมต้องการสร้าง git repository ไว้ที่ */var/repo/phizaz-blog.git*   
เพื่อจะได้เรียกได้จาก *ssh://user@domain/var/repo/phizaz-blog.git*
และผมต้องการเก็บไฟล์ที่ push มาไว้ที่ *var/www/html*

#### สร้าง repository
1. `cd /var`  
2. `mkdir repo`  
การเรียกอาจจะต้องพึ่ง `sudo` เนื่องจาก */var* มักเป็น directory ของ root หากเป็นเช่นนั้นเรายังต้อง `sudo chown username repo` ด้วยเพื่อให้เราสามารถแตะต้อง directory repo ได้อย่างสะดวก
3. `mkdir phizaz-blog.git && cd phizaz-blog.git`  
ครั้งนี้ไม่จำเป็นต้อง sudo แล้ว
4. `git init --bare`  
คำสั่งนี้คือการเสกให้ directory นี้เป็น git repository โดย --bare บอกว่า directory นี้จะไม่เก็บส่วนที่เป็น work tree จะเก็บเฉพาะส่วน version control เท่านั้น (แปลว่าเราจะโยนไฟล์ไปเก็บไว้ที่ directory ที่เราต้องการภายนอกได้ ในที่นี้คือ *var/www/html*)

#### ตั้งค่าให้ work tree เป็น directory ภายนอกที่ต้องการ
1. `cd hooks`
2. `cat > post-receive`  
ไฟล์ post-receive ใน directory *hooks* จะบอกว่าหลังจากได้รับไฟล์จากการ push (หลังจากถูก push) เข้ามาที่ repository นี้จะต้องทำคำสั่งใดบ้าง (คำสั่งต่าง ๆ ถูกเขียนในไฟล์นี้)
3. `#!/bin/sh`
`git --work-tree=/var/www/html --git-dir=/var/repo/phizaz-blog.git checkout -f`  
เมื่อเสร็จแล้วกด `ctrl + d` เพื่อส่งสัญญาณสิ้นสุดการ input
4. `chmod +x post-receive`  
เพื่อให้ script ที่เราเขียนไว้สามารถรันได้

เท่านี้ remote repository ของเราก็พร้อมสำหรับการใช้งานแล้ว

#### เพิ่ม repository นี้เข้าไปใน project ของเรา
สมมติว่าเรามี local git repository อยู่ที่ project ของเราอยู่แล้ว ซึ่งอาจจะมี remote อื่นอยู่แล้วก็ดี (เช่นผูกกับ github.com อยู่แล้ว) หรือไม่ผูกก็ได้ ใช้คำสั่งต่อไปนี้ที่ root directory ของ repository  
`git remote add live ssh://user@domain/var/repo/phizaz-blog.git`

ถ้าและหากเราใช้โปรแกรมพวก sourcetree อยู่แล้วก็จะเห็นว่าในแถบ *remote* จะมีอันใหม่ชื่อว่า *live* หากเรา push commit ไปยัง remote นี้สิ่งที่เรา push ก็จะไปโผล่ที่ */var/www/html* ยังเครื่อง server ดังที่เราได้กำหนดไว้

วิธีการนี้ผมใช้ในการ deploy บล็อก ๆ นี้ขึ้นไปยัง server อยู่ในปัจจุบัน
