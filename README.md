# ffapilimit

#### 介绍
快火api限流器，基于spring boot 2.x，开箱即用，并支持分布式服务限流
联系方式 qq：625995897


#### 使用说明
   1、引入jar包
   
        <dependency>
            <groupId>com.smallstep</groupId>
            <artifactId>ffapilimit-boot-starter</artifactId>
            <version>1.0</version>
        </dependency>
        
   2、设置拦截器并放置所以拦截器前面
   
     @Autowired
     private ApiLimitInterceptor apiLimitInterceptor;

     @Override
     public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(apiLimitInterceptor).addPathPatterns("/**");
     }
    
    
   3、配置使用
  
    #（/test/**接口限流单例模式)
    ff.http.api.enabled=true #限流器开关 
    
    #配置限流组
    ff.http.api.settings.group1.urlPatterns[0].method=GET
    ff.http.api.settings.group1.urlPatterns[0].path-pattern=/test/**
    ff.http.api.settings.group1.limit=30
    ff.http.api.settings.group1.priority=1
    
    
   4、 启用分布式限流（基于redis）
    
    ff.http.api.distribute=false
    # Redis相关配置
    spring.redis.host=127.0.0.1
    spring.redis.password=
    spring.redis.port=6379
    # 连接池最大连接数（使用负值表示没有限制）
    spring.redis.pool.max-active=8
    # 连接池最大阻塞等待时间（使用负值表示没有限制）
    spring.redis.pool.max-wait=-1
    # 连接池中的最大空闲连接
    spring.redis.pool.max-idle=8
    # 连接池中的最小空闲连接
    spring.redis.pool.min-idle=1
    # 连接超时时间（毫秒）
    spring.redis.timeout=300
        
        
#### 配置参数说明
    #限流器开关 默认false
    ff.http.api.enabled
    #限流配置
    ff.http.api.settings
         group1: #限流配置分组名称（可以任意取）
            urlPatterns: #url匹配规则(多个patterns为or的关系)
               - method: GET #匹配的method
                 pathPattern: /test1/** #匹配的path(支持通配符，*匹配一级，**匹配一级或多级)
               - method: GET
                 pathPattern: /test2
            #headerPatterns为2.0.1版本以上才支持
            headerPatterns: #header匹配规则(多个headerPatterns为and的关系)
               - key: test-1-id #header的key
                 exists: true #header是否存在，fasle表示匹配该header不存在或者值为空的场景，true表示匹配该header值不为空的场景（为true时，valuePattern必填）
                 valuePattern: \w+ #用来匹配header值的格式（正则表达式），exists值为true时，valuePattern配置才有效
               - key: test-2-id
                 exists: false          
               limit: 100 #限制每秒请求数
               priority: 5 #配置优先级，越大最高
    #分布式限流器开关 默认false，开启必须配置redis
    ff.http.api.distribute
