![[Pasted image 20250419163808.png]]

#### enviroments环境
- 可以配置多个 `enviroment` ，用来连接多个不同的数据库，将 `<enviroments default="development">` 中的 `default=""` 更改成 `<enviroment id="development">` 中的 `id=""` 即可更换数据源。 

#### typeAliases别名
使用如下代码块：
```javascript
<typeAliases>
	<package name="com.mybatis" />
</typeAliases>
```

