# Mybatis

## resultMap

自定义某个javaBean的封装规则

type：自定义规则的Java类型

id:唯一id方便引用

```java
<resultMap type="commybatis.bean.Employee" id="MySimpleEmp">
		<!--指定主键列的封装规则
		id定义主键会底层有优化；
		column：指定哪一列
		property：指定对应的javaBean属性
		  -->
		<id column="id" property="id"/>
		<!-- 定义普通列封装规则 -->
		<result column="last_name" property="lastName"/>
		<!-- 其他不指定的列会自动封装：我们只要写resultMap就把全部的映射规则都写上。 -->
		<result column="email" property="email"/>
		<result column="gender" property="gender"/>
</resultMap>
```

```java
public class Employee {
	private Integer id;
	private String lastName;
	private String email;
	private String gender;
}
```

```java
<!-- resultMap:自定义结果集映射规则；  -->
	<!-- public Employee getEmpById(Integer id); -->
	<select id="getEmpById"  resultMap="MySimpleEmp">
		select * from tbl_employee where id=#{id}
	</select>
```

### 联合查询：级联属性封装结果集

```java
<resultMap type="com.mybatis.bean.Employee" id="MyDifEmp">
		<id column="id" property="id"/>
		<result column="last_name" property="lastName"/>
		<result column="gender" property="gender"/>
		<result column="did" property="dept.id"/>
		<result column="dept_name" property="dept.departmentName"/>
	</resultMap>
```

```java
public class Employee {
	
	private Integer id;
	private String lastName;
	private String email;
	private String gender;
	private Department dept;
}
```

```java
<!--  public Employee getEmpAndDept(Integer id);-->
	<select id="getEmpAndDept" resultMap="MyDifEmp">
		SELECT e.id id,e.last_name last_name,e.gender gender,e.d_id d_id,
		d.id did,d.dept_name dept_name FROM tbl_employee e,tbl_dept d
		WHERE e.d_id=d.id AND e.id=#{id}
	</select>
```

### 使用association定义关联的单个对象的封装规则

```java
<resultMap type="com.mybatis.bean.Employee" id="MyDifEmp2">
		<id column="id" property="id"/>
		<result column="last_name" property="lastName"/>
		<result column="gender" property="gender"/>
		
		<!--  association可以指定联合的javaBean对象
		property="dept"：指定哪个属性是联合的对象
		javaType:指定这个属性对象的类型[不能省略]
		-->
		<association property="dept" javaType="com.mybatis.bean.Department">
			<id column="did" property="id"/>
			<result column="dept_name" property="departmentName"/>
		</association>
	</resultMap>
```

### 使用association进行分步查询：

```java
 <resultMap type="com.mybatis.bean.Employee" id="MyEmpByStep">
	 	<id column="id" property="id"/>
	 	<result column="last_name" property="lastName"/>
	 	<result column="email" property="email"/>
	 	<result column="gender" property="gender"/>
	 	<!-- association定义关联对象的封装规则
	 		select:表明当前属性是调用select指定的方法查出的结果
	 		column:指定将哪一列的值传给这个方法
	 		
	 		流程：使用select指定的方法（传入column指定的这列参数的值）查出对象，并封装给property指定的属性
	 	 -->
 		<association property="dept" 
	 		select="commybatis.dao.DepartmentMapper.getDeptById"
	 		column="d_id">
 		</association>
	 </resultMap>
```

### 使用延迟加载（懒加载）对于分步查询,在Mybaits的配置文件中配置

```java
<settings>		
		<!--显示的指定每个我们需要更改的配置的值，即使他是默认的。防止版本更新带来的问题  -->
		<setting name="lazyLoadingEnabled" value="true"/>
		<setting name="aggressiveLazyLoading" value="false"/>
</settings>
	
```

### 嵌套结果集的方式，使用collection标签定义关联的集合类型的属性封装规则

```java
<resultMap type="com.mybatis.bean.Department" id="MyDept">
		<id column="did" property="id"/>
		<result column="dept_name" property="departmentName"/>
		<!-- 
			collection定义关联集合类型的属性的封装规则 
			ofType:指定集合里面元素的类型
		-->
		<collection property="emps" ofType="com.mybatis.bean.Employee">
			<!-- 定义这个集合中元素的封装规则 -->
			<id column="eid" property="id"/>
			<result column="last_name" property="lastName"/>
			<result column="email" property="email"/>
			<result column="gender" property="gender"/>
		</collection>
	</resultMap>
```

```java
public class Department {
	
	private Integer id;
	private String departmentName;
	private List<Employee> emps;
}
```

### collection：分段查询，懒加载同上

```java
<resultMap type="com.mybatis.bean.Department" id="MyDeptStep">
		<id column="id" property="id"/>
		<id column="dept_name" property="departmentName"/>
		<collection property="emps" 
			select="com.mybatis.dao.EmployeeMapperPlus.getEmpsByDeptId"
			column="{deptId=id}" fetchType="lazy"></collection>
</resultMap>

<!-- 扩展：多列的值传递过去：
			将多列的值封装map传递；
			column="{key1=column1,key2=column2}"
       key1表示对应select查询语句中参数的key名
		fetchType="lazy"：表示使用延迟加载；
				- lazy：延迟
				- eager：立即
 -->
```



