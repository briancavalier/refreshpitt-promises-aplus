1. The problem - async is hard
	1. Where's the bug
		1. sync code
		1. async code
		1. node-style async
	1. Bloat
	1. Reasoning
1. The solution - promises
	1. History
	1. Show equivalent promise example
	1. Give you back
		1. `return` `try/catch/throw`
		1. Reasoning
		1. Refactorability
		1. Testing
	1. Allow mixing sync and async easily
	1. Other examples
1. Promises are building blocks
	1. More advanced examples
		1. map
		1. reduce
		1. composed map/reduce
		1. sequence/parallel
		1. nodefn call/apply/lift
1. Promise pitfalls
	1. "Silent" rejections
		1. Platforms not promise aware (until ES7!)
		1. when.js and Q have unhandled rejection monitoring/reporting
		1. stack traces
	1. Broken promise (chains): Return something!
1. The future
	1. Generators
	1. Proxies/Remotes
1. Promises/A+
	1. Promises/A and friends
		1. Dojo
		1. Gained traction, but
		1. $.Deferred fail
		1. Ember fail
		1. Domenic's "You're Missing the Point of Promises" and test suite
		1. Promises/A+ gist https://gist.github.com/briancavalier/eb5fc157825a170c9957
	1. Promises/A+ was born
		1. Spec
		1. Test Suite
		1. Implementations
		1. DOM Promises
		1. ES7 Promises

