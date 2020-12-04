【】刷新页面重复提交表单（2017/4/18）
	通过返回按钮导致的重复提交表单或者F5刷新页面重复提交表单，使用post-redirect-get方式解决。
	
【JSTL】forEach/forTokens和if（2017/6/12）
	<c:forEach items="collection" start="0" end="10" step="2" var="i" varStatus="abc" delims=",">
		<p>${i.id}</p>
		<p>${abc.index}</p>
	</c:forEach>
	注意: 
		1.varStatus标识循环状态的变量（奇数行、偶数行差异；最后一行特殊处理），可取值index/count/first/last/current。index表示当前从0开始的迭代计数；count表示当前从1开始的迭代计数；first当前是不是迭代的第一个，返回true或false；current当前迭代集合项，返回hash值。
		2.delims是forTokens特有的，它表示指定的分隔符，将字符串分割为数组然后迭代他们。
	
	
	<c:if test="${id=='1'}" var="result" scope="request">here</c:if>
	注意: var存储条件结果变量，这样做的目的就是避免在页面中多次进行相同的判断；scope表示var的作用域。
		
【servlet】访问非项目路径下的资源图片（2017/6/13）
	方案： 使用servlet拦截请求，解析拿到将资源写入response中进行返回
	Code:
		public void fileOutputStream(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
			String filepath = req.getRequestURI();
			int index = filepath.indexOf(Global.USERFILES_BASE_URL);
			if(index >= 0) {
				filepath = filepath.substring(index + Global.USERFILES_BASE_URL.length());
			}
			try {
				filepath = UriUtils.decode(filepath, "UTF-8");
			} catch (UnsupportedEncodingException e1) {
				logger.error(String.format("解释文件路径失败，URL地址为%s", filepath), e1);
			}
			File file = new File(Global.getUserfilesBaseDir() + Global.USERFILES_BASE_URL + filepath);
			try {
				FileCopyUtils.copy(new FileInputStream(file), resp.getOutputStream());
				resp.setHeader("Content-Type", "application/octet-stream");
				return;
			} catch (FileNotFoundException e) {
				req.setAttribute("exception", new FileNotFoundException("请求的文件不存在"));
				req.getRequestDispatcher("/WEB-INF/views/error/404.jsp").forward(req, resp);
			}
		}
【properties】properties文件动态加载（2017/6/16）【实现代码】
	方案： 要实现修改文件后动态加载的需求，架构（Srping）依赖的属性应该和其他的分开，系统架构一般主要使用的是数据库连接。现在需要两部分，一个是properties工具类，一个是监控线程，一个负责加载的类。仿照Mybatis动态加载xml配置文件的代码来实现线程监控（线程不熟悉），动态加载。
	引申：spring加载有两种方式,这两种是等价的关系。
		<context:property-placeholder location="classpath:jdbc.properties" ignore-unresolvable="true"/>
		<bean id="propertyConfigurer" class="org.springframework.beans.factory.config.PropertyPlaceholderConfigurer">
   <property name="ignoreUnresolvablePlaceholders" value="true"/>
   <property name="locations">
      <list>
         <value>classpath:jdbc.properties</value>
      </list>
    </property>
</bean>
		
【servlet】获取项目路径、classpath（2017/6/18）
	1)this.getClass().getResource（""），获得当前class的uri路径，如file:/D:/Workspaces/platform/target/classes/com/sdyy/controller/
	2)this.getClass().getResource（"/"）,获得当前classpath的绝对路径，如file:/D:/Workspaces/platform/target/classes
	3)this.getClass().getResource（""）.getPath()，获得的是url字符串，如/D:/Workspaces/platform/target/classes/com/sdyy/controller/
	
	4)request.getSession().getServletContext().getRealPath("")，获取工程路径，如E:\Tomcat\webapps\TEST
	5)request.getRequestURL()，获取浏览器地址，如http://localhost:8080/TEST/test
	6)request.getRequestURI()，获取相对地址，如/TEST/test
	7)request.getContextPath()，获得工程名
	
	联想：在项目中获得classpath的路径要加载配置文件；获得项目文件提供下载；获得当前文件的位置；
	总结获取路径方式：通过request对象获得；另外三种种在javaEE中使用：通过Class对象获得；通过File对象获得；System.getProperty()获得。
		
【jsp】jsp指令pageEncoding和contentType两个属性作用（2017/6/20）
	<%@page language="java" pageEncoding="UTF-8"%> pageEncoding是jsp本身的编码。
	<%@page contentType="text/html; charset=UTF-8"%> contenType中的charset指的是服务器发送给浏览器时内容编码。
	引申：
		1)转码存在三个阶段：第一阶段jsp编译为java，根据页面设置的pageEncoding编码翻译成统一的UTF-8的java源码；第二阶段是UTF-8java源码编译为UTF-8的class文件；第三阶段是tomcat载入和执行文件，在客户端浏览器输出结果，这时候contenType起作用。
		2)另外两种转码方式：request.setCharacterEncoding("UTF-8")和response.setCharacterEncoding("UTF-8")是对请求和相应重新编码。
	中文乱码以及编码的知识
		<http://blog.csdn.net/dragon4s/article/details/6604624>
		
【servlet】获取所有请求参数（2017/6/26）
	1)第一种方式 request.getParameterNames();
		Enumeration enu = request.getParameterNames();
		while(enu.hasMoreEnumerations()){
			String paramenterName = (String)enu.nextEnumeration();
			String parameter = request.getparameter(parameterName);
		}
	
	2)第二种方式 request.getParameterMap();
		Map map = request.getParameterMap();
		Iterator itr = map.entrySet().iterator();
		while(itr.hasNext()){
			Map.Entry me = (Map.Entry)itr.next();
			Object ok=me.getKey();  
			Object ov=me.getValue();  
			String[] value=new String[1];  
			if(ov instanceof String[]){  
				value=(String[])ov;  
			}else{  
				value[0]=ov.toString();  
			}  
			System.out.println(ok+"="+value[0]);
		}
	联想：遍历map的值（集合遍历）
		对于map，本身不能调用迭代方法进行输出，因为迭代每次找到一个值，而map中是一对值。
		应该通过map实例获取set，用set去实例化Iterator，然后迭代输出Map.Entry对象，通过此对象getKey()和getValue()获取。获得set有两种方式，第一种是map.keySet(),返回set<k>；第二种是map.entrySet(),返回Set<Map.Entry<K,V>>。
		使用中优先使用entrySet()方法和Iterator，在使用keySet获取value的时候获取不到值，只获取到内存地址。
	联想：map排序（集合排序） 
	map通过keySet得到内存地址？
	 
【servlet】获取请求的ip地址（2017/6/26）
	public static String getIp(HttpServletRequest request) {
	    String ip = request.getHeader("X-Forwarded-For");
	    if(StringUtils.isNotEmpty(ip) && !"unKnown".equalsIgnoreCase(ip)){
	       //多次反向代理后会有多个ip值，第一个ip才是真实ip
	        int index = ip.indexOf(",");
	        if(index != -1){
	            return ip.substring(0,index);
	        }else{
	            return ip;
	        }
	    }
	    ip = request.getHeader("X-Real-IP");
	    if(StringUtils.isNotEmpty(ip) && !"unKnown".equalsIgnoreCase(ip)){
	        return ip;
	    }
	    return request.getRemoteAddr();
	}
	
【tomcat】容器的解码和编码（2017/7/3）
	1)从tomcat5之后，get和post的请求方式编码不同。
	2)对于post来说，会使用request的setCharacterEncoding方法来设置编码格式，为设置采用iso-8859-1编码；对于get请求默认采用iso-8859-1编码，可以在tomcat的service中配置URLEncoding="UTF-8"。
	3)对于response来说，以setCharacterEncoding方法提供的编码方式为准。
	联想：在项目中使用Filter去拦截请求设置为utf-8，具体是使用springMVC的CharacterEncodingFilter
		CharacterEncodingFilter可以接受两个参数encoding（设置request的编码格式）和forceEncoding（设置response的编码格式是否和request一致，默认为false）。
		
【Testing】Junit和SpringTesting对controller方法单元测试
	@RunWith(SpringJUnit4ClassRunner.class)
	@ContextConfiguration(locations={"classpath:spring-mybatis.xml","classpath:spring-mvc.xml"})
	@WebAppConfiguration
	public class  MockTestController{
	   protected MockMvc mockMvc;
	   @Autowired
	   protected WebApplicationContext wac;
	   @Before()  //这个方法在每个方法执行之前都会执行一遍
	   public void setup() 
	   {
	       mockMvc = MockMvcBuilders.webAppContextSetup(wac).build();  //初始化MockMvc对象
	   }
	   @Test
	   public void getAllCategoryTest() throws Exception 
	   {
	       String responseString = mockMvc.perform
	           (
	               get("/user/showUser1")          //请求的url,请求的方法是get
	               //get("/user/showUser2")          //请求的url,请求的方法是get
	               .contentType(MediaType.APPLICATION_FORM_URLENCODED)//数据的格式
	               .param("id","1")   //添加参数(可以添加多个)
	               //.param("id","3")   //添加参数(可以添加多个)
	           )
	           .andExpect(status().isOk())    //返回的状态是200
	           .andDo(print())         //打印出请求和相应的内容
	           .andReturn().getResponse().getContentAsString();   //将相应的数据转换为字符串
	       System.out.println("-----返回的json = " + responseString);
	   }
	}
	
【servlet】修改request的parameter（2017/7/29）
	
【】后台校验工具类（2017/4/13）
	Bean Validation API.release版本 JSR-303 是JAVA EE 6 中的一项子规范，叫做Bean Validation,现在一共有两个规范:BeanValidation1.0(即JSR303)和BeanValidation1.1(即 JSR349),主要用于对数据进行校验,确保输入进来的数据从语义上来讲是正确的。 特点： 1.JSR 303 用于对Java Bean 中的字段的值进行验证,使得验证逻辑从业务代码中脱离出来。 2.是一个运行时的数据验证框架，在验证之后验证的错误信息会被马上返回。 应用场景:一般用于表单提交页面(如用户名必填、只能由数字字母组成等等)
	
