## page 뒤로가기 버튼 만들기 (typescript)

### Page1 -> DetailPage 또는 Page2 -> DetailPage로 이동 후 뒤로가기 버튼 동작구현

- mouse over시 어디로 돌아갈지 보여줌(attr: title)
  - ex. 'Back to page1' or 'Back to page2'
- ReactRouter history 사용

`Page1, Page2`

```js
//page1에서는 fromPage1: true, page2에서는 fromPage2: true로 설정
<Link className={classes.root} component={RouterLink} to={{ pathname: `/detailPage`, state: { fromPage1: true } }}>
  {label}
</Link>
```

`DetailPage`

```jsx
interface RouterParams {
  fromPage1?: string; //string만 가능함
  fromPage2?: string;
}

interface PropTypes extends RouteComponentProps<RouterParams, any, RouterParams>

//button onClick에 binding
const backButton = React.useMemo(() => {
  const props: {
    path?: string;
    title: string;
  } = { title: 'Back to Previous Page' };
  if (location.state) {
    if (location.state.fromPage1 === true) {
      props.title = 'Back to page1';
    }
    if (Boolean(location.state.fromPage2) === true) {
      props.title = 'Back to page2';
    }
  }
  return props;
}, [location]);
```
