
# jwt, jwk sample

- node의 crypto module을 사용하여 jwk generate.
- 서버를 재기동할때마다 private key값이 생성될경우, 이미 생성된(DB에 저장되어있던) JWT들은 decrypt(verify)를 하지못하므로 key들을 파일로 저장해두고 같은 private key를 사용하도록 해야한다.

- encoding 옵션을 주지 않고 generate할 경우 PublicKeyObject, PrivateKeyObject로 리턴된다.
```
{
    publicKey: PublicKeyObject { [Symbol(kKeyType)]: 'public' },
    privateKey: PrivateKeyObject { [Symbol(kKeyType)]: 'private' }
}
```

- public, private key encoding option을 주면 pem format으로 key가 생성되고, 파일로 만들수있다.

```js
{
    publicKeyEncoding: {
        type: 'spki',
        format: 'pem',
    },
    privateKeyEncoding: {
        type: 'pkcs8',
        format: 'pem',
    },
}
```

<details><summary>전체 코드</summary>
<p>

```js
const crypto = require('crypto');
const jwt = require('jsonwebtoken');

function getJWK() {
  let privateKey, publicKey;
  return function generate() {
    if (!privateKey || !publicKey) {
      console.log('generate key.....');
      const rst = crypto.generateKeyPairSync('rsa', {
        modulusLength: 2048,
        publicKeyEncoding: {
          type: 'spki',
          format: 'pem',
        },
        privateKeyEncoding: {
          type: 'pkcs8',
          format: 'pem',
        },
      });
      privateKey = rst.privateKey;
      publicKey = rst.publicKey;
    }
    return { privateKey, publicKey };
  };
}

const getKey = getJWK();

function encryptJWT() {
  const { privateKey } = getKey();
  var token = jwt.sign({ iss: 'maisy' }, privateKey, {
    algorithm: 'RS256',
    expiresIn: 60 * 60,
  });
  return token;
}

function decryptJWT(encryptedToken) {
  const { publicKey } = getKey();
  var decoded = jwt.verify(encryptedToken, publicKey, {
    algorithms: ['RS256'],
  });
  console.log(decoded);
}

function test() {
  const token = encryptJWT();
  console.log(token);

  decryptJWT(token);
  decryptJWT(token);
  decryptJWT(token);
}

test();
```

</p>
</details>
