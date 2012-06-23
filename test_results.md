# TOC
   - [collection.js](#collectionjs)
     - [id()](#collectionjs-id)
     - [find(), findItems(), findEach()](#collectionjs-find-finditems-findeach)
       - [mock find() error](#collectionjs-find-finditems-findeach-mock-find-error)
     - [findById(), updateById(), removeById()](#collectionjs-findbyid-updatebyid-removebyid)
       - [findById()](#collectionjs-findbyid-updatebyid-removebyid-findbyid)
       - [updateById()](#collectionjs-findbyid-updatebyid-removebyid-updatebyid)
       - [removeById()](#collectionjs-findbyid-updatebyid-removebyid-removebyid)
   - [db.js](#dbjs)
     - [bind()](#dbjs-bind)
     - [gridfs()](#dbjs-gridfs)
     - [open()](#dbjs-open)
     - [close()](#dbjs-close)
     - [ensureIndex()](#dbjs-ensureindex)
<a name="" />
 
<a name="collectionjs" />
# collection.js
<a name="collectionjs-id" />
## id()
should convert string id to ObjectID success.

```js
var id = '4ec4b2b9f44a927223000001';
id = db.testcollection.id(id);
id.should.be.instanceof(db.testcollection.ObjectID);
id = db.testcollection.id(id);
id.should.be.instanceof(db.testcollection.ObjectID);
id = '4ec4b2b9f44a927223000foo';
id = db.testcollection.id(id);
id.should.be.instanceof(db.testcollection.ObjectID);
```

should return source id when id length !== 24.

```js
var ids = [123, '4ec4b2b9f44a92722300000', 'abc', '4ec4b2b9f44a927223000f00123123'];
ids.forEach(function (id) {
  db.testcollection.id(id).should.equal(id);
});
```

<a name="collectionjs-find-finditems-findeach" />
## find(), findItems(), findEach()
should find().toArray() return 100 comments.

```js
db.comment.find().toArray(function (err, rows) {
  should.not.exist(err);
  rows.should.be.instanceof(Array).with.length(100);
  done();
});
```

should findItems(fn) all comments.

```js
db.comment.findItems(function (err, comments) {
  should.not.exist(err);
  should.exist(comments);
  comments.should.be.instanceof(Array).with.length(100);
  done();
});
```

should findItems({} fn) all comments.

```js
db.comment.findItems(function (err, comments) {
  should.not.exist(err);
  should.exist(comments);
  comments.should.be.instanceof(Array).with.length(100);
  done();
});
```

should findItems({limit: 10}) query wrong return top 0 comments.

```js
db.comment.findItems({limit: 10}, function (err, comments) {
  should.not.exist(err);
  comments.should.be.instanceof(Array).with.length(0);
  done();
});
```

should findItems({}, {limit: 10}) return top 10 comments.

```js
db.comment.findItems({}, {limit: 10}, function (err, comments) {
  should.not.exist(err);
  comments.should.be.instanceof(Array).with.length(10);
  done();
});
```

should findEach(fn) call fn 100 times.

```js
var count = 0;
db.comment.findEach(function (err, comment) {
  should.not.exist(err);
  if (!comment) {
    count.should.equal(100);
    return done();
  }
  count++;
});
```

should findEach({}, {limit: 20}, fn) call fn 20 times.

```js
var count = 0;
db.comment.findEach({}, {limit: 20}, function (err, comment) {
  should.not.exist(err);
  if (!comment) {
    count.should.equal(20);
    return done();
  }
  count++;
});
```

<a name="collectionjs-find-finditems-findeach-mock-find-error" />
### mock find() error
should findItems() error.

```js
db.comment.findItems(function (err, docs) {
  should.exist(err);
  err.should.be.instanceof(Error).with.have.property('message', 'mock find() error');
  should.not.exist(docs);
  done();
});
```

should findEach() error.

```js
db.comment.findEach(function (err, docs) {
  should.exist(err);
  err.should.be.instanceof(Error).with.have.property('message', 'mock find() error');
  should.not.exist(docs);
  done();
});
```

<a name="collectionjs-findbyid-updatebyid-removebyid" />
## findById(), updateById(), removeById()
<a name="collectionjs-findbyid-updatebyid-removebyid-findbyid" />
### findById()
should find one object by ObjectID.

```js
db.article.findById(articleId, function (err, article) {
  should.not.exist(err);
  should.exist(article);
  article.should.have.property('_id').with.instanceof(db.ObjectID);
  article.should.have.property('created_at').with.instanceof(Date);
  article.should.have.property('title').with.include(now.toString());
  article.created_at.toString().should.equal(now.toString());
  done();
});
```

should find one object by String id.

```js
db.article.findById(articleId.toString(), function (err, article) {
  should.not.exist(err);
  should.exist(article);
  article.should.have.property('_id').with.instanceof(db.ObjectID);
  article.should.have.property('created_at').with.instanceof(Date);
  article.should.have.property('title').with.include(now.toString());
  article.created_at.toString().should.equal(now.toString());
  done();
});
```

should not find when id not exists.

```js
db.article.findById('foo', function (err, article) {
  should.not.exist(err);
  should.not.exist(article);
  done();
});
```

<a name="collectionjs-findbyid-updatebyid-removebyid-updatebyid" />
### updateById()
should update obj by id.

```js
var updatedTime = new Date();
var doc = {
  $set: {
    title: 'new title ' + updatedTime,
    updated_at: updatedTime
  }
};
db.article.updateById(articleId.toString(), doc, function (err, article) {
  should.not.exist(err);
  should.not.exist(article);
  db.article.findById(articleId, function (err, article) {
    should.not.exist(err);
    should.exist(article);
    article.should.have.property('title', 'new title ' + updatedTime);
    article.should.have.property('updated_at').with.instanceof(Date);
    article.updated_at.toString().should.equal(updatedTime.toString());
    done();
  });
});
```

<a name="collectionjs-findbyid-updatebyid-removebyid-removebyid" />
### removeById()
should remove obj by id.

```js
var id = articleId.toString();
db.article.findById(id, function (err, article) {
  should.not.exist(err);
  should.exist(article);
  db.article.removeById(id, function (err, article) {
    should.not.exist(err);
    should.not.exist(article);
    db.article.findById(id, function (err, article) {
      should.not.exist(err);
      should.not.exist(article);
      done();
    });
  });
});
```

<a name="dbjs" />
# db.js
<a name="dbjs-bind" />
## bind()
should throw error when collection name wrong.

```js
var wrongNames = ['', null, 123, '    ', '\n   \t   ', undefined, 0, 1, new Date(), {}, []];
wrongNames.forEach(function (name) {
  (function () {
    db.bind(name);
  }).should.throw('Must provide collection name to bind.');
});
```

should throw error when options is not object.

```js
(function () {
  db.bind('foo', function () {});
}).should.throw('the args[1] should be object, but is `function () {}`');
```

should add helper methods to collection.

```js
db.bind('testCollection', {
  totalCount: function (calllback) {
    this.count(calllback);
  }
});
db.should.have.property('testCollection').with.have.property('totalCount').with.be.a('function');
db.testCollection.totalCount(function (err, total) {
  should.not.exist(err);
  total.should.equal(0);
  done();
});
```

<a name="dbjs-gridfs" />
## gridfs()
should start gridfs store.

```js
db.gridfs();
db.should.have.property('skinGridStore');
```

<a name="dbjs-open" />
## open()
should open a database connection.

```js
var db1 = mongoskin.db('localhost/mongoskin_test');
db1.state.should.equal(0);
db1.open(function (err) {
  should.not.exist(err);
  db1.state.should.equal(2);
});
db1.state.should.equal(1);
db1.open(function (err) {
  should.not.exist(err);
  db1.state.should.equal(2);
  done();
});
```

should open a database connection with user auth fail.

```js
var db2 = mongoskin.db('test:test@localhost/mongoskin_test');
db2.open(function (err, db) {
  should.exist(err);
  err.should.have.property('message', 'auth fails');
  err.should.have.property('name', 'MongoError');
  should.not.exist(db);
});
db2.open(function (err, db) {
  should.exist(err);
  err.should.have.property('message', 'auth fails');
  err.should.have.property('name', 'MongoError');
  should.not.exist(db);
  done();
});
```

should open 100 times ok.

```js
var db3 = mongoskin.db('localhost/mongoskin_test');
var counter = 0;
for (var i = 0; i < 100; i++) {
  db3.open(function (err, db) {
    should.not.exist(err);
    should.exist(db);
    counter++;
    if (counter === 100) {
      done();
    }
  });
}
```

<a name="dbjs-close" />
## close()
should close a database connection.

```js
var dbClose = mongoskin.db('localhost/mongoskin_test');
dbClose.state.should.equal(0);
dbClose.close(function (err) {
  dbClose.state.should.equal(0);
  should.not.exist(err);
});
dbClose.open(function (err) {
  dbClose.state.should.equal(2);
  should.not.exist(err);
});
dbClose.state.should.equal(1);
dbClose.close(function (err) {
  dbClose.state.should.equal(0);
  should.not.exist(err);
  done();
});
```

should close 100 times ok.

```js
var db3 = mongoskin.db('localhost/mongoskin_test');
var counter = 0;
db.open();
for (var i = 0; i < 100; i++) {
  db3.close(function (err) {
    should.not.exist(err);
    counter++;
    if (counter === 100) {
      done();
    }
  });
}
```

<a name="dbjs-ensureindex" />
## ensureIndex()
should index infos is empty.

```js
var barDb = mongoskin.db('localhost/mongoskin_test');
barDb.indexInformation('not-exists', function (err, result) {
  should.not.exist(err);
  should.exist(result);
  result.should.eql({});
  done();
});
```

should get index infos error.

```js
var barDb = mongoskin.db('test:test@localhost/mongoskin_test');
barDb.indexInformation('not-exists', function (err, result) {
  should.exist(err);
  should.not.exist(result);
  done();
});
```

should create title:1 index success.

```js
db.ensureIndex('foo', {title: 1}, function (err, result) {
  should.not.exist(err);
  should.exist(result);
  result.should.equal('title_1');
  done();
});
```

should create title:-1 index success.

```js
db.ensureIndex('foo', {title: -1}, function (err, result) {
  should.not.exist(err);
  should.exist(result);
  result.should.equal('title_-1');
  done();
});
```

should get all index infos.

```js
db.indexInformation('foo', function (err, result) {
  should.not.exist(err);
  should.exist(result);
  result.should.eql({
    _id_: [ [ '_id', 1 ] ],
    title_1: [ [ 'title', 1 ] ],
    'title_-1': [ [ 'title', -1 ] ] 
  });
  done();
});
```
