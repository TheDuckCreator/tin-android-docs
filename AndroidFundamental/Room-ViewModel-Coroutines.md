---
layout: post
title: Room Database and ViewModel
---

# Room Database with ViewModel and Coroutines

เรื่องนี้จะอ้างอิงตาม Google Codelabs [Coroutines and Room](https://codelabs.developers.google.com/codelabs/kotlin-android-training-coroutines-and-room) และจากคอนเซปต์ของ Database และ Coroutines ที่ผ่านมาแล้ว

หลังจากเราทำการสร้าง Database เสร็จเรียบร้อยแล้ว จากนั้นจะเข้าสู่การสร้าง ViewModel มาเรียกใช้ Database, ViewModelFactory มาเรียกใช้ ViewModel และ การเอา ViewModel ดังกล่าวโยงเข้ากันกับ Data Access Object (DAO) และใช้ DAO ในการ Query หรือทำ Transaction ต่าง ๆ

### Pre-Programing

ตรวจสอบว่าได้ทำการ implement dependency ของ Coroutines ไว้แล้วยัง รวมถึง Dependencies อื่น ๆ ด้วย

        implementation "org.jetbrains.kotlinx:kotlinx-coroutines-android:$coroutine_version"

## ViewModel and Database Reference

- สร้าง Reference ของ Activity หรือ Fragment นั้น ๆ (Get Application ออกมา)

        var application  = requireNotNull(this.activity).application

- สร้างตัวแปรมา Reference Datasource ของเรา โดย `getInstant()` ของ Database ออกมา และ ดึง DAO ที่เชื่อมต่อกับ Database ออกมา

        val dataSource = sleepDatabase.getInstance(application).sleepDatabaseDao

- เตรียมตัวแปรสำหรับ ViewModelFactory และ ส่งค่าพารามิเตอร์ให้กับมัน

        val viewModelFactory = SleepTrackerViewModelFactory(dataSource,application)

- เตรียม Reference สำหรับ ViewModel ที่เราจะใช้กับงานนี้

        val sleepTrackerViewModel = ViewModelProviders.of(this,viewModelFactory).get(SleepTrackerViewModel::class.java)


## Job, Scope and Dispatcher Preparing

จากที่ได้กล่าวไว้ในเรื่อง Coroutines ว่า จะมีส่วนที่สำคัญ 3 ส่วนคือ Job, Scope และ Dispatcher ขั้นตอนเริ่มแรกคือ การกำหนดสิ่งที่เราจะให้มันทำใน Coroutines หรือ Multithreading Scenario คืออะไร เมื่อเราต้องการเริ่มหรือหยุด Coroutines เราสามารถจัดการกับมันได้ผ่านตัวแปรตัวนี้

### Initial Job and Cancle Function

การสร้าง Job นอกจากจะต้องสร้างเป็น field ซึ่งเป็นตัวแปรชนิด `Job()` แล้ว สิ่งที่สำคัญอีกอย่างหนึ่งคือ Method สำหรับการ Cancle งานนี้

        private var viewModelJob = Job()

Override Function `onCleard()` ไว้ด้วย

        override fun onCleared() {
            super.onCleared()
            viewModelJob.cancel()
        }
