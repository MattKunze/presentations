# Unit Testing

* Matt Kunze
* Software developer at www.datasplice.com
* https://github.com/MattKunze

## Why Test?

* Document usage (executable specifications)
* Fix issues once (prevent regressions)
* Test driven development
  * Make writing tests as easy as not writing them

## Realistic Expectations

* We all suck at writing enough tests
* Be pragmatic
  * High-value tests vs coverage
  * Black/white/gray-box testing
  * Integration vs. unit tests
  * Keep tests as simple as is reasonable

## Moving Parts

* Test Runner
* Testing Framework
* Assertion library

# Arrange - Act - Assert

```javascript
describe('Test Suite', () => {
  it('should add numbers together'), () => {
    expect(1 + 2).to.equal(3)
  })
  it('should do the thing'), () => {
    let foo = service.getSomething('key')
    expect(foo.doTheThing()).to.be.true
  })
})
```

* Test framework provides the structure for defining the tests - `decribe`, `it`
* Assertion library provides syntax for describing expectations
* Test runner executes everything and compiles the results

## Properties of Good Tests

* Tests one thing
* Isolated
* Fast
* Doesn't make unnecessary assertions

## Making Code Easy to Test

* Minimal surface API
* Explicit dependencies
  * Isolate external state and side effects
* Hard to test is code smell

## Runners and Frameworks

* [Mocha](https://mochajs.org/) - full featured, easy to use
* [Jasmine](http://jasmine.github.io/edge/introduction.html) - another popular
  library
* [Karma](https://karma-runner.github.io/0.13/index.html) - powerful test
  runner, works with Mocha, Jasmine, and others
* [Tape](https://github.com/substack/tape) - minimalistic TAP framework

## Assertions

* [expect](https://www.npmjs.com/~mjackson) - simple assertion and spy library
* [Chai](http://chaijs.com/) - provides BDD (`should`, `expect`) and
  TDD (`assert`) variants
* [sinon](http://sinonjs.org/) - comprehensive spy and stub library

```javascript
expect(system.hasWhatever()).to.be.true

expect(service.fetch()).to.deep.equal(['one', 'two', 'three'])


expect(() =>
  cacheResource(null)
).to.throw(/invalid resource/)
```
## Spies, stubs, mocks

* Spies - listen for function calls and hook existing behavior
* Stubs - spies with preprogrammed behavior
* Mocks - encapsulate expectations within stubs

```javascript
it('should call the service with the correct values', () => {
  let stubs = {
    fetchUser: sinon.stub().returns(Promise.resolve())
  }
  let service = serviceFactory(stubs)
  service.cacheUser('test')
  stubs.fetcUser.should.have.been
    .calledOnce
    .and.calledWith('test')
})
```

## Asynchronous Code

Mocha supports two mechanisms for writing tests that resolve asynchronously.
By default tests fail if they take longer than 2 seconds

```javascript
it('should wait for the done callback to be invoked', done => {
  setTimeout( () =>
    expect(...)
    done()
  , 100)
})
it('should support returning Promises for async behavior', () => {
  api.doSomething()
    .then( () => {
      throw new Error('fail whale')
    })
    .catch(error => {
      expect(error.message).to.equal('fail whale')
    })
})
```

## Refactoring

```javascript
import api from './api'

let cache = {}

function getUser(name) {
  if(cache[name]) {
    Promise.resolve(cache[name])
  }
  else {
    api.fetchUser(name)
      .then(results => {
        let userInfo = {
          status: 'success',
          name: name,
          details: results
        }
        cache = Object.assign({}, cache, {
          [name]: userInfo
        })
      })
      .catch(error => {
        cache = Object.assign({}, cache, {
          [name]: { error }
        })
      })
  }
}
export { getUser }
```

```javascript
let cache = {}

function getUser(api, name) {
  if(cache[name]) {
    Promise.resolve(cache[name])
  }
  else {
    api.fetchUser(name)
      .then(cacheUser.bind(null, name))
      .catch(handleUserError.bind(null, name))
  }
}
function cacheUser(name, results, => {
  let userInfo = {
    status: 'success',
    name: name,
    details: results
  }
  cache = Object.assign({}, cache, {
    [name]: userInfo
  })
})
function handleUserError(name, error, => {
  cache = Object.assign({}, cache, {
    [name]: { error }
  })
})
export { getUser, cacheUser, handleUserError }
```
