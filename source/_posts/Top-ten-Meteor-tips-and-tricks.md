---
title: Top ten Meteor tips and tricks
date: 2017-06-10 17:02:11
tags:
---

## #1 Use validated methods
[Validated method](https://github.com/meteor/validated-method) use object which represents your method.
Any method can be exported from the place of its definition and imported anywhere (using ES6 import/export).   
This is better then call methods by a magic string name `Meteor.call('someMagicNameOfMethod');`

Another great think is that you have build in argument validation which uses [simple schema](https://github.com/aldeed/meteor-simple-schema).
 
#### How to create and call validated method

Define method *getExerciseProgress* on server:
```javascript
export const getExerciseProgress = new ValidatedMethod({
    name: 'exercises.getExerciseProgress',
    validate: new SimpleSchema({lessonUrl: Lessons.simpleSchema().schema('lessonUrl')}).validator(),
    run({lessonUrl}) {
        ...
    }
});
```

Then call method *getExerciseProgress* on client:

```javascript
// import method at the beginning of the file
import {getExerciseProgress} from "/imports/api/exercises/methods";

// call the method
getExerciseProgress.call({lessonUrl: this.lessonUrl}, (error, result) => {
    // do something here with error or result     
});
```


## #2 Don't use HTML comments, use Blaze comments

Don't you html comments when working with Blaze:
```html
<!-- this is HTML comment --> 
<div class="section">
  ...
</div>
```

HTML comments will be bundled your into your production code. You don't want that, because every byte counts.
Moreover why provide any extra information about how your frontend works.
   
```html
{{! this is Blaze single line comment}} 
<div class="section">
  ...
</div>

{{!-- This is a block comment.
We can write {{foo}} and it doesn't matter.
{{#with x}}This code is commented out.{{/with}}
--}}
```

See more in [documentation](http://blazejs.org/api/spacebars.html#Comment-Tags).


## #3 Validate data context of Blaze component
First of all you already use [SimpleSchema](https://github.com/aldeed/meteor-simple-schema) for collection validations, right?
Now we cant extend that to Blaze components.

Consider definition of Lessons collection:
```javascript
// imports/api/lessons/lessons.js

export const Lessons = new Meteor.Collection("lessons");

Lessons.schema = new SimpleSchema({
    _id: {type: String, regEx: SimpleSchema.RegEx.Id},
    name: {type: String, min: 1},    
    description: {type: String, optional: true},    
});
Lessons.attachSchema(Lessons.schema);
```

And then consider lesson component to be reusable component which works lessons data:

```html
{{! /imports/ui/lessons/lessons.html}}
{{> lesson lessonUrl=lessonUrl description=description }}
```

And then validate `Template.currentData()`.

```javascript
// imports/ui/lessons/lessons.js

Template.lesson.onCreated(function lessonOnCreated() {
    this.autorun(() => {
        new SimpleSchema({
            lessonUrl: {type: Lessons.simpleSchema().schema('lessonUrl')},
            description: {type: Lessons.simpleSchema().schema('description')},
        }).validate(Template.currentData());
    });
    
    ...
});
```

When developing a new component write data validation first. Then you will always know that component works with right kind of data.
When component does not have right date, you know it right away. You don't need to debug code inside the component.
[Todos app](https://github.com/meteor/todos/blob/master/imports/ui/components/lists-show.js#L31) use this patternt a lot.
You can also read about it more in [Meteor guide.](http://blazejs.org/guide/reusable-components.html#Validate-data-context) 
