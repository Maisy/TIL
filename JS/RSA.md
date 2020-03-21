# RSA encrypt
- web에서 입력받는 pw를 encrypt해서 넘기고싶다

### server
- node 내장 모듈인 [crypto](https://nodejs.org/api/crypto.html) 사용
```js
const crypto = require('crypto');
const rsaKeyMap = {};

//-------------------------------------------------------------
// /api/rsa/publickey
//-------------------------------------------------------------
function getPublicKey(req, res, next) {
  const { publicKey, privateKey } = crypto.generateKeyPairSync('rsa', {
    modulusLength: 1024,
    publicKeyEncoding: {
      type: 'spki',
      format: 'pem'
    },
    privateKeyEncoding: {
      type: 'pkcs8',
      format: 'pem',
      cipher: 'aes-256-cbc',
      passphrase: 'top secret'
    }
  });

  console.log(publicKey);
  console.log(privateKey);
  rsaKeyMap[publicKey] = privateKey;
  res.json({ publicKey, privateKey });
}

//-------------------------------------------------------------
// /api/rsa/decrypt
//-------------------------------------------------------------
function decryptTest(req, res, next) {
  const publicKey = req.body.publicKey;
  const encrypted = req.body.encrypted;
  const privateKey = rsaKeyMap[publicKey];

  var decryptBuffer = Buffer.from(encrypted.toString('base64'), 'base64');
  var decrypted = crypto.privateDecrypt(
    {
      key: privateKey,
      passphrase: 'top secret',
      padding: crypto.constants.RSA_PKCS1_PADDING
    },
    decryptBuffer
  );

  const rawString = decrypted.toString();
  const pw = encryptOneway(rawString);

  res.json({ decrypted: rawString, oneway: pw });
}

function encryptOneway(rawString) {
  crypto.DEFAULT_ENCODING = 'hex';
  //salt는 user정보와 함께 저장 (회원가입시)
  const key = crypto.pbkdf2Sync(rawString, 'salt', 100000, 128, 'sha512');
  return key;
}

exports.decryptTest = decryptTest;
exports.getPublicKey = getPublicKey;


```

### client
- js library [jsencrypt](https://github.com/travist/jsencrypt) 사용 (web에서만 사용가능)


```js
import JSEncrypt from 'jsencrypt';
const encrypt = new JSEncrypt();

//get public key from server
const publicKey =
  '-----BEGIN PUBLIC KEY-----\nMFwwDQYJKoZIhvcNAQEBBQADSwAwSAJBAMhlwTk/tM7BVtvSdHlJRsQ5e4l7VmN3\nqXEYxl7wgQw5hKKZB95tB8tE6ezGrnOlksnstddMTbQ4woRLzjgZarUCAwEAAQ==\n-----END PUBLIC KEY-----';
encrypt.setPublicKey(publicKey);
var encrypted = encrypt.encrypt('maisy');
console.log(encrypted);
```
