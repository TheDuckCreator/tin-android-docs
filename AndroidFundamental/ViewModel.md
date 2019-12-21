---
layout: post
title:  ViewModel and ViewModel Factory
# permalink: /structure/ui/uistructure
---
# ViewModel and ViewModelFactory Codelabs Study

Program Bootstrap with Viewmodel and ViewModelFactory Starter Code from Google Codelabs from this link https://codelabs.developers.google.com/codelabs/kotlin-android-training-view-model/ and the Readme of Starter Code is below my readme.

## Meaning of ViewModel and ViewModelFactory
- **ViewModel**  is class to store and manage UI-related data, it allow data survive in device configure saturation (like screen rotation).
- **ViewModelFactory** is class to instantiate and return ViewModel 

## Managing Configuration Changes Problem
Configure change like screen rotation, screen close or anything can make program run into problem the ViewModelFactory can make the ViewModel use in many configure change.

For handling th problem of configure changes can use `savedInstanceState()` to save and store device configure like screen rotation but it's too mush lines of codes and can store  minimum data. It more than useful if use Design under Android Architecture Guideline (lifecycle).It more better to seperate UI Controller , ViewModel and ViewModel Factory and make it under *Seperation of Concern*

### Seperation of Concern
 * UI Controller must only contain logic to handle UI, no decision making logic
 * ViewModel hold data to Display,Simple Calculate and Transformation, decision making
 * ViewModelFactory contains business logic to perform simple calculation and decide of ViewModel

In Configure Change like screen rotation,etc fragment will be destroy but ViewModel is still alive

## ViewModelProvider
If we use viewModel instance using `ViewModel` class, object will create everytime when fragment is re-created. For the better result we can Instance ViewModel using `ViewModelProvider`
- `ViewModelProvider()` return existing ViewModel (if exists) or create a new one if not exist will Create ViewModel instance in association with given scope
- `ViewModelProviders.of()` is for Initial ViewModel use that To Create ViewModelProvider like
        
        viewModel = ViewModelProviders.of(this).get(GameViewModel::class.java)
    
    inside of is `FragmentActivity`  so `this` refer to fragment and `.get()` is get the model to make this is fragment activity.


## ViewModelFactory
Sometimes the value must pass to viewModel at the initialization part, so we have the problem that we pass value and it don't affect UI because the initialization is running out. Factory Method Pattern is the way to let `ViewModel` wait for value to initialize or another logic that programmer want and then we pass value into ViewModel on Initialization part.

ViewModelFactory uses factory to create objects while **Factory** method is a method that return the copy of the same class.

### ViewModelFactory Class
ViewModelFactory Class will take responsible for make a copy of that ViewModel object it will extend from `ViewModelProvider.Factory` 

    class ScoreViewModelFactory(private val finalScore: Int):ViewModelProvider.Factory{}

After we write the prototype of this class IDE will automatically appear error that we are not implement abstract method.

The Solution to solve this is to override `create` method that will let `modelClass` to create **ViewModel** class like this


      override fun <T : ViewModel?> create(modelClass: Class<T>): T {
        // If model class is correct return them as ViewModel with Value
        if(modelClass.isAssignableFrom(ScoreViewModel::class.java)){
            return  ScoreViewModel(finalScore) as T
        }
        throw IllegalArgumentException("Unknown ViewModel Class")
    }

### Create ViewModel From ViewModel Factory Class
- Initial value of `viewModelFactory` Variable by Pass the value from argument bundle as a constructor of Factory class

      private lateinit var viewModelFactory: ScoreViewModelFactory
      viewModelFactory = ScoreViewModelFactory(ScoreFragmentArgs.fromBundle(arguments!!).score)

- Create `ViewModel` from `ViewModelFactory`

      private lateinit var viewModel:ScoreViewModel
      viewModel = ViewModelProviders.of(this,viewModelFactory).get(ScoreViewModel::class.java)

Document Summary and Writing By Theethawat Savastham