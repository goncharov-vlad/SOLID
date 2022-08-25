# L

## Liskov Substitution Principle [LSP]

_Objects in the program must be replaceable with instances of their subtypes without changing the correctness of the
program execution_

###### Example of violating of the principle

```js
class Warehouse {
    /**
     * @property {{items: string[], isPickup: boolean}}
     * @protected
     */
    _productsData = {
        items: ['west_product_1', 'west_product_2', 'north_product_1'],
        isPickup: false

    }

    /**
     * @return {{items: string[], isPickup: boolean}}
     */
    get productsData() {
        return this._productsData
        
    }

}

class WestWarehouse extends Warehouse {
    /**
     * @property {{items: string[], isPickup: boolean}}
     * @protected
     */
    _productsData = {
        items: ['west_product_1', 'west_product_2'],
        isPickup: true

    }

}

class NorthWarehouse extends Warehouse {
    /**
     * @property {{items: string[], isPickup: boolean}}
     * @protected
     */
    _productsData = {
        items: ['north_product_1'],
        isPickup: true

    }

}

class SouthWarehouse extends Warehouse {
    /**
     * @property {{list: string[], delivery: boolean}}
     * @protected
     */
    _productsData = {
        list: [],
        delivery: false

    }

}

/**
 * @param products {Object}
 */
function printProductData(products) {
    if (products.hasOwnProperty('list') && products.hasOwnProperty('delivery')) {
        console.log('Products: ', products.list, 'Pick Up:', !products.delivery)

        return

    }

    console.log('Products: ', products.items, 'Pick Up: ', products.isPickup)

}

const warehouse = new Warehouse()
const southWarehouse = new SouthWarehouse()
const westWarehouse = new WestWarehouse()
const northWarehouse = new NorthWarehouse()

printProductData(warehouse.productsData)
printProductData(southWarehouse.productsData)
printProductData(westWarehouse.productsData)
printProductData(northWarehouse.productsData)
```

### Why the code violates the principle ?

The if condition of function `printProductData()` tells about the problem. `SouthWarehouse` is subtype of `Warehouse`
but its property `SouthWarehouse::_productsData` has different data structure from other subtypes. This is the reason
why function `printProductData()` has to have the if condition to check the data structure, otherwise code throws error

### What should be done with code to follow the principle ?

Need to remake data structure of `SouthWarehouse::_productsData` property to the same as in other subtypes
of `Warehouse`. After it's done the if condition can be removed from `printProductData()` function

###### Refactored code to make it follow the principle

```js
class Warehouse {
    /**
     * @property {{items: string[], isPickup: boolean}}
     * @protected
     */
    _productsData = {
        items: ['west_product_1', 'west_product_2', 'north_product_1'],
        isPickup: false

    }

    /**
     * @return {{items: string[], isPickup: boolean}}
     */
    get productsData() {
        return this._productsData
        
    }

}

class WestWarehouse extends Warehouse {
    /**
     * @property {{items: string[], isPickup: boolean}}
     * @protected
     */
    _productsData = {
        items: ['west_product_1', 'west_product_2'],
        isPickup: true

    }

}

class NorthWarehouse extends Warehouse {
    /**
     * @property {{items: string[], isPickup: boolean}}
     * @protected
     */
    _productsData = {
        items: ['north_product_1'],
        isPickup: true

    }

}

class SouthWarehouse extends Warehouse {
    /**
     * @property {{items: string[], isPickup: boolean}}
     * @protected
     */
    _productsData = {
        items: [],
        isPickup: true

    }

}

/**
 * @param products {{items: string[], isPickup: boolean}}
 */
function printProductData(products) {
    console.log('Products: ', products.items, 'Pick Up: ', products.isPickup)

}

const warehouse = new Warehouse()
const southWarehouse = new SouthWarehouse()
const westWarehouse = new WestWarehouse()
const northWarehouse = new NorthWarehouse()

printProductData(warehouse.productsData)
printProductData(southWarehouse.productsData)
printProductData(westWarehouse.productsData)
printProductData(northWarehouse.productsData)
```
### In total

After refactoring the code follows the principle: _Objects in the program must be replaceable with instances of their
subtypes without changing the correctness of the program execution._
`Warehouse` can be replaced by all its subtypes without error and hardcode of workaround