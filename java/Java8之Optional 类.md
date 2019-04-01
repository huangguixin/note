# Java8之Optional 类

Optional<T> 类(java.util.Optional) 是一个容器类，代表一个值存在或不存在，
原来用 null 表示一个值不存在，现在 Optional 可以更好的表达这个概念。并且
可以避免空指针异常。

Optional 容器类：用于尽量避免空指针异常常用方法：
Optional.of(T t) : 创建一个 Optional 实例
Optional.empty() : 创建一个空的 Optional 实例
Optional.ofNullable(T t):若 t 不为 null,创建 Optional 实例,否则创建空实例
isPresent() : 判断是否包含值
orElse(T t) : 如果调用对象包含值，返回该值，否则返回t
orElseGet(Supplier s) :如果调用对象包含值，返回该值，否则返回 s 获取的值
map(Function f): 如果有值对其处理，并返回处理后的Optional，否则返回 Optional.empty()
flatMap(Function mapper):与 map 类似，要求返回值必须是Optional 

```java
@Test
	public void test1(){
		Optional<Employee> op = Optional.of(new Employee());
		Employee emp = op.get();
		System.out.println(emp);
	}
	
	
		@Test
	public void test2(){
		Optional<Employee> op = Optional.ofNullable(null);
		System.out.println(op.get());
		
		Optional<Employee> op = Optional.empty();
		System.out.println(op.get());
	}
	
	
	@Test
	public void test3(){
		Optional<Employee> op = Optional.ofNullable(new Employee());
		
		if(op.isPresent()){
			System.out.println(op.get());
		}
		
		Employee emp = op.orElse(new Employee("张三"));
		System.out.println(emp);
		
		Employee emp2 = op.orElseGet(() -> new Employee());
		System.out.println(emp2);
	}
	
	
	@Test
	public void test4(){
		Optional<Employee> op = Optional.of(new Employee(101, "张三", 18, 9999.99));
		
		Optional<String> op2 = op.map(Employee::getName);
		System.out.println(op2.get());
		
		Optional<String> op3 = op.flatMap((e) -> Optional.of(e.getName()));
		System.out.println(op3.get());
	}
	
```



```
@Test
	public void test5(){
		Man man = new Man();
		
		String name = getGodnessName(man);
		System.out.println(name);
	}
	
	//需求：获取一个男人心中女神的名字
	public String getGodnessName(Man man){
		if(man != null){
			Godness g = man.getGod();
			
			if(g != null){
				return g.getName();
			}
		}
		
		return "苍老师";
	}
```

运用 Optional 的实体类

```java
@Test
	public void test6(){
		Optional<Godness> godness = Optional.ofNullable(new Godness("林志玲"));
		
		Optional<NewMan> op = Optional.ofNullable(new NewMan(godness));
		String name = getGodnessName2(op);
		System.out.println(name);
	}
	
	public String getGodnessName2(Optional<NewMan> man){
		return man.orElse(new NewMan())
				  .getGodness()
				  .orElse(new Godness("苍老师"))
				  .getName();
	}
```



```java

public class Man {

	private Godness god;

	public Man() {
	}

	public Man(Godness god) {
		this.god = god;
	}

	public Godness getGod() {
		return god;
	}

	public void setGod(Godness god) {
		this.god = god;
	}

	@Override
	public String toString() {
		return "Man [god=" + god + "]";
	}

}
```



```java
public class Godness {

	private String name;

	public Godness() {
	}

	public Godness(String name) {
		this.name = name;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	@Override
	public String toString() {
		return "Godness [name=" + name + "]";
	}

}
```



```java
import java.util.Optional;

//注意：Optional 不能被序列化
public class NewMan {

	private Optional<Godness> godness = Optional.empty();
	
	private Godness god;
	
	public Optional<Godness> getGod(){
		return Optional.of(god);
	}

	public NewMan() {
	}

	public NewMan(Optional<Godness> godness) {
		this.godness = godness;
	}

	public Optional<Godness> getGodness() {
		return godness;
	}

	public void setGodness(Optional<Godness> godness) {
		this.godness = godness;
	}

	@Override
	public String toString() {
		return "NewMan [godness=" + godness + "]";
	}

}
```