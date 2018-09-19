# SpringBoot注解（siwang.hu&nbsp;&nbsp;V1.0）  
## **1.&nbsp;&nbsp;@SpringBootApplication**  
> + 注解在启动类上  
>  
> + 等效于以默认属性使用 @Configuration ， @EnableAutoConfiguration 和 @ComponentScan  
```
@SpringBootApplication
public class StartApplication {

    public static void main(String args[])throws Exception{
        SpringApplication.run(StartApplication.class,args);
    }

}
```  
>  
## **2.&nbsp;&nbsp;@value**  
> + 该注解可以读取springboot中的配置文件中的参数值  
> + 方便读取自定义的配置参数  
>  
> application.properties文件中的自定义配置如下:  
```
port=8080
ip=127.0.0.1
```  
使用方式:  
```
@Value("${port}")
private int Port;

@Value(("${ip}"))
private String Ip;
```
## **3.&nbsp;&nbsp;@Configuration**  
> + 注解在类上,相当于用spring的时候xml里面的```<beans>```标签  
>  
> + 代表这个类是一个配置类  
>  
## **4.&nbsp;&nbsp;@Bean**  
> + 注解在方法上，产生bean的方法，并且交给Spring容器管理  
>  
> + 如果未通过@Bean指定bean的名称，则默认与标注的方法名相同  
>  
> + @Bean注解默认作用域为单例singleton作用域，可通过@Scope(“prototype”)设置为原型作用域  
```
@Configuration
public class RedisCache extends CachingConfigurerSupport {
    
    @Autowired
    RedisConnectionFactory redisConnectionFactory;
    
    @Bean
    public RedisTemplate<String,Object> redisTemplate(){
        RedisTemplate<String, Object> redisTemplate = new RedisTemplate<>();
        initDomainRedisTemplate(redisTemplate, redisConnectionFactory);
        return redisTemplate;
    }
    
    private void initDomainRedisTemplate(RedisTemplate<String, Object> redisTemplate, RedisConnectionFactory factory) {
        redisTemplate.setKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashKeySerializer(new StringRedisSerializer());
        redisTemplate.setHashValueSerializer(new GenericJackson2JsonRedisSerializer());
        redisTemplate.setValueSerializer(new JdkSerializationRedisSerializer());
        redisTemplate.setConnectionFactory(factory);
    }
    
    @Bean
    public HashOperations<String, String, Object> hashOperations(RedisTemplate<String, Object> redisTemplate) {
        return redisTemplate.opsForHash();
    }
    
    @Bean
    public ValueOperations<String, Object> valueOperations(RedisTemplate<String, Object> redisTemplate) {
        return redisTemplate.opsForValue();
    }
} 
```  
## **5.&nbsp;&nbsp;@Autowired和@Resource**  
> + @Autowired与@Resource都可以用来装配bean  
>  
> + 可以写在字段上,或写在setter方法上  
>  
> + @Autowired是默认按照类型装配的 @Resource默认是按照名称装配  
>  
## **6.&nbsp;&nbsp;@Qualifier**  
> + 当有多个同一类型的Bean时，可以用@Qualifier(“name”)来指定  
>  
> + @Qualifier限定描述符能进行更细粒度的控制如何选择候选者  
```
@Autowired 
@Qualifier(value = “userOpService”) 
private UserService userOpService;
```  
## **7.&nbsp;&nbsp;@ResponseBody**  
> + 注解在方法上，表明将结果直接写入HTTP response body，不做界面跳转  
>  
> + 比如获取json数据，加上@responsebody后，会直接返回json数据  
## **8.&nbsp;&nbsp;@RestController与@Controller**   
> + @Controller一般用于前端界面跳转  
>  
> + @RestController相当于@ResponseBody ＋ @Controller合在一起的作用  
>  
> + @RestController用于开发restful api服务  
## **9.&nbsp;&nbsp;@RequestMapping**  
> + 对外Url链接地址
```
@RequestMapping(value="/voice/add",method ={RequestMethod.GET,RequestMethod.POST})
public boolean addVoice(Voice voice) {
    return voiceService.insertVoice(voice);
}
```