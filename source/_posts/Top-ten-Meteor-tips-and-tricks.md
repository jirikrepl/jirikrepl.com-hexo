---
title: Top ten Meteor tips and tricks
date: 2017-06-10 17:02:11
tags:
---

## #1 Use validated methods
[Validated methods](https://github.com/meteor/validated-method) use object which represents your method. Any method can be exported in place of its definition and imported anywhere. 
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