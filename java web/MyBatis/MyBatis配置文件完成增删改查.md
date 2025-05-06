### MyBatisX插件介绍
*MyBatisX是一款基于IDEA的快速开发插件，为效率而生*
- XML和接口方法相互跳转
- 根据接口方法生成statement

### 查询
##### 查询所有数据
- 1.编写接口方法：**Mapper接口**
	`List<Brand> selectAll();`
- 2. 编写SQL语句：**SQL映射文件**
```java
<select id="selectAll" resultType="brand">
	select * from tb_brand;
</select>
```
- 3.执行方法，测试

*遇到的问题：
1.只将mapper对应xml放在一个文件夹下，忘了在MyBatis配置文件中引用mapper.xml
2.在Brand类中必须写上getset方法以及toString()方法.*
#### 结果映射
![[Pasted image 20250504153629.png]]

#### 查询详情
- 1.编写接口方法：**Mapper接口**
	`Brand selectById(int id);`
- 2. 编写SQL语句：**SQL映射文件**
```java
<!--  
	参数站位符：  
    1.#{}:会将其替换为 ? ,为了防止sql注入  
    2.${}:拼sql，会存在sql注入问题
    3.使用时机：
	    * 参数传递的时候使用:#{}
	    * 表名或者列名不固定的情况下使用:${}
    parameterType:传入参数的类型(可以不写，一般没人写)
    特殊字符处理：比如<  
	    1.转义字符：&lt;  
	    2.CDATA区
-->
<select id="selectById" parameterType="int" resultType="brand">
	select * from tb_brand where id = #{id};
</select>
```
- 3.执行方法，测试

#### 条件查询
###### 多条件查询
- 1.编写接口方法：**Mapper接口**，三种参数接收方式
	`List<Brand> selectIf(@Param("status")int status, @Param("companyName")String companyName);
	`List<Brand> selectIf(Brand brand);`
	`List<Brand> selectIf(Map map)`
- 2. 编写SQL语句：**SQL映射文件**
```java
<!--  
    有多个查询参数的时候，使用param1, param2, ... , paramn来占位符，  
    分别代表参数列表中第1,2,...,n个参数  
-->
<select id="selectIf" resultMap="brand_resultMap">  
    select * from tb_brand where company_name = #{companyName} and status = #{status};  
</select>
```
- 3.执行方法，测试

###### 动态多条件查询
- sql语句会随着用户的输入或外部条件的变化而变化，我们称为**动态SQL**
```java
<select id="selectIf" resultMap="brand_resultMap">  
    select * from tb_brand where company_name = #{companyName} and status = #{status};  
</select>
```
- if、choose(when, otherwise)、trim(where =, set)、foreach
```java
<!--  
    动态条件判断：  
        if:条件判断  
            test:逻辑表达式  
        使用恒等式来解决相应问题        或者使用<where>标签解决  
-->  
    <select id="selectIf" resultMap="brand_resultMap">  
        select * from tb_brand  
        <where>  
            <if test="companyName != null and companyName != ''">  
                and company_name = #{companyName} and  
            </if>  
            <if test="status != null">  
                and status = #{status};  
            </if>  
        </where>  
    </select>
```

### 添加
- 1.编写接口方法：**Mapper接口*
	`void add(Brand brand);`
- 2.编写sql语句：**sql映射文件**
```java
<insert id="add">
	insert into tb_brand(brand_name, company_name, ordered, description, status)
	values (#{brandName}, #{companyName}, #{ordered}, #{description}, #{status});
</insert>
```
- 3.执行方法，测试。*需要手动提交事务*
#### 主键返回
*在添加成功后，需要插入数据库数据的主键的值*
- 添加订单
- 添加订单项，订单项中需要设置所属订单的id
```java
<insert id="addOrder" useGeneratedKeys="true" keyProperty="id">
	insert into tb_order (payment, payment_type, status)
	values(#{payment}, #{payment_type}, #{status});
</insert>
```

```java
<insert id="addOrderItem">
	insert into tb_order_item(goods_name, goods_price, count, order_id)
	values(#{goodsName}, #{goodsPrice}, #{orderId});
</insert>
```

### MyBatis事务
- openSession():默认开始事务，进行增删改查操作后需要使用sqlSession.commit()提交事务
- openSession(true):可以设置为自自动提交事务（关闭事务）

### 修改
#### 修改全部字段
- 1.编写接口方法：**Mapper方法**
	`void update(Brand brand);`
- 2.编写sql语句：**SQL映射文件**
```java
<update id="update">
	update tb_brand
	set brand_name = #{brandName},
		company_name = #{companyName},
		ordered = #{ordered},
		description = #{description},
		status = #{status}
	where id = #{id};
</update>
```
- 3.执行方法，测试

#### 修改动态字段
- 1.编写接口方法：**Mapper接口**
- 2.编写sql语句：**SQL映射文件**
```java
<update id="updateIf">
	update tb_brand
	<set>
		<if test="brandName != null and brandName != " "">
			brand_name = #{brandName},
		</if>
		<if test="companyName != null and companyName != " "">
			company_name = #{companyName},
		</if>
		<if test="ordered != null">
			ordered = #{ordered},
		</if>
		<if test="description != null and description != " ">
			description = #{description},
		</if>
		<if test="status != null">
			status = #{status},
		</if>
	</set>
	where id = #{id};
</update>
```
- 3.执行方法，测试

### 删除
#### 删除一个
- 1.编写接口方法：**Mapper接口**
	`void deleteById(int id);`
- 2.编写SQL语句：**SQL映射文件**
```java
<delete id="deleteById">  
    delete from tb_brand where id = #{id};  
</delete>
```
- 3.执行方法，测试

#### 批量删除
- 1.编写接口方法：**Mapper接口**
	`void deleteByIds(@Param("ids:) int[] ids)`
- 2.编写SQL语句：**SQL映射文件**
```java
<delete id="deleteByIds">  
    delete from tb_brand  
    where id in   
    <foreach collection="ids" item="id" separator="," open="(" close=")">  
        #{id}  
    </foreach>  
</delete>
```