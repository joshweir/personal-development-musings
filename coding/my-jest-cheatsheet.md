# My Jest Cheatsheet

Mocking is way easier than sinon, once an object is mocked, you can spy on its calls as well as mock it's implementation.

### Examples

(The first 2 lines of code below are taken from the official jest mock documentation)

```javascript
const myMock = jest.fn();

myMock
  .mockReturnValueOnce(10)
  .mockReturnValueOnce('x')
  .mockReturnValue(true);

myMock.mockImplementation(() => {
  //mocked function here
});

expect(myMock).toHaveBeenCalledTimes(1);
expect(myMock).toHaveBeenCalledWith(args);

# the .mock method available on all jest mocks provides all the info you could require.
myMock.mock.calls
```

## Mock default import

```javascript
import myFunc from './myModule';

jest.mock('./myModule');

//...

myFunc.mockImplementation(() => {
  //do something
  return 'hello world';
});

expect(myFunc('myArg')).toEqual('hello world');
expect(myFunc).toHaveBeenCalledWith('myArg');
expect(myFunc).toHaveBeenCalledTimes(1);
```

## Mock named imports

See the Mock redux thunks section below, same the thunk is a named import.

## Mock redux thunks

Useful for testing the mapDispatchToProps function for connected components, to assert that the connected component receives the thunk from dispatch. Ensure that the mocked implementation returns a function.

```javascript
import thunk from 'redux-thunk';
import configureMockStore from 'redux-mock-store';
import initialState from '../../tests/helpers/initialState';
import LoginOrRegister from '../../containers/LoginOrRegister';
import { manualLogin } from '../../modules/users/actions';

jest.mock('../../modules/users/actions');
const middlewares = [thunk];
const mockStore = configureMockStore(middlewares);

describe('<LoginOrRegister> container', () => {
  let wrapper;
  let store;

  beforeEach(() => {
    store = mockStore(initialState);
    wrapper = shallow(
      <LoginOrRegister store={store} />
    );
  });

  test('receives manualLogin from dispatch', () => {
    manualLogin.mockImplementation(() => {
      return jest.fn();
    });
    wrapper.props().manualLogin();
    expect(manualLogin).toHaveBeenCalledTimes(1);
  });
});
```

## Mock redux sagas

TODO
