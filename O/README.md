# O

## Open–Closed Principle

_Entities should be open for extension, but closed for modification._

###### Example of violating of the principle:

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
     * @param data
     * @param type
     */
    transfer(data, type) {
        switch (type) {
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

let transmitter = new Transmitter()
transmitter.transfer(`(~_~)`, 'http')
```

### Why the code violates the principle ?

...

### What should be done with code to follow the principle ?

...

###### Refactored code to make it follow the principle:

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

let transmitter = new Transmitter()
let protocol = new Http()

transmitter.protocol = protocol
transmitter.transfer(`(".")`)
```

...