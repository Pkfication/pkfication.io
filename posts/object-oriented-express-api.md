---
title: 'Object Oriented Express API'
date: '2021-01-31'
meta:
  name: 'Object Oriented Express API'
  content: 'Object Oriented JS, NodeJS, ExpressJS, Boiler Plate'
---

I've been toying around with the idea of having a scalable express api service, which will handle some of the basic crud operations for a model without writing the actual code for the controller. I've three years of experience and I was not sure if you could something like that but then something happened.

I started to work on Ruby on Rails last year and my perspective has been changed a lot since then, working on different tech stacks, with different people really opens you up. Someone had written a generic handler for all the models and all the basic operations of CRUD were handled without writing a piece of code for the new controller. It was then I realised the power of Object Oriented Programming.

There are a lots of language specific perks of writing code in Ruby, but Object Orientation is a fundamental concept which can be very useful, so I decided to use it in Node and Express and make my first worthwhile contribution to the Open Source Community. I choose Node and Express because I have worked on it before and wanted to see the difference in the quality of code, In other words, I wanted to see how my skills have improved over the years.

I started the project on a Saturday afternoon, I was almost done on Sunday. During the project, I found out some important things:

- In Express 5, which is currently a beta version, you don't have to throw the error explicitly, It throws the error itself and call the next() with the error, so no more if this then that in the handlers.
- You can use a whole javascript class as a route handler. (AMAZING!!)

It had to dig a bit to find these two solutions. Now with the help of these two solutions my handlers were looking something like this.

```javascript
var express = require('express');
var router = express.Router();

const Book = require('../models/Book');
const books = require('../caches/book.cache');
const Base = require('./base.controller');

class BookController extends Base {
  constructor(model) {
    super()
    this.model = model;

    // All the routes declared here!
    router.get('/', books.cache, this.index);
    router.get('/:id',books.cache, this.get);
    router.post('/', this.create);
  }
}

new BookController(Book);

module.exports = router;
```

You can use this in your routes as this:
```javascript
var booksRouter = require("../controllers/book.controller");

module.exports = function(app) {
  app.use("/books", booksRouter);
};
```

Isn't this neat and clean ? As you can see I've imported the Model, and a cache class I've implemented for this. If you look closely you'll realize how is this working ? Thats the best part. I've implemented a base controller which can be extented to every controller and they can use the methods defined in the base controller to implement the basic crud.

The base controller looks something like this:

```javascript
class Base {
  constructor() {}

  index = async (req, res, next) => {
    const resources = await this.model.find({})
    return res.status(200).json(this.apiSend(resources))
  }

  get = async (req, res, next) => {
    const resource = await this.model.findById(req.params.id)
      .orFail(new NotFoundError());
    return res.status(200).json(this.apiSend(resource))
  }

  create = async (req, res) => {
    const { ...body } = req.body
    const resource = await this.model.create(body)
    return res.status(200).json(this.apiSend(resource))
  }

  apiSend = (json) => {
    return {
      data: json
    }
  }
}

module.exports = Base;
```

As You can see there is nothing specific to any model or controller. If you have a create method method which needs a bit of manipulations of post changes, You can implement that in your new controller using a service module specific to the controller or the model.

A important point to notice is there is no try catch blocks in any of the handlers, it's the magic of express 5.

If you guys are interested you can check it out [here](https://github.com/Pkfication/pkfication.io)

This project still needs a lot of improvements, but I am happy that it started this way! I'll keep posting further updates.