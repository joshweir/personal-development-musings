# My Sinon Cheatsheet

It's a hassle using `mock` because can't inject a spy (or at least I haven't looked far enough to know how to) - use `stub` / `spy`.

## Use `sandbox`, it makes life easier

```javascript
let sandbox;

beforeEach(() => {
  sandbox = sinon.sandbox.create();
});

afterEach(() => {
  sandbox.restore();
});

it('tests something with the stub', () => {
  //myObj.myFunc will be auto restored between tests
  sandbox.stub(myObj, 'myFunc');
});
```

## Stub object method, verify arguments and/or call mock function

### stubbed method calls mock function

Similar to RSpec's:

```ruby
expect(obj).to receive(:func)
.withArgs('arg1', 'arg2')
.and_return(false)
```

```javascript
// stub: obj.func(arg1, arg2) and mock return

import obj from './my/obj';

//spy allows checking arguments and can mock return
const spyReturnsFalse = sinon.spy(() => false);
sandbox
.stub(obj, 'func')
.callsFake(spyReturnsFalse);
const returned = obj.func('arg1', 'arg2');
expect(
  spyReturnsFalse
  .withArgs('arg1', 'arg2')
  .calledOnce
).toBeTruthy();
//assert the mock function return
expect(returned).toBeFalsy();
```

### dont care about stub method's return

Similar to RSpec's:

```ruby
expect(obj).to receive(:func)
.withArgs('arg1', 'arg2')
```

```javascript
// stub: obj.func(arg1, arg2)

import obj from './my/obj';

//spy allows checking arguments and can mock return
const spyReturnsFalse = sinon.spy(() => false);
sandbox
.stub(obj, 'func')
.callsFake(spyReturnsFalse);
obj.func('arg1', 'arg2');
expect(
  spyReturnsFalse
  .withArgs('arg1', 'arg2')
  .calledOnce
).toBeTruthy();
//only difference is you don't assert mock function return
```
