# D
## Dependency inversion principle

_Abstractions should not depend on details. Details should depend on abstractions.
High-level modules should not depend on low-level modules. Both should depend on abstractions._
###### Example of violating of the principle:
```js
class Mysql {
    /**
     * @param data {Object}
     */
    mysqlCreate(data) {
        console.log('Mysql create', data)

    }

    /**
     * @param id {number}
     */
    mysqlRemove(id) {
        console.log('Mysql remove', id)

    }

}

class MongoDB {
    /**
     * @param data {Object}
     */
    mongoDBCreate(data) {
        console.log('MongoDB create', data)

    }

    /**
     * @param id {number}
     */
    mongoDBRemove(id) {
        console.log('MongoDB remove', id)

    }

}

class Application {
    /**
     * @param data {Object}
     */
    createPost(data) {
        new Mysql().mysqlCreate(data)

    }

    /**
     * @param id {number}
     */
    removePost(id) {
        new Mysql().mysqlRemove(id)

    }

}

let id = 1
let name = 'Test Name'

let app = new Application()
app.createPost({id, name})
app.removePost(id)
```

### Why the code violates the principle ?
...

### What should be done with code to follow the principle ?
...


###### Refactored code to make it follow the principle:
```js
class Mysql {
    /**
     * @param data {Object}
     */
    mysqlCreate(data) {
        console.log('Mysql create', data)

    }

    /**
     * @param id {number}
     */
    mysqlRemove(id) {
        console.log('Mysql remove', id)

    }

}

class MongoDB {
    /**
     * @param data {Object}
     */
    mongoDBCreate(data) {
        console.log('MongoDB create', data)

    }

    /**
     * @param id {number}
     */
    mongoDBRemove(id) {
        console.log('MongoDB remove', id)

    }

}

/**
 * @abstract
 */
class Adapter {
    constructor() {
        if (new.target === Adapter) {
            throw new Error('Adapter is abstract')

        }

    }

    create() {
        throw new Error('Adapter has to implement create method')

    }

    remove() {
        throw new Error('Adapter has to implement remove method')

    }

}

class MysqlAdapter extends Adapter {
    /**
     * @param data {Object}
     */
    create(data) {
        new Mysql().mysqlCreate(data)

    }

    /**
     * @param id {number}
     */
    remove(id) {
        new Mysql().mysqlRemove(id)

    }

}

class MongoDBAdapter extends Adapter {
    /**
     * @param data {Object}
     */
    create(data) {
        new MongoDB().mongoDBCreate(data)

    }

    /**
     * @param id {number}
     */
    remove(id) {
        new MongoDB().mongoDBRemove(id)

    }

}

class Application {
    _db

    /**
     * @param db {Adapter}
     */
    constructor(db) {
        this._db = db

    }

    /**
     * @param data {Object}
     */
    createPost(data) {
        this._db.create(data)

    }

    /**
     * @param id {number}
     */
    removePost(id) {
        this._db.remove(id)

    }

}

let id = 1
let name = 'Test Name'

let app = new Application(new MongoDBAdapter())
app.createPost({id, name})
app.removePost(id)
```

...