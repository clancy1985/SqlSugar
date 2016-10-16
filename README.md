#Contact information

Email 610262374@qq.com
QQ Group 225982985
Blog http://www.cnblogs.com/sunkaixuan


#Other versions

ASP.NET CORE MSSQL https://github.com/sunkaixuan/ASP_NET_CORE_ORM_SqlSugar

ASP.NET 4.0+ MYSQL https://github.com/sunkaixuan/MySqlSugar

ASP.NET 4.0+ Sqlite  https://github.com/sunkaixuan/SqliteSugar

ASP.NET 4.0+ ORACLE https://github.com/sunkaixuan/OracleSugar

# 1.Select 

##### 1.1 queryable
```csharp

//select all
var student = db.Queryable<Student>().ToList();
var studentDynamic = db.Queryable<Student>().ToDynamic();
var studentJson = db.Queryable<Student>().ToJson();


//select single
var single = db.Queryable<Student>().Single(c => c.id == 1);
//select single by primarykey
var singleByPk = db.Queryable<Student>().InSingle(1);
//select single or default
var singleOrDefault = db.Queryable<Student>().SingleOrDefault(c => c.id == 11111111);
//select single or default
var single2 = db.Queryable<Student>().Where(c => c.id == 1).SingleOrDefault();

//select first
var first = db.Queryable<Student>().Where(c => c.id == 1).First();
var first2 = db.Queryable<Student>().Where(c => c.id == 1).FirstOrDefault();

//between 11 and 20
var page1 = db.Queryable<Student>().Where(c => c.id > 10).OrderBy(it => it.id).Skip(10).Take(10).ToList();

//between 11 and 20 
var page2 = db.Queryable<Student>().Where(c => c.id > 10).OrderBy(it => it.id).ToPageList(2, 10);

//get count
var count = db.Queryable<Student>().Where(c => c.id > 10).Count();

//skip 2
var skip = db.Queryable<Student>().Where(c => c.id > 10).OrderBy(it => it.id).Skip(2).ToList();

//take 2
var take = db.Queryable<Student>().Where(c => c.id > 10).OrderBy(it => it.id).Take(2).ToList();

//Not like 
string conval = "a";
var notLike = db.Queryable<Student>().Where(c => !c.name.Contains(conval.ToString())).ToList();

//Like
conval = "三";
var like = db.Queryable<Student>().Where(c => c.name.Contains(conval)).ToList();

//where sql string
var student12 = db.Queryable<Student>().Where(c => "a" == "a").Where("id>@id", new { id = 1 }).ToList();
var student13 = db.Queryable<Student>().Where(c => "a" == "a").Where("id>100 and id in( select 1)").ToList();


//is any
bool isAny100 = db.Queryable<Student>().Any(c => c.id == 100);
bool isAny1 = db.Queryable<Student>().Any(c => c.id == 1);


//get max id
object maxId = db.Queryable<Student>().Max(it => it.id);
int maxId1 = db.Queryable<Student>().Max(it => it.id).ObjToInt();
int maxId2 = db.Queryable<Student>().Max<int>("id"); 

//get min id
int minId1 = db.Queryable<Student>().Where(c => c.id > 0).Min(it => it.id).ObjToInt();
int minId2 = db.Queryable<Student>().Where(c => c.id > 0).Min<int>("id");


//order By 
var orderList = db.Queryable<Student>().OrderBy("id desc,name asc").ToList();
//order by 
var order2List = db.Queryable<Student>().OrderBy(it => it.name).OrderBy(it => it.id, OrderByType.desc).ToList(); // order by name as ,order by id desc

//In
var intArray = new[] { "5", "2", "3" };
var intList = intArray.ToList();
var listnew = db.Queryable<Student>().Where(it => intArray.Contains(it.name)).ToList();
var list0 = db.Queryable<Student>().In(it => it.id, 1, 2, 3).ToList();
var list1 = db.Queryable<Student>().In(it => it.id, intArray).ToList();
var list2 = db.Queryable<Student>().In("id", intArray).ToList();
var list3 = db.Queryable<Student>().In(it => it.id, intList).ToList();
var list4 = db.Queryable<Student>().In("id", intList).ToList();
var list6 = db.Queryable<Student>().In(intList).ToList(); //in primary fileds

//group by
var list7 = db.Queryable<Student>().Where(c => c.id < 20).GroupBy(it => it.sex).Select("sex,count(*) Count").ToDynamic();
var list8 = db.Queryable<Student>().Where(c => c.id < 20).GroupBy(it => it.sex).GroupBy(it => it.id).Select("id,sex,count(*) Count").ToDynamic();
List<StudentGroup> list9 = db.Queryable<Student>().Where(c => c.id < 20).GroupBy(it => it.sex).Select<StudentGroup>("Sex,count(*) Count").ToList();
List<StudentGroup> list10 = db.Queryable<Student>().Where(c => c.id < 20).GroupBy("sex").Select<StudentGroup>("Sex,count(*) Count").ToList();
//SELECT Sex,Count=count(*)  FROM Student  WHERE 1=1  AND  (id < 20)    GROUP BY Sex --生成结果



//join
var jList = db.Queryable<Student>()
	.JoinTable<School>((s1, s2) => s1.sch_id == s2.id) //默认left join
	.Where<School>((s1, s2) => s1.id == 1)
	.Select("s1.*,s2.name as schName")
	.ToDynamic();

/*join sql
						 SELECT s1.*,s2.name as schName 
						 FROM [Student]  s1 
						 LEFT JOIN [School]  s2 ON  s1.sch_id  = s2.id 
						 WHERE  s1.id  = 1 */

//join and page
var jList2 = db.Queryable<Student>()
	.JoinTable<School>((s1, s2) => s1.sch_id == s2.id) //默认left join
	//left inner join 
	//.JoinTable<School>((s1, s2) => s1.sch_id == s2.id ,JoinType.INNER)
	.Where<School>((s1, s2) => s1.id > 1)
	.OrderBy(s1 => s1.name)
	.Skip(10)
	.Take(20)
	.Select("s1.*,s2.name as schName")
	.ToDynamic();

//join three tables
var jList3 = db.Queryable<Student>()
	.JoinTable<School>((s1, s2) => s1.sch_id == s2.id) // left join  School s2  on s1.id=s2.id
	.JoinTable<School>((s1, s3) => s1.sch_id == s3.id) // left join  School s3  on s1.id=s3.id
	.Where<School>((s1, s2) => s1.id > 1)  // where s1.id>1
	.Where(s1 => s1.id > 0)
	.OrderBy<School>((s1, s2) => s1.id) //order by s1.id 多个order可以  .oderBy().orderby 叠加 
	.Skip(10)
	.Take(20)
	.Select("s1.*,s2.name as schName,s3.name as schName2")//select目前只支持这种写法
	.ToDynamic();


//join five
List<V_Student> jList4 =
	db.Queryable<Student>()
	.JoinTable<School>((s1, s2) => s1.sch_id == s2.id) // left join  School s2  on s1.id=s2.id
	.JoinTable<School, Area>((s1, s2, a1) => a1.id == s2.AreaId)// left join  Area a1  on a1.id=s2.AreaId  
		.JoinTable<Area, School>((s1, a1, s3) => a1.id == s3.AreaId)// left join  School s3  on a1.id=s3.AreaId  
			.JoinTable<School>((s1, s4) => s1.sch_id == s4.id) // left join  School s2  on s1.id=s4.id
			.Select<School, Area, V_Student>((s1, s2, a1) => new V_Student { id = s1.id, name = s1.name, SchoolName = s2.name, AreaName = a1.name }).ToList();

//join five sql
//SELECT id = s1.id, name = s1.name, SchoolName = s2.name, AreaName = a1.name  
//FROM [Student]   s1 
//LEFT JOIN School  s2 ON  ( s1.sch_id  = s2.id )    
//LEFT JOIN Area  a1 ON  ( a1.id  = s2.AreaId )     
//LEFT JOIN School  s3 ON  ( a1.id  = s3.AreaId )    
//LEFT JOIN School  s4 ON  ( s1.sch_id  = s4.id )    
//WHERE 1=1    


//Join child
var childQuery = db.Queryable<Area>().Where("id=@id").Select(it => new { id = it.id }).ToSql();//create child SQL
string childTableName =SqlSugarTool.PackagingSQL(childQuery.Key);//package sql
var queryable = db.Queryable<Student>()
	.JoinTable<School>((s1, s2) => s1.sch_id == s2.id)  //LEFT JOIN School  s2 ON  ( s1.sch_id  = s2.id )  
	.JoinTable(childTableName, "a1", "a1.id=s2.areaid", new { id = 1 }, JoinType.INNER) //INNER JOIN (SELECT *  FROM [Area]   WHERE 1=1  AND id=@id   ) a1 ON a1.id=s2.areaid
	.OrderBy(s1 => s1.id);

var list = queryable.Select<School, Area, V_Student>((s1, s2, a1) => new V_Student { id = s1.id, name = s1.name, SchoolName = s2.name, AreaName = a1.name })
	.ToPageList(0, 200);
var count2 = queryable.Count();


//append queryable
var queryable2 = db.Queryable<Student>().Where(it => true);
if (maxId.ObjToInt() == 1)
{
	queryable2.Where(it => it.id == 1);
}
else
{
	queryable2.Where(it => it.id == 2);
}
var listJoin = queryable2.ToList();


//queryable SqlSugarClient 
var par = new Queryable<Student>().Where(it => it.id == 1);//声名没有connection对象的Queryable
par.DB = db;
var listPar = par.ToList();


//get sql和 pars
var id = 1;
var sqlAndPars = db.Queryable<Student>().Where(it => it.id == id).OrderBy(it => it.id).ToSql();



//express functions
var par1 = "2015-1-1"; var par2 = "   我 有空格A, ";
var r1 = db.Queryable<Student>().Where(it => it.name == par1.ObjToString()).ToList(); //ObjToString会将null转转成""
var r2 = db.Queryable<InsertTest>().Where(it => it.d1 == par1.ObjToDate()).ToList();
var r3 = db.Queryable<InsertTest>().Where(it => it.id == 1.ObjToInt()).ToList();//ObjToInt会将null转转成0
var r4 = db.Queryable<InsertTest>().Where(it => it.id == 2.ObjToDecimal()).ToList();
var r5 = db.Queryable<InsertTest>().Where(it => it.id == 3.ObjToMoney()).ToList();
var r6 = db.Queryable<InsertTest>().Where(it => it.v1 == par2.Trim()).ToList();
var convert1 = db.Queryable<Student>().Where(c => c.name == "a".ToString()).ToList();
var convert2 = db.Queryable<Student>().Where(c => c.id == Convert.ToInt32("1")).ToList();
var convert3 = db.Queryable<Student>().Where(c => c.name == par2.ToLower()).ToList();
var convert4 = db.Queryable<Student>().Where(c => c.name == par2.ToUpper()).ToList();
var convert5= db.Queryable<Student>().Where(c => DateTime.Now > Convert.ToDateTime("2015-1-1")).ToList();
var c1 = db.Queryable<Student>().Where(c => c.name.Contains("a")).ToList();
var c2 = db.Queryable<Student>().Where(c => c.name.StartsWith("a")).ToList();
var c3 = db.Queryable<Student>().Where(c => c.name.EndsWith("a")).ToList();
var c4 = db.Queryable<Student>().Where(c => !string.IsNullOrEmpty(c.name)).ToList();
var c5 = db.Queryable<Student>().Where(c => c.name.Equals("小杰")).ToList();
var c6 = db.Queryable<Student>().Where(c => c.name.Length > 4).ToList();
var time = db.Queryable<InsertTest>().Where(c => c.d1>DateTime.Now.AddDays(1)).ToList();
var time2 = db.Queryable<InsertTest>().Where(c => c.d1 > DateTime.Now.AddYears(1)).ToList();
var time3 = db.Queryable<InsertTest>().Where(c => c.d1 > DateTime.Now.AddMonths(1)).ToList();
var intList = intArray.ToList();
var list0 = db.Queryable<Student>().In(it => it.id, 1,2,3).ToList();
var list1 = db.Queryable<Student>().In(it=>it.id, intArray).ToList();
var list2 = db.Queryable<Student>().In("id", intArray).ToList();
var list3 = db.Queryable<Student>().In(it => it.id, intList).ToList();
var list4 = db.Queryable<Student>().In("id", intList).ToList();

//group by
var list7 = db.Queryable<Student>().Where(c => c.id < 20).GroupBy(it => it.sex).Select("sex,Count=count(*)").ToDynamic();
var list8 = db.Queryable<Student>().Where(c => c.id < 20).GroupBy(it => it.sex).GroupBy(it=>it.id).Select("id,sex,Count=count(*)").ToDynamic();
List<SexTotal> list5 = db.Queryable<Student>().Where(c => c.id < 20).GroupBy(it => it.sex).Select<Student, SexTotal>("Sex,Count=count(*)").ToList();
List<SexTotal> list6 = db.Queryable<Student>().Where(c => c.id < 20).GroupBy("sex").Select<Student, SexTotal>    ("Sex,Count=count(*)").ToList();
//SELECT Sex,Count=count(*)  FROM Student  WHERE 1=1  AND  (id < 20)    GROUP BY Sex 


//join
var jList = db.Queryable<Student>()
	.JoinTable<Student, School>((s1, s2) => s1.sch_id == s2.id) //默认left join
		.Where<Student, School>((s1, s2) => s1.id == 1)
			.Select("s1.*,s2.name as schName")
			.ToDynamic();

//join by page
var jList2 = db.Queryable<Student>()
	.JoinTable<Student, School>((s1, s2) => s1.sch_id == s2.id) //default left join
		//inner join
		//.JoinTable<Student, School>((s1, s2) => s1.sch_id == s2.id  ,JoinType.INNER)
		.Where<Student, School>((s1, s2) => s1.id > 1)
			.OrderBy<Student, School>((s1, s2) => s1.name)
				.Skip(10)
				.Take(20)
				.Select("s1.*,s2.name as schName")
				.ToDynamic();

//join select new 
var jList3 = db.Queryable<Student>()
	.JoinTable<Student, School>((s1, s2) => s1.sch_id == s2.id) // left join  School s2  on s1.id=s2.id
		.Where<Student, School>((s1, s2) => s1.id > 1)  // where s1.id>1
			.OrderBy<Student, School>((s1, s2) => s1.id) //order by s1.id no one  ordder   .oderBy().orderby  
				.Skip(1)
				.Take(2)
				.Select<Student, School, classNew>((s1, s2) => new classNew() { newid = s1.id, newname = s2.name, xx_name = s1.name }) 
					.ToList();
```

1.2 SqlQuery
```csharp
//to list
List<Student> list1 = db.SqlQuery<Student>("select * from Student");
//to list with par
List<Student> list2 = db.SqlQuery<Student>("select * from Student where id=@id", new { id = 1 });
//to dynamic
dynamic list3 = db.SqlQueryDynamic("select * from student");
//to json
string list4 = db.SqlQueryJson("select * from student");
//get int
var list5 = db.SqlQuery<int>("select top 1 id from Student").SingleOrDefault();
//get dictionary
Dictionary<string, string> list6 = db.SqlQuery<KeyValuePair<string, string>>("select id,name from Student").ToDictionary(it => it.Key, it => it.Value);
//get List<string[]>
var list7 = db.SqlQuery<string[]>("select top 1 id,name from Student").SingleOrDefault();
//get sp result
var spResult = db.SqlQuery<School>("exec sp_school @p1,@p2", new { p1 = 1, p2 = 2 });

//get sp and output 
var pars = SqlSugarTool.GetParameters(new { p1 = 1,p2=0 }); //dynmaic to SqlParameter
db.IsClearParameters = false;//close clear parametrs
pars[1].Direction = ParameterDirection.Output; //set  output
var spResult2 = db.SqlQuery<School>("exec sp_school @p1,@p2 output", pars);
db.IsClearParameters = true;//open  clear parameters
var outPutValue = pars[1].Value;//get output @p2 value


//sp 
var pars2 = SqlSugarTool.GetParameters(new { p1 = 1, p2 = 0 }); 
db.CommandType = CommandType.StoredProcedure;
var spResult3 = db.SqlQuery<School>("sp_school", pars2);
db.CommandType = CommandType.Text;


//get first row first column
string v1 = db.GetString("select '张三' as name");
int v2 = db.GetInt("select 1 as name");
double v3 = db.GetDouble("select 1 as name");
decimal v4 = db.GetDecimal("select 1 as name");
//....
```
