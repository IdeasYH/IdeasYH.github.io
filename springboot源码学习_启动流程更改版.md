# springboot源码学习_启动流程

### 注解 @SpringBootApplication

#### 1. 点击进入__@SpringBootApplication__ 注解类

> __说明文档__：指示声明一个或多个@Bean方法并触发自动配置和组件扫描的配置类。这是一个很方便的注释，相当于声明了@Configuration、@EnableAutoConfiguration和@ComponentScan。
>
> @Target(ElementType.TYPE)
> @Retention(RetentionPolicy.RUNTIME)
> @Documented
> @Inherited
> @SpringBootConfiguration
> @EnableAutoConfiguration
> @ComponentScan(excludeFilters = {
> 		@Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
> 		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
>  <font color="cb7731">public</font>  <font color="cb7731">@interface</font> SpringBootApplication {
> 
> ##### 2. 点击进入__@SpringBootConfiguration__ 注解类
>
> > __文档说明__:指示类提供Spring引导应用程序@Configuration。可以作为Spring的标准@Configuration注释的替代品，以便可以自动找到配置(例如在测试中)。
>> 应用程序应该只包含一个@SpringBootConfiguration，大多数惯用的Spring引导应用程序将从@SpringBootApplication继承它。
> >
> > @Target(ElementType.TYPE)
> > @Retention(RetentionPolicy.RUNTIME)
> > @Documented
> > @Configuration
> >  <font color="cb7731">public</font>  <font color="cb7731">@interface</font> SpringBootConfiguration {
> > 
> > } 
> > 
> > ##### 3. 点击进入__@Configuration__ 注解类
> >
> > > @Target(ElementType.TYPE)
> >> @Retention(RetentionPolicy.RUNTIME)
> > > @Documented
> > > @Component
> > >  <font color="cb7731">public</font>  <font color="cb7731">@interface</font> Configuration {
> > > 
> > > 文档说明:指示类声明一个或多个@Bean方法，并可由Spring容器处理，以便在运行时为这些bean生成bean定义和服务请求 for example:
> > >
> > > @Configuration
> > > <font color="cb7731">public</font>  <font color="cb7731">class</font> AppConfig {
> > >     @Bean
> > >      <font color="cb7731">public</font> MyBean myBean() {
> > >             <font color="#559354">// instantiate, configure and  <font color="cb7731">return</font> bean ...</font> 
> > >        }
> > >    }
> > >    
> > >    __Config原理：__
> > >    
> > > AnnotationConfigApplicationContext ctx =  <font color="cb7731">new</font> AnnotationConfigApplicationContext();
> > > <font color="#559354">// 注册配置类</font> 
> > > ctx.register(AppConfig.class);
> > > <font color="#559354">// 刷新</font> 
> > > ctx.refresh();
> > >  <font color="#559354">// 获取Bean</font> 
> > > MyBean myBean = ctx.getBean(MyBean.class);
> > >     <font color="#559354">// use myBean ...</font> 
> > > 
> > >    @Configuration
> > >  <font color="cb7731">public</font>  <font color="cb7731">class</font> AppConfig {
> > >     @Bean
> > >      <font color="cb7731">public</font> MyBean MyBean(){
> > >       <font color="cb7731">return</font>  <font color="cb7731">new</font> MyBean();
> > > 	}
> > > }
> > 
> > >##### 使用@Configuration与@Import注解进行@Configuration嵌套
> > >
> > >@Configuration
> > >    <font color="cb7731">public</font>  <font color="cb7731">class</font> DatabaseConfig {
> > >   
> > >    @Bean
> > >        <font color="cb7731">public</font> DataSource dataSource() {
> > >            <font color="#559354">// instantiate, configure and  <font color="cb7731">return</font> DataSource</font> 
> > >       }
> >>     }
> > >   
> > >  @Configuration
> > >   @Import(DatabaseConfig.class)
> > >    <font color="cb7731">public</font>  <font color="cb7731">class</font> AppConfig {
> > >   
> > >     <font color="cb7731">private</font>  <font color="cb7731">final</font> DatabaseConfig dataConfig;
> > >     
> > >     <font color="cb7731">public</font> AppConfig(DatabaseConfig dataConfig) {
> > >           this.dataConfig = dataConfig;
> > >       }
> > >     
> > >    @Bean
> > >        <font color="cb7731">public</font> MyBean myBean() {
> > >            <font color="#559354">// reference the dataSource() bean method</font> 
> > >            <font color="cb7731">return</font>  <font color="cb7731">new</font> MyBean(dataConfig.dataSource());
> > >       }
> > >     }
> > >   
> > >
> 
> ##### 2. 点击进入@EnableAutoConfiguration注解类
> 
> > __文档说明:__启用Spring应用程序上下文的自动配置，尝试猜测和配置可能需要的bean。自动配置类通常是基于类路径和您定义的bean应用的。例如，如果您的类路径上有tomcat-embedded.jar，那么您可能需要一个TomcatServletWebServerFactory(除非您已经定义了自己的ServletWebServerFactory bean)。
> >
> > 在使用SpringBootApplication时，上下文的自动配置是自动启用的，因此添加这个注释没有额外的效果。
> >
> > 自动配置试图尽可能地智能化，并在您定义更多自己的配置时后退。您总是可以手动排除()任何您永远不想应用的配置(如果您不能访问它们，请使用excludeName())。您还可以通过spring.autoconfigure.exclude排除它们。排除属性。自动配置总是在注册了用户定义的bean之后应用。
> >
> > <font color="red">用@EnableAutoConfiguration(通常是通过@SpringBootApplication)注释的类包具有特定的重要性，通常被用作“默认”。例如，它将在扫描@Entity类时使用。通常建议您将@EnableAutoConfiguration(如果您没有使用@SpringBootApplication)放在root包中，这样就可以搜索所有子包和类。</font>
> >
> > 自动配置类是常规的Spring配置bean。使用SpringFactoriesLoader机制(针对这个类设置键值)定位它们。通常自动配置bean是@Conditional bean(通常使用@ConditionalOnClass和@ConditionalOnMissingBean注释)。
> >
> > 
>
> ##### 2.点击进入@ComponentScan注解类
>
> > __文档说明:__配置与@Configuration类一起使用的组件扫描指令。提供与Spring XML的 <<context:component-scan>>元素并行的支持。
> >
> > 可以指定basePackageClasses或basePackages(或其别名值)来定义要扫描的特定包。__如果没有定义特定的包，则扫描将从声明此注释的类的包中开始进行。__
> >
> > <font size="3">注意<<context:component-scan>>元素有一个注解-配置属性;但是，这个注释不是。这是因为在几乎所有使用@ComponentScan的情况下，默认的注释配置处理(例如处理@Autowired和friends)都是假定的。此外，在使用AnnotationConfigApplicationContext时，注释配置处理器总是被注册的，这意味着在@ComponentScan级别禁用它们的任何尝试都将被忽略。</font>
> >
> > 



### SpringApplication.<font color="red">run</font>(MyApplication.class,args);

__文档说明:__SpringApplication类，可用于从Java mian方法中加载和启动Spring应用程序。默认情况下，类将执行以下步骤来加载您的应用程序:

1. 创建一个适当的__ApplicationContext__实例(取决于您的类路径)

2. 注册一个__CommandLinePropertySource__以将命令行参数公开为Spring属性

3. 刷新application context，加载所有单例bean

4. 触发任何__CommandLineRunner__ bean

__<font color="red" size="5">进入springApplication.run</font>__

> __文档说明:__它是一个静态辅助方法，可用于使用默认设置从指定的源(启动的主类)运行SpringApplication。
>
>  <font color="cb7731">public</font>  <font color="cb7731">static</font> ConfigurableApplicationContext run(Class<?> primarySource,
> 			String... args) {
> 		 <font color="cb7731">return</font> run( <font color="cb7731">new</font> Class<?>[] { primarySource }, args);
> 	}
>
> springApplication.run又调用了另一个run
>
>  <font color="cb7731">public</font>  <font color="cb7731">static</font> ConfigurableApplicationContext run(Class<?>[] primarySources,
> 			String[] args) {
>  <font color="#559354">// 创建了一个SpringApplication的实例</font> 
> 		 <font color="cb7731">return</font>  <font color="cb7731">new</font> SpringApplication(primarySources).run(args);
> 	}
>
> __文档说明:__它是一个静态辅助方法，可用于使用默认设置和__用户提供的参数__从指定的源(启动的主类)运行SpringApplication。
> __<font color="gree" size="5">进入springApplication的构造方法</font>__
>
> >  <font color="cb7731">public</font> SpringApplication(Class<?>... primarySources) {
> > 		this(null, primarySources);
> > 	}
> >
> > __<font color="red" size="5">重点</font>__
> > __文档说明:__创建一个新的SpringApplication实例。 应用程序上下文将从指定的主要源加载Bean(有关详细信息，请参见类级文档)可以在调用run（String ...）方法之前进行自定义。
> >
> >  <font color="cb7731">public</font> SpringApplication(ResourceLoader resourceLoader, Class<?>... primarySources) {
> >  <font color="#559354">// resourceLoader为null</font> 
> > 		this.resourceLoader = resourceLoader;
> > 		Assert.notNull(primarySources, "PrimarySources must not be null");
> > 		this.primarySources =  <font color="cb7731">new</font> LinkedHashSet<>(Arrays.asList(primarySources));
> >  <font color="#559354">// 判断应用的类型 </font> 
> > 		this.webApplicationType = WebApplicationType.deduceFromClasspath();//webApplicationType:"SERVLET"
> >  <font color="#559354">// ApplicationContextInitializer. <font color="cb7731">class</font> 回调接口，用于在刷新之前初始化Spring ConfigurableApplicationContext。</font> 
> > 		setInitializers((Collection) getSpringFactoriesInstances(
> > 				ApplicationContextInitializer.class));
> > 		setListeners((Collection) getSpringFactoriesInstances(ApplicationListener.class));
> > 		this.mainApplicationClass = deduceMainApplicationClass();
> > 	}
> >
> > __进入WebApplicationType.deduceFromClasspath(); 判断应用的类型__
> >
> > > <font color="cb7731">public</font>  <font color="cb7731">enum</font> WebApplicationType {
> > > 	  <font color="#559354">// 应用程序不应该作为web应用程序运行，也不应该启动嵌入式web服务器。</font> 
> > > 	NONE,
> > > 	 <font color="#559354">// 应用程序应该作为基于servlet的web应用程序运行，并且启动嵌入式servlet web服务器。</font> 
> > > 	SERVLET,
> > > 	  <font color="#559354">// 该应用程序应作为反应式Web应用程序运行，并应启动嵌入式反应式Web服务器。</font> 
> > > 	REACTIVE;
> > >      <font color="cb7731">private</font>  <font color="cb7731">static</font>  <font color="cb7731">final</font> String[] SERVLET_INDICATOR_CLASSES = { "javax.servlet.Servlet",
> > > 			"org.springframework.web.context.ConfigurableWebApplicationContext" };
> > > 	 <font color="cb7731">private</font>  <font color="cb7731">static</font>  <font color="cb7731">final</font> String WEBMVC_INDICATOR_CLASS = "org.springframework."
> > > 			+ "web.servlet.DispatcherServlet";
> > > 	 <font color="cb7731">private</font>  <font color="cb7731">static</font>  <font color="cb7731">final</font> String WEBFLUX_INDICATOR_CLASS = "org."
> > > 			+ "springframework.web.reactive.DispatcherHandler";
> > > 	 <font color="cb7731">private</font>  <font color="cb7731">static</font>  <font color="cb7731">final</font> String JERSEY_INDICATOR_CLASS = "org.glassfish.jersey.servlet.ServletContainer";
> > > 	 <font color="cb7731">private</font>  <font color="cb7731">static</font>  <font color="cb7731">final</font> String SERVLET_APPLICATION_CONTEXT_CLASS = "org.springframework.web.context.WebApplicationContext";
> > > 	 <font color="cb7731">private</font>  <font color="cb7731">static</font>  <font color="cb7731">final</font> String REACTIVE_APPLICATION_CONTEXT_CLASS = "org.springframework.boot.web.reactive.context.ReactiveWebApplicationContext";
> > > 
> > > 
> > >  <font color="cb7731">static</font> WebApplicationType deduceFromClasspath() {
> > > 		 <font color="cb7731">if</font> (ClassUtils.isPresent(WEBFLUX_INDICATOR_CLASS, null)
> > > 				&& !ClassUtils.isPresent(WEBMVC_INDICATOR_CLASS, null)
> > > 				&& !ClassUtils.isPresent(JERSEY_INDICATOR_CLASS, null)) {
> > > 			 <font color="cb7731">return</font> WebApplicationType.REACTIVE;
> > > 		}
> > > 		for (String className : SERVLET_INDICATOR_CLASSES) {
> > >			 <font color="cb7731">if</font> (!ClassUtils.isPresent(className, null)) {
> > > 				 <font color="cb7731">return</font> WebApplicationType.NONE;
> > >			}
> > >		}
> > > 		 <font color="cb7731">return</font> WebApplicationType.SERVLET;
> > > 	}
> > > 
> > > __<font color="red">它返回了应用的类型 SERVLET</font>__
> >
> > > __进入getSpringFactoriesInstances(ApplicationContextInitializer.class));__
> > >
> > >  <font color="#559354">// 获取Spring工厂实例</font> 
> > >  <font color="cb7731">private</font> <T> Collection<T> <font color="blue">getSpringFactoriesInstances</font>(Class<T> type) {
> > > 	 <font color="cb7731">return</font> getSpringFactoriesInstances(type,  <font color="cb7731">new</font> Class<?>[] {});
> > > }
> > >
> > >  <font color="cb7731">private</font> <T> Collection<T> <font color="blue">getSpringFactoriesInstances</font>(Class<T> type,
> > > Class<?>[] parameterTypes, Object... args) {
> > >  <font color="#559354">// 获取类加载器</font> 
> > > ClassLoader classLoader = [getClassLoader();](#getClassLoader)
> > >  <font color="#559354">// 使用名称并确保唯一以防止重复</font> 
> > >  <font color="#559354">// 返回初始化器实例的名称</font> 
> > > Set<String> names =  <font color="cb7731">new</font> LinkedHashSet<>(
> > >     [ SpringFactoriesLoader.loadFactoryNames(type, classLoader));](#loadFactoryNames)
> > > List<T> instances = createSpringFactoriesInstances(type, parameterTypes,
> > >       classLoader, args, names);
> > > 	AnnotationAwareOrderComparator.sort(instances);
> > > 	 <font color="cb7731">return</font> instances;
> > > }
> > >
> > > 
> > >
> > > > <span id="getClassLoader">__进入getClassLoader();__</span>
> > > >
> > > >  <font color="cb7731">public</font> ClassLoader getClassLoader() {
> > > >   <font color="#559354">// 如果resourceLoader不为空，返回它的类加载器</font> 
> > > >    		 <font color="cb7731">if</font> (this.resourceLoader != null) {
> > > > 			 <font color="cb7731">return</font> this.resourceLoader.getClassLoader();
> > > > 		}
> > > > 		 <font color="cb7731">return</font> ClassUtils.getDefaultClassLoader();
> > > > 	}
> > > > 
> > > > __进入ClassUtils.getDefaultClassLoader();__
> > > >
> > > > > <font color="#559354">// 不能为空</font> 
> > > >> @Nullable
> > > > > 	 <font color="cb7731">public</font>  <font color="cb7731">static</font> ClassLoader getDefaultClassLoader() {
> > > > > 		ClassLoader cl = null;
> > > > > 		 <font color="cb7731">try</font> {
> > > > >          <font color="#559354">// 返回默认的类加载器，通常是线程上下文类加载器 如果有的话</font> 
> > > > > 			cl = Thread.currentThread().getContextClassLoader();
> > > > >     		}
> > > > > 		 <font color="cb7731">catch</font> (Throwable ex) {
> > > > > 			 <font color="#559354">// Cannot access thread context ClassLoader - falling back...</font> 
> > > > > 		}
> > > > > 		 <font color="cb7731">if</font> (cl == null) {
> > > > >          <font color="#559354">// 如果默认类加载器不存在，返回加载ClassUtils类的类加载器 一般是启动类加载器</font> 
> > > > > 			 <font color="#559354">// No thread context  <font color="cb7731">class</font> loader -> use  <font color="cb7731">class</font> loader of this class.</font> 
> > > > >     			cl = ClassUtils.class.getClassLoader();
> > > > > 			 <font color="cb7731">if</font> (cl == null) {
> > > > >              <font color="#559354">// 如果还为空 返回系统类加载器</font> 
> > > > > 				 <font color="#559354">// getClassLoader() returning  <font color="cb7731">null</font> indicates the bootstrap ClassLoader</font> 
> > > > >     				 <font color="cb7731">try</font> {
> > > > > 					cl = ClassLoader.getSystemClassLoader();
> > > > > 				}
> > > > > 				 <font color="cb7731">catch</font> (Throwable ex) {
> > > > > 					 <font color="#559354">// Cannot access system ClassLoader - oh well, maybe the caller can live with null...</font> 
> > > > > 				}
> > > > > 			}
> > > > > 		}
> > > > > 		 <font color="cb7731">return</font> cl;
> > > > > 	}
> > > > > 
> > > > > 
> > >
> > > <span id="loadFactoryNames"><font color="red" size="5">__进入SpringFactoriesLoader.loadFactoryNames(type, classLoader));__</font></span>
> > >
> > > > __方法功能:使用给定的类加载器从<font color="red">各个</font>“ META-INF / spring.factories”中加载并实例化给定类型的工厂。__
> > > >
> > > >  <font color="cb7731">public</font>  <font color="cb7731">final</font>  <font color="cb7731">class</font> <font color="blue">SpringFactoriesLoader</font> {
> > > >  <font color="cb7731">private</font>  <font color="cb7731">static</font>  <font color="cb7731">final</font> Map<ClassLoader, MultiValueMap<String, String>> cache =  <font color="cb7731">new</font> ConcurrentReferenceHashMap<>();
> > > >  <font color="cb7731">public</font>  <font color="cb7731">static</font>  <font color="cb7731">final</font> String FACTORIES_RESOURCE_LOCATION = "META-INF/spring.factories";
> > > >  <font color="#559354">// 加载给定类型的工厂实现的完全限定的类名，从"META-INF/spring.factories"中读取，使用给定的类加载器</font> 
> > > >  <font color="cb7731">public</font>  <font color="cb7731">static</font> List<String> loadFactoryNames(Class<?> factoryClass, @Nullable ClassLoader classLoader) {
> > > >    String factoryClassName = <span id="R_getName">factoryClass.[getName();](#getName)</span>
> > > >     <font color="cb7731">return</font> loadSpringFactories(classLoader).getOrDefault(factoryClassName, Collections.emptyList());
> > > > }
> > > >
> > > >  <font color="#559354">// 获取META-INF/spring.factories中默认的配置</font> 
> > > >   <font color="cb7731">private</font>  <font color="cb7731">static</font> Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader) {
> > > >   <font color="#559354">// MultiValueMap<K, V> extends Map<K, List<V>> MultiValueMap是一个Map的扩展，一个key可以存储多个value </font> 
> > > >   <font color="#559354">// 其实就是一个key对应一个存储多个值的集合</font> 
> > > >     MultiValueMap<String, String> result = cache.get(classLoader);
> > > >      <font color="cb7731">if</font> (result != null) {
> > > >         <font color="cb7731">return</font> result;
> > > >     }
> > > >      <font color="cb7731">try</font> {
> > > >  <font color="#559354">// 去获取一个枚举对象</font> 
> > > >        Enumeration<URL> urls = (classLoader !=  <font color="cb7731">null</font> ?
> > > >  <font color="#559354">// 如果类加载器不为空，通过getResources去加载"META-INF/spring.factories"中的内容</font> 
> > > >              classLoader.<span id="R_getResources">[getResources(FACTORIES_RESOURCE_LOCATION)](#"getResources")</span> :
> > > >              ClassLoader.getSystemResources(FACTORIES_RESOURCE_LOCATION));
> > > >        result =  <font color="cb7731">new</font> LinkedMultiValueMap<>();
> > > >        while (urls.hasMoreElements()) {
> > > >  <font color="#559354">// jar:file:/C:/Users/idea/.gradle/caches/*/*/*/spring-boot-autoconfigure-2.1.3.RELEASE.jar!/META-INF/spring.factories//jar:file:/C:/Users/idea/.gradle/caches/*/*/*/spring-boot-2.1.3.RELEASE.jar!/META-INF/spring.factories   <font color="#559354">// *\*\spring-beans-5.1.5.RELEASE.jar!\META-INF\spring.factories</font> 
> > > >           URL url = urls.nextElement();
> > > >           UrlResource resource =  <font color="cb7731">new</font> UrlResource(url);
> > > >           Properties properties = PropertiesLoaderUtils.<span id="R_loadProperties">[loadProperties(resource);](#"loadProperties")</span>
> > > >           for (Map.Entry<?, ?> en <font color="cb7731">try</font> : properties.entrySet()) {
> > > >              String factoryClassName = ((String) entry.getKey()).trim();
> > > >              for (String factoryName : StringUtils.commaDelimitedListToStringArray((String) entry.getValue())) {
> > > >  <font color="#559354">// factoryClassName:]"org.springframework.boot.autoconfigure.AutoConfigurationImportFilter" 接口</font> 
> > > >  <font color="#559354">// factoryName:"org.springframework.boot.autoconfigure.condition.OnBeanCondition" 具体工厂实现</font> 
> > > >  <font color="#559354">// 循环将对应键的配置全部添加进值里面 如果有其他同key的value 直接追加</font> 
> > > >                 result.add(factoryClassName, factoryName.trim());
> > > >              }
> > > >           }
> > > >        }
> > > > 	 <font color="#559354">// 将每次遍历结果放在cache缓存中</font> 
> > > >      <font color="#559354">// Map<ClassLoader, MultiValueMap<String, String>> cache</font> 
> > > >        cache.put(classLoader, result);
> > > > 			 <font color="cb7731">return</font>  result;
> > > >     }
> > > >      <font color="cb7731">catch</font> (IOException ex) {
> > > >        throw  <font color="cb7731">new</font> IllegalArgumentException("Unable to load factories from location [" +
> > > >              FACTORIES_RESOURCE_LOCATION + "]", ex);
> > > >     }
> > > >  }
> > > > }
> > > >
> > > >
> > > >  <font color="cb7731">public</font>  <font color="cb7731">final</font>  <font color="cb7731">class</font> SpringFactoriesLoader {
> > > >   <font color="cb7731">private</font>  <font color="cb7731">static</font>  <font color="cb7731">final</font> Map<ClassLoader, MultiValueMap<String, String>> cache =  <font color="cb7731">new</font> ConcurrentReferenceHashMap<>();
> > > >   <font color="cb7731">public</font>  <font color="cb7731">static</font>  <font color="cb7731">final</font> String FACTORIES_RESOURCE_LOCATION = "META-INF/spring.factories";
> > > >   <font color="#559354">// 加载给定类型的工厂实现的完全限定的类名，从"META-INF/spring.factories"中读取，使用给定的类加载器</font> 
> > > >   <font color="cb7731">public</font>  <font color="cb7731">static</font> List<String> loadFactoryNames(Class<?> factoryClass, @Nullable ClassLoader classLoader) {
> > > >     String factoryClassName = factoryClass.getName();
> > > >      <font color="cb7731">return</font> loadSpringFactories(classLoader).getOrDefault(factoryClassName, Collections.emptyList());
> > > >  }
> > > >
> > > >   <font color="#559354">// 获取META-INF/spring.factories中默认的配置</font> 
> > > >   <font color="cb7731">private</font>  <font color="cb7731">static</font> Map<String, List<String>> loadSpringFactories(@Nullable ClassLoader classLoader) {
> > > >      <font color="#559354">// MultiValueMap<K, V> extends Map<K, List<V>> MultiValueMap是一个Map的扩展，一个key可以存储多个value</font> 
> > > >      <font color="#559354">// 其实就是一个key对应一个存储多个值的集合</font> 
> > > >     MultiValueMap<String, String> result = cache.get(classLoader);
> > > >      <font color="cb7731">if</font> (result != null) {
> > > >         <font color="cb7731">return</font> result;
> > > >     }
> > > >      <font color="cb7731">try</font> {
> > > >         <font color="#559354">// 去获取一个枚举对象</font> 
> > > >        Enumeration<URL> urls = (classLoader !=  <font color="cb7731">null</font> ?
> > > >  <font color="#559354">// 如果类加载器不为空，通过getResources去加载"META-INF/spring.factories"中的内容</font> 
> > > >              classLoader.getResources(FACTORIES_RESOURCE_LOCATION) :
> > > >              ClassLoader.getSystemResources(FACTORIES_RESOURCE_LOCATION));
> > > >        result =  <font color="cb7731">new</font> LinkedMultiValueMap<>();
> > > >        while (urls.hasMoreElements()) {
> > > >  <font color="#559354">// jar:file:/C:/Users/idea/.gradle/caches/*/*/*/spring-boot-autoconfigure-2.1.3.RELEASE.jar!/META-INF/spring.factories</font> 
> > > >  <font color="#559354">// jar:file:/C:/Users/idea/.gradle/caches/*/*/*/spring-boot-2.1.3.RELEASE.jar!/META-INF/spring.factories   <font color="#559354">// *\*\spring-beans-5.1.5.RELEASE.jar!\META-INF\spring.factories</font> 
> > > >           URL url = urls.nextElement();
> > > >           UrlResource resource =  <font color="cb7731">new</font> UrlResource(url);
> > > >           Properties properties = PropertiesLoaderUtils.loadProperties(resource);
> > > >           for (Map.Entry<?, ?> en <font color="cb7731">try</font> : properties.entrySet()) {
> > > >              String factoryClassName = ((String) entry.getKey()).trim();
> > > >              for (String factoryName : StringUtils.commaDelimitedListToStringArray((String) entry.getValue())) {
> > > >  <font color="#559354">// factoryClassName:]"org.springframework.boot.autoconfigure.AutoConfigurationImportFilter" 接口</font> 
> > > >  <font color="#559354">// factoryName:"org.springframework.boot.autoconfigure.condition.OnBeanCondition" 具体工厂实现</font> 
> > > >  <font color="#559354">// 循环将对应键的配置全部添加进值里面 如果有其他同key的value 直接追加</font> 
> > > >                 result.add(factoryClassName, factoryName.trim());
> > > >              }
> > > >           }
> > > >        }
> > > >         <font color="#559354">// 将每次遍历结果放在cache缓存中</font> 
> > > >         <font color="#559354">// Map<ClassLoader, MultiValueMap<String, String>> cache</font> 
> > > >        cache.put(classLoader, result);
> > > >         <font color="cb7731">return</font> result;
> > > >     }
> > > >      <font color="cb7731">catch</font> (IOException ex) {
> > > >        throw  <font color="cb7731">new</font> IllegalArgumentException("Unable to load factories from location [" +
> > > >              FACTORIES_RESOURCE_LOCATION + "]", ex);
> > > >     }
> > > >  }
> > > > }
> > > >
> > > > <span id="getName">[<font color="blue">__getName()底层扩展__</font>](#"R_getName")</span>
> > > >
> > > > > factoryClass.<font color="blue" size="5">getName();</font>
> > > > > __文档说明:__以String形式返回此Class对象表示的实体名称（类，接口，数组类，原始类型或void）。
> > > > > 如果该类对象表示的引用类型不是数组类型，则返回该类的二进制名称，如Java™语言规范所指定。
> > > > > 如果此类对象表示原始类型或void，则返回的名称是一个String，该字符串等于对应于原始类型或void的Java语言关键字。
> > > > > 如果此类对象表示一类数组，则名称的内部形式由元素类型的名称组成，其后是一个或多个代表数组嵌套深度的'['字符。
> > > > > __元素类型名称的编码如下：__
> > > > >
> > > > > |    Element Type    |  Encoding   |
> > > > > | :----------------: | :---------: |
> > > > > |       <font color="cb7731">boolean</font>       |      Z      |
> > > > > |        byte        |      B      |
> > > > > |        char        |      C      |
> > > > > |  <font color="cb7731">class</font> or interface | Lclassname; |
> > > > > |       double       |      D      |
> > > > > |       float        |      F      |
> > > > > |         <font color="cb7731">int</font>         |      I      |
> > > > > |        long        |      J      |
> > > > > |       short        |      S      |
> > > > >
> > > > >
> > > > > 
> > > > >
> > > > > ```java
> > > > > Examples:
> > > > >        String.class.getName()
> > > > >            returns "java.lang.String"
> > > > >        byte.class.getName()
> > > > >            returns "byte"
> > > > >        ( <font color="cb7731">new</font> Object[3]).getClass().getName()
> > > > >            returns "[Ljava.lang.Object;"
> > > > >        ( <font color="cb7731">new</font> int[3][4][5][6][7][8][9]).getClass().getName()
> > > > >            returns "[[[[[[[I"
> > > > > ```
> > > > >
> > > > > Class.java
> > > > >  <font color="cb7731">public</font> String getName() {
> > > > >      String name = this.name;
> > > > >          <font color="cb7731">if</font> (name == null)
> > > > >             this.name = name = getName0();
> > > > >          <font color="cb7731">return</font> name;
> > > > >     }
> > > >
> > > > <span id="getResources">[<font color="blue">__getResources()底层扩展__</font>](#"R_getResources")</span>
> > > >
> > > > > __文档说明:__查找具有给定__名称(name)__的所有资源。 资源是某些数据（图像，音频，文本等），可由类代码以与代码位置无关的方式进行访问。
> > > > > 资源的名称是用/分隔的路径名，用于标识资源。
> > > > > 搜索顺序在getResource（String）的文档中进行了描述。
> > > > > 参数： 名称–资源名称
> > > > > 返回值： 资源的URL对象的枚举。 如果找不到资源，则枚举将为空。 类加载器无权访问的资源将不在枚举中。
> > > > > api注意： 重写此方法时，建议实现确保任何委托与getResource（String）方法一致。 这应该确保Enumeration的nextElement方法返回的第一个元素与getResource（String）方法将返回的资源相同。
> > > > >
> > > > >ClassLoader.java
> > > > > 
> > > > >  <font color="cb7731">public</font> Enumeration<URL> getResources(String name) throws IOException {
> > > > >      @SuppressWarnings("unchecked")
> > > > >      Enumeration<URL>[] tmp = (Enumeration<URL>[])  <font color="cb7731">new</font> Enumeration<?>[2];
> > > > >          <font color="cb7731">if</font> (parent != null) {
> > > > >             tmp[0] = parent.getResources(name);
> > > > >         }  <font color="cb7731">else</font> {
> > > > >             tmp[0] = getBootstrapResources(name);
> > > > >         }
> > > > >         tmp[1] = findResources(name);
> > > > >    
> > > > >    ​      <font color="cb7731">return</font>  <font color="cb7731">new</font> CompoundEnumeration<>(tmp);
> > > > > 
> > > > >     }
> > > > >    
> > > > >  <font color="#559354">// 这个枚举中可以包含它自己(CompoundEnumeration)的枚举,进而一层层嵌套 可以寻找一层层中的文件 如jar包</font> 
> > > > >  <font color="cb7731">public</font>  <font color="cb7731">class</font> CompoundEnumeration<E> implements Enumeration<E> {
> > > > >  <font color="cb7731">private</font> Enumeration<E>[] enums;
> > > > >   <font color="cb7731">private</font>  <font color="cb7731">int</font> index = 0;
> > > > >   <font color="cb7731">public</font> CompoundEnumeration(Enumeration<E>[] var1) {
> > > > >      this.enums = var1;
> > > > >     }
> > > > >    
> > > > >    ![image-20201027220506344](C:\Users\idea\AppData\Roaming\Typora\typora-user-images\image-20201027220506344.png)
> > > >
> > > > <span id="loadProperties">[<font color="red">__PropertiesLoaderUtils.loadProperties(resource);详解__</font>](#"R_loadProperties")</span>
> > > >
> > > > >  <font color="cb7731">public</font>  <font color="cb7731">static</font> Properties loadProperties(Resource resource) throws IOException {
> > > > > 		Properties props =  <font color="cb7731">new</font> Properties();
> > > > > 		fillProperties(props, resource);
> > > > > 		 <font color="cb7731">return</font> props;
> > > > > 	}
> > > > > 
> > > > > ![image-20201028164943724](C:\Users\idea\AppData\Roaming\Typora\typora-user-images\image-20201028164943724.png)
> > > > >
> > > > > 
> > > > > 
> > > > > <font color="cb7731">public</font>  <font color="cb7731">static</font>  <font color="cb7731">void</font> fillProperties(Properties props, Resource resource) throws IOException {
> > > > >		InputStream is = resource.getInputStream();
> > > > > 		 <font color="cb7731">try</font> {
> > > > >           <font color="#559354">// 获取文件名字 filename:"spring.factories</font> 
> > > > > 			String filename = resource.getFilename();
> > > > >           <font color="#559354">// 如果文件是xml结尾的 使用loadFromXML加载</font> 
> > > > >    			 <font color="cb7731">if</font> (filename !=  <font color="cb7731">null</font> && filename.endsWith(XML_FILE_EXTENSION)) {
> > > > > 				props.loadFromXML(is);
> > > > >    			}
> > > > > 			 <font color="cb7731">else</font> {
> > > > >               <font color="#559354">// 使用文件加载</font> 
> > > > > 				props.load(is);
> > > > > 			}
> > > > >    		}
> > > > > 		 <font color="cb7731">finally</font> {
> > > > > 			is.close();
> > > > > 		}
> > > > > 	}
> > > >
> > > > > <span id="commaDelimitedListToStringArray">[__StringUtils.commaDelimitedListToStringArray((String) entry.getValue())__](#"R_commaDelimitedListToStringArray")</span>
> > > > >
> > > > >  <font color="#559354">// 把逗号分隔的转换为一个字符串数组</font> 
> > > > >  <font color="cb7731">public</font>  <font color="cb7731">static</font> String[] commaDelimitedListToStringArray(@Nullable String str) {
> > > > > 		 <font color="cb7731">return</font> delimitedListToStringArray(str, ",");
> > > > > 	}
> > > > > 
> > > > > 
> > > >
> > > > <details>
> > > > 	<summary>spring-boot-autoconfigure-2.1.3.RELEASE.jar!\META-INF\spring.factories</summary>
> > > > <pre><code>
> > > > #Initializers
> > > > org.springframework.context.ApplicationContextInitializer=\
> > > > org.springframework.boot.autoconfigure.SharedMetadataReaderFactoryContextInitializer,\
> > > > org.springframework.boot.autoconfigure.logging.ConditionEvaluationReportLoggingListener
> > > > #Application Listeners
> > > > org.springframework.context.ApplicationListener=\
> > > > org.springframework.boot.autoconfigure.BackgroundPreinitializer
> > > > #Auto Configuration Import Listeners
> > > > org.springframework.boot.autoconfigure.AutoConfigurationImportListener=\
> > > > org.springframework.boot.autoconfigure.condition.ConditionEvaluationReportAutoConfigurationImportListener
> > > > #Auto Configuration Import Filters
> > > > org.springframework.boot.autoconfigure.AutoConfigurationImportFilter=\
> > > > org.springframework.boot.autoconfigure.condition.OnBeanCondition,\
> > > > org.springframework.boot.autoconfigure.condition.OnClassCondition,\
> > > > org.springframework.boot.autoconfigure.condition.OnWebApplicationCondition
> > > > #Auto Configure
> > > > org.springframework.boot.autoconfigure.EnableAutoConfiguration=\
> > > > org.springframework.boot.autoconfigure.admin.SpringApplicationAdminJmxAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.aop.AopAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.amqp.RabbitAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.batch.BatchAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.cache.CacheAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.cassandra.CassandraAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.cloud.CloudServiceConnectorsAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.context.ConfigurationPropertiesAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.context.MessageSourceAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.context.PropertyPlaceholderAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.couchbase.CouchbaseAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.dao.PersistenceExceptionTranslationAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.cassandra.CassandraDataAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.cassandra.CassandraReactiveDataAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.cassandra.CassandraReactiveRepositoriesAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.cassandra.CassandraRepositoriesAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.couchbase.CouchbaseDataAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.couchbase.CouchbaseReactiveDataAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.couchbase.CouchbaseReactiveRepositoriesAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.couchbase.CouchbaseRepositoriesAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchDataAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.elasticsearch.ElasticsearchRepositoriesAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.jdbc.JdbcRepositoriesAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.jpa.JpaRepositoriesAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.ldap.LdapRepositoriesAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.mongo.MongoDataAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.mongo.MongoReactiveDataAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.mongo.MongoReactiveRepositoriesAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.mongo.MongoRepositoriesAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.neo4j.Neo4jDataAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.neo4j.Neo4jRepositoriesAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.solr.SolrRepositoriesAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.redis.RedisAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.redis.RedisReactiveAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.redis.RedisRepositoriesAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.rest.RepositoryRestMvcAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.data.web.SpringDataWebAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.elasticsearch.jest.JestAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.elasticsearch.rest.RestClientAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.flyway.FlywayAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.freemarker.FreeMarkerAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.gson.GsonAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.h2.H2ConsoleAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.hateoas.HypermediaAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.hazelcast.HazelcastAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.hazelcast.HazelcastJpaDependencyAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.http.HttpMessageConvertersAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.http.codec.CodecsAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.influx.InfluxDbAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.info.ProjectInfoAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.integration.IntegrationAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.jackson.JacksonAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.jdbc.DataSourceAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.jdbc.JdbcTemplateAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.jdbc.JndiDataSourceAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.jdbc.XADataSourceAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.jdbc.DataSourceTransactionManagerAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.jms.JmsAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.jmx.JmxAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.jms.JndiConnectionFactoryAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.jms.activemq.ActiveMQAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.jms.artemis.ArtemisAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.groovy.template.GroovyTemplateAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.jersey.JerseyAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.jooq.JooqAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.jsonb.JsonbAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.kafka.KafkaAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.ldap.embedded.EmbeddedLdapAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.ldap.LdapAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.liquibase.LiquibaseAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.mail.MailSenderAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.mail.MailSenderValidatorAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.mongo.embedded.EmbeddedMongoAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.mongo.MongoAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.mongo.MongoReactiveAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.mustache.MustacheAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.orm.jpa.HibernateJpaAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.quartz.QuartzAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.reactor.core.ReactorCoreAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.security.servlet.SecurityAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.security.servlet.SecurityRequestMatcherProviderAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.security.servlet.UserDetailsServiceAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.security.servlet.SecurityFilterAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.security.reactive.ReactiveSecurityAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.security.reactive.ReactiveUserDetailsServiceAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.sendgrid.SendGridAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.session.SessionAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.security.oauth2.client.servlet.OAuth2ClientAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.security.oauth2.client.reactive.ReactiveOAuth2ClientAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.security.oauth2.resource.servlet.OAuth2ResourceServerAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.security.oauth2.resource.reactive.ReactiveOAuth2ResourceServerAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.solr.SolrAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.task.TaskExecutionAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.task.TaskSchedulingAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.thymeleaf.ThymeleafAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.transaction.TransactionAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.transaction.jta.JtaAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.validation.ValidationAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.web.client.RestTemplateAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.web.embedded.EmbeddedWebServerFactoryCustomizerAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.web.reactive.HttpHandlerAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.web.reactive.ReactiveWebServerFactoryAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.web.reactive.WebFluxAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.web.reactive.error.ErrorWebFluxAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.web.reactive.function.client.ClientHttpConnectorAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.web.reactive.function.client.WebClientAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.web.servlet.DispatcherServletAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.web.servlet.ServletWebServerFactoryAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.web.servlet.error.ErrorMvcAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.web.servlet.HttpEncodingAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.web.servlet.MultipartAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.web.servlet.WebMvcAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.websocket.reactive.WebSocketReactiveAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.websocket.servlet.WebSocketServletAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.websocket.servlet.WebSocketMessagingAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.webservices.WebServicesAutoConfiguration,\
> > > > org.springframework.boot.autoconfigure.webservices.client.WebServiceTemplateAutoConfiguration
> > > > #Failure analyzers
> > > > org.springframework.boot.diagnostics.FailureAnalyzer=\
> > > > org.springframework.boot.autoconfigure.diagnostics.analyzer.NoSuchBeanDefinitionFailureAnalyzer,\
> > > > org.springframework.boot.autoconfigure.jdbc.DataSourceBeanCreationFailureAnalyzer,\
> > > > org.springframework.boot.autoconfigure.jdbc.HikariDriverConfigurationFailureAnalyzer,\
> > > > org.springframework.boot.autoconfigure.session.NonUniqueSessionRepositoryFailureAnalyzer
> > > > #Template availability providers
> > > > org.springframework.boot.autoconfigure.template.TemplateAvailabilityProvider=\
> > > > org.springframework.boot.autoconfigure.freemarker.FreeMarkerTemplateAvailabilityProvider,\
> > > > org.springframework.boot.autoconfigure.mustache.MustacheTemplateAvailabilityProvider,\
> > > > org.springframework.boot.autoconfigure.groovy.template.GroovyTemplateAvailabilityProvider,\
> > > > org.springframework.boot.autoconfigure.thymeleaf.ThymeleafTemplateAvailabilityProvider,\
> > > > org.springframework.boot.autoconfigure.web.servlet.JspTemplateAvailabilityProvider
> > > > </code></pre>
> > > >
> > > > <details>
> > > > 	<summary>spring-boot-2.1.3.RELEASE.jar!\META-INF\spring.factories</summary>
> > > > <pre><code>
> > > > #PropertySource Loaders
> > > > org.springframework.boot.env.PropertySourceLoader=\
> > > > org.springframework.boot.env.PropertiesPropertySourceLoader,\
> > > > org.springframework.boot.env.YamlPropertySourceLoader
> > > > #Run Listeners
> > > > org.springframework.boot.SpringApplicationRunListener=\
> > > > org.springframework.boot.context.event.EventPublishingRunListener
> > > > #Error Reporters
> > > > org.springframework.boot.SpringBootExceptionReporter=\
> > > > org.springframework.boot.diagnostics.FailureAnalyzers
> > > > #Application Context Initializers
> > > > org.springframework.context.ApplicationContextInitializer=\
> > > > org.springframework.boot.context.ConfigurationWarningsApplicationContextInitializer,\
> > > > org.springframework.boot.context.ContextIdApplicationContextInitializer,\
> > > > org.springframework.boot.context.config.DelegatingApplicationContextInitializer,\
> > > > org.springframework.boot.web.context.ServerPortInfoApplicationContextInitializer
> > > > #Application Listeners
> > > > org.springframework.context.ApplicationListener=\
> > > > org.springframework.boot.ClearCachesApplicationListener,\
> > > > org.springframework.boot.builder.ParentContextCloserApplicationListener,\
> > > > org.springframework.boot.context.FileEncodingApplicationListener,\
> > > > org.springframework.boot.context.config.AnsiOutputApplicationListener,\
> > > > org.springframework.boot.context.config.ConfigFileApplicationListener,\
> > > > org.springframework.boot.context.config.DelegatingApplicationListener,\
> > > > org.springframework.boot.context.logging.ClasspathLoggingApplicationListener,\
> > > > org.springframework.boot.context.logging.LoggingApplicationListener,\
> > > > org.springframework.boot.liquibase.LiquibaseServiceLocatorApplicationListener
> > > > #Environment Post Processors
> > > > org.springframework.boot.env.EnvironmentPostProcessor=\
> > > > org.springframework.boot.cloud.CloudFoundryVcapEnvironmentPostProcessor,\
> > > > org.springframework.boot.env.SpringApplicationJsonEnvironmentPostProcessor,\
> > > > org.springframework.boot.env.SystemEnvironmentPropertySourceEnvironmentPostProcessor
> > > > #Failure Analyzers
> > > > org.springframework.boot.diagnostics.FailureAnalyzer=\
> > > > org.springframework.boot.diagnostics.analyzer.BeanCurrentlyInCreationFailureAnalyzer,\
> > > > org.springframework.boot.diagnostics.analyzer.BeanDefinitionOverrideFailureAnalyzer,\
> > > > org.springframework.boot.diagnostics.analyzer.BeanNotOfRequiredTypeFailureAnalyzer,\
> > > > org.springframework.boot.diagnostics.analyzer.BindFailureAnalyzer,\
> > > > org.springframework.boot.diagnostics.analyzer.BindValidationFailureAnalyzer,\
> > > > org.springframework.boot.diagnostics.analyzer.UnboundConfigurationPropertyFailureAnalyzer,\
> > > > org.springframework.boot.diagnostics.analyzer.ConnectorStartFailureAnalyzer,\
> > > > org.springframework.boot.diagnostics.analyzer.NoSuchMethodFailureAnalyzer,\
> > > > org.springframework.boot.diagnostics.analyzer.NoUniqueBeanDefinitionFailureAnalyzer,\
> > > > org.springframework.boot.diagnostics.analyzer.PortInUseFailureAnalyzer,\
> > > > org.springframework.boot.diagnostics.analyzer.ValidationExceptionFailureAnalyzer,\
> > > > org.springframework.boot.diagnostics.analyzer.InvalidConfigurationPropertyNameFailureAnalyzer,\
> > > > org.springframework.boot.diagnostics.analyzer.InvalidConfigurationPropertyValueFailureAnalyzer
> > > > #FailureAnalysisReporters
> > > > org.springframework.boot.diagnostics.FailureAnalysisReporter=\
> > > > org.springframework.boot.diagnostics.LoggingFailureAnalysisReporter
> > > > </code></pre>
> > > > 
> > > >
> > > > <img src="C:\Users\idea\AppData\Roaming\Typora\typora-user-images\image-20201027184006010.png" alt="image-20201027184006010"  />
> > > >
> > > > 
> > > >
> > > > 



