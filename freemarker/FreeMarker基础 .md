# FreeMarker基础 

## 指令 

**注意点**

```freemarker
1、注释，即<#‐‐和‐‐>，介于其之间的内容会被freemarker忽略
2、插值（Interpolation）：即${..}部分,freemarker会用真实的值代替${..}
3、FTL指令：和HTML标记类似，名字前加#予以区分，Freemarker会解析标签中的表达式或逻辑。
4、文本，仅文本信息，这些不是freemarker的注释、插值、FTL指令的内容会被freemarker忽略解析，直接输出内
容。
```

**创建模型数据**

```freemarker
@RequestMapping("/test1")
public String freemarker(Map<String, Object> map){
    //向数据模型放数据
    map.put("name","程序员");
    Student stu1 = new Student();
    stu1.setName("小明");
    stu1.setAge(18);
    stu1.setMondy(1000.86f);
    stu1.setBirthday(new Date());
    Student stu2 = new Student();
    stu2.setName("小红");
    stu2.setMondy(200.1f);
    stu2.setAge(19);
    // stu2.setBirthday(new Date());
    List<Student> friends = new ArrayList<>();
    friends.add(stu1);
    stu2.setFriends(friends);
    stu2.setBestFriend(stu1);
    List<Student> stus = new ArrayList<>();
    stus.add(stu1);
    stus.add(stu2);
    //向数据模型放数据
    map.put("stus",stus);
    //准备map数据
    HashMap<String,Student> stuMap = new HashMap<>();
    stuMap.put("stu1",stu1);
    stuMap.put("stu2",stu2);
    //向数据模型放数据
    map.put("stu1",stu1);
    //向数据模型放数据
    map.put("stuMap",stuMap);
    //返回模板文件名称
    return "test1";
}
```



**创建一个模板**

```freemarker
<table>
  <tr>
    <td>序号</td>
    <td>姓名</td>
    <td>年龄</td>
    <td>钱包</td>
  </tr>
  <#list stus as stu>
    <tr>
      <td>${stu_index + 1}</td>
      <td>${stu.name}</td>
      <td>${stu.age}</td>
      <td>${stu.mondy}</td>
    </tr>
  </#list>
</table>
```

### List指令 

```freemarker
说明：
index：得到循环的下标，使用方法是在stu后边加"_index"，它的值是从0开始
```

### 遍历Map数据 

**创建一个模板**

```freemarker
输出stu1的学生信息：<br/>
姓名：${stuMap['stu1'].name}<br/>
年龄：${stuMap['stu1'].age}<br/>
输出stu1的学生信息：<br/>
姓名：${stuMap.stu1.name}<br/>
年龄：${stuMap.stu1.age}<br/>
遍历输出两个学生信息：<br/>
<table>
<tr>
<td>序号</td>
<td>姓名</td>
<td>年龄</td>
<td>钱包</td>
</tr>
<#list stuMap?keys as k>
<tr>
<td>${k_index + 1}</td>
<td>${stuMap[k].name}</td>
<td>${stuMap[k].age}</td>
<td >${stuMap[k].mondy}</td>
</tr>
</#list>
</table>
```

**结果**

```freemarker
输出stu1的学生信息：
姓名：小明
年龄：18
输出stu1的学生信息：
姓名：小明
年龄：18
遍历输出两个学生信息：
序号 姓名 年龄 钱包
1 小红 19 200.1
2 小明 18 1,000.86
```

### if指令 

if 指令即判断指令，是常用的FTL指令，freemarker在解析时遇到if会进行判断，条件为真则输出if中间的内容，否
则跳过内容不再输出。 

**创建一个模板**

```freemarker
<table>
<tr>
<td>姓名</td>
<td>年龄</td>
<td>钱包</td>
</tr>
<#list stus as stu>
<tr>
<td <#if stu.name =='小明'>style="background:red;"</#if>>${stu.name}</td>
<td>${stu.age}</td>
<td >${stu.mondy}</td>
</tr>
</#list>
</table>

实现的功能是：如果姓名为“小明”则背景色显示为红色。
```



## 运算符 

1、算数运算符 FreeMarker表达式中完全支持算术运算,FreeMarker支持的算术运算符包括:+, - , * , / , % 

2、逻辑运算符 逻辑运算符有如下几个: 逻辑与:&& 逻辑或:|| 逻辑非:! 逻辑运算符只能作用于布尔值,否则将产生错误 

3、比较运算符 表达式中支持的比较运算符有如下几个: 

1 =或者==:判断两个值是否相等. 

2 !=:判断两个值是否不等. 

3 >或者gt:判断左边值是否大于右边值 

4 >=或者gte:判断左边值是否大于等于右边值

5 <或者lt:判断左边值是否小于右边值

6 <=或者lte:判断左边值是否小于等于右边值 



注意: =和!=可以用于字符串,数值和日期来比较是否相等,但=和!=两边必须是相同类型的值,否则会产生错误,而且
FreeMarker是精确比较,"x","x ","X"是不等的.其它的运行符可以作用于数字和日期,但不能作用于字符串,大部分的时候,使用gt等字母运算符代替>会有更好的效果,因为 FreeMarker会把>解释成FTL标签的结束字符,当然,也可以使用括号来避免这种情况,如:<#if (x>y)> 



## 空值处理 

**判断某变量是否存在使用 “??” 用法为:variable??,如果该变量存在,返回true,否则返回false** 

```freemarker
例：为防止stus为空报错可以加上判断如下：

<#if stus??>
<#list stus as stu>
......
</#list>
</#if>
```

**缺失变量默认值使用 “!” 使用!要以指定一个默认值，当变量为空时显示默认值。** 

```freemarker
例： ${name!''}表示如果name为空显示空字符串
```

**如果是嵌套对象则建议使用（）括起来。** 

```freemarker
例： ${(stu.bestFriend.name)!''}表示，如果stu或bestFriend或name为空默认显示空字符串
```

## 内建函数 

内建函数语法格式： 变量+?+函数名称 

### 集合的大小

```freemarker
${集合名?size}
```

###  日期格式化 

```freemarker
显示年月日: ${today?date}
显示时分秒：${today?time}
显示日期+时间：${today?datetime} <br>
自定义格式化： ${today?string("yyyy年MM月")}
```

### 内建函数c 

```freemarker
map.put("point", 102920122);
point是数字型，使用${point}会显示这个数字的值，不并每三位使用逗号分隔。
如果不想显示为每三位分隔的数字，可以使用c函数将数字型转成字符串输出
${point?c}
```

### 将json字符串转成对象 

**一个例子：**
其中用到了 assign标签，assign的作用是定义一个变量。 

```freemarker
<#assign text="{'bank':'工商银行','account':'10101920201920212'}" />
<#assign data=text?eval />
开户行：${data.bank} 账号：${data.account}
```

## 完整的模板 

```freemarker
<!DOCTYPE html>
<html>
<head>
<meta charset="utf‐8">
<title>Hello World!</title>
</head>
<body>
Hello ${name}!
<br/>
<table>
<tr>
<td>序号</td>
<td>姓名</td>
<td>年龄</td>
<td>钱包</td>
</tr>
<#list stus as stu>
<tr>
<td>${stu_index + 1}</td>
<td <#if stu.name =='小明'>style="background:red;"</#if>>${stu.name}</td>
<td>${stu.age}</td>
<td >${stu.mondy}</td>
</tr>
</#list>
</table>
<br/><br/>
输出stu1的学生信息：<br/>
姓名：${stuMap['stu1'].name}<br/>
年龄：${stuMap['stu1'].age}<br/>
输出stu1的学生信息：<br/>
姓名：${stu1.name}<br/>
年龄：${stu1.age}<br/>
遍历输出两个学生信息：<br/>
<table>
<tr>
<td>序号</td>
<td>姓名</td>
<td>年龄</td>
<td>钱包</td>
</tr>
<#list stuMap?keys as k>
<tr>
<td>${k_index + 1}</td>
<td>${stuMap[k].name}</td>
<td>${stuMap[k].age}</td>
<td >${stuMap[k].mondy}</td>
</tr>
</#list>
</table>
</br>
<table>
<tr>
<td>姓名</td>
<td>年龄</td>
<td>出生日期</td>
<td>钱包</td>
<td>最好的朋友</td>
<td>朋友个数</td>
<td>朋友列表</td>
</tr>
<#if stus??>
<#list stus as stu>
<tr>
<td>${stu.name!''}</td>
<td>${stu.age}</td>
<td>${(stu.birthday?date)!''}</td>
<td>${stu.mondy}</td>
<td>${(stu.bestFriend.name)!''}</td>
<td>${(stu.friends?size)!0}</td>
<td>
<#if stu.friends??>
<#list stu.friends as firend>
${firend.name!''}<br/>
</#list>
</#if>
</td>
</tr>
</#list>
</#if>
</table>
<br/>
<#assign text="{'bank':'工商银行','account':'10101920201920212'}" />
<#assign data=text?eval />
开户行：${data.bank} 账号：${data.account}
</body>
</html>
```

## 静态化测试 

### 使用模板文件静态化 

定义模板文件，使用freemarker静态化程序生成html文件。 

```freemarker
//基于模板生成静态化文件
@Test
public void testGenerateHtml() throws IOException, TemplateException {
//创建配置类
Configuration configuration=new Configuration(Configuration.getVersion());
//设置模板路径
String classpath = this.getClass().getResource("/").getPath();
configuration.setDirectoryForTemplateLoading(new File(classpath + "/templates/"));
//设置字符集
configuration.setDefaultEncoding("utf‐8");
//加载模板
Template template = configuration.getTemplate("test1.ftl");
//数据模型
Map<String,Object> map = new HashMap<>();
map.put("name","程序员");
//静态化
String content = FreeMarkerTemplateUtils.processTemplateIntoString(template, map);
//静态化内容
System.out.println(content);
InputStream inputStream = IOUtils.toInputStream(content);
//输出文件
FileOutputStream fileOutputStream = new FileOutputStream(new File("d:/test1.html"));
int copy = IOUtils.copy(inputStream, fileOutputStream);
}
```

### 使用模板字符串静态化 

定义模板字符串，使用freemarker静态化程序生成html文件 

```freemarker
/
/基于模板字符串生成静态化文件
@Test
public void testGenerateHtmlByString() throws IOException, TemplateException {
//创建配置类
Configuration configuration=new Configuration(Configuration.getVersion());
//模板内容，这里测试时使用简单的字符串作为模板
String templateString="" +
"<html>\n" +
" <head></head>\n" +
" <body>\n" +
" 名称：${name}\n" +
" </body>\n" +
"</html>";
//模板加载器
StringTemplateLoader stringTemplateLoader = new StringTemplateLoader();
stringTemplateLoader.putTemplate("template",templateString);
configuration.setTemplateLoader(stringTemplateLoader);
//得到模板
Template template = configuration.getTemplate("template","utf‐8");
//数据模型
Map<String,Object> map = new HashMap<>();
map.put("name","程序员");
//静态化
String content = FreeMarkerTemplateUtils.processTemplateIntoString(template, map);
//静态化内容
System.out.println(content);
InputStream inputStream = IOUtils.toInputStream(content);
//输出文件
FileOutputStream fileOutputStream = new FileOutputStream(new File("d:/test1.html"));
IOUtils.copy(inputStream, fileOutputStream);
}
```

