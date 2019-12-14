---
layout: post
title:  Application Structure
permalink: /structure/programstructure
---
# โครงสร้างการทำงานของแอพพลิเคชั่น Personal Health Connected
การทำงานของแอพพลิเคชั่นนี้แบ่งการทำงานเป็นสองส่วนคือส่วนที่ติดต่อกับผู้ใช้ และ ส่วนที่ทำงานเบื้องหลัง โดยเขียนบน Android Studio ด้วยภาษา Kotlin และใช้ การเขียนโปรแกรมแบบ Reactive Programing โดยไลบารี่ [Reactive X (RxJava2)](http://reactivex.io) ซึ่งเป็นการเขียนโปรแกรมแบบ Asynchronous เป็นหลัก 

## Overall Main Structure
โปรแกรมนี้เขียนโดยแบ่งสองฝั่งอย่างชัดเจน คือ ฝั่ง Frontend และ Backend โดยที่มีฐานข้อมูล (SQLite) ซึ่ง Frontend เข้าถึงได้โดยใช้ Room Database เป็นสิ่งที่ขึ้นระหว่างสองฝั่ง โดยที่ Frontend จะเป็นคนสั่งให้ตัว Backend เริ่มอ่าน จากนั้นจะรอในรูปแบบของ Asynchronous อีกส่วนหนึ่งเมื่อ Backend ได้รับจะทำการ Fetch ข้อมูล หรือ อื่น ๆ แล้วนำข้อมูลมาเก็บไว้ใน Database

## Backend Main Structure
ในส่วนของ Backend มัการใช้ Reactive X เป็นตัวหลักในการทำงาน ซึ่งผู้ที่พัฒนาควรที่จะเข้าใจพื้นฐานการทำงานของการเขียนโปรแกรมแบบ Asynchronous โดยใช้ Reactive X ประมาณหนึ่ง ซึ่งจะง่ายกว่าการเขียน Asynchronous บน Javascript หรือ บน Java, Kotlin โดยตรง เช่น รู้วิธีการเขียนฟังก์ชัน โดย Reactive X Library ถึงแม้จะเป็นภาษาต่างกัน แต่ Function Prototype มักจะคล้าย ๆ กัน เช่น `takeuntil()`, `compose` ต่าง ๆ เป็นต้น โดยปัจจุบัน ReactiveX Java (RxJava) ได้เข้าสู่ Version ที่ 3 แล้ว แต่โปรเจกต์นี้ยังไม่ได้ Migration เพื่อรองรับ Version ที่ 3 ในปัจจุบัน

## Frontend Main Structure
ในส่วนของส่วนประสานงานผู้ใช้ จะมีการใช้ Reactive X เหมือนกัน แต่จะน้อยกว่าฝั่งเบื้องหลัง โดยที่จะใช้ Dagger2 ค่อนข้างมาก ในซอร์สโค้ดเวอร์ชัน 1 และ เวอร์ชัน 2 ที่ผ่านมา ในเวอร์ชัน 3 พยายามใช้ Dagger2 ให้น้อยที่สุด เพราะผู้เขียนไม่ค่อยถนัด

## Logical Structure
ในการวางโครงสร้างของโปรแกรม มีการวางเป็น 4 Sub Module ใน 1 Module ได้แก่ App, Applogic, Util และ Data โดยที่ 
- **App**  จะเป็นตัวแอพพลิเคชั่นหลักที่จะรันอยู่บนโทรศัพท์มือถือ จะมีทั้ง Application Control และ Resource ต่าง ๆ (String Resource, Layout, Navigation) ตัว User Interface ทั้งหมดจะอยู่ในส่วนนี้ แพคเกตจะเขียนว่า `com.cnr.phr-android` รวมถึงการสร้างฐานข้อมูล และนำข้อมูลออกจากฐานข้อมูลจะอยู่ในส่วนนี้ด้วย โดยใช้ Data Access Object (DAO) ในการดึงข้อมูล 
- **Applogic** จะเก็บข้อมูลสำหรับทำการทดสอบแอพพลิเคชั่น `com.cnr.phr-android.applogic`
- **Util** จะเก็บข้อมูลเสริม หรือ ฐาน หรือ โครงสร้างต่าง ๆ ของตัวแปร ของข้อมูล และสิ่งต่าง ๆ ของแอพพลิเคชั่น `com.cnr.phr-android.base`
- **Data** จะเป็นส่วนที่ติดต่อกับอุปกรณ์ดูและสุขภาพต่าง ๆ ด้วยเทคโนโลยีบลูทูธ เพื่อที่จะให้ได้มาซึ่งข้อมูล และ นำข้อมูลเหล่านั้น เก็บไว้ในฐานข้อมูล ซึ่งส่วนนี้จะใช้ Reactive X ในการทำงานค่อนข้างมาก จะเก็บไว้ที่ `com.cnr.phr-android.data`