# Find the bug

```javascript
// var input = ...
try {
	var result = computeResult(input);
	displayResult(result);
} catch(e) {
	displayError(e);
}
```

---
# Find the bug

```javascript
computeResult(input, function(e, result) {
	if(e) {
		displayError(e);
	} else {
		displayResult(result);
	}
});
```

---
# Find the bug

```javascript
computeResult(input, function(e, result) {
	if(e) {
		displayError(e);
	} else {
		try {
			displayResult(result);
		} catch(e) {
			displayError(e);
		}
	}
});
```

---
# Find the bug

```javascript
function getTheResult() {

    try {
        return thisMightFail();
    } catch(e) {
        return recoverFromFailure(e);
    } finally {
        alwaysCleanup();
    }

}
```

---
# Find the bug

```javascript
function getTheResult(nodeCallback) {

    thisMightFail(function(error, result) {
        if(error) {
            try {
                result = recoverFromFailure(error);
                error = null;
            } catch(e) {
                error = e;
            }
        }

        try {
            alwaysCleanup();
        } catch(e) {
            error = e;
        }

        // If error is still falsey, we've succeeded
        nodeCallback(error, result);
    });
}

function thisMightFail(nodeCallback) {
    var result, error;

    // Do work, then:

    if(error) {
        nodeCallback(error);
    } else {
        nodeCallback(null, result);
    }
}
```

---
# Find the bug

```javascript
function getTheResult(onSuccess, onFailure) {
    thisMightFail(
        function(result) {
            try {
                alwaysCleanup(); // finally
                onSuccess(result); // showResult
            } catch(e) {
                onFailure(e); // showError
            }
        },
        function(error) {
            var recoveryResult;
            try {
                recoveryResult = recoverFromFailure(error); // "catch" and recover
                error = null; // successfully handled the error
            } catch(e) {
                error = e; // recovery failed
            }

            try {
                alwaysCleanup(); // finally
            } catch(e) {
                error = e; // cleanup failed
            }

            if(error) {
                // showError if *either* recovery or alwaysCleanup failed
                onFailure(error);
            } else {
                // We dodged all the bullets, showResult
                onSuccess(recoveryResult);
            }
        }
    );
}

function thisMightFail(onSuccess, onFailure) {
    var result, error;

    // Do work, then:

    if(error) {
        onFailure(error)
    } else {
        onSuccess(result);
    }
}
```

---
# The problem

* Recreate flow control and error handling machinery
* Impossible to reason about and maintain

---
# Promises: The solution

## Promises give you async versions of `return` and `throw`

---
# Promises: The solution

```javascript
computeResult(input)
	.then(displayResult)
	.otherwise(displayError);
```

---
# Promises: The solution

```javascript
function getTheResult() {

    return thisMightFail()
        .otherwise(recoverFromFailure);
        .then(alwaysCleanup, alwaysCleanup);
}
```

---
# Promises

* MultiLisp, Act 1, Prolog concurrent logic variables
* Joule and E promises
* Java `java.util.concurrent.Future`
* Python Twisted deferred
* Dojo `Deferred`
* F# `Async<T>`
* .NET `Task<T>`
* C++11 `std::future` and `std::promise`
* Dart `Future<T>`
* Scala `Future`
* Javascript Promises/A+

---
# Promises are building blocks

* map
* reduce
* map/reduce
* sequence/parallel tasks
* promisify other style async APIs

---
# Promises are building blocks

```javascript
var when = require('when');

when.reduce(when.map($.get(url), mapFunc), reduceFunc);
```

---
# Promises are building blocks

```javascript
var nodefn = require('when/node/function');
var fs = require('fs');
var readFile = nodefn.lift(fs.readFile);
var writeFile = nodefn.lift(fs.writeFile);

writeFile('out.txt', readFile('in.txt'));
```

---
# Promise aren't perfect

* VMs/platforms aren't promise-aware
* disjoint or missing stack traces
* when.js and Q can trap and report unhandled rejections
	* both stitch long stack traces

---
# Promise pitfalls

* `return` something!!
* Don't break the chain
* rejection handlers work like `catch`, if you don't rethrow, the error is considered "handled" and won't propagate
	* This is a *good thing*

---
# Code smell

```javascript
promise.then(doSomething);
return promise;
```

---
# Code smell

```javascript
return promise.otherwise(function(e) {
	logError(e);
});
```
