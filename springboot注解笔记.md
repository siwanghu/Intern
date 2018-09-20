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
## **10.&nbsp;&nbsp;组件注解**  
> + @Component注解放在类的头上,是所有受Spring 管理组件的通用形式,不推荐使用  
>  
> + @Service对应的是业务层Bean  
>  
> + @Repository对应数据访问层Bean  
>  
## **11.&nbsp;&nbsp;@Transactional**  
> + 事务注解，添加在方法上，声明为事务  
>  
> + 一般用于数据库操作函数中  
```
@Transactional
public boolean checkVoiceById(int id,Stringcommit){
    Voice voice=voiceDao.findById(id);
    voice.setCommit(commit);
    voiceDao.save(voice);
    return true;
}
```  
## **12.&nbsp;&nbsp;@Entity与@Table(name="tb_voice")**  
> + @Entity声明持久化类  
>  
> + @Table声明表名  
>  
> + @Id声明表主键  
>  
> + @GeneratedValue(strategy= GenerationType.IDENTITY)声明表主键自动增长  
>  
> + @Column(nullable = false,name = "id")声明表字段的属性和名称  
```
@Entity
@Table(name="tb_voice")
public class Voice implements Serializable{
    private static final long serialVersionUID = 2300044412175011558L;

    @Id
    @GeneratedValue(strategy= GenerationType.IDENTITY)
    @Column(nullable = false,name = "id")
    private int id;

    @Column(nullable = false , name = "name")
    private String name;

    @Column(nullable = false , name = "label")
    private String label;

    @Column(nullable = false , name = "datetime")
    private Date datetime;

    @Column(nullable = false , name = "uri")
    private String uri;

    @Column(nullable = false , name = "commit")
    private String commit;

    @Column(nullable = false , name = "size")
    private String size;

    public void setId(int id) {
        this.id = id;
    }

    public void setName(String name) {
        this.name = name;
    }

    public void setLabel(String label) {
        this.label = label;
    }

    public void setDatetime(Date datetime) {
        this.datetime = datetime;
    }

    public void setUri(String uri) {
        this.uri = uri;
    }

    public void setCommit(String commit) {
        this.commit = commit;
    }

    public void setSize(String size) {
        this.size = size;
    }

    public int getId() {
        return id;
    }

    public String getName() {
        return name;
    }

    public String getLabel() {
        return label;
    }

    public Date getDatetime() {
        return datetime;
    }

    public String getUri() {
        return uri;
    }

    public String getCommit() {
        return commit;
    }

    public String getSize() {
        return size;
    }

    @Override
    public String toString() {
        return "Voice{" +
                "id=" + id +
                ", name='" + name + '\'' +
                ", label='" + label + '\'' +
                ", datetime=" + datetime +
                ", uri='" + uri + '\'' +
                ", commit='" + commit + '\'' +
                ", size='" + size + '\'' +
                '}';
    }
}
```  
## **13.&nbsp;&nbsp;@Query**  
> + 用于springboot JPA中的自定义sql查询语句  
```
public interface BookDao extends JpaRepository<Book, Integer>{
    
    //Hql语句查询
    @Query("select b from Book b where b.bookName like %?1%")
    public List<Book> findByBookName(String bookName);
    
    //本地sql语句查询
    @Query(value="select * from t_book order by RAND() limit ?1",nativeQuery=true)
    public List<Book> randomList(Integer n);
}
```