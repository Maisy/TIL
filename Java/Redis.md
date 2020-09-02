# Redis - Java 연동 테스트
참고: https://github.com/lettuce-io/lettuce-core/wiki/Asynchronous-API

## Redis Java client
- [Lettuce](https://github.com/lettuce-io/lettuce-core) 사용

## maven dependency
```xml
<!-- https://mvnrepository.com/artifact/io.lettuce/lettuce-core -->
<dependency>
  <groupId>io.lettuce</groupId>
  <artifactId>lettuce-core</artifactId>
  <version>5.3.3.RELEASE</version>
</dependency>
```

## Example

`Application.java`
```java
package go.home;

public class Application {

	public static void main(String[] args) {
		try {
			SimpleRedisClient myRedisClient = new SimpleRedisClient();
			myRedisClient.connect();
			String result = myRedisClient.getValue("mk");
			System.out.println(result);
			myRedisClient.setValue("mk", "kim2");
			result = myRedisClient.getValue("mk");
			System.out.println(result);

		} catch (Exception e) {
			e.printStackTrace();
		}
	}
}

```

`SimpleRedisClient.java`
```java

import java.util.concurrent.ExecutionException;

import io.lettuce.core.RedisClient;
import io.lettuce.core.RedisFuture;
import io.lettuce.core.RedisURI;
import io.lettuce.core.api.StatefulRedisConnection;
import io.lettuce.core.api.async.RedisStringAsyncCommands;

public class SimpleRedisClient {

	private StatefulRedisConnection<String, String> connection;

	public void connect() {
		if (connection == null) {
//			RedisClient client = RedisClient.create(RedisURI.create("localhost", 6379));
			RedisURI redisUri = RedisURI.Builder.redis("localhost")
					.withPort(6379)
					.build();
			RedisClient client = RedisClient.create(redisUri);
			connection = client.connect();
		}
	}

	public String getValue(String key) throws Exception {
		if (connection == null) {
			throw new Exception("Invalid connection");
		}
		RedisStringAsyncCommands<String, String> async = connection.async();
		RedisFuture<String> getter = async.get(key);
		return getter.get();
	}

	public void setValue(String key, String value) {
		RedisStringAsyncCommands<String, String> async = connection.async();
		RedisFuture<String> setter = async.set(key, value);
		try {
			if (!"OK".equals(setter.get())) {
				throw new ExecutionException(new Throwable("Failed to set value..."));
			}
		} catch (InterruptedException | ExecutionException ee) {
			ee.printStackTrace();
		}
	}
}
```

## How to check
```bash
> redis-cli
> get mk # "kim2"
```
