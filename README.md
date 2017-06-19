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
![alt text](https://github.com/justin-sb-fl/JS-Unit-Testing/blob/master/img/unit-test-output.jpg)
![alt text](/img/unit-test-output.jpg "Sample Unit Test Output")

---
### Chai
- coming soon

---
### Sinon
- coming soon


   [Mocha Documentation]: <https://mochajs.org/#getting-started>
