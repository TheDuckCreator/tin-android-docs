---
layout: post
title: Room DB Connect Example
---
# Example for Bootstraper Database Connection
## Example 1
Reference from Google Codelabs - Kotlin Fundamental Course
- Get Reference fragment to the app to pass into view-model factory provider
        
        val application = requireNotNull(this.activity).application 

    `requireNotNull` is Throw Argument if value is null
- Get Reference to data source by reference to DAO
        
        val dataSource = SleepDatabase.getInstance(application).sleepDatabaseDao

- Create Instance of ViewModel Factory send it parameter
        
        val viewModelFactory = SleepTrackerViewModelFactory(dataSource,application)

- Get Reference to View Model
        
        val sleepTrackerViewModel = ViewModelProviders.of(this,viewModelFactory).get(SleepTrackerViewModel::class.java)

