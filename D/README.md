# D

## Dependency inversion principle [DIP]

_Abstractions should not depend on details. Details should depend on abstractions. High-level modules should not depend
on low-level modules. Both should depend on abstractions_

###### Example of violating of the principle

```js
class MysqlLibrary {
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

class MongoDBLibrary {
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
        new MysqlLibrary().mysqlCreate(data)

    }

    /**
     * @param id {number}
     */
    removePost(id) {
        new MysqlLibrary().mysqlRemove(id)

    }

}

let id = 1
let name = 'Test Name'

let app = new Application()
app.createPost({id, name})
app.removePost(id)
```

### Why the code violates the principle ?

The program uses `MysqlLibrary`, but it is difficult to replace it with `MongoDBLibrary` because `Application` works
directly with them. It means high-level module `Application` depend on low-level modules like `MysqlLibrary`
or `MongoDBLibrary`, in other words, if the program should use `MongoDBLibrary`
instead of `MysqlLibrary`, all places where `MysqlLibrary` is, must be changed for using new interface
of `MongoDBLibrary`. This situation can happen every time when there are multiple ways to do the same

### What should be done with code to follow the principle ?

`MongoDBLibrary` and `MysqlLibrary` must have the same interface, then the high-level code `Application`
will not need to change to use them. For this, it is recommended to create an `AbstractAdapter` as main interface and
implement it with the `MysqlAdapter`, `MongoDBAdapter` classes, where the low-level `MysqlLibrary` and `MongoDBLibrary`
will be used. By that `Application` does not depend on low-level code and uses `AbstractAdapter` interface for any
implementations (polymorphism)

###### Refactored code to make it follow the principle

```js
class MysqlLibrary {
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

class MongoDBLibrary {
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
class AbstractAdapter {
    constructor() {
        if (new.target === AbstractAdapter) {
            throw new Error('AbstractAdapter cannot be instaced directly')

        }

    }

    create() {
        throw new Error('Have to implement create method')

    }

    remove() {
        throw new Error('Have to implement remove method')

    }

}

class MysqlAdapter extends AbstractAdapter {
    /**
     * @param data {Object}
     */
    create(data) {
        new MysqlLibrary().mysqlCreate(data)

    }

    /**
     * @param id {number}
     */
    remove(id) {
        new MysqlLibrary().mysqlRemove(id)

    }

}

class MongoDBAdapter extends AbstractAdapter {
    /**
     * @param data {Object}
     */
    create(data) {
        new MongoDBLibrary().mongoDBCreate(data)

    }

    /**
     * @param id {number}
     */
    remove(id) {
        new MongoDBLibrary().mongoDBRemove(id)

    }

}

class Application {
    _db

    /**
     * @param db {AbstractAdapter}
     */
    constructor(db) {
        if (!(db instanceof AbstractAdapter)) {
            throw new TypeError(`DB has to be instance of AbstractAdapter`)

        }

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

### In total

After refactoring the code follows the principle: _Abstractions should not depend on details. Details should depend on
abstractions. High-level modules should not depend on low-level modules. Both should depend on abstractions._
Now `Application` which is the high-level module doesn't depend on `MysqlLibrary` or `MongoDBLibrary` which are low-level,
both of them depend on `AbstractAdapter` abstraction which doesn't depend on a specific implementation
like `MongoDBAdapter`
or `MysqlAdapter`