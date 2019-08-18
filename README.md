![Amazon and Dynasaur](https://github.com/tglines/dynasaur/raw/master/dino.png)

dynasaur
========

A DynamoDB ORM for Node.js

It aims to provide a few added features that most people will want from a DynamoDB tool.  These things include a Mongoose inspired way of doing things, periodic S3 backups, multi-indexing (clones data to separate tables, having different indexes on each) and more.

For now it provides a simple way to interact with DynamoDB and create/read rows in the table.  More features coming.


## An Example

### Install Dynasaur

```bash
npm install dynasaur
```

### Defining a Model


```coffeescript
module.exports = (dynasaur) ->

  blog_post_schema =
    attributes:
      author: String
      title: String
      body: String
      date: Number
    index: [{type:'hash',field:'author'}, {type:'range',fields:['title','date']}]

  BlogPost = dynasaur.model 'BlogPost', blog_post_schema
```



### Saving the blog post

First make sure that the `AWS_ACCESS_KEY_ID` and `AWS_SECRET_ACCESS_KEY` shell variables are set to their respective values.

```coffeescript
aws_settings = {
  credentials: {
    accessKeyId: process.env.AWS_ACCESS_KEY_ID,
    secretAccessKey: process.env.AWS_SECRET_ACCESS_KEY
  },
  region:'us-west-1'
}

dynasaur = require 'dynasaur'

BlogPost = require('./models/BlogPost')(dynasaur)

blog_post = BlogPost.new()
blog_post.title = 'Dynasaur'
blog_post.author = 'Someone'
blog_post.body = 'Dynasaur is a DynamoDB ORM that builds some neat features on top of DynamoDB'
blog_post.date = new Date().getTime()
blog_post.save (err,data) ->
  console.log err
```

### Reading some posts back out, select one at random ... reload it and delete it

The tool every serious tech blogger needs

```coffeescript
BlogPost.find {}, (err,blog_posts) ->
  console.log blog_posts
  n = Math.floor( Math.random() * blog_posts.length)
  BlogPost.get {id:blog_posts[n].id}, (err,blog_post_to_delete) ->
    console.log blog_post_to_delete
    blog_post_to_delete.remove (err) ->
       console.log 'REMOVED'
```

## Stuff to come

* Full test coverage
* Better comments, docs and examples
* Periodic (user defined time) backups to S3
* Multi-indexing to allow one data type to be indexed via multiple keys (we'll use DynamoDB's strong consistency and data replication here)
* Elastic Map Reduce (so you can run SQL like queries over the data sets you store)


## Shoulders it stands on

A big thanks go out to Jed Schmidt and other contributors for [dynamo](https://github.com/jed/dynamo) which sits as an API abstraction for DynamoDB that made this process significantly easier.

Another big thanks go out to all the contributors of [mongoose](https://github.com/LearnBoost/mongoose) as it was a huge inspiration to the design of how users interact with the project.


## License 

(The MIT License)

Copyright (c) 2012 Travis Glines &lt;travisglines [->] gmail.com &gt;

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
