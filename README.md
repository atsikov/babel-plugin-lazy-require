[![Build Status](https://travis-ci.org/princjef/babel-plugin-lazy-require.svg?branch=master)](https://travis-ci.org/princjef/babel-plugin-lazy-require)
[![NPM version](https://img.shields.io/npm/v/babel-plugin-lazy-require.svg)](https://www.npmjs.com/package/babel-plugin-lazy-require)
[![Coverage Status](https://img.shields.io/codecov/c/github/princjef/babel-plugin-lazy-require/master.svg)](https://codecov.io/gh/princjef/babel-plugin-lazy-require)

# babel-plugin-lazy-require

Transform global require statements that run on module load to lazily evaluated
statements that get evaluated when first accessed later on in the file.

This is particularly useful when you have modules that are only needed under
certain conditions and when startup time and/or memory footprint are important.

## Usage

To install:

```
npm install --save-dev babel-cli@6 babel-plugin-lazy-require
```

To run:

```
babel <your-code> --plugins babel-plugin-lazy-require
```

## Options

### ignoreModulesPatters

Optional. An array of RegExps to skip lazy evaluation for matched modules. Useful when module has a side-effect which should be evaluated at initialisation.

## Example

**Input**

```js
// Module is imported here
const someModule = require('some-module');

function myCode() { 
    // Module is only actually used here
    someModule.doSomething();
}
```

**Output**

```js
const _someModule = {
    initialized: false
};

function _imports() {
    return {
        get someModule() {
            if (!_someModule.initialized) {
                _someModule.value = require('some-module');
                _someModule.initialized = true;
            }
            return _someModule.value;
        }
    }
}

function myCode() {
    // Module is imported and used here
    _imports().someModule.doSomething();
}
```
