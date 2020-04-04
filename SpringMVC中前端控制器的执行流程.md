# SpringMVC中前端控制器的执行流程

1. 用户发送请求，被 SpringMVC 的**前端控制器**`DispatcherServlet` 拦截。
2. DispatcherServlet 收到请求后自己不进行处理，而是将请求转发给**处理器映射器**`HandlerMapping`。
3. **处理器映射器**根据请求的URL确定映射关系找出相应的**处理器适配器**，并且返回HandlerExecutionChain对象给前端控制器。
   处理器映射器找到具体的**处理器适配器**，生成处理器对象及处理器拦截器（如果有则生成）一并返回给 DispatcherServlet。
4. DispatcherServlet根据3返回的`HandlerExecutionChain` 调用相应的**处理器适配器**`HandlerAdapter`。
5. 经过**处理器适配器**`HandlerAdapter`调用具体的处理器（`Controller`，也叫后端控制器）。
6. Controller将结果封装到ModelAndView返回给HandlerAdapter。
7. HandlerAdapter将Controller执行结果ModelAndView返回给DispatcherServlet。
8. DispatcherServlet将ModelAndView传给**视图解析器**`ViewReslover`,查询到相应的视图View。
9. ViewReslover解析后返回具体的View。
10. DispatcherServlet把Model交给View进行渲染（即将模型数据填充至视图中）。
11. DispatcherServlet响应用户。