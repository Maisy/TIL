# Redis - insert JSONObject

- Model
```ts
{
  id: int;
  name: string;
  updateDate: string;
}
```
- id와 name이 같으면 같은 object(data)로 가정하고 update하지않는다.
- Redis에는 `{ [key:string]: value: Model}` type으로 저장한다.

---

## maven dependency
```xml
<!-- https://mvnrepository.com/artifact/io.lettuce/lettuce-core -->
<dependency>
  <groupId>io.lettuce</groupId>
  <artifactId>lettuce-core</artifactId>
  <version>5.3.3.RELEASE</version>
</dependency>
<!-- https://mvnrepository.com/artifact/org.json/json -->
<dependency>
  <groupId>org.json</groupId>
  <artifactId>json</artifactId>
  <version>20200518</version>
</dependency>
```

## Example

`Application.java`
```java
import java.util.Date;

public class Application {

	public static void main(String[] args) {
		SimpleRedisClient myRedisClient = new SimpleRedisClient();
		try {
			myRedisClient.connect();

			String key = "test";
			Model result = myRedisClient.getValue(key);
			Model newData = new Model(25271, "maisy.kim", new Date().getTime() + "");

			if (result == null || !result.equals(newData)) {
				System.out.println("insert new value...");
				myRedisClient.setValue(key, newData);
			} else {
				System.out.println("do not need to insert...");
			}

		} catch (Exception e) {
			e.printStackTrace();
		} finally {
			myRedisClient.close();
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
			RedisURI redisUri = RedisURI.Builder.redis("localhost").withPort(6379).build();
			RedisClient client = RedisClient.create(redisUri);
			connection = client.connect();
		}
	}

	public Model getValue(String key) throws Exception {
		if (connection == null) {
			throw new Exception("Invalid connection");
		}
		RedisStringAsyncCommands<String, String> async = connection.async();
		RedisFuture<String> getter = async.get(key);
		return Model.parseJSONStringToModel(getter.get());
	}

	public void setValue(String key, Model value) {
		RedisStringAsyncCommands<String, String> async = connection.async();
		RedisFuture<String> setter = async.set(key, Model.parseModelToJSONString(value));
		try {
			if (!"OK".equals(setter.get())) {
				throw new ExecutionException(new Throwable("Failed to set value..."));
			}
		} catch (InterruptedException | ExecutionException ee) {
			ee.printStackTrace();
		}
	}

	public void close() {
		if (connection != null) {
			connection.close();
		}
	}
}
```

`Model.java`
```java
import java.io.Serializable;

import org.json.JSONObject;

public class Model implements Serializable {
	private static final long serialVersionUID = 1L;

	int id;
	String name;
	String updateDate;

	Model(int id, String name, String updateDate) {
		this.id = id;
		this.name = name;
		this.updateDate = updateDate;
	}

	static String parseModelToJSONString(Model model) {
		return new JSONObject(model.toString()).toString();
	}

	static Model parseJSONStringToModel(String str) {
		if (str == null || "".equals(str)) {
			return null;
		}
		System.out.println(str);
		JSONObject temp = new JSONObject(str);
		return new Model(temp.getInt("id"), temp.getString("name"), temp.optString("updateDate"));
	}

	@Override
	public String toString() {
		return String.format("{'id': %d, 'name': %s, 'updated': %s}", id, name, updateDate);
	}

	@Override
	public boolean equals(Object obj) {
		if (obj == null)
			return false;
		if (getClass() != obj.getClass())
			return false;
		Model other = (Model) obj;
		if (id == other.id && name.equals(other.name)) {
			return true;
		}
		return false;
	}

}
```

## How to check
```bash
> redis-cli
> get test # ""{\"name\":\"maisy.kim\",\"id\":2527,\"updated\":1599218945406}""
```
