# debounce in react

```js
import React, { useEffect, useState, useCallback } from 'react';
import TextField from '@material-ui/core/TextField';
import debounce from 'lodash.debounce';

export default function SearchInputText({ valueKey: key, onChanged }) {
  const [input, setInput] = useState('');

  const handleOnChange = useCallback(
    debounce(inputValue => {
      if (key) {
        onChanged({ [key]: inputValue });
      } else {
        onChanged(inputValue);
      }
    }, 300),
    [],
  );

  useEffect(() => {
    handleOnChange(input);
  }, [input, handleOnChange]);

  const setInputText = event => {
    setInput(event.target.value);
  };

  return (
    <TextField
      onChange={setInputText}
    />
  );
}

```
