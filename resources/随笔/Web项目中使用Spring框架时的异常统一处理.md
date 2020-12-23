# Web项目中使用Spring框架时的异常统一处理

## 在执行被@RequestMapping标记的方法时发生的异常

对于RESTfulAPI，使用@ControllerAdvice和@ExceptionHandler注解处理。继承ResponseEntityExceptionHandler，示例如下：

```java
    //集中处理异常
    //集成ResponseEntityExceptionHandler类来实现针对RestfulAPI的全局异常捕获
    @ControllerAdvice
    public class CustomExceptionHandler extends ResponseEntityExceptionHandler {

        /**Todo
        * 针对exception中抛出的每一种运行异常进行处理
        * @param  exception
        * @param  request
        * @return
        * @throws
        *
        */
        @ExceptionHandler({PageNotFundException.class})
        public ResponseEntity<?> pageNotfund (PageNotFundException exception, WebRequest request) {
            return getResponseEntity(exception, request, HttpStatus.NOT_FOUND);
        }

        @ExceptionHandler({UnhandledArgumentException.class})
        public ResponseEntity<?> unhandledArgument (UnhandledArgumentException exception, WebRequest request) {
            return getResponseEntity(exception, request, HttpStatus.SERVICE_UNAVAILABLE);
        }

        @ExceptionHandler({InvalidRequestException.class})
        public ResponseEntity<?> invalidRequest (InvalidRequestException exception, WebRequest request) {
            return getResponseEntity(exception, request, HttpStatus.BAD_REQUEST);
        }


        /**Todo
        * 统一的ResponseEntity工厂，负责将错误信息装配为ResponseEntity
        * @param exception
        * @param request
        * @param status
        * @return
        */
        private ResponseEntity<?> getResponseEntity(RuntimeException exception, WebRequest request, HttpStatus status) {
            return new ResponseEntity(status);
        }

    }
```

对于使用模板渲染HTML（如thymeleaf），使用HandlerExceptionResolver接口的实现类处理

```java
@Component
public class MyExceptionResolver implements HandlerExceptionResolver {
    @Override
    public ModelAndView resolveException(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Object o, Exception e) {
        ModelAndView mv = new ModelAndView(new MappingJackson2JsonView());
        Map<String, String> map = new HashMap<>();
        map.put("status", "error");
        if (e instanceof DataIntegrityViolationException) {
            map.put("msg", "该角色尚有关联的资源或用户，删除失败!");
        }
        map.put("exception",e.toString());
        mv.addAllObjects(map);
        return mv;
    }
}
```