1.Select
```xml
<select id="selectX" parameterType="int" resultType="hashmap">
select * from table1 where column = #{param}
</select>
```
名为selectX的sql接受名称为param的int类型参数，并返回一个Entity类型的对象，键是列名，值是行中对应的值。

#{param}
Mybatis会创建预处理语句PreparedStatement参数，在JDBC中，这样的参数会在SQL以“？”进行标识，并被传递到一个新的预处理语句。
// 近似的 JDBC 代码，非 MyBatis 代码...
String selectPerson = "SELECT * FROM PERSON WHERE ID=?";
PreparedStatement ps = conn.prepareStatement(selectPerson);
ps.setInt(1,id);
select 元素允许你配置很多属性来配置每条语句的行为细节。

<select
  id="selectPerson"
  parameterType="int"
  parameterMap="deprecated"
  resultType="hashmap"
  resultMap="personResultMap"
  flushCache="false"
  useCache="true"
  timeout="10"
  fetchSize="256"
  statementType="PREPARED"
  resultSetType="FORWARD_ONLY">

Select元素的属性