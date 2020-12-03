# Error handling with a React higher-order component (HOC)

[React Error Boundaries](https://ko.reactjs.org/docs/error-boundaries.html)

### HOC file(`ErrorBoundary/index.tsx`)
- 에러가 있으면 ErrorPage Component를 렌더링해주는 HOC
- 없으면 Component 그대로 전달

```ts
import React from 'react';
import ErrorPage from './ErrorPage';

type StateProps = {
  hasError: boolean;
  error: Error;
  errorInfo: object;
  componentProps: object;
};

export default function withErrorBoundary(Component: React.FunctionComponent) {
  return class extends React.Component<object, StateProps> {
    constructor(props: object) {
      super(props);
      this.state = {
        hasError: false,
        error: null,
        errorInfo: null,
        componentProps: {},
      };
    }

    static getDerivedStateFromProps(props: object, state: StateProps) {
      return { ...state, componentProps: props };
    }

    componentDidCatch(error: Error | null, errorInfo: object) {
      // handle errors
      // console.error(error);
      // console.error(errorInfo);
      this.setState({ error, errorInfo, hasError: true });
    }

    render() {
      const { hasError, componentProps } = this.state;
      if (hasError) {
        return <ErrorPage />;
      }
      return <Component {...componentProps} />;
    }
  };
}
```

### HOC file(`ErrorBoundary/ErrorPage.tsx`)
- 간단한 Error page component
```ts
import React from 'react';
import { Typography, makeStyles } from '@material-ui/core';

const useStyles = makeStyles({
  errorPage: {
    display: 'flex',
    flexDirection: 'column',
    height: '100vh',
    alignItems: 'center',
    justifyContent: 'center',
  },
});

type PropTypes = {};

const ErrorPage: React.FunctionComponent<PropTypes> = () => {
  const classes = useStyles({});
  return (
    <div className={classes.errorPage}>
      <Typography variant="h2">500</Typography>
      <Typography variant="h5">Unexpected Error Occured.</Typography>
    </div>
  );
};

export default ErrorPage;
```

### `App.tsx`
- 컴포넌트별로 더 세세하게 컨트롤하는게 좋지만 일단 App에다 작업하면 다잡을수있다
```ts
import React from 'react';
import { HashRouter as Router } from 'react-router-dom';

import withErrorBoundary from './molecules/ErrorBoundary';
import PageLayout from './layout/PageLayout';


const App: React.FunctionComponent = () => {
  return (
    <Router basename="/">
      <PageLayout />
    </Router>
  );
};

export default withErrorBoundary(App); //여기!

```
