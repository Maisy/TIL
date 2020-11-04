# spring boot + react: render html with variables

- spring boot view engine 중 `thymeleaf`를 사용한다.
- ejs처럼 html render하는 시점에 서버(springboot)에서 설정한 configuration값을 세팅해서 넘겨주고싶다.(baseUrl을 넘겨준다던가)

## React 
### public/index.html 파일 수정
- `<html xmlns:th="http://www.thymeleaf.org">` 와 같이 thymeleaf namespace를 추가해준다.
- hidden tag에 값을 넣어놓고 react app 내에서 빼서 쓰기위해 `th:inline="javascript"`로 스크립트를 작성한다.
- [thymeleaf with js guide](https://www.thymeleaf.org/doc/tutorials/2.1/usingthymeleaf.html#script-inlining-javascript-and-dart)

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  <title>Sample Title</title>
</head>
<body>
  <div id="app"></div>
  <span id="webConfig" hidden></span>
  <script th:inline="javascript">
    /*<![CDATA[*/
      var webConfig = [[${baseUrl}]];
      document.getElementById("webConfig").innerHTML = webConfig;
    /*]]>*/
  </script>
</body>
</html>
```

### React app 수정
- 그냥 아래와같이 빼서쓰면된다^_^
```js
const webConfig = document.getElementById('webConfig').innerHTML;
console.log('webConfig', webConfig);
```

## Spring Boot
### pom.xml

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-thymeleaf</artifactId>
</dependency>
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

### ViewController
- `@RestController` 말고 `@Controller`로 작성해야한다. 
```java
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;

@Controller
public class ViewController {

	@GetMapping("/show")
	public String index(Model model) {
		// Call Thymeleaf to set the data value in the .html file
		model.addAttribute("baseUrl", "/maisy");
		return "index";
	}
}
```

### static file (index.html) 위치
- src/main/resources/templates/index.html 에 위치해야함.
