---
layout: post
title: Kotlin Coroutines
---
# Kotlin Coroutines Concept
อ้างอิงตาม Google Codelabs Android Kotlin Fundamental Course [Coroutines and Room](https://codelabs.developers.google.com/codelabs/kotlin-android-training-coroutines-and-room/index.html?index=..%2F..android-kotlin-fundamentals#4) 

Coroutines เป็น วิธีการเขียนโปรแกรมในภาษาคอทลินรูปแบบหนึ่ง ที่นิยมใช้ในการเขียน Task ที่ทำงานยาว ๆ มีวิธีจัดการกับการเขียนโปรแกรมทำงานยาว ๆ หลายอย่าง เช่น Callback Function ที่เคยเขียนไว้ใน LiveData และ Coroutines เป็นอีกวิธีหนึ่งที่ได้ประสิทธิภาพดี จะเปลี่ยนรูปแบบของ Code ที่เป็น Callback ให้มาเป็น Sequential Code  

## คุณสมบัติของ Kotlin Coroutines
- Asynchronous โดยโปรแกรมใน Coroutines จะรันอิสระจากโปรแกรมหลัก ไม่ว่าจะใช้หลักการรันขนานกันไป (Parallel) หรือ จะแยกออกไปบนแต่ละคอร์ของซีพียูก็ได้ ส่วนใดที่ต้องรอการตอบกลับก็รอไป ส่วนใดที่ไม่ได้รอก็ทำงานต่อไป 
- Non-Blocking จะไม่มีการหยุด Main Thred เพื่อให้การทำงานเป็นไปได้อย่างต่อเนื่อง 
- ใช้ `suspend` เพื่อที่จะให้โค้ดที่รันแบบ Asynchronous เป็นบบ Sequencial 

โดยจะใส่คำว่า `suspend` ไว้ข้างหน้าชื่อของฟังก์ชัน เมื่อ Coroutines เรียกใช้ฟังก์ชันที่มี `suspend` นำหน้าแล้วนั้น Coroutines จะไม่ block โปรแกรม โปรแกรมยังคงเดินต่อ จน suspend function นั้นได้รับข้อมูลและ พร้อมที่จะทำงาน Coroutine ก็จะกลับไปทำงาน จากจุดเดิมของฟังก์ชันนั้นที่มันหยุดเอาไว้ *If the thread is suspended, other

## 3 สิ่งที่ควรรู้จักใน Kotlin Coroutines
- **Job** Job ก็คืออะไรก็ตามที่สามารถ Cancle ได้ มันก็คืองาน ทุก Coroutines มี Job โดย Job สามารถอยู่แบบปกติ หรือ เป็นขั้นเป็นตอนลงมาได้ ดังนั้นถ้าเรา Cancle Parent Job แล้ว Jobs ที่เป็น Children ของ Parent Job นั้น จะถูก Cancle ไปด้วย
- **Dispatcher** Dispatch แปลว่าส่ง Dispatcher ใน Coroutines คือการส่ง Coroutines ไปทำงานในเทรดต่าง ๆ ที่หลากหลาย เช่น `Dispatcher.Main` คือ การรัน Task ใน Main Thread ส่วน `Dispatcher.IO` คือเอา IO ที่มันเป็น blocking มาแชร์งานกัน ในหลาย ๆ thread
- **Scope** คือ สถานการณ์ (Context) ไหนที่จะให้ Coroutine รันอยู่ โดย Scope จะบอกว่า ตรงนี้นั้น Job และ Dispatcher อะไรบ้าง  Scope นี้ก็จะเก็บกระบวนการ การดำเนินไปของ Coroutine 