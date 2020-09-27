#### 实验1 SAS基础(一)

##### 承子杰 1807402074



###### 2.SAS库的建立与使用(自定),并将sashelp库下的数据集class存放到自己库中。

​    ==步骤 :== STEP1  先在C盘中建立一个文件夹

​     ![](C:\Users\14002\Desktop\SAS实验1\1.png)

​            STEP2  然后再SAS界面中创建一个自己的数据库，引用路径为上面建立数据库的文件位置，我在这里给数据库起名为A。

 ![](C:\Users\14002\Desktop\SAS实验1\2.png)

​            STEP3  找到sashelp.class中的文件，右击复制，再粘贴到逻辑库A中

![](C:\Users\14002\Desktop\SAS实验1\3.png)

------



###### 3.产生正态分布随机数，计算正态分布上0.05，0.025，0.01分位数

   ==分析：==可以调用rannor（seed，x）函数来生成正态分布随机数，对于正态分布的分位数可以采用PROBIT(p)函数来调用。

   ==代码与结果：==生成正态分布随机数代码如下：

```SAS
data a,b;
seed=2;
do i=1 to 10;
call rannor(seed,x);
*采用种子为2，生存10个随机数
output ;
end;
run;

proc print data=a;
run;
```

结果如下：                      

![](C:\Users\14002\Desktop\SAS实验1\4.png)

生成各分位数的代码如下：

```SAS
data a;
input x;
x_p=PROBIT(x);
*正态分布分位数函数
datalines;
0.05 
0.025 
0.01 
run;

proc print data=a;
run;
```

结果如下：

![](C:\Users\14002\Desktop\SAS实验1\5.png)

==总结：== 正态分布的0.05分位数是 -1.64，0.025的分位数为-1.96 ，0.01的分位数为 -2.33

------



###### 4.产生标准正态分布表（计算正态分布的概率）

==分析：== 对于正态分布的概率，可以采用PROBNORM（x）函数（P（X$\leq$x））

==代码与结果：==

```SAS
data a;
do x=-4 to 4;
p_x=PROBNORM(x);
*正态分布概率计算函数
output;
end;
run;

proc print data=a;
run;
```

结果如下：

![](C:\Users\14002\Desktop\SAS实验1\6.png)

==总结：==考虑到正态分布的$3\sigma$原则，故我们只生成了-4到4的正态分布概率。

------



###### 5. SAS数据集的整理（增加、修改变量；计数变量）：新建数据集，对sashelp.class中的变量进行如下操作：（1）使变量$weight$值加10。（2）增加变量$bmi=weight/height^2$。(3)统计数据集中观测个数（用累计变量的形式），计算总体重（用累加变量的形式）

==分析：==在2中建立的逻辑库A中新建立一个数据集result，拷贝A.class中的数据。

​           对于（1），只需直接引用$weight$变量，执行$weight=weight+10$语句。

​           对于（2），只需引入一个新变量$bmi$，执行$bmi=weight/(height**2)$语句。

​           对于（3），只需引入一个累计变量$i$，执行$i+1$语句。

​           对于（4），只需引入一个总体重变量$Sum\_Weight$,执行$Sum\_Weight+weight$语句。

==代码与结果：==

```SAS
data a.result;
set a.class;
* a.result 拷贝 a.class 中的数据
weight=weight+10;
*引用 weight 变量，并加10
bmi=weight/(height**2);
*创建 bmi 变量，bmi = weight / (height ** 2)
format bmi 5.3;
*控制 bmi 格式 5个位宽，小数点后3位，整数1位
i+1;
*累加变量i，记录观测数量
Sum_Weight+weight;
*体重累加求和
run;

proc print data=a.result;
*输出结果
run;
```

结果如下图：

<img src="C:\Users\14002\Desktop\SAS实验1\7.png" style="zoom:80%;" />

==总结:==考虑到 $bmi$ 为小数，故在此做了格式设定，保留了三位小数。

------



###### 6.自己编程举例，使得程序中包括不同形式的条件语句、三种循环语句

==分析：==仍旧新建数据集，采用sashelp.class中的数据进行实验。

​            对于 *IF* 语句，我们进行如下操作，将男性的观测分别输出到数据集M中。

​            对于 *IF-THEN* 语句，我们做如下操作，男生的身高加10英尺，女生的体重减10磅

​            对于 *SELECT-WHEN-OTHERWISE* 语句，我们做如下操作，年龄小于13岁的 $bmi$ 加 0.01，年龄大于14岁的 $bmi$ 减0.01

​            最后对 *DO-END* , *DO-WHILE-END* 和 *DO-UNTIL-END* 单独进行试验，通过三种方法分别对100以内的整数求和。

==代码与结果：==

```SAS
data a.m;
set a.class;
if(Sex='男');
*删选出性别为男的记录
run;

proc print data=a.m;
*打印结果
run;

data a.result;
set a.class;
if(Sex='男') then
	height=height+10;
	*性别为男的身高加10
else
	weight=weight-10;
	*性别为女的体重-10
bmi=weight/(height**2);
format bmi 5.3;
select(age);
	when(11,12) bmi=bmi+0.01;
	*年龄为11，12岁的bmi+0.01
	when(15,16) bmi=bmi-0.01;
	*年龄为15，16岁的bmi-0.01
	otherwise bmi=bmi;
	*其余的bmi不变
end;

proc print data=a.result;
*打印结果
run;
```

结果：

男性观测如下：

<img src="C:\Users\14002\Desktop\SAS实验1\8.png" style="zoom:80%;" />

Result数据集操作后结果如下：

<img src="C:\Users\14002\Desktop\SAS实验1\9.png" style="zoom:67%;" />

求和代码如下：

```SAS
data Sum;
sum1=0;
sum2=0;
sum3=0;
*初始化总和变量
do i=1 to 100;
	sum1=sum1+i;
end;
j=0;
do while(j<=100);
	sum2=sum2+j;
	j=j+1;
end;
k=0;
do until(k>100);
	sum3=sum3+k;
	k=k+1;
end;
drop i j k;
*删除 i j k变量
run;

proc print data=sum;
*输出结果
run;
```

结果如下：

![](C:\Users\14002\Desktop\SAS实验1\10.png)