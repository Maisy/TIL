# cancel a request (axios)

[document](https://github.com/axios/axios#cancellation)

- request한 API를 cancel하고싶다
- `CancelToken`을 사용!

## axios

```js
const cancelSource = (() => {
  const CancelToken = axios.CancelToken;
  return CancelToken.source();
})();

export const cancelRequest = () => {
  return cancelSource.cancel();
};

export const getAPI = (url, param) => {
  return axios.get(url, { param, cancelToken: cancelSource.token });
};
```

### redux

```js
const CANCEL_REQUEST_ACTION = createAction('GET_DATA_LIST_CANCEL');

function* sagaCancelRequest() {
  try {
    yield call(API.cancelRequest);
  } catch (err) {
    console.error(err); //TODO: error handler
  }
}

// action, saga
function* cancelSagaFunc() {
  yield takeEvery(CANCEL_REQUEST_ACTION, sagaCancelRequest);
}
```
