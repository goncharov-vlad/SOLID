# I

## Interface Segregation Principle

_Code should not be forced to depend upon interfaces that they do not use_

###### Example of violating of the principle

```js
class User {
    /**
     * @property {string}
     * @protected
     */
    _firstname

    /**
     * @property {string}
     * @protected
     */
    _lastname

    /**
     * @return {_firstname}
     */
    get firstname() {
        return this._firstname

    }

    /**
     * @return {_lastname}
     */
    get lastname() {
        return this._lastname

    }

    /**
     * @param firstname {string}
     * @param lastname {string}
     */
    constructor(firstname, lastname) {
        this._firstname = firstname
        this._lastname = lastname

    }

    /**
     * @param post
     */
    createPost(post) {
        //... implementation

    }

    /**
     * @param post
     */
    updatePost(post) {
        //... implementation

    }

    /**
     * @param post
     */
    deletePost(post) {
        //... implementation

    }

    /**
     * @param post
     */
    readPost(post) {
        //... implementation

    }

}

class PostReader extends User {
    /**
     * @param post
     */
    createPost(post) {
        throw new Error('PostReader cannot create post')

    }

    /**
     * @param post
     */
    deletePost(post) {
        throw new Error('PostReader cannot delete post')

    }

    /**
     * @param post
     */
    updatePost(post) {
        throw new Error('PostReader cannot update post')

    }

}

class Admin extends User {
    /**
     * @param post
     */
    approvePost(post) {
        //... implementation

    }

    /**
     * @param post
     */
    disapprovePost(post) {
        //... implementation

    }

}
```

### Why the code violates the principle ?

`PostReader` has need methods and properties by inhering `User` but after inheriting it also has unnecessary methods
like `PostReader:createPost()`
, `PostReader:updatePost()` and `PostReader:deletePost()`, this reason why they are redefined to throw errors. So, the
problem is that class `PostReader` is dependent on the interface of `User` that it can't use but `Admin` and `User` can

### What should be done with code to follow the principle ?

Apply the principle and split `User` into two: `AbstractUser` as the main one and `PostEditor` for those who can edit
posts. Then, it's possible to make class `PostReader` without unnecessary methods and make a combination
of `AbstractUser` and `PostEditor` classes using ES6 mixins

###### Refactored code to make it follow the principle

```js
/**
 * @abstract
 */
class AbstractUser {
    /**
     * @property {string}
     * @protected
     */
    _firstname

    /**
     * @property {string}
     * @protected
     */
    _lastname

    /**
     * @return {_firstname}
     */
    get firstname() {
        return this._firstname

    }

    /**
     * @return {_lastname}
     */
    get lastname() {
        return this._lastname

    }

    /**
     * @param firstname {string}
     * @param lastname {string}
     */
    constructor(firstname, lastname) {
        if (new.target === AbstractUser) {
            throw new TypeError("Cannot construct AbstractUser instance directly")

        }

        this._firstname = firstname
        this._lastname = lastname

    }

}

class PostReader extends AbstractUser {
    /**
     * @param post
     */
    readPost(post) {
        //... implementation

    }

}

class PostEditor extends PostReader {
    /**
     * @param post
     */
    createPost(post) {
        //... implementation

    }

    /**
     * @param post
     */
    deletePost(post) {
        //... implementation

    }

    /**
     * @param post
     */
    updatePost(post) {
        //... implementation

    }

}

function AdminExtending() {
    /**
     * @class Admin
     */
    return class AbstractUser extends PostEditor {
        /**
         * @param post
         */
        approvePost(post) {
            //... implementation

        }

        /**
         * @param post
         */
        disapprovePost(post) {
            //... implementation

        }

    }

}
```

### In total

After refactoring the code follows the principle: _Code should not be forced to depend upon interfaces that they do not
use._
Using the principle of interface separation, the `User` class is split into two: `AbstractUser` and `PostEditor`.
`PostReader` is now independent of an interface that it doesn't need, and the `AdminExtending()` function can be called
to mix the `AbstractUser` and` PostEditor` classes to have the full functionality of the admin class
