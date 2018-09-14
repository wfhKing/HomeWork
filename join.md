### 我们要查询出广东省下所有市下的所有区，就算没有区也要显示null
所以我们就要先把广东省下所有的市先查询出来

	select s.id,p.cityName,s.cityName from s_provinces s
		join s_provinces p on s.parentId=p.id
	where p.cityName='广东省';

这样就可以查询出所有的市，
之后，我们可以在查询出广东省下所有市下的所有区

	select p.id,p.cityName,s.cityName,x.cityName from s_provinces p
        join s_provinces s on s.parentId=p.id
        join s_provinces x on x.parentId=s.id
	where p.cityName='广东省'

但这样查询出来的数据都是‘区’有值的，而不会显示‘区’为null的值
这样肯定是不符合规定的，这是我们就可以用到'union',它可以实现
两个结果集的合并，但两个结果集中的数量要相等，所有我们可以用
null或是“”来占一个位置

	select p.id,p.cityName,s.cityName,x.cityName from s_provinces p
        join s_provinces s on s.parentId=p.id
        join s_provinces x on x.parentId=s.id
	where p.cityName='广东省'
	union
	select s.id,p.cityName,s.cityName,null from s_provinces s
		join s_provinces p on s.parentId=p.id
	where p.cityName='广东省';

union和union all的区别就是，union会把结果集中不包含重复的数据，而union
all 会包含重复的数据。
