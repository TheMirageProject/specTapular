[![SpecTapular Logo](./SpecTapular-Logo.svg?sanitize=true)](https://spectapularjs.com)

<!-- BIG LOGO^  -->
1st Class Async, Declarative Test(s) Runner for Artristocrats

> Tests dripping in sweet, sweet sophistication

<!-- All the Badges goes here -->

SpecTapular is the eldest grandchild child of:

1. Impatience²
2. Declarative Testing
3. 💘 of Progress
4. Spec-style outputs from [mochajs.org#spec](https://mochajs.org#spec) and the [TAP standards](http://testanything.org) for testing output.

<!-- Are love chidren from 3 things the result of a Ménage à trois?? 
If so I can't figure out how to write tests for that.. Err, at least I have not figured out how to get my boss to approve writing those tests -->

Spectapular is an opinionated <!--and cheeky--> test runner/framework that is dripping in class and rugged individualism. Put on your spectacle (no, not your monicle, you guilded railroad barron) and now let's write some useful tests.

C'mon, chin up, and can't you feel the progress, my good chap.

<!-- Animated gif  exmaple  -->

### TL DR;

Installation : `npm i spectapular -D`

Typical Usage : `npx spectapular -v -p "./tests/**/*.tests.js" `


## Contents

- [Why Why Why](#why)
  - [What Is This?](#wat)
  - [Do I Really Need This?](#really)
- [Intall && Use](#)
	- [Installation](#cli)
	- [Configuration](#configuration)
	- [Debugging](#debugging)
- [The Tests](#)
	- [Writing Tests](#writing)
	- [CLI](#cli)
	- [Using Higher Order Tests](#HOTs)
	- [Assertions](#assertions)
	- [API](#api)
- [Other Goodies](#)
	- [Documentation](#documentation)
	- [Reporters](#reporters)
	- [Tips](#tips)
	- [FAQ](#faq)
	- [Recipes](#recipes)
	- [Support](#support)
	- [Roadmap](#roadmap)
- [Related](#related)
- [Links](#links)
- [Team](#team)


### Wait Why? Yeah But Why?

In a world with [AVA](https://github.com/avajs/ava), [Mocha](https://github.com/mochajs/mocha), [Jasmine](https://github.com/jasmine/jasmine), [Jest](https://github.com/facebook/jest), [tape](https://github.com/substack/tape), [tap](https://github.com/tapjs/node-tap), [scripts](https://github.com/qunitjs/qunit), [scraps](https://github.com/hapijs/lab), and [give a dog a bone](https://github.com/search?q=test+runner) - why on earth make another test runner?

Some reasons in no particular order:

1. Given the opportunity to skimp on tests humans tend to take that opportunity
2. Some of the frameworks listed ( each amazing in their own right) creates enough overhead for a "hypothetical friend of mine" to make the mental justifcation of writing tests "tomorrow"
3. I am extremely impatient and think that my laptop should wait for me to type - and not me wait for some network request to complete before the next test starts. Maybe someone else out there thinks this way too?
4. I want the framework to encourage good practices - like pure|stateless functions, atomic tests
5. I wanted a testing tool that would play nice with other tools - by producing or consuming testing output.
6. I wanted something that had a nice summary view - but not too summarized
7. I wanted something that let me quickly see if everything was passing - and only if not then drill into something more verbose - and for that to actually be fun.
8. Because I could.
9. The list could keep going...

#### What is this?

Spectapular is a nodejs testing framework that aims to be:

1. Simple
2. Pretty Fast
3. Maybe even just a little bit fun

Useful tests are good! 

Tests are the way that we codify the hard lessons fought and won in production about race conditions, and other logical errors that occur create fire-drills in production.

Spectapular follows in the footsteps of `testmatrix` and `tead` that strip down testing frameworks to be stupidly simple. And thats when I realized that I do actually enjoy writing tests.


#### Do I Really Need this? 

coming soon...

### Installation

Reuirements:

- Nodejs version >= 7.6 

Initialization works with npm and Yarn, but running npx requires npm@5.2.0 or greater to be installed. Otherwise, you'll have to manually install ava and configure the test script in your package.json as per above:

	$ npm install --save-dev --save-exact ava@next

Or if you prefer using Yarn:

	$ yarn add ava@next --dev --exact

### Writing Tests

SpecTapular is heavily inspired by [`testmatrix`](https://github.com/jorgebucaran/testmatrix) and [`tead`](https://github.com/teadjs/tead). For me the ideas there are the logical conclusion to what AVA started in my brain.

Tests in Spectapular follow the protocol laid out by `testmatrix`. Where if you have ever written "fixtures" for your tests - you would now be about 95% done.

So what do tests look like now?

Lifted from the `testmatrix` documentation:

	exports.default = {
	  "array.indexOf()": [
	    {
	      test: "returns the index at which a given element is in the array",
	      assert: (a, b) => a === b,
	      actual: ["A", "B", "C"].indexOf("A"),
	      expected: 0
	    },
	    {
	      test: "returns the index at which a given element is in the array",
	      assert: (a, b) => a === b,
	      actual: ["A", "B", "C"].indexOf("C"),
	      expected: 2
	    }
	  ]
	}

1. Each test file exports a test suite object to `exports.default`
2. The keys of that test suite object are the name of a test group
3. The the test group has an array of tests
4. The each test in that array is an object that includes:
	1. name: "The Description of the Test"
	2. 	assert: The function that will be used to evaluate if actual and expected are the same.
	3. actual: Excercise your code base here
	4. 	expected: plop down what your test case should evaluate to
	
#### Using a TestGen Function (or Higher-Order-Test-Functions)
<!-- Hardly resisting the urge to call them HOTs -->
Sometimes its nice to clean up your test groups, especially if you want some short hand to always use a certain assert, or you want a short, easy typable way to give inputs that are then passed into your `actual` function/value.

Consider the example `Slow Double Test` generator

	const SlowDoubleTest = ({name, input, slowness=300, expected }) => {
	    let sleep = (ms)=>{
	        return new Promise(resolve => setTimeout(resolve, ms));
	    }
	    
	    let doubler = async (_input)=>{ await sleep(slowness); return 2*_input;}
	    return {
	        name, 
	        assert: equal,
	        actual: doubler(input),
	        expected
	    }
	}

it would be used in a `test.js` file like this:

	exports.default = {
		"Double Little Doubles (2.3s)":[
	        SlowDoubleTest({
	            name: "(1) Slowly do 2x(4.0)=8.0",
	            slowness: 2300,
	            input: 4,
	            expected: 8
	        }),
	        SlowDoubleTest({
	            name: " (2) Slowly do 2x(12.0)=24.0",
	            slowness: 2300,
	            input: 12.0,
	            expected: 24.0
	        })
	    ]
	}

Again, this format is the brain child of @jorgeburchan & @okwolf with `testmatrix` and `tead` respectively.

### Running Tests

Send those tests off for an invigorating race to the finish line, with my preferred way to start the test sprint:

`npx spectapular -c`

The `-c` is a flag for the "clipboard" it copies the command for a verbose re-run of just the failing tests. So spectapular assumes your tests will pass (Ha, right) but when they don't you can dig in on just the files that have failing tests.

So assume you have 42 test files, and only 2 of them have failing tests - the `-c` will show you the command to re-run with `-v` or `-verbose` mode enabled.

### CLI

```
NAME : SpecTapular

DESCRIPTION

...the SPEC part of the name is based on the 'Spec' test reporter from @mochajs.
Albiet this reporter has a functional-programing (fp) implementation flavor,
the output styling gives a solid hat-tip to the console styles of mochajs.org#spec .
Spectapular also comes with a batteries included "summary table" display, and programatic
extension points for more hacking, and work flow integraitons
...the TAP part of the name is inspired by @testmatrix and @tead
- that consume very declarative tests and output a standards based TAP format.
So in honor of the TAP stadards, spectapular can consume TAP output
and format it into Spec style too

MODES

- main exec:  Spectapular can be the test runner as in the first examples below
- unix pipe:  or spectapular can be a test reporter/formatter as in the last example below


Basic Options:

-p | -pattern
         @default:'./tests/**/*.test.js'
         This glob pattern is used to discover your test cases on the filesystem
-v | -verbose
         @default:'false'
         Is used to view the pretty Spec-style output of from every
            test file, group, and test case in the pretty mochajs.org#spec style
-d | -diagnostic
         @default:'false'
         Is a little helper to see mistakes you might have made in your glob pattern
-c | -autoClipboard
         @default:'false'
         (NOT IMPLEMENTED YET) - "follow-on" commands will be placed on the clipboard
            for convenience, so that in the case of a failed test set - you can quickly
            rerun again with verbose mode on just the failed tests
            you have it ready to hit paste
-w | -watch
         @default:'false'
         Watch mode re-runs the any test reachable via the configured test pattern
            triggered by a change on the filesystem within the current working dir.
-h | -help
         @default:'false'

EXAMPLES

- Use VERBOSE mode:                         $> node spectapular.js -v 
- Use WATCH & VERBOSE mode:                 $> node spectapular.js -vw 
- Set the test pattern                      $> node spectapular.js -p "../tests/**/*.js" 
- Kithen Sink (careful of your clipboard)   $> node spectapular.js -vdw -p "../tests/**/*.js"
- TAP PIPE                                  $> npx testmatrix great.tests.js | spectapular.js -v
```

### Debugging

1. Sometimes your glob pattern is not right... add the `-d` flag and re-run
2. 

### Configuration

All the configuration for Spectapular is handled on the command line for now. But if needed, it could be expanded to handle a config file or ENV variables. 

Submit an issue or send a PR if you think this would be useful.

Where the order of precedance would likely be : 

Highest Precedance:

1. Command Line
2. ENV VARS
3. File
4. Hard coded value (Lowest)

### API

Right now the API assumes you are messing with the source code of SpecTapular. This is getting re-thought so that you can extend SpecTapular without changing its own source code.

### Inspiration

`Declarative Programing` is when you say "Be This" as opposed to `Imperative Programming` that says "Do it This Way". HTML or SQL are likley the most widely adopted versions of Declarative styles. For a declarative style to exist - there is an implicit idea of an engine running the function calls to make the "THIS" you asked for. 

	SELECT COUNT(*) FROM CUSTOMERS WHERE STATE = "TEXAS" AND COUNTRY="US"

vs

	let count = 0
	for(let i=0; i < CustomerArray ; i++){
		let customer = CustomerArray[i];
		if(customer.state === 'TEXAS' && customer.country === "US) count++
	}

### Assertions

Most people will be good with `equal` and `deepEqual` assertions. But the beauty is that you can write what ever function you need to compare the `actual` and `expected` 

	yourAssertionFunction(actual, expected)=>{
		let comparison = false;
		// do some comparing and set comparison to true if applicable
		return comparison
	}

Assertions are a nice and flexible part here you can even include your own for example if you are making a function that produces arrays of randomized numbers.

you could: 

	[{
	  name: "Randomized Array Length Check"
	  assert: (a, e)=>{a.length === e.length}
	  actual: randomizer(5).length
	  expected: 5
	}]

or you could

	[{
	  name: "Randomized Array Length Check"
	  assert: (a, e)=>{a.length === e.length}
	  actual: myRandomizingArray(5)
	  expected: [1,2,3,4,5]
	}]

or still you could use a Higher Order Test

	[	RandomizedArray({
	   		name: "A Test Using a Higher Order Test Function",
	   		inputLength: 2+3,
	   		expected: 5
		})
	]

### Tips

### FAQ

### Support

### Related

### Links

### Team

#### License
Spectapular is MIT licensed. See LICENSE.
