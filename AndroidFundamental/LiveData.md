---
layout: post
title:  LiveData 
# permalink: /structure/ui/uistructure
---
# LiveData and LiveData Observer
เรื่องนี้เป็นเรื่องที่ต่อเนื่องจาก [ViewModel](ViewModel) และนำมาจาก Google Codelabs ของ [Android Kotlin Fundamental](https://codelabs.developers.google.com/codelabs/kotlin-android-training-live-data)

LiveData คือ คลาสที่คอยสังเกตุการเปลี่ยนแปลงของข้อมูล และ ตัวมันก็แบกข้อมูลด้วย (LiveData is observable and LiveData holds data) ด้วยความสามารถนี้ LiveData จึงมี Attribute ที่ผู้เขียนโปรแกรมสามารถเอาไว้ตรวจสอบการเปลี่ยนแปลงของข้อมูลได้ 

นอกจากนั้น LiveData ยังเป็น lifecycle-aware คือ จะจัดการ อัพเดท และ แจ้งเตือน เฉพาะใน lifecycle ที่มันอยู่เท่านั้น เช่น `onCreate()`, `onStart()` หมด lifecycle ของมันก็ไม่ทำ

## MutableLiveData
`MutableLiveData` คือ LiveData ที่ค่าด้านในนั้นสามารถเปลี่ยนไปได้ เนื่องจากมันเป็นคลาสทั่วไป เราก็ต้องใส่ Type ของข้อมูลที่จะให้มันแบกเอาไว้ เช่น

        val word = MutableLiveData<String>()

ในการเซ็ทข้อมูลในตัวแปร เราจะใช้การเข้าไปที่ฟังก์ชัน `setValue()` ซึ่งในภาษา Kotlin เราสามารเขียนได้โดยใช้ `.value` แทน 

        word.value = "Hello"

หลังจากนั้น เราจะใช้ฟังก์ชัน `minus(ค่าที่จะลบออก)` และ `plus(ค่าที่จะบวกเข้า)` ในการลบ หรือ เพิ่มค่า ในบาง Record โดยจะเรียกใช้แบบ Null Safety (ใช้ `?`หรือ`!!` เข้ามาช่วย ตามกรณี ๆ ไป) โดยเราจะต้องลบออกจาก Value หรือ อยู่ในฟังก์ชัน `setValue()` เสมอ เหมือนตัวอย่างข้างล่าง เราจะไปจัดการกับค่าของมันเท่านั้น ไม่ได้จัดการกับตัวแปร เช่น

        score.value = (score.value)?.plus(1)

นอกจากนี้ MutableLiveData ยังสามารถใช้กับ Attribute อื่นได้ด้วย เช่นพวก `removeAt()` แต่จะต้องเป็นการแก้ไขที่ระดับ value โดยจะแก้ข้อมูลโดยใช้ Attribute ไหนก็ได้ของชนิดตัวแปรที่ได้ตั้งไว้

        word.value = wordList.removeAt(0)

## Observer LiveData Object
Observer คือ การเขียนฟังก์ชัน Call back ง่าย ๆ ให้กับข้อมูลที่ได้มาจาก LiveData วิธีการเขียน Observer แบบง่าย ๆ ก็คือ การ getData ออกมาจากตัวแปรที่เก็บ ViewModel ไว้ จากนั้น `.observe` ดังนี้

        viewModel.score.observe(this,Observer{
                newscore ->
        })

โดยที่ `this` ในที่นี่ เป็น Field ของ owner คือ Activity ตรงนี้ทำหน้าที่เป็น Owner ส่วน `Observer {function}` ทำหน้าที่เป็น Observer จากนั้นจะเป็นฟังก์ชันคอลแบค ซึ่งสามารถสั่งให้ตัวแปรทำอะไรก็ได้ เมื่อมีการเปลี่ยนแปลงที่ LiveData อย่างเช่น

        viewModel.score.observe(this,Observer{newScore->
                binding.scoreText.text = newScore.toString()
        })

แม้ว่าการทำงานจะออกมาเหมือนเดิม แต่เราจะเขียนโค้ดน้อยลง จะเป็นโปรแกรมที่ดีกว่า

## Encapsulate LiveData