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

### Make UI Scope

สร้าง UI Scope สำหรับ Coroutines เพื่อให้ Coroutine รู้ว่าตัวมันต้องรันบน Thread ใด แล้วก็ต้องบอก Coroutine ด้วยว่าจะมี Job อะไร โดยพารามิเตอร์ของการสร้าง Scope คือ ใส่ Dispatcher และ Job ลงไป

        private val uiScope = CoroutineScope(Dispatcher.Main + viewModelJob)

## Lauching Your Scope

หลังจากตั้งค่าตัวแปรต่าง ๆ ครบแล้ว จากนั้นเราจะเริ่มเรียกใช้ข้อมูลจากฐานข้อมูลดยเริ่มจาก

### LiveData Variable Declare

เพื่อให้ได้ประสิทธิภาพ เราจะเก็บข้อมูลที่ได้มาจาก Database ในรูปของ LiveData โดยสามารถจัดอยู่ในแบบง่าย ๆ คือ `MutableLiveData<>()` แต่มันจะสามารถแก้ไขได้ง่าย โดยสามารถเลี่ยงไปใช้ตัวแปรแบบ `LiveData<>()` แทนก็ได้ และใช้ Backing Property ช่วย โดยในที่นี้จะใช้ MutableLiveData ธรรมดาก่อน

    private var tonight = MutableLiveData<SleepNight?>()

### Start Coroutines and LiveData

หลังจากเราได้ LiveData อันเป็นที่เก็บของข้อมูลที่เราต้องการดึงจากฐานข้อมูลแล้วนั้น เราก็จะเรียกค่าจาก Database เพื่อเข้ามาเก็บไว้ในตัวแปร LiveData นั้น ซึ่งในตรงนี้จะมีการทำงาน ที่ใช้หลัก Blocking ไม่ดีนัก ต้องใช้หลักของ Coroutines ดังนั้นตรงนี้ จะเป็นการ เรียกใช้ หรือ Implement ในส่วนของ Coroutines ด้วย

- เรียกใช้ฟังก์ชันที่จะใช้เข้าถึง LiveData ซึ่งเกี่ยวข้องกับ Coroutines ตังแต่ scope `init{}` หรือ Constructor ของ class เช่น เราจะเรียกให้ Coroutine ทำงานในฟังก์ชัน `initializeTonight()` เราก็เรียกฟังก์ชันนี้ใน init

        init{
                initializeTonight()
        }

- หลังจากนั้น เราก็ต้อง declare function ลงใน class นี้ โดยการเขียนฟังก์ชันที่จะให้มันทำงานใน Coroutines นั้นไม่เหือนกับฟังก์ชันธรรมดาทั่วไป เราจะต้องเอา Scope ที่เราเคยกำหนดไว้มาเป็นตัวเริ่ม กล่าวคือ LiveData Coroutine จะรันบน Scope เราก็ต้องบอก Scope ก่อน เพราะเราเริ่มคำลั่งตอน Init คือเริ่มต้นเริ่ม Class แต่จริง ๆ เรายังไม่ได้ต้องการให้มันเริ่มตอนนั้น เราจะให้เริ่มต้น uiScope ของเราพร้อม ไม่งั้นมันก็ไม่รู้

        private fun initializeTonight(){
                uiScope.launch{
                        tongiht.value = getTonightFromDatabase()
                }
        }

### Put Data out form Database

เราได้เตรียมค่าที่จะใช้ในการเก็บข้อมูล ซึ่งเก็บอยู่ในรูปของ LiveData เรียบร้อยแล้ว คือ ให้ค่าของมันเป็น `getTonightFromDatabase()` ซึ่งเรายังไม่ได้ Implement Function ดังกล่าว

- Implement ฟังก์ชัน ในการจะ Implement นั้น ในการรันผ่าน Coroutines ซึ่งมันเป็นแบบ Asynchronous นั้น เราก็จะไม่สั่งฟังก์ชันให้ทำงานเป็นบล็อก ๆ แต่เราจะใช้หลักการของ Suspend Function แทน เพราะว่าถ้าหากไม่มีข้อมูล ไม่มีอะไรพร้อมให้มันทำงาน มันจะได้ไม่ต้องทำงาน

        private suspend fun getTonightFromDatabase:SleepNight?{}

- ให้ฟังก์ชันนั้นส่งค่าออกมา ซึ่งแน่นอนว่าคงจะไม่สามารถส่งออกแบบธรรมดาได้ มันต้องส่งแบบขึ้นกับสถานะการณ์ ในสถานการณ์ของ Database นี้ จัดเป็นส่วนหนึ่งที่ทำบน I/O การ return แบบนี้เราจะใช้ `return WithContext(){}` ในที่นี้ Context ของเราคือ Dispatchers.IO จะได้เป็น

        return withContext(Dispatchers.IO){
                var night = database.getTonight()
                if (night?.endTimeMilli != night?.startTimeMilli) {
                        night = null
                }
                night
        }

ท้ายที่สุดแล้ว เราจะได้หลักการคร่าว ๆ ของการทำงานของโปรแกรม แบบ Database + Coroutines ตามตัวอย่างที่ Google Codelabs ให้มาดังนี้

        fun someWorkNeedsToBeDone {
                uiScope.launch {
                        suspendFunction()
                }
        }

        suspend fun suspendFunction() {
                withContext(Dispatchers.IO) {
                        longrunningWork()
                }
        }

ขอบคุณโค้ดเฉลยจาก Google Codelabs Code of this if a copy from [Google Codelabs Android Kotlin Fundamentals 06.2 Coroutines and Room](https://codelabs.developers.google.com/codelabs/kotlin-android-training-coroutines-and-room/index.html?index=..%2F..android-kotlin-fundamentals#5)
