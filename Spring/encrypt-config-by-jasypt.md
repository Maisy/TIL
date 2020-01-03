# jasypt으로 proeperty값 encrypt/decrypt하기

- spring framework의 `@Configuration`, `@Bean("jasyptStringEncryptor")`으로 등록만 해주면 application.properties에서 **ENC()**로 입력한 값들을 알아서 decrypt해준다 :laughing:

### 1. (spring maven project) `pom.xml`
- jasypt dependency를 추가해준다.
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
<dependency>
    <groupId>com.github.ulisesbocchio</groupId>
    <artifactId>jasypt-spring-boot-starter</artifactId>
    <version>2.1.0</version>
</dependency>
```

### 2. `PropertyEncryptConfig.java`
```java
@Configuration
public class PropertyEncryptConfig {

	@Bean("jasyptStringEncryptor")
	public StringEncryptor stringEncryptor() {
		PooledPBEStringEncryptor encryptor = new PooledPBEStringEncryptor();
		SimpleStringPBEConfig config = new SimpleStringPBEConfig();
		config.setPassword("RAMDOM_STRING");
		config.setAlgorithm("PBEWithMD5AndDES");
		config.setKeyObtentionIterations("1000");
		config.setPoolSize("1");
		config.setProviderName("SunJCE");
		config.setSaltGeneratorClassName("org.jasypt.salt.RandomSaltGenerator");
		config.setStringOutputType("base64");
		encryptor.setConfig(config);
		return encryptor;
	}
	
}
```

### 3. `application.properties`에 value를 encrypted된 값으로 설정
```properties
maisy.password=ENC(Pqt/sPtgxDiF7m6rhLW9aA==)
```

### 4. `TestController.java`
- @value로 propery값을 가져온걸 바로 리턴

```java
@RestController
public class TestController {
	
	@Value("${maisy.password:#{null}}")
	private Optional<String> remotePassword;

	@RequestMapping("/simpletest")
	public Optional<String> getDecrypted() {
		return remotePassword;
	}
}
```

### 5. TEST
```
GET /simpletest
```
RESPONSE
```
"maisy"
```
