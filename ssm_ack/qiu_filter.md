### 拦截器

就是对发起的请求进行处理， 如果满足条件， 允许进入下一层 拦截器（每一个请求可能通过多个拦截器）。

- preHandle 前置拦截器， 只有返回值为true, 才表示允许通过拦截器， 进入下一层
- postHandle 后置拦截器
- afterCompletion 结束拦截器

**拦截器执行的顺序**

- 如果N的拦截器， 不相关， 先后顺序不影响，
- 如果都对同一个urI 处理， 拦截器的先后顺序则会对请求先后进行处理
- 如果有 A，B，C三个拦截器
  - 前置处理执行顺序 A， B，C
  - 后置处理执行顺序 C，B，A



**案例一 请求时间**



```java
/*
* 方法计时器
* */
public class MethodTimeInterceptor implements HandlerInterceptor {

     private static final Logger logger = Logger.getLogger(MethodTimeInterceptor.class);
     @Override
     public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
          long startTime = System.currentTimeMillis();
          request.setAttribute("startTime", startTime);
          logger.info(request.getRequestURL() + "请求到了");
          return true ; // 只有返回 为 true 的话， 才能进入下一层
     }

     @Override
     public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
          long endTime = System.currentTimeMillis();
          long startTime = (long) request.getAttribute("startTime");
          long spendTime = endTime - startTime;
          if(spendTime > 1000) {
               logger.warn("你的请求超时" + spendTime + "毫秒");
          }else{
               logger.info("该请求一共用了 " + spendTime + "毫秒");
          }
     }

     @Override
     public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {

     }
}
```

**springmcv 配置拦截器**

==<mvc:interceptors>中可以有多个拦截器， 包括请求路径以及拦截器对应的类==

```xml
<mvc:interceptors>
     <mvc:interceptor>
         <mvc:mapping path="/**/*"/>    <!--拦截路径-->
         <bean class="com.bourne.springmvc6.interceptor.MethodTimeInterceptor"/>
     </mvc:interceptor>
</mvc:interceptors>
```



案例二 会话拦截器

```java
@Controller
@RequestMapping("/user")
public class UserController {
     @RequestMapping("/login") // 登陆拦截器， 把 登陆用户放置在 当前会话中
     public String addUser(User user, HttpSession session) {
          System.out.println(user);
          session.setAttribute("user", user);
          return "welcome";
     }
     @RequestMapping("/delete")	// 删除用户操作
     public String delUser() {
          return "welcome";
     }
}
```

拦截器配置

==这里的含义表示对用户操作进行一定的权限设置， 如果用户登陆之后， 就可以进行该用户所拥有权限的所有操作， 如果没有成功，则表示该用户未登录， 需要登陆之后， 才能进行其他操作==

```xml
<mvc:interceptor>
     <mvc:mapping path="/user/**/*"/>    <!--拦截路径--> 
     <mvc:exclude-mapping path="/user/login"/>// 表示拦截除了 /user/login 之外的任意 /user/**/*请求
     <bean class="com.bourne.springmvc6.interceptor.SessionInterpretor"/>	<!--拦截类-->
</mvc:interceptor>
```

拦截器

==记住， 在会话当中获取 属性， 要用 request.getSession()==

```java
@Override
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
     Object user = request.getSession().getAttribute("user");
     if(user == null) {
          logger.warn("该用户不存在");
     }
     if(user instanceof User) {
          logger.info("你可以进行操作");
          return true;
     }else{
          logger.warn("什么情况");
     }
     return  false;
}
```



