# Find the bug

```javascript
// var input = ...
try {
	var result = computeResult(input);
	displayResult();
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

* Recreating error handling machinery
* Impossible to reason about and maintain
