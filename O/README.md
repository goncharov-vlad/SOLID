# O

## Open–Closed Principle [OCP]

_Entities should be open for extension, but closed for modification_

###### Example of violation of the principle

```js
class Protocol {
    /**
     * @param data {string}
     */
    send(data) {
        console.log(`${this.constructor.name}: ${data}`)

    }

}

class Transmitter {
    /**
     *
     * @param data {string}
     * @param type {string}
     */
    transfer(data, protocol) {
        switch (protocol) {
            case 'ftp':
                (new Ftp()).send(data)
                break
            case 'smtp':
                (new Smtp()).send(data)
                break
            case 'dns':
                (new Dns()).send(data)
                break
            case 'http':
                (new Http()).send(data)
                break
            default:
                throw new Error('Wrong protocol')
                
        }
        
    }

}

class Dns extends Protocol {
}

class Smtp extends Protocol {
}

class Http extends Protocol {
}

class Ftp extends Protocol {
}

const transmitter = new Transmitter()
transmitter.transfer(`(~_~)`, 'http')
```

### Why the code violates the principle?

Because if a new `Protocol` is added

```js
class NewProtocol extends Protocol {
}
```

`Transmitter::transfer()` method needs to be changed to add a new switch condition for that `Protocol`

```js
case 'newProtocol':
    (new NewProtocol()).send(data)
    break
```

to use like

```js
transmitter.transfer(`(~_~)`, 'newProtocol')
```

The code of method `Transmitter::transfer()` has to be changed by introducing new conditions for each added `Protocol`,
which means `Transmitter` is not closed for modification. This is a violation of the principle

### What should be done with code to follow the principle?

Given example is very easy, there are many ways to make it follows the principle like class extending, passing
the entity to the method params instead of the string, dependency injection. I have chosen the last one. By this `Transmitter`
should have `_protocol` property which stores specific `Protocol`
and setter `Transmitter::set protocol()` for that

###### Refactored code to make it follow the principle

```js
class Transmitter {
    /**
     * @property {Protocol}
     * @protected
     */
    _protocol

    /**
     * @param protocol {Protocol}
     */
    set protocol(protocol) {
        this._protocol = protocol

    }

    /**
     * @param data {string}
     */
    transfer(data) {
        if (!(this._protocol instanceof Protocol)) {
            throw new Error('Wrong protocol')

        }

        this._protocol.send(data)

    }

}

class Protocol {
    /**
     * @param data {string}
     */
    send(data) {
        console.log(`${this.constructor.name}: ${data}`)

    }

}

class Dns extends Protocol {
}

class Smtp extends Protocol {
}

class Http extends Protocol {
}

class Ftp extends Protocol {
}

const transmitter = new Transmitter()
const protocol = new Http()

transmitter.protocol = protocol
transmitter.transfer(`(".")`)
```

### In total

After refactoring the code follows the principle: _Objects should be open for extension, but closed for modification._
The `Transmitter` is open to extension through dependency injection to use any type of `Protocol`, and it's closed for
modification because for this no needs to change the code of `Transmitter::transfer()` method