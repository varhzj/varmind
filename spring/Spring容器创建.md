# Spring容器创建过程分析

## Spring容器[AbstractApplicationContext] refresh() 方法 [创建刷新]

### `prepareRefresh()` 刷新前的预处理

1. `initPropertySources();` 初始化一些属性配置,子类自定义属性配置
2. `getEnvironment().validateRequiredProperties();` 检验属性合法等
3. `earlyApplicationEvents = new LinkedHashSet<>();` 保存容器中的早期事件

### `obtainFreshBeanFactory();` 获取BeanFactory

1. `refreshBeanFactory();` 刷新BeanFactory  
    1. 创建一个`this.beanFactory = new DefaultListableBeanFactory();`
    2. 设置id
2. `getBeanFactory();` 返回刚才GenericApplicationContext创建的BeanFactory对象;
3. 返回`BeanFactory`[`DefaultListableBeanFactory`]返回;

### `prepareBeanFactory(beanFactory);` BeanFactory的预准备

1. 设置BeanFactory的类加载器,支持表达式解析器...
    ``` java
    beanFactory.setBeanClassLoader(getClassLoader());  
    beanFactory.setBeanExpressionResolver(new StandardBeanExpressionResolver(beanFactory.getBeanClassLoader()));  
    beanFactory.addPropertyEditorRegistrar(new ResourceEditorRegistrar(this, getEnvironment()));
    ```
2. 添加部分`BeanPostProcessor`;
    ``` java
    beanFactory.addBeanPostProcessor(new ApplicationContextAwareProcessor(this));
    ```
3. 设置忽略的自动装配接口;
    ``` java
    beanFactory.ignoreDependencyInterface(EnvironmentAware.class);
    beanFactory.ignoreDependencyInterface(EmbeddedValueResolverAware.class);
    beanFactory.ignoreDependencyInterface(ResourceLoaderAware.class);
    beanFactory.ignoreDependencyInterface(ApplicationEventPublisherAware.class);
    beanFactory.ignoreDependencyInterface(MessageSourceAware.class);
    beanFactory.ignoreDependencyInterface(ApplicationContextAware.class);
    ```
4. 注册可以解析的自动装配;[`BeanFactory,ResourceLoader,ApplicationEventPublisher.ApplicationContext`]
5. 添加`BeanPostProcessor`;[`ApplicationListenerDetector`]
6. 添加编译时的`AspectJ`支持
7. 注册环境配置bean
    * environment[`ConfigurableEnvironment`]
    * systemProperties[`Map<String, Object>`]
    * systemEnvironment[`Map<String, Object>`]

### `postProcessBeanFactory(beanFactory);` BeanFactory准备工作完成后进行的后置处理

1. 子类通过重写这个方法来在BeanFactory创建并预准备完成以后做进一步的设置,如在web环境中[`GenericWebApplicationContext`]:
    ``` java
    beanFactory.addBeanPostProcessor(new ServletContextAwareProcessor(this.servletContext));
    beanFactory.ignoreDependencyInterface(ServletContextAware.class);
    WebApplicationContextUtils.registerWebApplicationScopes(beanFactory, this.servletContext);
    WebApplicationContextUtils.registerEnvironmentBeans(beanFactory, this.servletContext);
    ```

### `invokeBeanFactoryPostProcessors(beanFactory);`执行`BeanFactoryPostProcessor`,在`BeanFactory`标准初始化之后执行

1. 执行`BeanDefinitionRegistryPostProcessor`
    1. 获取所有的`BeanDefinitionRegistryPostProcessor`
    2. 先执行实现了`PriorityOrdered`接口的`BeanDefinitionRegistryPostProcessor`,执行`postProcessor.postProcessBeanDefinitionRegistry(registry);`[注册bean]
    3. 再执行实现了`Ordered`接口的`BeanDefinitionRegistryPostProcessor`,执行`postProcessor.postProcessBeanDefinitionRegistry(registry);`
    4. 最后执行没实现任何优先级或顺序的`BeanDefinitionRegistryPostProcessor`
2. 执行`BeanFactoryPostProcessor`
    1. 获取所有的`BeanFactoryPostProcessor`  
    2. 先执行实现了`PriorityOrdered`接口的`BeanFactoryPostProcessor`,执行`postProcessor.postProcessBeanFactory(registry);`  
    3. 再执行实现了`Ordered`接口的`BeanFactoryPostProcessor`,执行`postProcessor.postProcessBeanFactory(registry);`  
    4. 最后执行没实现任何优先级或顺序的`BeanFactoryPostProcessor`

### `registerBeanPostProcessors(beanFactory);` 注册`BeanPostProcessor`

``` java
BeanPostProcessor
DestructionAwareBeanPostProcessor
InstantiationAwareBeanPostProcessor
SmartInstantiationAwareBeanPostProcessor
MergedBeanDefinitionPostProcessor [internalPostProcessors]
```

1. 获取所有的`BeanPostProcessor`,可以通过`PriorityOrdered,Ordered`设置优先级/顺序
2. 先注册实现`PriorityOrdered`接口的`BeanPostProcessor`,把每一个`BeanPostProcessor`添加到`BeanFactory`中: `beanFactory.addBeanPostProcessor(postProcessor);`
3. 再注册实现`Ordered`接口的`BeanPostProcessor`
4. 最后注册其他的`BeanPostProcessor`
5. 最终注册`internalPostProcessors[MergedBeanDefinitionPostProcessor]`
6. 注册`ApplicationListenerDetector`到最后,用于检查创建的`Bean`是否是`ApplicationListener`,如果是,添加到`ApplicationContext`中: `this.applicationContext.addApplicationListener((ApplicationListener<?>) bean);`

### `initMessageSource();`初始化`MessageSource`组件(做国际化,消息绑定,消息解析)

1. 获取`BeanFactory`
2. 看容器中是否有messageSource的组件,如果有,赋值,没有则创建`DelegatingMessageSource`
3. 把创建好的`MessageSource`注册到容器中: `beanFactory.registerSingleton(MESSAGE_SOURCE_BEAN_NAME, this.messageSource);`

### `initApplicationEventMulticaster();` 初始化事件派发器

1. 获取`BeanFactory`
2. 获取id为`applicationEventMulticaster`的`ApplicationEventMulticaster`
3. 如果上一步没有配置,创建一个`SimpleApplicationEventMulticaster`,注册到`ApplicationContext`中: `beanFactory.registerSingleton(APPLICATION_EVENT_MULTICASTER_BEAN_NAME, this.applicationEventMulticaster);`

### `onRefresh();` 留给子类重写

1. 子类重写这个方法,在容器刷新时自定义逻辑

### `registerListeners();` 注册所有的`ApplicationListener`

1. 从容器中获取所有的`ApplicationListener`组件
2. 将每个监听器添加到事件派发器中
3. 派发之前产生的事件`[earlyApplicationEvents]`

### `finishBeanFactoryInitialization(beanFactory);` 初始化所有剩下的单实例`Bean`

1. `beanFactory.preInstantiateSingletons()[DefaultListableBeanFactory];`初始化非懒加载的单实例`Bean`
    1. 获取到容器中所有的`Bean`信息,依次进行初始化和创建对象
    2. 获取`Bean`的定义信息: `RootBeanDefinition`
    3. `Bean`不是抽象的,是单例,不是懒加载
        1. 判断是否`FactoryBean`
        2. 不是工厂`Bean`,使用`getBean(beanName);[AbstractBeanFactory]`创建对象
            1. `getBean(beanName);`
            2. `doGetBean(name, null, null, false);`
            3. 先获取缓存中保存的单实例`Bean`.如果能获取到,说明已创建过: `Object sharedInstance = getSingleton(beanName);`
                * `getSingleton(String beanName, boolean allowEarlyReference)`,可以获取到未创建好提前暴露的`Object/ObjectFactory`,用于解决循环依赖问题
                ``` java
                /** Cache of singleton objects: bean name to bean instance. */
                private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);
                /** Cache of singleton factories: bean name to ObjectFactory. */
                private final Map<String, ObjectFactory<?>> singletonFactories = new HashMap<>(16);

                /** Cache of early singleton objects: bean name to bean instance. */
                private final Map<String, Object> earlySingletonObjects = new HashMap<>(16);

                /** Set of registered singletons, containing the bean names in registration order. */
                private final Set<String> registeredSingletons = new LinkedHashSet<>(256);

                /** Names of beans that are currently in creation. */
                private final Set<String> singletonsCurrentlyInCreation = Collections.newSetFromMap(new ConcurrentHashMap<>(16));

                /** Names of beans currently excluded from in creation checks. */
                private final Set<String> inCreationCheckExclusions = Collections.newSetFromMap(new ConcurrentHashMap<>(16));

                protected Object getSingleton(String beanName, boolean allowEarlyReference) {
                    Object singletonObject = this.singletonObjects.get(beanName);
                    if (singletonObject == null && isSingletonCurrentlyInCreation(beanName)) {
                        synchronized (this.singletonObjects) {
                            singletonObject = this.earlySingletonObjects.get(beanName);
                            if (singletonObject == null &&  allowEarlyReference) {
                                ObjectFactory<?> singletonFactory = this.singletonFactories.get(beanName);
                                if (singletonFactory != null) {
                                    singletonObject = singletonFactory.getObject();
                                    this.earlySingletonObjects.put(beanName, singletonObject);
                                    this.singletonFactories.remove(beanName);
                                }
                            }
                        }
                    }
                    return singletonObject;
                }
                ```
            4. 缓存中获取不到,查看父容器中是否能够获取,如果依然不能,开始`Bean`的创建对象流程
            5. 标记`Bean`创建
            6. 获取`Bean`定义信息
            7. 获取当前`Bean`的依赖`Bean[String[] dependsOn = mbd.getDependsOn();]`,先把依赖创建出来`getBean(beanName)`
            8. 启动单实例`Bean`的创建流程:`createBean(),[AbstractAutowireCapableBeanFactory]`
                ``` java
                sharedInstance = getSingleton(beanName, () -> {
                    try {
                        return createBean(beanName, mbd, args);
                    } catch (BeansException ex) {
                        // Explicitly remove instance from singleton cache: It  might have been put there
                        // eagerly by the creation process, to allow for circular reference resolution.
                        // Also remove any beans that received a temporary  reference to the bean.
                        destroySingleton(beanName);
                        throw ex;
                    }
                }
                ```
                1. `createBean(beanName, mbd, args);`
                2. `Object bean = resolveBeforeInstantiation(beanName, mbdToUse);` 让`[InstantiationAwareBeanPostProcessor]`可以提前执行,返回一个代理对象:先触发`postProcessBeforeInstantiation(beanClass, beanName);`[`AbstractAutoProxyCreator`尝试创建代理],如果有返回值,再执行:`postProcessAfterInitialization(result, beanName);`
                3. 如果`InstantiationAwareBeanPostProcessor`没有返回代理对象,进入第4步
                4. `Object beanInstance = doCreateBean(beanName, mbdToUse, args);`创建`Bean`
                    1. [创建`Bean`实例]:`createBeanInstance(beanName, mbd, args);` 实例化`Bean`,利用工厂方法或者对象的构造器创建出`Bean`实例
                    2. [让`MergedBeanDefinitionPostProcessor`修改`bean`定义]`applyMergedBeanDefinitionPostProcessors(mbd, beanType, beanName);`,调用`[MergedBeanDefinitionPostProcessor]`的`postProcessMergedBeanDefinition()`,例如`AutowiredAnnotationBeanPostProcessor`
                    3. 提前暴露一个`ObjectFactory`,用于解决循环依赖问题:`addSingletonFactory(beanName, () -> getEarlyBeanReference(beanName, mbd, bean));`
                    4. [`Bean`属性赋值]:`populateBean(beanName, mbd, instanceWrapper);`
                        1.赋值之前,拿到`InstantiationAwareBeanPostProcessor`执行`postProcessAfterInstantiation()`方法
                        2. 拿到`InstantiationAwareBeanPostProcessor`执行`postProcessPropertyValues()`方法
                        3. 应用`Bean`属性值:为属性利用setter方法赋值`applyPropertyValues(beanName, mbd, bw, pvs);`
                    5. [`Bean`初始化]:`initializeBean(beanName, exposedObject, mbd);`
                        1. [执行Aware接口方法]`invokeAwareMethods(beanName, bean);`执行`xxxAweare`接口方法
                        2. [执行后置处理器初始化之前方法]`applyBeanPostProcessorsBeforeInitialization(wrappedBean, beanName);`
                        3. [执行初始化方法]`invokeInitMethods(beanName, wrappedBean, mbd);`,`InitializingBean`/`init`方法
                        4. [执行后置处理器初始化之后方法]`applyBeanPostProcessorsAfterInitialization(wrappedBean, beanName);`,[注解方式aop`AnnotationAwareAspectJAutoProxyCreator[AbstractAutoProxyCreator]`创建代理对象]
                    6. [注册`Bean`销毁方法]registerDisposableBeanIfNecessary(beanName, bean, mbd);
    4. 判断是否是`SmartInitializingSingleton`,如果是,执行`smartSingleton.afterSingletonsInstantiated();`

### `finishRefresh();`完成`BeanFactory`的初始化创建工作

1. `clearResourceCaches();`
2. `initLifecycleProcessor();`
3. `getLifecycleProcessor().onRefresh()`;
4. `publishEvent(new ContextRefreshedEvent(this));`
5. `LiveBeansView.registerApplicationContext(this);`