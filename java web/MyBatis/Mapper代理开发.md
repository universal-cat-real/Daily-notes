- 需要将mapper接口和mapper配置文件在一个目录下，直接在resources目录下创建一个相同的目录结构就可以
![[Pasted image 20250419162949.png]]
- 设置sql映射文件的namespace属性为Mapper接口全限定名![[Pasted image 20250419145816.png]]
- 在Mapper接口中定义方法，方法名就是SQL映射文件中sql语句的id，并保持参数类型和返回值类型一致
![[Pasted image 20250419150134.png]]
![[Pasted image 20250419150201.png]]
	*ps:因为resultType即返回值类型，返回值类型是com.maven.User这个对象，所以接口中写的返回值类型应该是User，但是为什么是List< User >呢，因为查询出来的结果是一个列表，要是只写User那么返回的就是一个User对象而不是所有User对象*
- 编码：
	- 通过SqlSeesion的getMapper方法获取 Mapper接口的代理对象
	- 调用对应方法完成sql的执行
![[Pasted image 20250419162854.png]]