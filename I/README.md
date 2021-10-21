# I
## Interface Segregation Principle

_Code should not be forced to depend upon interfaces that they do not use_

###### Example of violating of the principle:
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
        if (new.target === User) {
            throw new TypeError("Cannot construct AbstractUser instance directly")

        }

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

class PostReader extends AbstractUser {
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

class Admin extends AbstractUser {
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
...

### What should be done with code to follow the principle ?
...


###### Refactored code to make it follow the principle:
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
        if (new.target === User) {
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

...