##Mongoose CRUD Helper

You make mongoose files by hand.  The goal of this project is to help making Mongoose models easier, programatically. Either via REST or a generator or some other method.

Here is an example of a generated mongoose file
person.js
```
exports = module.exports = function(mongoose) {
  var fs = require('fs');
  var Schema = mongoose.Schema;
  var schema = new Schema(require('./Person_Schema')(mongoose))
  schema.statics.slug = function(cb) {
    return 'Person'
  }
  schema.statics.displayName = function(cb) {
    return 'Person'
  }
  return mongoose.model('Person',schema);
}

```

person_schema.js
```
exports = module.exports = function(mongoose) {
  return {Name:String,Age:Number,birthDate:{ type: Date, default: Date.now }};
}
```

What did it take to make this autogenerated file?

```
  var mongoose = require('mongoose')
  var Helper = require ('mongoose-crud-tools');
  var helper = new Helper(mongoose, {modelsDir: __base + 'models/', modelTemplatesDir: __base + 'views/'});
  helper.createModel("Person", {}, function() {});
  helper.addToSchema("Person", {Name:String,Age:Number,birthDate:{ type: Date, default: Date.now }}, function() {});

```
Of course addToSchema can be called as needed as your application grows.

You can create your own schema templates to override the default ones provided. See the views folder in the module for the sample default schema.


Where
mongoose is your required mongoose var
modelsDir is where you want your outputted models to end up at.
modelTemplatesDir is a folder containing your templates for making models and schemas. This is optional.  Please see the views folder for how you can make your own model templates


####Create Model

```
  helper.createModel('sample', schema, callback);
```

where schema is your default schema.  Please use {} for default if you want an empty schema to start

####Add To Schema

```
    helper.addToSchema(modelName, fields, function(err) {
```

Schemas are json as such

Key: Value
e.g.

date: { type: Date, default: Date.now }


####Create/Replace Schema

Replaces the entire schema. Here is an example of receiving new schema pieces in a request body to replace your schema with.

```
  var schema = {};

  for (var item in req.body) {
    schema[item] = req.body[item];
  }
  helper.createSchema(modelName, schema, callback)
```

There is an example project coming soon using a REST API on top of this, but it should work fine for a yeoman generator, or other applications.

Schema updates and new models should reflect immediately in your application, invalidating the required module is handled internally