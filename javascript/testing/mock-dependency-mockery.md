# Mock dependency with Mockery

Test Subject

```javascript
var userRepository = require('./userRepository');

module.exports = {
	addUser: function(user) {
		user.lastName = user.lastName.toUpperCase();
		userRepository.persist(user);
	}
};
```

Test

```javascript
require('mocha');
var chai = require('chai');
var sinon = require('sinon');
var mockery = require('mockery');

chai.should();

describe('TestSubject', function() {
	var testSubject, persistSpy;

	before(function() {
		mockery.enable({
			warnOnReplace: false,
			warnOnUnregistered: false,
			useCleanCache: true
		});

		persistSpy = sinon.spy();
		mockery.registerMock('./userRepository', {
			persist: persistSpy
		});

		testSubject = require('../src/TestSubject');
	});

	after(function() {
		mockery.disable();
	});

	describe('#addUser', function() {
		it('should persist user with upper case last name', function() {
			var FIRST_NAME = 'Franz';
			var LAST_NAME = 'Wong';
			var LAST_NAME_UPPER = 'WONG';

			testSubject.addUser({
				firstName: FIRST_NAME,
				lastName: LAST_NAME
			});

			persistSpy.calledOnce.should.be.true;
			persistSpy.calledWith({
				firstName: FIRST_NAME,
				lastName: 'LAST_NAME_UPPER'
			}).should.be.true;
		});
	});
});

```