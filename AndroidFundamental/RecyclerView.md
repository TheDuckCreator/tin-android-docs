---
layout: post
title: RecyclerView
---

# RecyclerView

บทความนี้อ้างอิงจาก Codelab ตาม Link นี้ [Codelab](https://codelabs.developers.google.com/codelabs/kotlin-android-training-recyclerview-fundamentals/) อาจจะมีข้อผิดพลาดในความเข้าใจ เลยสรุปมาผิด สามารถรายงานมาโดย Pull Request มาที่ Github [tin-android-docs](https://github.com/TheDuckCreator/tin-android-docs)

## เกี่ยวกับ RecyclerView

RecyclerView เป็น View หรือ พื้นที่เก็บ Element of Data อย่างหนึ่งที่พร้อมรองรับการจัดเก็บ Data ที่มีขนาดใหญ่ได้ จะขนาดเล็กก็ได้ จะมีขนาดเกินหน้าจอก็ได้ โดยระบบจะทำการประมวลผลแค่ที่มันปรากฏขึ้นบนหน้าจอ ส่วนอื่นถ้ายังไม่ถูกดึงขึ้นมาก็จะยังไม่ประมวลผล เพื่อที่จะประหยัดทรัพยากรของระบบ

สิ่งที่เราต้องมีเวลาจะทำ RecyclerView ก็คือ

1. **Layout** สำหรับเก็บ Data Element 1 Data ซึ่ง RecyclerView จะเหมือนกับเอา Layout เหล่านี้มาป้อนค่า และเรียงต่อ ๆ กัน ดังนั้นเราต้องมี Container เดี่ยว ๆ ก่อน
2. **Layout Manager** จะต้องมีสำหรับทุก RecyclerView คือเราจะต้องบอกมันใน layout file (xml file) เลยว่า Layout นี้ มีอะไรเป็น Layout Manager ตัว Layout Manager จะเป็นตัวที่ไปจัดรูปแบบ UI
3. **View Holder** extend `ViewHolder` จะทำหน้าที่ถือ View ไว้ เพื่อเตรียมที่จะ Display ผู้ใช้ เหมือนกับการเก็บสิ่งที่ผู้ใช้จะเห็น หรือ Interact ด้วยไว้ในตัวแปรชนิดหนึ่ง
4. **Adapter** เป็นตัวจัดการข้อมูลให้เรียบร้อย และเหมาะสำหรับการแสดงบน ViewHolder
