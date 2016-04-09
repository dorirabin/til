# Fake clock with Sinon

Test subject

```javascript
module.exports = {
	getTask: function() {
		return {
			creationTime: new Date()
		};
	}
};
```

Test

```javascript
require('mocha');
var chai = require('chai');
var sinon = require('sinon');

chai.should();

describe('TestSubject', function() {
	var testSubject;
	var MOCK_CURRENT_DATETIME = new Date(2016, 1, 1, 0, 0, 0);

	before(function() {
		clock = sinon.useFakeTimers(MOCK_CURRENT_DATETIME.getTime(), 'Date');
		testSubject = require('../src/TestSubject');
	});

	after(function() {
		clock.restore();
	});

	describe('#getTask', function() {
		it('should return task with creation time', function() {
			testSubject.getTask().should.eql({
				creationTime: MOCK_CURRENT_DATETIME
			}); // use eql instead of equal to compare value
		});
	});
});
```