```js
import React, { useState } from 'react';
import { makeStyles } from '@material-ui/core/styles';
import Button from '@material-ui/core/Button';
import { Typography } from '@material-ui/core';

const useStyles = makeStyles(theme => ({
  root: {
    display: 'flex',
    alignItems: 'center',
    // '& > *': {
    //   margin: theme.spacing(1),
    // },
  },
  input: {
    display: 'none',
  },
  button: {
    width: 'auto',
  },
}));

function UploadButton({ label = 'Upload File' }) {
  const classes = useStyles();
  const [fileName, setFileName] = useState('');
  const handelSelectedFile = e => {
    const fileList = e.target.files;
    if (fileList.length > 0) {
      setFileName(fileList[0].name);
    }
  };

  return (
    <div className={classes.root}>
      <input
        type="file"
        accept=".json"
        id="contained-button-file"
        className={classes.input}
        onChange={handelSelectedFile}
      />
      <label htmlFor="contained-button-file">
        <Button
          variant="outlined"
          color="primary"
          component="span"
          className={classes.button}
        >
          {label}
        </Button>
      </label>
      <Typography>{fileName}</Typography>
    </div>
  );
}

export default React.memo(UploadButton);

```
