分割表我们可以分步来进行
首先把一些基本的，好筛选出来的数据，先提取出来放到我们创建的表里

    create table lagou_city as
	select d.id,p.cityName as province,c.cityName as 	city,d.cityName as district from
	(select*from s_provinces where depth='3') d
	join s_provinces c on d.parentId=c.id and c.depth='2'
	join s_provinces p on c.parentId=p.id and p.depth='1'

这样我们就先可以把所有‘省，市，县’都有数据的行，从s_provinces中提取到
lagou_city表中，之后我们再把那些县为null的行数添加进去，

	insert into lagou_city
	select c.id,p.cityName as province,c.cityName as city,null 	as district from (select*from s_provinces where depth='2') c
	join s_provinces p on c.parentId=p.id and p.depth='1';

这样就可以列出所有是‘省，市，县’，并且还包括了县为null的数据，因为这样创建的lagou_city表都会默认给字段添上not null的约束，使得我们在添加数据的时候回出错，所以我们在添加数据前要先把该字段的not null改成null

	ALTER TABLE lagou_city MODIFY COLUMN district VARCHAR(60) DEFAULT NULL;
这种分步的方法有些繁琐，我们也可以用union的方法进行拼接

	create table lagou_city as
	select d.id, p.cityName as province, c.cityName as city, d	.cityName as district from
  	(select * from s_provinces where depth=3) d
    	join s_provinces c on d.parentId = c.id and c.depth=2
    	join s_provinces p on c.parentId = p.id and p.depth=1
	union
	select c.id, p.cityName as province, c.cityName as city, null as district from (select * from s_provinces where depth=2) c
  	join s_provinces p on c.parentId = p.id and p.depth = 1;
这种方法的优点是为我们简化了代码，缺点是它的运行效率比上面的分步来得更低

上面的方法都是通过创建数据库，搬移的数据方法，来达到分表的目的，我们也可以直接在表上进行数据或是字段的删除或修改，但在使用这种方法的时候要小心，稍有操作不当就会造成数据的丢失，所以在用这个方法的时候要注意数据的备份。
所以我认为创建表的方法更好，虽然它会占用更多的内存。
