定义使用 `@ControllerAdvice` 注释的类，以自定义要为特定控制器和 `/` 或异常类型返回的 JSON 文档，如以下示例所示：

```java
@ControllerAdvice(basePackageClasses = AcmeController.class)
public class AcmeControllerAdvice extends ResponseEntityExceptionHandler {

	@ExceptionHandler(YourException.class)
	@ResponseBody
	ResponseEntity<?> handleControllerException(HttpServletRequest request, Throwable ex) {
		HttpStatus status = getStatus(request);
		return new ResponseEntity<>(new CustomErrorType(status.value(), ex.getMessage()), status);
	}

	private HttpStatus getStatus(HttpServletRequest request) {
		Integer statusCode = (Integer) request.getAttribute("javax.servlet.error.status_code");
		if (statusCode == null) {
			return HttpStatus.INTERNAL_SERVER_ERROR;
		}
		return HttpStatus.valueOf(statusCode);
	}

}
```

在前面的示例中，如果 `YourException` 在与 `AcmeController` 相同的包中定义的控制器抛出，则使用 `CustomErrorType` POJO的JSON表示而不是 `ErrorAttributes` 表示。