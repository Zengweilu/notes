学习笔记

Spring 常用拦截器

我将以记录访问EESTFul API 访问者的ip地址为例

常见的三种拦截机制是实现以下三种实现的
1.过滤器 Filter
2.拦截器 Interceptor
3.切片Aspect


先让我们写一个简单的Filter
import org.apache.commons.lang.StringUtils;
import org.springframework.stereotype.Component;

import javax.servlet.*;
import javax.servlet.http.HttpServletRequest;
import java.io.IOException;
//实现javax.servlet.Filter
@Component
public class IpFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        //初始化Filter
        System.out.println("my filtr init");
    }

    @Override
    public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
            //业务代码
        System.out.println("my filtr start");
        HttpServletRequest      reque =  (HttpServletRequest)request;//;
        String ipString =reque.getHeader("x-forwarded-for");
        if (StringUtils.isBlank(ipString) || "unknown".equalsIgnoreCase(ipString))
        { ipString = reque.getHeader("Proxy-Client-IP"); }
        if (StringUtils.isBlank(ipString) || "unknown".equalsIgnoreCase(ipString))
        { ipString = reque.getHeader("WL-Proxy-Client-IP"); }
        if (StringUtils.isBlank(ipString) || "unknown".equalsIgnoreCase(ipString))
        { ipString = reque.getRemoteAddr(); }
        System.out.println(ipString);
        chain.doFilter(request,response);//业务代码处理完毕执行后续代码
        System.out.println("my filtr finish");

    }
    @Override
    public void destroy() {
      //销毁Filter
        System.out.println("my filtr destroy");
    }
}

但是还是会有一些需要使我们不得应用启动项目的组件，那如何做到这一步了
这里我不找其他的过滤器我就用我写的Filter做Demo

//@Component 注释掉
public class IpFilter implements Filter 

@Configuration
public class FilterConfig  {

    @Bean
    public FilterRegistrationBean IpFilter(){
        //声明FilterRegistrationBean
        FilterRegistrationBean registrationBean=new FilterRegistrationBean();
        //声明IpFilter 第三方Filter
        IpFilter ipFilter=new IpFilter();

        registrationBean.setFilter(ipFilter);
        //设置拦截路径
        List<String> url=new ArrayList<>();
        //all
        url.add("/*");
        registrationBean.setUrlPatterns(url);
        return  registrationBean;
    }
}
Filter是可以拿到request,responst,但是他是不知道是哪个contrllor 哪个方法传过来的

但是用Interceptor可以解决
import org.springframework.stereotype.Component;
import org.springframework.web.method.HandlerMethod;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.util.Date;
@Component
public class IpInterceptor implements HandlerInterceptor {

    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
     //在进contrllor的末个方法调用
        System.out.println("my IpInterceptor preHandle");
        System.out.println(((HandlerMethod)handler).getBean().getClass().getName());
        System.out.println(((HandlerMethod)handler).getMethod().getName());
        request.setAttribute("time",new Date().getTime());
        return false;
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("my IpInterceptor postHandle");
        Long time=(Long)request.getAttribute("time");
        System.out.println("耗时=》"+(new Date().getTime()-time));
            //在进入方法执行。但是调用的方法出现了异常，就不会调用
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("my IpInterceptor afterCompletion");
        //后执行，多会执行
        //这里由于有个Exception，所以我们可以对这里做一些Exception的处理
    }
}

@Configuration
public class WebConfig extends WebMvcConfigurerAdapter {
  @Autowired
  private IpInterceptor ipInterceptor;
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(ipInterceptor);

    }
}
最终执行的结果
my IpInterceptor preHandle
org.springframework.boot.autoconfigure.web.servlet.error.BasicErrorController
errorHtml
my IpInterceptor preHandle
com.zwl.security.demo.controller.userController
hello


如果我们还想拿到传参，那么就需要用切片


import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

import java.util.Date;

@Aspect
@Component
public class IpAspect  {
 //Around中的表达式不会请访问这个网址
//https://docs.spring.io/spring/docs/4.3.21.BUILD-SNAPSHOT/spring-framework-reference/htmlsingle/#aop-pointcuts


    @Around("execution(* com.zwl.security.demo.controller.userController.*(..))")
    public Object handleControllerMethod(ProceedingJoinPoint pjp) throws Throwable {
        System.out.println("my IpAspect start");
        long start=System.currentTimeMillis();
        Object[] args=pjp.getArgs();
        for(Object arg : args){
            System.out.println("start =>"+arg);
        }
        Object object=pjp.proceed();
        System.out.println("time=>"+(System.currentTimeMillis()-start));
        System.out.println("my IpAspect end");
;


        return  null;
    }
}

总结的以上几种问题，我们来谈谈着三种的执行速度
我通过打印得到
1.Filter
2.Interseptor
3.controllerAdvice
4.Aspect
5.controller

