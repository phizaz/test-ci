---
layout: post
title: Off-policy Importance Sampling
categories: academic
tags: rl thai
---
ขั้นตอนในการเรียนรู้ policy ที่ดีใน reinforcement learning นั้น มักจะประกอบไปด้วยสองส่วน หนึ่ง คือส่วนที่เรียกว่า **prediction** ก็คือส่วนที่ตอบว่า "ถ้าเราเดินไปตามเส้นทางนี้ แล้วจะดีขนาดไหน" นั่นก็คือ "คาดการณ์" (prediction) ค่าของ $$v_\pi$$ และหรือ $$q_\pi​$$ 

อีกส่วนหนึ่งก็คือส่วนที่เรียกว่า **control** ถ้าเรามีข้อมูลเหล่านี้ ($$v_\pi, q_\pi$$) หรือไม่มี จะสามารถหา policy ที่ดีได้อย่างไร ในกรณีของ [GPI (Generalized Policy Iteration)](http://www.incompleteideas.net/book/ebook/node46.html) นั้น control (การหา policy ที่ดี) นั้นอาศัยการ prediction ที่ดีมาก่อนด้วย นั่นทำให้ในหลาย ๆ ครั้งเราถือว่า prediction กับ control เหมือนกับสองส่วนที่ขาดจากกันไม่ได้

ในที่นี้เราพูดถึงโจทย์ prediction เป็นหลัก โดยเฉพาะเราพูดถึงการ predict ค่า $$q_\pi$$ โดยที่เราไม่มีประสบการณ์จากการเล่น policy $$\pi$$ เลย  แต่เราได้ประสบการณ์จากาแหล่งอื่น ๆ แทน เราเรียกแหล่งนั้นว่า **behavioral policy** หรือ $$b$$ 

**จะเป็นไปได้ไหม หรือต้องทำอย่างไรเราถึงจะได้ $$v_\pi$$ ในเมื่อเรามีแต่ประสบการณ์จาก $$b​$$?** 

โจทย์นี้มีชื่ออย่างเป็นทางการว่า **Off-policy prediction** ก็เพราะว่าประสบการณ์ที่เรามีมัน off ไปจาก policy $$\pi$$

ก่อนที่จะไปต่อ กล่าวก่อนว่า off-policy prediction นั้นก็ไม่ได้มีสูตรสำเร็จ แต่ละวิธีก็อาจจะมีจุดแข็งจุดอ่อนของตัวเอง (เหมือนกับทุกอย่างใน RL) แต่เนื่องจาก off-policy prediction นั้นยากกว่า on-policy prediction (กรณีที่ไม่มี $$b​$$) มาก ดังนั้นงานวิจัยในด้านนี้ก็ยังไม่เจริญเท่า 

เพราะฉะนั้นในบทความนี้เราก็อาจจะพูดแบบเกริ่น ๆ วิธีที่มีมานานแล้วของการทำ off-policy prediction ไปก่อนซึ่งในที่นี้เราจะเสนอไอเดียที่เรียกว่า Importance Sampling ซึ่งเป็นเทคนิคทางสถิติ ซึ่งเข้าใจได้ง่ายแม้จะไม่ได้เข้าใจ RL มากเท่าไหร่ 

## On-policy prediction 

ในกรณีที่ $$\pi$$ คือ $$b$$ กล่าวคือ $$\pi(a \vert s) = b(a \vert s)$$ สำหรับทุก ​$$a, s$$ เราจะได้ว่า เราสามารถแก้โจทย์ prediction ด้วยวิธีการแบบ Monte Carlo นั่นก็คือ

$$
\mathrm{E}_{r_{t+1}, r_{t+2}, ... \sim \pi} \left[ r_{t+1} + r_{t+2} + ... \right] = v_\pi
$$

แปลเป็นภาษาไทยก็คือ เราสามารถหาค่า $$v_\pi$$ ได้ (prediction) ด้วยการใช้ประสบการณ์จำนวนมากมาหาค่า "คาดหวัง" โดยมีเงื่อนไขว่าประสบการณ์จะต้องมาจาก policy $$\pi$$ เท่านั้น 

ข้อสังเกต: เราทำการ sample $$r$$ หลายครั้งจนกว่าจะจบ episode

เพื่อความง่ายเราจะกำหนด $$G_t = r_{t+1} + r_{t+2} + ...​$$ โดยเราเรียก $$G_t​$$ ว่า return อาจจะแปลได้ว่า ค่าทดลองการเล่น (จากการเล่น 1 ครั้งจนจบ แล้วรวมรางวัลที่ได้ทั้งหมด)

โจทย์ในที่นี้สำหรับ off-policy prediction ก็น่าจะเป็น ถ้าเราไม่ได้ $$r_{t+1}, r_{t+2}, ... \sim \pi​$$ แต่มาจาก $$b​$$ แทน เป็นไปได้มั้ยที่เราจะยังประมาณค่า $$v_\pi​$$ ได้เหมือนเดิม

## Off-policy prediction โดยใช้ Importance Sampling

Importance Sampling เป็นวิธีทางสถิติ โดยสามารถแสดงให้เห็นแบบง่าย ๆ ได้ดังนี้ 

สมมติว่าเรามีฟังก์ชัน $$f(x)​$$ เรามีฟังก์ชันความน่าจะเป็น $$p(x)​$$ และเราต้องการหา "ค่าคาดหวังของ $$f(x)​$$ ภายใต้ $$p(x)​$$" เราจะได้ว่า

$$
\mathrm{E}_{x \sim p(x)} f(x)
$$

แต่ว่าถ้าเราไม่ได้ sample $$x​$$ จาก $$p(x)​$$ แต่เป็น $$g(x)​$$ แทนล่ะ ? เราก็ยังหาค่าคาดหวังได้อยู่ดีแหละดังนี้

$$
\begin{equation}
\begin{split}
\mathrm{E}_{x \sim p(x)} f(x) &= \sum_x p(x) f(x) \\
&= \sum_x \frac{g(x)}{g(x)} p(x) f(x) \\
&= \sum_x g(x) \frac{p(x)}{g(x)} f(x) \\
&= \mathrm{E}_{x \sim g(x)} \frac{p(x)}{g(x)} f(x) 
\end{split}
\end{equation}
$$

การคูณ $$\frac{g(x)}{g(x)}​$$ เป็นทริกที่ไม่ได้เปลี่ยนค่าแต่ใด เพราะว่าคูณด้วย $$1​$$ แต่ว่าช่วยให้เราสามารถเปลี่ยนการ sample ได้ แทนที่จะต้อง sample จาก $$p(x)​$$ กลายเป็น sample จาก $$g(x)​$$

เราจะได้ว่า หากทุกครั้งที่เรา sample $$x \sim g(x)$$ แล้วแทนที่จะใช้ค่า $$f(x)$$ ตรง ๆ เลยเราเอาค่านั้นมาคูณด้วย $$\frac{p(x)}{g(x)}$$ (เรียกว่า **Importance sampling ratio**) ก่อน นั่นคือ $$\frac{p(x)}{g(x)} f(x)$$ เราก็จะได้ค่าคาดหวังอันเดิมได้นั่นเอง แต่นั่นก็แปลว่าเราต้องรู้ด้วยว่า $$g(x)$$ ของเรานั้นมีค่าเท่าไหร่

### การใช้งานกับ Reinforcement Learning

เราจะเริ่มจากกรณีของ on-policy prediction ซึ่งก็คือการ sample $$r$$ จำนวนมากจาก $$\pi$$ คราวนี้เราต้องเปลี่ยนเป็น sample จาก $$b$$ แทน

แต่มันอาจจะง่ายกว่าถ้าเราลองมองแค่ reward เดียวก่อน

การจะ sample reward แต่ละอันสิ่งที่เราต้องทำก็คือ 

1. ทำการ sample action จาก policy $$\pi(a \vert s)​$$ 
2. ทำการ sample reward จาก reward distribution $$p(r \vert s, a)​$$
3. ทำการ sample state ต่อไป (ถ้าต้อง sample reward ต่อไป) จาก state transition probability $$p(s' \vert s, a)​$$

สำหรับ reward แรกในที่นี้ $$r_{t+1}$$ (สมมติว่าเราเริ่มที่ $$t$$) จะได้ว่า

$$
r_{t+1} \sim \pi(a_t|s_t) p(r_{t+1}|s_t, a_t)
$$

สำหรับ reward ต่อไป $$r_{t+2}$$ ก็จะได้

$$
r_{t+2} \sim \pi(a_t|s_t) p(s_{t+1}|s_t, a_t) \pi(a_{t+1}|s_{t+1}) p(r_{t+2}|s_{t+1}, a_{t+1})
$$

หากเราไม่ได้ sample action จาก $$\pi$$ แต่เป็นจาก $$b$$ แทน เราจะเขียนใหม่ได้ว่า

$$
r_{t+1} \sim b(a_t|s_t) p(r_{t+1}|s_t, a_t)
$$

และ 

$$
r_{t+2} \sim b(a_t|s_t) p(s_{t+1}|s_t, a_t) b(a_{t+1}|s_{t+1}) p(r_{t+2}|s_{t+1}, a_{t+1})
$$

ถ้าเรามองเฉพาะ reward แรก $$r_{t+1}$$ แล้วลองเปรียบเทียบกับ กรณี $$f(x), p(x), g(x)$$ เราจะเห็นว่า:

- $$\pi(a_t \vert s_t) p(r_{t+1} \vert s_t, a_t)$$ ราวกับเป็น $$p(x)$$ แต่เดิม และ 
- $$b(a_t \vert s_t) p(r_{t+1} \vert s_t, a_t)$$ ก็คือ $$g(x)​$$ อันใหม่
- $$r_{t+1}$$ ก็คือ $$f(x)$$ ของเรานั่นเอง 

เมื่อเห็นฉะนี้เราก็สามาถรเขียนได้ว่า Importance sampling ratio ระหว่าง $$\pi$$ กับ $$b$$ สำหรับ reward $$r_{t+1}$$ ก็คือ

$$
\begin{equation}
\begin{split}
\rho_{t:t} &= \frac{\pi(a_t|s_t) \cancel{p(r_{t+1}|s_t, a_t)}}{b(a_t|s_t) \cancel{p(r_{t+1}|s_t, a_t)}} \\
&= \frac{\pi(a_t|s_t)}{b(a_t|s_t)}
\end{split}
\end{equation}
$$

ใช้อักษร $$\rho$$ (โรห์) แทน importance sampling ratio 

ข้อสังเกต: จะเห็นว่าถ้า $$b(a_t \vert s_t)$$ มีค่าน้อยมาก ๆ อาจจะทำให้ค่า $$\rho​$$ ระเบิดได้ดังนั้น behavioral policy จะต้องไม่ "คม" เกินไป  กล่าวคือจะต้องให้โอกาสเลือก action ต่าง ๆ ไม่น้อยเกินไปนั่นเอง

และถ้าเราคิดต่อไปสำหรับกรณี reward ตัวที่สองเราก็จะได้ว่า Importance sampling ratio ระหว่าง $$\pi$$ กับ $$b$$ สำหรับ $$r_{t+2}$$ ก็คือ 

$$
\begin{equation}
\begin{split}
\rho_{t:t+1} &= \frac{\pi(a_t|s_t) \cancel{p(s_{t+1}|s_t, a_t)} \pi(a_{t+1}|s_{t+1}) \cancel{p(r_{t+2}|s_{t+1}, a_{t+1})}}{b(a_t|s_t) \cancel{p(s_{t+1}|s_t, a_t)} b(a_{t+1}|s_{t+1}) \cancel{p(r_{t+2}|s_{t+1}, a_{t+1})}} \\
&= \frac{\pi(a_t|s_t)\pi(a_{t+1}|s_{t+1})}{b(a_t|s_t)b(a_{t+1}|s_{t+1})}
\end{split}
\end{equation}
$$

$$\rho_{t:t+1}$$ บอกถึงว่าเรา "คูณกันจาก t ถึง t+1" 

จะเห็นว่าแม้ในตอนคูณกันนั้นจะมีส่วนของ state transition probability และ reward probability สุดท้ายแล้วก็ํจะตัดกันเองอยู่ดี เพราะว่าไม่ว่าจะเป็น $$\pi$$ หรือ $$b​$$ ก็ล้วนอยู่ภายใต้ environment (MDP) เดียวกัน ทำเราให้เราสนใจเฉพาะความต่างของทั้งสองก็พอ

สำหรับกรณี reward อื่น ๆ นั้นเราสามารถเดาไปจากตรงนี้ว่า สำหรับ $$r_T$$ จะมี importance sampling ratio ของตัวเองเท่ากับ

$$
\begin{equation}
\begin{split}
\rho_{t:T-1} 
&= \prod_{k=t}^{T-1} \frac{\pi(a_k|s_k)}{b(a_k|s_k)}
\end{split}
\end{equation}
$$

เอาทุกอย่างมารวมกัน เราจะได้ว่าสำหรับแต่ต้นนั้นในกรณีของ on-policy เรามี

$$
\mathrm{E}_{r_{t+1}, r_{t+2}, \dots \sim \pi} \left[ r_{t+1} + r_{t+2} + \dots \right] = v_\pi
$$

ในกรณีของ off-policy เราจะต้องคูณ $$\rho$$ ของแต่ละ reward ให้ถูกต้อง หน้าแต่ละ reward เพื่อทำให้ได้ค่าเหมือนเดิม

$$
\mathrm{E}_{r_{t+1}, r_{t+2}, \dots \sim b} \left[ \rho_{t:t}r_{t+1} + \rho_{t:t+1}r_{t+2} + \dots \right] = v_\pi
$$

จะเห็นว่าเราต้องใช้ แต่ละ $$\rho$$ สำหรับแต่ละ reward วิธีการนี้จึงมีชื่อว่า **Per-decision Importance Sampling** ก็เพราะว่าเราสร้าง importance sampling สำหรับแต่ละ decision (แต่ละ action แต่ละ reward) เลย 

ในการ Implement จริง ๆ เราจะต้องเขียนได้ว่า $$v(s)$$ นั้นหามาจากไหน เราจะเขียนได้ว่า

$$
\begin{equation}
v(s) = \frac{\sum_{t \in \tau(s)} \sum_{k=t}^{T-1} \rho_{t:k} r_{t+1}}{|\tau(s)|}
\end{equation}
$$

อาจจะดูเข้าใจยากซักนิด แต่หากแยกเป็นส่วน ๆ จะเห็นว่า

- $$\sum_{k=t}^{T-1} \rho_{t:k} r_{t+1}​$$ ตรงนี้จริง ๆ แล้วก็คือ ผลรวมของ reward ที่ผ่านการคูณด้วย importance sampling ratio แล้ว
- ส่วนที่เหลือก็คือการหาค่า "เฉลี่ย" จากหลาย ๆ ครั้งนั่นเอง (เพราะว่าค่าคาดหวังก็คือค่าเฉลี่ย)

การหาค่าเฉลี่ยในที่นี้ใช้เครื่องหมาย $$\tau(s)​$$ ช่วย โดยเรากำหนดขึ้นมาที่นี้ว่า $$\tau(s)​$$ เป็น set ของทุก ๆ timestep ที่ state "ผ่าน" state $$s​$$ พอดิบพอดี ดังนั้นการหาค่าเฉลี่ยของทุก ๆ ครั้งที่เราวิ่งผ่าน $$s​$$ ก็คือการหา "ค่าเฉลี่ยของผลรวม reward ทุก ๆ ครั้งที่เริ่มจาก $$s​$$" นั่นเอง

เพิ่มเติม: การกำหนด $$\tau(s)$$ ว่าเป็น set ของทุก ๆ timestep ที่ผ่าน state $$s$$ เรียกว่า **every-visit Monte Carlo** อีกวิธีหนึ่งที่เราทำได้ ก็คือ "สนใจเฉพาะ $$s$$ แรกของแต่ละ episode เท่านั้น" เราก็จะแก้ความหมาย $$\tau(s)$$ เล็กน้อย แล้วเรียกว่า **first-visit Monte Carlo** ความต่างของทั้งสองก็คือ every-visit นั้น implement ง่ายกว่า เพราะเราไม่ต้องจำว่าเราผ่าน state นี้ครั้งแรกหรือเปล่า แต่ว่าอาจจะให้ค่าที่แปลก ๆ เพราะหากเราผ่าน state $$s$$ หลายครั้งในหนึ่ง episode ค่า $$v(s)​$$ จะเป็นค่าเฉลี่ยจากทุกครั้งที่เราผ่าน ต่างกับกรณี first-visit ที่จะสนใจเฉพาะค่าแรกเท่านั้น

### Importance Sampling สำหรับผลรวมทั้งเส้น

แต่ว่าก็มีอีกวิธีที่เรามอง "ทุก reward เป็นภาพรวม" กล่าวคือเราไม่แยกมองเป็นทีละชิ้น แต่เรามองทั้งหมดเป็น return $$G_t​$$ เลย

กล่าวคือ 

$$
G_t = r_{t+1} + r_{t+2} + r_{t+3} + \dots \quad \sim \pi
$$

โดยเราจะหาความน่าจะเป็นที่จะ sample ได้ค่า $$r_{t+1}, r_{t+2}, \dots$$ พร้อมกันทั้งหมดแทน ซึ่งก็สามารถทำได้ในลักษณะเดียวกัน โดยตรงนี้จะแสดงให้เห็นกรณี reward 2 ตัวแรก

$$
r_{t+1}, r_{t+2} \sim \pi(a_t|s_t) p(r_{t+1}|s_t, a_t) p(s_{t+1}|s_t, a_t) \pi(a_{t+1}|s_{t+1}) p(r_{t+2}|s_{t+1}, a_{t+1})
$$

หลังจากนั้นเราสามารถเขียนสำหรับกรณีทั่วไปได้ดังนี้

$$
r_{t+1}, r_{t+2}, \dots, r_{T} \sim \prod_{k=t}^{T-1} \pi(a_k|s_k) p(r_{k+1}|s_k, a_k) p(s_{k+1}|s_k, a_k)
\label{eq:pG}
$$

จากสมการ $$\eqref{eq:pG}$$ เราสามารถหาค่า Importance sampling ratio ระหว่าง $$\pi$$ กับ $$b$$ สำหรับ $$G_t$$ ได้ดังต่อไปนี้

$$
\begin{equation}
\begin{split}
\rho_{t:T-1} &= \prod_{k=t}^{T-1} \frac{\pi(a_k|s_k) \cancel{p(r_{k+1}|s_k, a_k)} \cancel{p(s_{k+1}|s_k, a_k)}}{b(a_k|s_k) \cancel{p(r_{k+1}|s_k, a_k)} \cancel{p(s_{k+1}|s_k, a_k)}} \\
&= \prod_{k=t}^{T-1} \frac{\pi(a_k|s_k)}{b(a_k|s_k)}
\end{split}
\end{equation}
$$

จะเห็นว่าหน้าตาคล้ายเดิมอย่างยิ่ง เพียงแต่ ณ ตอนนี้เราสามารถใช้ ค่า $$\rho_{t:T-1}$$ คูณเข้าไปยัง $$G_t$$ ทั้งเส้น เพื่อให้ได้ค่าที่ถูกต้อง แทนที่จะต้องใช้ค่า $$\rho$$ สำหรับแต่ละ reward

$$
\begin{equation}
\mathrm{E}_{r_{t+1}, r_{t+2}, \dots \sim b} \left[ \rho_{t:T-1} \sum_t^{T-1} r_{t+1} \right] = v_\pi
\end{equation}
$$

วิธีนี้เรียกว่า **importance sampling** แบบปกติ (ทั้งเส้น)

เราสามารถเขียนนิยามของ $$v(s)$$ ได้ในลักษณะเดียวกัน ก็คือการเฉลี่ยจากหลาย ๆ ครั้งที่เราผ่าน state $$s$$ นั้น ๆ 

$$
\begin{equation}
v(s) = \frac{\sum_{t \in \tau(s)} \rho_{t:T-1} G_t}{|\tau(s)|}
\label{eq:ord_is}
\end{equation}
$$

### เปรียบเทียบ Importance Sampling และ Per-decision Importance Sampling

ปัญหาของ Importance sampling ในภาพรวมก็คือการคูณทบ ๆ กัน ของ $$\frac{\pi}{b}​$$ ซึ่งมันจะทำให้ค่าที่ได้มีการแกว่งมาก ๆ ก็เพราะว่ามันขึ้นอยู่กับการ sampling ต่อเนื่องเป็นระยะยาว และเอาแต่ละพจน์มาคูณกัน ยิ่งส่งผลให้ช่วงของค่ามากขึ้นไปอีก ทำให้ importance sampling มีปัญหากับขนาดของ variance มีการกล่าวในหนังสือของ (Sutton, 2018) ว่า variance ของ importance sampling นั้นอาจจะถึง **อนันต์** ซึ่งก็เห็นด้วยได้ง่ายเพราะว่าความยาวของ episode นั้นอาจจะยาวเท่าใดก็ได้ 

เพราะฉะนั้นสำหรับงานใดที่ต้องการใช้ importance sampling จำต้องพิเคราะห์ถึงการจำกัด variance ให้ดี โดยวิธีที่จำกัด variance ได้มาก มักก็จะส่งผลให้มีผลดีในการใช้งานจริงด้วย 

เป็นที่ทราบกันว่าหากใช้ importance sampling (แบบทั้งเส้น) โดยตรงนั้น การเทรนจะทำได้ยากอย่างยิ่ง และอาจจะไม่ converge เลยด้วยซ้ำ แต่ว่า per-decision importance sampling ซึ่ง แม้ว่าจะมีพจน์ $$\rho_{t:T-1}$$ เช่นกัน แต่ว่าก็ส่งผลเพียงต่อ reward ท้าย ๆ ซึ่งก็อาจจะถูกพลังของ discount $$\gamma$$ ลดความสำคัญลงไปเยอะ ก็จะช่วยทำให้ variance ลดลงได้

อย่างไรก็ดีในการใช้งานจริงนิยมใช้วิธีที่ "ใกล้เคียง" แต่ไม่ใช่ทั้ง importance sampling หรือ per-decision importance sampling ซึ่งเรียกว่า **weighted importance sampling** ซึ่งวิธีนี้นั้นจริง ๆ แล้ว "ไม่ให้ค่าที่ถูกต้อง" (มี bias) แต่ว่าสามารถควบคุม variance ได้เป็นอย่างดีจึงทำให้การใช้งานจริงนั้นให้ผลที่ดีกว่ามาก

### Weighted Importance Sampling

การที่เราคูณ $$\prod \frac{\pi}{b}$$ หลาย ๆ ครั้งส่งผลให้ค่า $$\rho$$ นี้ แกว่งมาก ๆ และก็แกว่งมากขึ้นเรื่อย ๆ ตามจำนวนการคูณ วิธีหนึ่งที่จะช่วย "จำกัด" การแกว่งก็คือการ "หาร" ด้วยอะไรที่เยอะพอ ๆ กัน 

เราทำการแก้ไขเล็กน้อยจากสมการ $$\eqref{eq:ord_is}$$ โดยการแก้ตัวส่วนให้มีค่าแกว่งไปพร้อม ๆ กับตัวเศษ

$$
v(s) = \frac{\sum_{t \in \tau(s)} \rho_{t:T-1} G_t}{\sum_{t \in \tau(s)} \rho_{t:T-1}}
\label{eq:weighted_is}
$$

สิ่งที่เราเห็นในทันทีก็คือ $$v(s)$$ ใหม่นี้จะแกว่งน้อยกว่ามากส่งผลให้ variance น้อยลงตามไปด้วย วิธีนี้จึงเหมาะสมมากกว่าในการใช้งานจริง

สิ่งที่เราเห็นต่อมาก็คือ อยู่ดี ๆ เราจะแก้ตามอำเภอใจแบบนี้ไม่ได้สิ คำตอบแบบสั้น ๆ ก็คือ เรายอม "ผิด" เพราะว่าสมการ $$\eqref{eq:weighted_is}$$ นั้น bias ไม่ได้ให้ค่าที่ถูกต้องโดยเฉลี่ยเหมือนกับ importance sampling ทั่วไป แต่เราก็ยอมจ่ายเพราะว่ามันช่วยให้เราทำงานกับมันได้ง่ายมากขึ้น

คำถามต่อมาก็คือ แล้วมัน bias ไปขนาดไหนล่ะ? เพราะว่าถ้ามัน bias แบบไม่เห็นเค้าเดิมเลยมันก็ไม่น่าจะดีอยู่แล้ว จริงอย่างว่า เพราะว่า สมการ $$\eqref{eq:weighted_is}​$$ นั้นมี bias ก็จริง แต่ว่าขนาดของ bias นั้น "น้อยลง" เรื่อย ๆ หากเราเฉลี่ยด้วยจำนวนที่มากขึ้น และมัน "เข้าใกล้" ค่าจริงเมื่อเราเฉลี่ยด้วยจำนวนอนันต์ แต่แน่นอนว่าเราไม่ได้เฉลี่ยด้วยจำนวนอนันต์จึงต้องยอมรับว่า มันก็ยัง bias อยู่ดี 

การแสดงว่ามันเข้าใกล้ค่าจริง เมื่อเฉลี่ยด้วยจำนวนอนันต์สามารถทำได้ดังนี้

- เราอาศัยความจริงที่ว่า $$\mathrm{E}[\rho] = 1​$$ 
- และเราจะแสดงว่า weighted importance sampling นั้นเข้าใกล้ importance sampling เมื่อ $$\tau(s)$$ มีขนาดอนันต์

$$
\begin{equation}
\frac{\sum_{t \in \tau(s)} \rho_{t:T-1}}{\left| \tau(s) \right|} = \mathrm{E}_b \left[ \rho_{t:T-1} \right] = 1
\label{eq:expect_rho_tau}
\end{equation}
$$

สมการ $$\eqref{eq:expect_rho_tau}$$ จะเป็นจริงก็ด้วย [Law of large numbers](http://www.incompleteideas.net/book/ebook/node46.html) เท่านั้น ดังนั้นหาก $$\left \vert  \tau(s) \right \vert $$ ไม่ได้เยอะเข้าใกล้ $$\infty$$ แล้วก็พูดอย่างนั้นไม่ได้ 

หลังจากนั้นเราก็ทำการย้ายข้างเล็กน้อยดังนี้

$$
\begin{equation} 
\sum_{t \in \tau(s)} \rho_{t:T-1} = \left| \tau(s) \right| \mathrm{E}_b \left[ \rho_{t:T-1} \right] = \left| \tau(s) \right|
\end{equation}
$$

นำค่าที่ได้ไปแทนในสมการ $$\eqref{eq:weighted_is}$$ จะได้ว่า

$$
\begin{equation}
\begin{split}
v(s) &= \frac{\sum_{t \in \tau(s)} \rho_{t:T-1} G_t}{\sum_{t \in \tau(s)} \rho_{t:T-1}} \\
&= \frac{\sum_{t \in \tau(s)} \rho_{t:T-1} G_t}{\left|\tau(s)\right|} \\ 
\end{split}
\end{equation}
$$

เราจะเห็นว่าจริง ๆ แล้ว weighted importance sampling กับ importance sampling ธรรมดานั้นมีค่าเท่ากันเมื่อ $$\vert \tau(s) \vert$$ เป็นอนันต์ (ใหญ่พอ)

หมายเหตุ: การจะทำแบบเดียวกันนี้กับ per-decision importance sampling นั้นไม่ตรงไปตรงมาเท่าใดนักก็เพราะว่า $$\rho​$$ ของแต่ละ reward ไม่เหมือนกัน ทำให้พูดได้ยากว่าอะไรคือ weight ที่เหมาะสมกันแน่ อย่างไรก็ดีมีงานที่เสนอการทำ weighted per-decision importance sampling ชื่อว่า [Eligibility Traces for Off-Policy Policy Evaluation (2000)](https://scholarworks.umass.edu/cgi/viewcontent.cgi?article=1079&context=cs_faculty_pubs)

#### แสดงว่า $$\mathrm{E}[\rho] = 1$$

เพื่อให้เห็นภาพจะแสดงให้ดูในกรณีของ $$r_{t+1}, r_{t+2}​$$ อย่างละเอียดเพื่อให้เห็นภาพชัดเจน

$$
\rho_{t:t+1} = \frac{\pi(a_t|s_t)\pi(a_{t+1}|s_{t+1})}{b(a_t|s_t)b(a_{t+1}|s_{t+1})}
$$

ลองหาค่าคาดหวังของ $$\rho$$ ภายใต้ behavioral policy $$b$$

$$
\begin{equation}
\begin{split}
\mathrm{E}_{a_t, a_{t+1} \sim b} \left[ \frac{\pi(a_t|s_t)\pi(a_{t+1}|s_{t+1})}{b(a_t|s_t)b(a_{t+1}|s_{t+1})} \right]
&= \sum_{a_t} b(a_t|s_t) \sum_{a_{t+1}} b(a_{t+1}|s_{t+1}) \frac{\pi(a_t|s_t)\pi(a_{t+1}|s_{t+1})}{b(a_t|s_t)b(a_{t+1}|s_{t+1})} \\
&= \sum_{a_t} \cancel{b(a_t|s_t)} \frac{\pi(a_t|s_t)}{\cancel{b(a_t|s_t)}} 
\sum_{a_{t+1}} \cancel{b(a_{t+1}|s_{t+1})} \frac{\pi(a_{t+1}|s_{t+1})}{\cancel{b(a_{t+1}|s_{t+1})}} \\
&= \sum_{a_t} \pi(a_t|s_t) \sum_{a_{t+1}} \pi(a_{t+1}|s_{t+1}) \\
&= \sum_{a_t} \pi(a_t|s_t) 1 \\
&=  1
\end{split}
\end{equation}
$$

จะเห็นว่าจริง ๆ แล้วเนื่องจากระหว่าง $$a_t$$ กับ $$a_{t+1}$$ นั้นไม่เกี่ยวข้องกัน (เมื่อกำหนด $$s$$ ให้) ดังนั้นจึงราวกับว่าแต่ละ term ที่คูณกันใน $$\rho$$ สามารถแยกกันคิดได้ ซึ่งก็ส่งผลให้ค่าคาดหวังทั้งหมดกลายเป็น 1 เพราะว่าแต่ละส่วนเป็น 1 นั่นเอง 

### การใช้งานกับ n-step TD

โดยปกติ TD หรือ Temporal Difference จะใช้ตัวอย่าง reward เพียง 1 ตัวอย่างเพื่ออัพเดทค่าประมาณ $$v$$ หรือ $$q$$ เป็นที่ทราบกันดีว่า TD นั้นอยู่ฝั่งตรงข้ามกับ Monte Carlo ในมุมของ variance และ bias กล่าวคือ 

- TD มี bias มาก มี variance น้อย
- Monte Carlo ไม่มี bias แต่มี variance มาก 

n-step TD คือความพยายามหา "จุดกึ่งกลาง" ระหว่าง 2 วิธีการนี้ แทนที่จะใช้ reward เพียงอันเดียวแบบ TD ดังนี้

$$
G_{t:t+1} = r_{t+1} + \gamma v(s_{t+1})
$$

เราจะใช้ reward หลาย ๆ ตัว ยกตัวอย่างเช่น 2 ตัว ดังต่อไปนี้

$$
G_{t:t+2} = r_{t+1} + \gamma r_{t+2} + \gamma^2 v(s_{t+2})
$$

เราก็สามารถเดาได้ว่า $$G_{t:t+n}$$ จะมีหน้าตาเป็นอย่างไร

n-step TD คือการใช้ $$G_{t:t+n}$$ มาแทนที่ของ $$G_{t:t+1}$$ เราจะได้ว่าหน้าตาของ **n-step SARSA** เป็นดังนี้

$$
q(s_t,a_t) \leftarrow q(s_t, a_t) + \alpha \left[ G_{t:t+n} - q(s_t, a_t) \right]
$$

จะเห็นว่า $$G_{t:t+n}$$ ต้องการ $$v$$ แต่ในกรณีที่เรามีเฉพาะค่า $$q$$ เราจะสามารถหาค่า $$G_{t:t+n}$$ ได้ดังนี้

$$
G_{t:t+n} = \left( \sum_{i=0}^{n-1} \gamma^i r_{t+i+1} \right) + \gamma^n \mathrm{E}_{a \sim \pi} \left[ q(s_{t+n}, a) \right]
$$

หากเราใช้การ sampling สำหรับประมาณ $$v$$ ดังด้านบน เราจะเรียกว่า SARSA เฉย ๆ แต่ว่าถ้าเราใช้การหาค่าเฉลี่ยแบบเป๊ะ ๆ ดังต่อไปนี้

$$
G_{t:t+n} = \left( \sum_{i=0}^{n-1} \gamma^i r_{t+i+1} \right) + \gamma^n \sum_a \pi(a|s_{t+n}) q(s_{t+n}, a)
$$

เราเรียกวิธีการนี้ว่า **n-step expected SARSA** 

ซึ่งเวลาที่เรามาใช้กับ off-policy importance sampling สิ่งที่เราต้องสนใจก็คือ "สำหรับแต่ละ term มีการ sampling action หรือเปล่า?" เพราะว่าเราต้องคูณ $$\rho$$ ทุกที่ที่มีการ sampling action

จะเห็นว่าในกรณีของ n-step SARSA เรามีการสุ่ม n-1 ครั้งสำหรับ n reward แรก ที่เป็น n-1 ก็เพราะว่า SARSA เป็นฟังก์ชัน $$q(s,a)$$ ดังนั้น action แรกไม่ได้เกิดจากการสุ่ม และรวมกับอีก 1 ครั้งตอนสุ่มหาค่า $$v$$ 

จึงได้ว่าสำหรับ n-step SARSA เราจะเขียนแบบ off-policy ได้ดังนี้

$$
G_{t:t+n} = \left( \sum_{i=0}^{n-1} \rho_{t:t+i} \gamma^i r_{t+i+1} \right) + \rho_{t:t+n} \gamma^n \mathrm{E}_{a \sim \pi} \left[ q(s_{t+n}, a) \right]
$$

สำหรับ n-step expected SARSA เรามีการ n-1 ครั้งสำหรับ n reward แรก เหมือนกัน แต่ว่าเราไม่ได้สุ่มอีกเลยตอนหาค่า v เพราะเราหาค่าแบบเป๊ะ ๆ จึงได้ว่าสำหรับ n-step expected SARSA เราสามารถเขียน off-policy ได้ดังนี้

$$
G_{t:t+n} = \left( \sum_{i=0}^{n-1} \rho_{t:t+i} \gamma^i r_{t+i+1} \right) + \rho_{t:t+n-1} \gamma^n \sum_a \pi(a|s_{t+n}) q(s_{t+n}, a)
$$

หมายเหตุ: เราสามารถใช้ได้ทั้ง importance sampling หรือ per-decision importance sampling หรือ weighted importance sampling เพียงแค่แก้ค่า $$G_{t:t+n}$$ ให้เหมาะสม

### การใช้งานกับ Experience Replay

สำหรับอัลกอริทึมที่ใช้ข้อมูลแบบ off-policy เรามักจะใช้งาน experience replay เนื่องจากเราสามารถใช้ขอมูลเก่า ๆ ได้ (ต่างจาก on-policy ที่ต้องการประสบการณ์สดใหม่เท่านั้น จึงไม่นิยมใช้ experience replay) 

หมายเหตุ: กรณีของ DQN และ Q-learning โดยทั่วไปไม่ต้องทำการคูณด้วย importance sampling ratio เนื่องจากสมการของ Q-learning ไม่ได้สนใจว่าประสบการณ์นั้นมาจาก policy ใด จึงเรียกว่า Q-learning เป็น off-policy โดยกำเนิด แต่นี่ไม่เป็นจริงสำหรับการใช้ n-step Q-learning (และ n-step algorithm ในภาพรวม)

เนื่องจากการใช้งาน importance sampling เราจำเป็นต้องรู้ว่า policy ที่ใช้เก็บข้อมูลนั้นมีหน้าตาเป็นอย่างไร กล่าวคือ $$b(a \vert s)$$ มีค่าเท่าไหร่ เพราะว่าต้องเอาไปเป็นตัวหาร ดังนั้น นอกจากจะต้องเก็บข้อมูลอย่าง state, action, reward ใน experience replay แล้ว ก็ยังจะต้องเก็บด้วยว่า ณ ตอนที่เก็บข้อมูลนี้นั้น $$b(a \vert s)$$ มีค่าเท่าไหร่

# อ้างอิง

Sutton, R. S., & Barto, A. G. (2018). Reinforcement learning: An introduction. MIT press. https://doi.org/10.1016/S1364-6613(99)01331-5

Hernandez-Garcia, J. F., & Sutton, R. S. (2019). Understanding Multi-Step Deep Reinforcement Learning: A Systematic Study of the DQN Target. Retrieved from http://arxiv.org/abs/1901.07510

Amherst, S., Precup, D., Sutton, R. S., & Singh, S. (2000). Eligibility Traces for Off-Policy Policy Evaluation (Vol. 80).