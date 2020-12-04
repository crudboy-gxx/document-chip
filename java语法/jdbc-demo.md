外部配置数据源
public class Db {
	private static String driver;
	private static String url;
	private static String user;
	private static String password;
	private static BasicDataSource ds; //?????

	static {
		try {
			Properties props = new Properties();
			// 从类路径中加文件因为.class.getClassLoader()是为了获得一个类加载器，用来加载classpath下的.class文件而已。
			props.load(Db.class.getClassLoader().getResourceAsStream( "db.properties"));
			//用这种方式获得外部配置文件。
			props.load(new FileInputStream("src/db.properties"));
			driver = props.getProperty("driver");
			url = props.getProperty("url");
			user = props.getProperty("user");
			password = props.getProperty("password");

			ds = new BasicDataSource();
			ds.setDriverClassName(driver);
			ds.setUrl(url);
			ds.setUsername(user);
			ds.setPassword(password);

		} catch (Exception e) {
			e.printStackTrace();
			throw new RuntimeException(e);
		}
	}
	public static Connection getConnection() throws Exception {
			Connection con = ds.getConnection();
			return con;
		}
	public static void close(Connection conn){
		if(conn != null){
			try {
				conn.close();
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}
	
	
	public class UserRealize implements UserInterface{
	public List<User> findAll() throws Exception {
		List<User> userList = new ArrayList<User>();
		Connection conn = null;
		PreparedStatement prep = null;
		ResultSet rst = null;
		try{
			conn = Db.getConnection();
			prep = conn.prepareStatement(
					"SELECT * FROM T_USER");
			rst = prep.executeQuery();
			while(rst.next()){
				User user = new User();
				user.setIdNo(rst.getString("idno"));
				user.setAge(rst.getInt("age"));
				user.setIdNum(rst.getString("idnum"));
				user.setPhNo(rst.getString("phno"));
				user.setPwd(rst.getString("pwd"));
				user.setUserName(rst.getString("username"));
				userList.add(user);
			}
		}catch(Exception e){
			e.printStackTrace();
			throw e;
		}finally{
			if(rst != null){
				rst.close();
			}
			if(prep != null){
				prep.close();
			}
			Db.close(conn);
		}
		return userList;
	}
	
配置在内部
public class DBConnection {
    private static final String DBDRIVER = "oracle.jdbc.driver.OracleDriver" ;	//驱动类类名
    private static final String DBURL = "jdbc:oracle:thin:@localhost:1521:GUOXIN";//连接URL
    private static final String DBUSER = "ONE_20150916" ;	//数据库用户名
    private static final String DBPASSWORD = "123456";	//数据库密码
	public static Connection getConnection(){
		Connection conn = null;		//声明一个连接对象
		try {
			Class.forName(DBDRIVER);	//注册驱动
			conn = DriverManager.getConnection(DBURL,DBUSER,DBPASSWORD);//获得连接对象
		} catch (ClassNotFoundException e) {	//捕获驱动类无法找到异常
			e.printStackTrace();										
		} catch (SQLException e) {	//捕获SQL异常
			e.printStackTrace();
		}
		return conn;
	}
	public static void close(Connection conn) {//关闭连接对象
		if(conn != null) {				//如果conn连接对象不为空
			try {
				conn.close();			//关闭conn连接对象对象
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}
	public static void close(PreparedStatement pstmt) {//关闭预处理对象
		if(pstmt != null) {				//如果pstmt预处理对象不为空
			try {
				pstmt.close();			//关闭pstmt预处理对象
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}
	public static void close(ResultSet rs) {//关闭结果集对象
		if(rs != null) {				//如果rs结果集对象不为null
			try {
				rs.close();				//关闭rs结果集对象
			} catch (SQLException e) {
				e.printStackTrace();
			}
		}
	}
}


public void addCriticism(Criticism criticism) {
		Connection conn = DBConnection.getConnection();	//获得连接对象
		String addSQL = "insert into tb_criticism(criticismContent,employeeID,criticismTime,messageID) values(?,?,?,?)";
		PreparedStatement pstmt = null;					//声明预处理对象
		try {
			pstmt = conn.prepareStatement(addSQL);		//获得预处理对象并赋值
			pstmt.setString(1, criticism.getCriticismContent());	//设置第一个参数
			pstmt.setString(2, criticism.getEmployeeID());//设置第二个参数
			pstmt.setTimestamp(3,new Timestamp(criticism.getCriticismTime().getTime()));//设置第三个参数
			pstmt.setString(4, criticism.getMessageID());
			pstmt.executeUpdate();								//执行更新
		} catch (SQLException e) {
			e.printStackTrace();
		} finally{
			DBConnection.close(pstmt);							//关闭预处理对象
			DBConnection.close(conn);							//关闭连接对象
		}
	}

1.JDBC是sun公司定义的一组接口，实现类是各个数据库厂商提供的驱动程序。
2.加减运算符可以实现对date型数据的运算。

1.//获得本地路径
  String BASE_HOME_CONF=System.getProperty("user.dir")+File.separator;
  //获取本地配置文件
  props.load(new FileInputStream("src/db.properties"));
2.jdbc查询出来的结果集也是可以更新的。
3.数据库中处理声音图像。
4.在查询出来ResultSet后使用ResultSetMetaOata分析结果集。
	ResultSetMetaData rsm=rs.getMetaData();
	System.out.println(rsm.getColumnCount());
5.事务的四个特性：原子性、一致性、隔离性、持续性。
