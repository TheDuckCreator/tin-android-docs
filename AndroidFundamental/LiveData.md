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
Encapsulation ในภาษาอังกฤษ แปลว่าการเสนอส่วนที่สำคัณที่สุด หรือ แปลว่าการจัดการที่เหมาะสม ในทางโปรแกรม คือ วิธีหนึ่งที่เราจะไม่เปิดเส้นทางให้ไปแก้ไขค่าในตัวแปรของ Object ตรง ๆ  แต่จะใช้สร้างเป็น public method ที่จะให้มันไปแก้ไข private field ใน class ข้างในเอง 

ในโค้ดก่อนหน้านี้ตาม Codelab เรายังไม่มีการทำ Encapsultation เพราะเราแก้ไขต่าง ๆ โดยตรง แต่ทางที่ดีเราไม่ควรจะทำอย่างนั้น เราควรจะให้การแก้ไขค่าต่าง ๆ เป็นการทำของ ViewModel เท่านั้น เพื่อเป็นการแยกงานเป็นส่วน ๆ แต่เรายังจำเป็นต้องอ่านข้อมูลเหล่านั้น โดย UI Controller ถ้าจะใช้ private เลยอาจจะไม่สะดวก จึงมี 2 ชนิดตัวแปร ให้ใช้แทน

- **MutableLiveData** คือ ข้อมูลในออกเจ็กต์นี้สามารถเปลี่ยนได้ จากข้างในและนอก ViewModel
- **LiveData** คือ ข้อมูลที่ถูกเก็บเข้าไปในชนิดนี้ สามรถอ่านได้เท่านั้น ไม่สามารนำมาแก้ไขได้

### Backing Property
ฺBacking Property เป็นวิธีหนึ่งในภาษา Kotlin ซี่งอนุญาติให้ get method (getter) สามารถเข้าถึง Object ได้ และส่งออกค่าบางอย่างที่ต้องการได้ โดยที่ค่านั้นที่ไม่ใช่ตัว Object นั้นจริง ๆ วิธีนี้สามารถเอามาประยุกต์กับ การใช้ `LiveData` ได้ เพราะ LiveData นั้น แก้ไขไม่ได้

วิธีนี้มีขั้นตอนคือ สร้างตัวแปรอีกตัวหนึ่งที่เป็น `MutableLiveData<>` เคียงคู่กับตัวแปรที่เราเก็บข้อมูลหลัก ซึ่งเป็น `LiveData<>`ที่เราแก้ไขไม่ได้ 

        private val _score = MutableLiveData<Int>()
        val score:LiveData<Int>

ตัวแปรที่เป็น MultableLiveData สามารถเป็น Private ได้เลย เพราะมีการเข้าถึงเฉพ่าะในคลาส ไม่จำเป็นต้องเปิดต่อข้างนอก ในขณะดียวกัน ตัวแปรที่ข้างนอกเข้ามาแก้ไขได้อย่าง `score` ก็ถูกกำหนดเป็นชนิดที่ไม่สามารถแก้ไขได้อยู่แล้ว

จากนั้น Override `get()` Method ของตัวแปร LiveData เช่นดังตัวอย่าง

        val score:LiveData<Int>
         get() = _score
        
