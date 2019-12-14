---
layout: post
title:  UI Structure
# permalink: /structure/ui/uistructure
---
# New User Interface Structure
Located in `ActivityMain.kt` in new folder *userview* `com.cnr.phr_android.userview` use **DataBinding** as the main methodology and using Android Jetpack (Android X Library) instead of Android Support Library.
## DataBinding
We will use data binding by cover layouts or anythings in layout resource file with `<layout></layout>` and in Activity or Fragment we will not nessecory to use `findViewById` or like this we will make a variable `binding` or another name that refer to *this activity binding* or *this fragment binding*
### My Data Binding Reference 
- use `lateinit` to make the Class Variable like this use this both Activity and Fragment, the type of variable (on Example `ActivityMainBinding`) will automatically change due to xml file name that we want to use.

        // Example For Activity
        private lateinit var  binding:ActivityMainBinding

- **In Activity** initial value in Oncreate function
    
        binding = DataBindingUtil.setContentView(this,R.layout.activity_main)

- **In Fragment** initial value in OnFragmentCreate function
## Navigation
### Dependencies Setting
The Navigation based on [android official documents](https://developer.android.com/guide/navigation/navigation-getting-started) use this in `build.gradle` app module in dependencies. other dependencies please focus on [Document Index Page](/)

  
        def nav_version = "2.1.0"
        implementation "androidx.navigation:navigation-fragment-ktx:$nav_version"
        implementation "androidx.navigation:navigation-ui-ktx:$nav_version"

    
    

## Navigation Host Fragment

