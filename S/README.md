# S

## Single-Responsibility Principle

**Each class has its own single responsibility**.

Below example of violating of Single-Responsibility Principle:
```js
class Product {
    /**
     * @property {string}
     * @protected
     */
    _name

    /**
     * @property {number}
     * @protected
     */
    _price

    /**
     * @return {_name}
     */
    get name() {
        return this._name
    }

    /**
     * @return {_price}
     */
    get price() {
        return this._price

    }

    /**
     * @param name {string}
     * @param price {number}
     */
    constructor(name, price) {
        this._name = name
        this._price = price

    }

    /**
     * @return {{price: Product.price, name: Product.name}}
     */
    formatToJson() {
        let name = this.name
        let price = this.price

        return {name, price}

    }

    /**
     * @return {string}
     */
    formatToHtml() {
        let name = this.name
        let price = this.price

        return `<b>name: </b>${name}<br><b>price: </b>${price}`
    }

}

let product = new Product('Phone', 10)

let html = product.formatToHtml()
let json = product.formatToJson()

console.log(html)
console.log(json)
```

The mistake is that `Product::formatToHtml()` and `Product::formatToJson()` methods cannot be part of the class.
These methods need to be moved from a `Product` class to another place, because formatting is another responsibility.
Let's be "another place" is class with name `Formatter`

Below refactored code to make it follow the principle:
```js
class Product {
    /**
     * @property {string}
     * @protected
     */
    _name

    /**
     * @property {number}
     * @protected
     */
    _price

    /**
     * @return {_name}
     */
    get name() {
        return this._name
    }

    /**
     * @return {_price}
     */
    get price() {
        return this._price

    }

    /**
     * @param name {string}
     * @param price {number}
     */
    constructor(name, price) {
        this._name = name
        this._price = price

    }

}

class Formatter {
    /**
     * @param product {Product}
     * @return {{price: Product.price, name: Product.name}}
     */
    formatToJson(product) {
        let name = product.name
        let price = product.price

        return {name, price}

    }

    /**
     * @param product {Product}
     * @returns {string}
     */
    formatToHtml(product) {
        let name = product.name
        let price = product.price

        return `<b>name: </b>${name}<br><b>price: </b>${price}`
    }

}

let product = new Product('Phone', 10)
let formatter = new Formatter()

let html = formatter.formatToHtml(product)
let json = formatter.formatToJson(product)

console.log(html)
console.log(json)
```

After refactoring, the code is following the principle: _each class has its own single responsibility_. 

Responsibility of `Product` is to make and give data of product, responsibility of `Formatter` is format the data.
