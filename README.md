# JS Unit Testing

- Here is some info to speed up getting started with unit testing
- The 3 primary JS libraries we are using are Mocha, Chai, and Sinon. They all work together and serve their own purpose

### Mocha

- Testing framework that runs on Node.js
- Check out [Mocha Documentation] to get started

##### Defining a test suite
- Suites are defined with 'describe'
- They act as a container for individual tests, providing structure/organization and scope
```javascript
describe('My Test Suite', function() {
    // Testing takes place here
    // Test suite names should be descriptive to simplify testing/debugging
    // Test suites can be nested
    describe('My 2nd Test Suite', function() {
        // Drill down to more specific tests
        // Creates child scope
    });
  });
});
```

##### Testing
- Tests are defined with 'it' and will pass or fail
- With some assertion libraries, a 3rd param can be included as added description for test
```javascript
describe('Hello World', function() {
    it('Should return Hello', function(done) {
        // assert statements are one method for validating tests
        // Node.js contains an assert library that can be used
        // There are serveral other popular assertion libraries, like Chai
        assert.equal(helloWorld(), 'Hello');
   });
  });
});
```

##### Running Test
Several methods for running your tests
- Run 'mocha' from terminal
- Include a test script in the package.json file and run 'npm test' from terminal
- Include in Gruntfile

##### Results
- Results can be reviewed in terminal or in browser
![alt text](/img/unit-test-output.jpg "Sample Unit Test Output")

---
### Chai
- An assertion library for node and browser that is used to define the literal test conditions
- Able to be paired with any javascript testing framework
- Assertion styles/format options can be read at [Chai Documentation]

##### Style 1: Assert Examples
- Just a few examples of chai assertions, many more can be found in Documentation
- Optional 3rd param can be provided as a message to display on testing output
```javascript
var chai = require('chai');
var assert = chai.assert;

assert.typeOf('foo', 'string');  // true
assert.equal(10, 5);  // false, 10 != 5
assert.lengthOf('foo', 3, 'string foo has length of 3');  // true
assert.strictEqual(1, true); // false, 1 !== true
```

##### Style 2: Expect Exampels
- Expect is part of Chai's BDD style and uses a chaining language to construct assertions
- Also allows for optional message param
```javascript
var chai = require('chai');
var expect = chai.expect;
var obj = { a: 5 }

expect('foo').to.be.a('string');  // true
expect(5).to.equal(10);  // false
expect(1, '1 does not equal 2').to.not.equal(5) // true, with optional message
expect(obj).to.have.property(a);
```




---
### Sinon
- Utility for simplifying JS testing, can be used with any testing framework
- Provides Spies, Stubs, Mocks, and more for simplifying complicated/unpredictable code
- More info at [Sinon Documentation]

##### Spies
- Test spies are functions that record arguments, return values, 'this', and exceptions
- Useful for testing internal functions or callback functions
```javascript
var callback = sinon.spy();

testFunction(obj1, callback);

assert.equal(callback.called, true);  // will fail if testFunction failed to call callback
```
- spies can also be used to monitor existing methods on objects by wrapping them with spy functionality
```javascript
sinon.spy(jQuery, "ajax");
jQuery.getJSON("/some/resource");
 assert(jQuery.ajax.calledOnce);
assertEquals("/some/resource", jQuery.ajax.getCall(0).args[0].url);
assertEquals("json", jQuery.ajax.getCall(0).args[0].dataType);
```

##### Stubs
- Stubs are functions with pre-programmed behavior
- Can be anonymous or wrap existing functions, similar to spies

Anonmyous Stub:
```javascript
var stub = sinon.stub();
```
Replace Object.method with a stub:
```javascript
var stub = sinon.stub(object, "method");
```

Important - restore the function to original state when finished
```javascript
stub.restore()
```

- Very useful when required to force a function down a specific path, such as throwing an error or calling a callback with specific parameters
- Often necessary to achieve 100% test coverage, as we need to force functions down specific conditional paths


### Example Test Suite

This example test suite is testing functionality of a database helper
```javascript
  // Testing utils.js function findOne(model, args, callback)
  describe('app/utils.findOne()', function() {
    // Nested test suites are used to organize tests and improve readability
    // This test suite is using Sinon.stub to re-define the model.get call
    // This test suite is not returning an error on the callback
    // A spy is used to verify that callback was called properly
    describe('model.get stub with no error', function() {
      var dbResult = {
        data: 'some data returned from dynamodb'
      };
      var stub,
        callback;

      beforeEach(function() {
        stub = sinon.stub(dynamoose.models.DEV_EmbedHash, 'get', function(args, cb) {
          cb(null, dbResult);
        });
        callback = sinon.spy();
      });

      afterEach(function() {
        stub.restore();
      });

      it('should return item from db and no error', function(done) {
        utils.findOne(EmbedHash, 'args', callback);
        expect(callback.args[0][0] === null && callback.args[0][1] !== null).to.equal(true);
        done();
      });

      it('should not respond if no callback is provided', function(done) {
        utils.findOne(EmbedHash, 'args');
        expect(callback.called).to.equal(false);
        done();
      });

    });

    // This test suite forces error on callback
    describe('model.get stub with error', function() {
      var stub,
        callback;

      beforeEach(function() {
        stub = sinon.stub(dynamoose.models.DEV_EmbedHash, 'get', function(args, cb) {
          cb('error mssg');
        });
        callback = sinon.spy();
      });

      afterEach(function() {
        stub.restore();
      });

      it('should return an error and no db item', function(done) {
        utils.findOne(EmbedHash, 'args', callback);
        expect(callback.args[0][0] !== null && callback.args[0][1] === undefined).to.equal(true);
        done();
      });

      it('should not respond if no callback is provided', function(done) {
        utils.findOne(EmbedHash, 'args');
        expect(callback.called).to.equal(false);
        done();
      });

    });

    // This test suite forces the model.get method to throw an error
    describe('model.get stub throws error', function() {
      var stub,
        callback;

      beforeEach(function() {
        stub = sinon.stub(dynamoose.models.DEV_EmbedHash, 'get').throws();
        callback = sinon.spy();
      });

      afterEach(function() {
        stub.restore();
      });

      it('should callback with message object', function(done) {
        utils.findOne(EmbedHash, 'args', callback);
        expect(typeof callback.args[0][0]).to.equal('object');
        done();
      });

      it('should not respond if no callback is provided', function(done) {
        utils.findOne(EmbedHash, 'args');
        expect(callback.called).to.equal(false);
        done();
      });
    });
  });
```


[Mocha Documentation]: <https://mochajs.org/#getting-started>
[Chai Documentation]: <http://chaijs.com/guide/styles/>
[Sinon Documentation]: <http://sinonjs.org/releases/v2.3.5/>
