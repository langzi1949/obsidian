

| 序号 | 题目 | 考察点 | 重要程度 |
|------|------|---------|----------|
| 1 | Spring IOC容器的启动过程是怎样的？请从源码角度描述ApplicationContext的初始化流程。 | IOC容器的启动机制、BeanFactoryPostProcessor、BeanPostProcessor的调用时机、refresh()方法的内部步骤。 | 高 |
| 2 | Bean的生命周期包括哪些阶段？请结合源码解释InstantiationAwareBeanPostProcessor的作用。 | Bean创建、初始化、销毁过程，postProcessBeforeInstantiation等方法的介入点。 | 高 |
| 3 | Spring如何处理循环依赖？请从源码层面分析三级缓存的机制。 | AbstractBeanFactory中的singletonObjects、earlySingletonObjects、singletonFactories的作用。 | 高 |
| 4 | @Autowired注解的注入原理是什么？请描述AutowiredAnnotationBeanPostProcessor的处理逻辑。 | 依赖注入的反射机制、Qualifier和Required的处理、源码中的注入点查找。 | 高 |
| 5 | Spring的Scope机制如何实现？请从源码解释自定义Scope如RequestScope的原理。 | AbstractBeanFactory的getBean方法、ScopedProxyFactoryBean的代理实现。 | 中 |
| 6 | AOP的动态代理有几种方式？请对比JDK动态代理和CGLIB在Spring中的应用，并从源码分析ProxyFactory的创建过程。 | AOP代理机制、JdkDynamicAopProxy和CglibAopProxy的区别、Advice链的构建。 | 高 |
| 7 | @Aspect注解如何被Spring解析？请从源码描述AspectJAutoProxyBeanDefinitionParser的注册过程。 | AOP的注解驱动、EnableAspectJAutoProxy的内部机制、AspectJExpressionPointcut的匹配逻辑。 | 高 |
| 8 | Spring AOP的事务管理如何实现？请分析TransactionInterceptor的invoke方法源码。 | 声明式事务的AOP拦截、TransactionAttributeSource的属性解析。 | 高 |
| 9 | Around Advice的执行顺序是怎样的？请从源码解释DefaultAdvisorChainFactory的链构建。 | Advice链的排序、InterceptorAndDynamicMethodMatcher的动态匹配。 | 中 |
| 10 | Spring如何支持AOP的负载时织入？请描述LoadTimeWeaverAwareProcessor的作用。 | LTW机制、InstrumentationLoadTimeWeaver的类加载织入、源码中的织入时机。 | 中 |
| 11 | Spring MVC的请求处理流程是怎样的？请从源码分析DispatcherServlet的doDispatch方法。 | HandlerMapping、HandlerAdapter、ViewResolver的协作、请求分发逻辑。 | 高 |
| 12 | @ControllerAdvice如何全局处理异常？请描述ExceptionHandlerExceptionResolver的resolveException方法。 | 异常处理的优先级、ResponseStatus的注解解析、源码中的异常链。 | 高 |
| 13 | Spring MVC的参数绑定机制是什么？请从源码解释WebDataBinder的bind过程。 | HandlerMethodArgumentResolver的自定义、ConversionService的转换逻辑。 | 高 |
| 14 | 如何实现自定义的HandlerInterceptor？请分析MappedInterceptor的匹配源码。 | 拦截器的preHandle、postHandle、afterCompletion调用时机。 | 中 |
| 15 | Spring WebFlux的响应式编程如何与MVC集成？请从源码描述WebFluxConfigurer的配置。 | Reactor的Mono/Flux处理、DispatcherHandler的响应式分发。 | 中 |
| 16 | Spring事务的传播行为有哪些？请从源码分析TransactionDefinition的Propagation枚举。 | REQUIRED、REQUIRES_NEW等行为的嵌套事务处理、TransactionStatus的保存点。 | 高 |
| 17 | @Transactional注解的回滚规则如何配置？请描述TransactionInterceptor的completeTransactionAfterThrowing方法。 | rollbackFor、noRollbackFor的异常匹配、事务提交/回滚逻辑。 | 高 |
| 18 | Spring如何支持分布式事务？请从源码解释JtaTransactionManager的集成。 | XA协议、UserTransaction的begin/commit、两阶段提交。 | 中 |
| 19 | Spring Data JPA的Repository接口如何代理？请分析JpaRepositoryFactory的getTargetRepository方法。 | QueryByExampleExecutor、CrudRepository的动态代理、EntityManager的注入。 | 高 |
| 20 | 如何自定义Spring Data的Query方法？请从源码描述PartTree的解析过程。 | @Query注解的SpEL支持、DerivedQuery的自动生成。 | 中 |
| 21 | Spring Boot的自动配置原理是什么？请从源码分析@EnableAutoConfiguration的导入。 | AutoConfigurationImportSelector的条件过滤、META-INF/spring.factories的加载。 | 高 |
| 22 | @SpringBootApplication注解的作用？请描述SpringApplication的run方法源码。 | ComponentScan、AutoConfiguration的组合、上下文的bootstrap过程。 | 高 |
| 23 | Spring Boot Actuator的健康检查如何实现？请分析HealthIndicator的check方法。 | Endpoint的暴露、CompositeHealthContributor的聚合。 | 中 |
| 24 | Spring Boot的配置文件加载顺序是怎样的？请从源码解释PropertySourceLoader的优先级。 | application.yml/properties的多环境支持、Profile的激活。 | 高 |
| 25 | 如何在Spring Boot中自定义Starter？请描述AutoConfigureBefore/After的顺序控制。 | 条件注解如@ConditionalOnClass、模块化的自动装配。 | 中 |
| 26 | Spring Security的认证流程是怎样的？请从源码分析UsernamePasswordAuthenticationFilter的attemptAuthentication。 | AuthenticationManager的Provider链、UserDetailsService的加载。 | 高 |
| 27 | @EnableWebSecurity如何配置过滤器链？请描述WebSecurityConfigurerAdapter的init方法。 | SecurityFilterChain的构建、HttpSecurity的authorizeRequests。 | 高 |
| 28 | Spring Cache的注解如@Cacheable如何工作？请分析CacheInterceptor的缓存键生成源码。 | CacheManager的实现、KeyGenerator的自定义、EhCache/Redis集成。 | 中 |
| 29 | Spring如何集成消息队列？请从源码描述JmsListenerAnnotationBeanPostProcessor的注册。 | @JmsListener的Endpoint创建、DefaultMessageHandlerMethodFactory的处理。 | 中 |
| 30 | Spring Batch的Job执行流程是怎样的？请分析JobLauncher的run方法源码。 | Step的chunk-oriented处理、JobRepository的持久化。 | 中 |
