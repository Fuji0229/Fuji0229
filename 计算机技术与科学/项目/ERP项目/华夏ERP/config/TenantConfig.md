
1.注入PaginationInterceptor（Mybatis Plus分页插件）

MybatisPlusInterceptor（核心插件）
代理方法：
-   Executor#query
-   Executor#update
-   StatementHandler#prepare
- 属性：
  > private List<InnerInterceptor> interceptors = new ArrayList<>();
  提供的插件都基于改接口实现功能
  已有的功能：
  -   自动分页: PaginationInnerInterceptor
  -   多租户:TenantLineInnerInterceptor
  -   动态表名: DynamicTableNameInnerInterceptor
  -   乐观锁：OptimisticLockerInnerInterceptor
  -   sql性能规范: IllegalSQLInnerInterceptor
  -   防止全表更新与删除: BlockAttackInnerInterceptor
使用多个功能需要注意顺序关系，建议使用如下顺序
	- 多租户
	- 动态表名
	- 分页
	- 乐观锁
	- sql性能规范
	- 防止全表更新与删除 
	  (对sql单次改造的优先放入，不对sql进行改造的最后放入)
Mapper及mapper.xml
``` java
@Mapper 
public interface UserMapper extends BaseMapper<User> {
	List<User> findPageUsers(Page<User> page); 
}
```
``` xml
<select id="findPageUsers" resultType="org.wxmx.mybatis_plus_study.entity.User"> 
select * 
from `user` </select>
```

带查询条件的分页
使用PaginationInterceptor 作为分页插件

在编写mapper.xml中的SQL语句的时候, 语句末尾不能使用 ; 结尾，因为做分页时，会在编写的SQL语句后面拼接Limit语句，导致出现SQL语法错误(SQLSyntaxErrorException)。如下：
