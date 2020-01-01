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

# Implement RecyclerView

## Pick RecyclerView in Layout File

ลาก RecyclerView มาวางไว้ในพื้นที่ที่ต้องการใน Layout File หรืออาจจะเขียนเป็น xml ก็ได้ จากนั้นทำการใส่ LayoutManager ในฟิลด์ของ RecyclerView ซึ่งเราสามารถใส่โดย ค้นหาจาก GUI ข้าง ๆ หรือ เปิด text mode แล้วแก้ไขในไฟล์ `.xml` ก็ได้เช่นกัน

        <RecyclerView
            ...
            app.layoutManager="androidx.recyclerview.widget.LinearLayoutManager"/>

## Create One Element Layout

เตรียม Layout สำหรับการเก็บ element 1 element เหมือนเป็นแบบร่าง หรือ แม่พิมพ์ ให้กับ element ต่าง ๆ ที่จะรวมกันเป็น List และแสดงบน View โดยสร้างเป็น layout xml file จากนั้นใน Layout มีเพียงแค่ Text-View หรือ View อื่น ๆ ที่เราต้องการให้เป็นที่เก็บค่าแค่ค่าเดียว

สร้าง RecyclerView ViewHolder โดยสร้างเป็น Class หนึ่งที่ hold View กลุ่มนั้นเอาไว้ โดยเก็บ view เหล่านี้เป็นพารามิเตอร์ และผลที่ได้ออกมาเป็นเก็บอยู่ในรูป View ของ Recycler View ตัวอย่างเช่น

        class TextItemViewHolder(val textView:TextView):RecyclerView.ViewHolder(textView)

โดยเราสามารถสร้างไฟล์ที่ไว้เก็บสิ่งสัพเพเหระต่าง ๆ เช่น Class นี้ได้ หรือ สร้างเป็นไฟล์แยกก็ได้ หรือ สร้างเป็น Inner Class ใน Class Adapter ได้เช่นกัน

## Create Data Adapter

เริ่มจากการสร้าง Class มา 1 Class ที่ Implement `RecyclerView.Adapter<ViewHolder>()` ซึ่ง `ViewHolder` ก็คือ ViewHolder ที่เราสร้างไว้นั่นเอง ในตัวอย่างนี้คือ `TextItemViewHolder` หรือ เราจะสร้างแบบเป็น Group ที่เก็บหลาย ๆ อย่างไว้ใน Block เดียวก็ได้ (เป็น Inner Class ก็ได้ตามที่บอก)

### Define Data Variable

จากนั้นเราต้องเริ่ม define ตัวแปรตัวหนึ่งซึ่งเตรียมไว้สำหรับเก็บ data ของเรา เช่น

        var data = listOf<SleepNight>()

ทำการ Override Setter function ของตัวแปร data เสียใหม่ เพราะ RecyclerView ไม่ได้รับรู้เวลามี data ที่เปลี่ยนไป เราจะใช้ฟังก์ขัน `notifyDataSetChanged()` ซึ่งเป็นส่วนหนึ่งใน RecyclerView ที่เรา Extend มา ในการแจ้งเตือนไปยัง RecyclerView ว่ามีการเปลี่ยนแปลงของข้อมูล ให้ RecyclerView จัดการ List ใหม่ โดยการ Override Setter Function ทำล่างการประกาศตัวแปร data ดังนี้

        set(value){
            field = value
            notifyDataSetChanged()
        }

### Method Overiding

จากนั้นเราต้องทำการ **_Overide 3 ฟังก์ชัน_** ได้แก่ `getItemCount()`, `onBindViewHolder(holder,position)` และ `onCreateViewHolder(parent,viewtype)` โดยเราจำเป็นที่จะต้อง Implement ทั้ง 3 ฟังก์ชัน ตามหลักการของ Interface

Return Size ของ Data ใน `getItemCount()`

        override fun getItemCount(){
            return data.size
        }

Hold Data ลงในตำแหน่งที่เหมาะสมใน `onBindViewHolder()` ซึ่งลักษณะจะเป็นแบบเดียวกันกับ Array คือ List ใน RecyclerView จะมี posiion ของมันอยู่ อ้างอิงตามเลขต่าง ๆ เราไม่ต้องไล่จัดทุกเลข โดยใช้ while หรือ for loop เนื่องจาก การใช้ RecyclerView เป็นการวนอยู่แล้ว ในฟังก์ชัน onBindViewHolder() จะมีพารามิเตอร์ ก็คือตำแหน่งมา ดังนั้นการจัดการโดยใช้ `onBindViewHolder()` ก็เหมือนกับการจัดการ ณ Index ใด ๆ ของชุดข้อมูล

        override fun onBindViewHolder(holder:TextItemViewHolder,position:Int){
            val item = data[position]
            holder.textView.text = item.sleepQuality.toString()
        }

ในส่วนของ `onCreateViewHolder()` เราจะต้องสร้างตัวแปรตัวหนึ่งไว้เก็บ Instance ซึ่งเป็น Context ของ ParentView จากนั้น สร้าง View ขึ้นมา ชนิดเดียวกับ SubElement ของมัน แล้ว inflate layout จาก Parent View ที่เราได้สร้างตัวแปร Reference ไว้ ท้ายสุดก็จะ Return ViewHolder ของ View นี้ กลับไป

        override fun onCreateViewHolder(parent:ViewGroup,viewType:Int):TextItemViewHolder{
            val layoutInflater = LayoutInflater.from(parent.context)
            val view = layoutInflater.inflate(R.layout.text_item_view,parent,false) as TextView
            return TextItemViewHolder(view)
        }

## Add Adapter to Fragmemt
