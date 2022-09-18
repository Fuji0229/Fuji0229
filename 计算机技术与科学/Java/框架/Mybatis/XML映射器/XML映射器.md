1.Select
```xml
<select id="selectX" parameterType="int" resultType="Entity">
select * from table1 where column = #{param}
</select>
```