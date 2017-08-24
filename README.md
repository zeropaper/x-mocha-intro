# x-mocha-intro

This exercise is aimed to introduce unit testing with [mocha](https://mochajs.org/) and the [require](http://devdocs.io/node~6_lts/modules#modules_module_require_id) function from Node.js.

__Note:__ at the end of __every steps__, build your code, make a commit (with the step's name) and push to to the working branch (will be named `feat-test`).

## Steps

### Preparation

If you have doubt about the following tasks, refer to the [x-loader-simulation](https://github.com/zeropaper/x-loader-simulation) exercise.

- In the repository of your [x-array-matrix](https://github.com/zeropaper/x-array-matrix) exercise, create a git branch called `feat-test` (`git checkout -b feat-test`).
- Create a file called `.gitignore` in which you will add a line with `node_modules` (this will prevent your `node_modules` to be added to git)
- Create a `src` folder and move your `index.js` and `index.html` in it.
- Create a `package.json` (with the right `npm` command).  
  Answer `mocha` when you will be asked for a "test command" by the wizard.
- Make sure that your project is "private".
- Install the following development dependencies
  - mkdirp
  - cp
  - browserify
  - uglify-js
  - jshint
  - [mocha](https://www.npmjs.com/package/mocha)
  - [expect.js](https://www.npmjs.com/package/expect.js)
- In your `package.json` create the following `script` entries:
  - `prebuild` which:
    - uses `jshint` to lint the `src/*.js` files
    - creates a `docs` folder
    - then copy the `src/index.html` into it
  - `build`: which compiles the `src/index.js` into `docs/bundle.js` with `browserify`
  - `postbuild`: which will minify the `docs/bundle.js`
- Modify your `src/index.html` to load `bundle.js` instead of `index.js`

### Refactoring

Refactoring is an operation in which the some code is changed without changing its functionnalities.

For each functions (except `renderTableDom` if you have it) in your `src/index.js`, create a file named after the function and use `require` to load the file.  
Example with the `createMatrix` function, you will create a file called `create-matrix.js` (in the `src` folder) in which you will have:

````js
module.exports = function createMatrix(rowsCount, columnsCount) {
  /*... */
};
````
(the `module.exports` make it possible for other files to "require" the function)

and in your `src/index.js` you will have
  
````js
var createMatrix = require('./create-matrix'); // you don't need the '.js' here
````
In the end, you should have 8 files for your functions in your `src` folder (and your `index.js`).

### Writing tests

- Create a `test` folder in which you create a file `create-matrix.spec.js` in which you will put the following code:
  
  ````js
  var expect = require('expect.js');
  var createMatrix = require('./../src/create-matrix');
  
  describe('createMatrix', function() {
    it('is a function', function() {
      expect(createMatrix).to.be.a('function');
    });
    
    it('returns a multi-dimensional array', function() {
      var result = createMatrix(1, 1);
      expect(result).to.be.an('array');
      expect(result[0]).to.be.an('array');
    });
    
    it('creates the given amount of entries given by the first argument', function() {
      var result = createMatrix(3, 3);
      expect(result.length).to.be(3);
    });
    
    // YOU ;) complete that one
    it('creates the given amount of entries given by the second argument');
  });
  ````
  
- Run the following command `npm run test`
- Complete the missing test of the file
- Again in the `test` folder, create a file `matrix-increment.spec.js` in which you will put the following code:
  
  ````js
  var expect = require('expect.js');
  var createMatrix = require('./../src/create-matrix');
  var matrixIncrement = require('./../src/matrix-increment');
  
  describe('matrixIncrement', function() {
    it('is a function', function() {
      expect(matrixIncrement).to.be.a('function');
    });

    it('increments the values of the matrix', function() {
      var matrix = createMatrix(2, 2);
      var result = matrixIncrement(matrix);
      expect(result[0][0]).to.be(1);
      expect(result[1][1]).to.be(1);
    });
  });
  
  ````
  And so on for __all__ the functions :) (except the `renderTableDom`).


### Travis

- Copy the `.travis.yml` file from the `loader-simulation` exercise in the your `array-matrix` exercise
- Activate the repository on travis-ci.org
  

### Make it a library

To be able to reuse the array-matrix project functions in other projects in a convenient way:

- Create a file `src/lib.js` which exports an object which has the properties
- Create a test file for the library (`test/lib.spec.js`) and test that:
  - The library is an object
  - Each property of the object is a function
- In your `package.json`
  - Make sure that the name of the package is `array-matrix`
  - Change the `main` entry to `src/lib.js`

<details>
<summary>The library</summary>


````js
module.exports = {
  createMatrix: require('./create-matrix'),
  matrixFillSequence: require('./matrix-fill-sequence'),
  // ...
};
````

</details>


<details>
<summary>The library test</summary>


````js
var expect = require('expect.js');
var library = require('./../src/lib');
describe('array matrix library', function() {
  it('is an object', function() {
    expect(library).to.be.an('object');
  });

  it('has a createMatrix function', function() {
    expect(library.createMatrix).to.be.an('function');
  });
  
  // ...
});
````

</details>


<details>
<summary>Using the library in a other project</summary>

In the `package.json` of your other project:


````js
//...
  "dependencies": {
    "array-matrix": "<your-gh-username>/<name-of-the-repository>"
//...
````

__Note:__ It is important that the name of the dependency (here `array-matrix`) matches with the name of your package (`name` property in the `package.json`).


In the code of your other project:


````js
var arrayMatrixLib = require('array-matrix');
var createMatrix = arrayMatrixLib.createMatrix;
// or
var createMatrix = require('array-matrix').createMatrix;
````

</details>


### Merging to master

