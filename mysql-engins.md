### MySQL存储引擎

##### 1.**MyISAM**

它不支持事务，也不支持外键，尤其是访问速度快，对事务完整性没有要求或者以SELECT、INSERT为主的应用基本都可以使用这个引擎来创建表。  
每个MyISAM在磁盘上存储成3个文件，其中文件名和表名都相同，但是扩展名分别为：

* .frm\(存储表定义\)

* MYD\(MYData，存储数据\)

* MYI\(MYIndex，存储索引\)

数据文件和索引文件可以放置在不同的目录，平均分配IO，获取更快的速度。要指定数据文件和索引文件的路径，需要在创建表的时候通过DATA DIRECTORY和INDEX DIRECTORY语句指定，文件路径需要使用绝对路径。  
　　每个MyISAM表都有一个标志，服务器或myisamchk程序在检查MyISAM数据表时会对这个标志进行设置。MyISAM表还有一个标志用来表明该数据表在上次使用后是不是被正常的关闭了。如果服务器以为当机或崩溃，这个标志可以用来判断数据表是否需要检查和修复。如果想让这种检查自动进行，可以在启动服务器时使用--myisam-recover现象。这会让服务器在每次打开一个MyISAM数据表是自动检查数据表的标志并进行必要的修复处理。MyISAM类型的表可能会损坏，可以使用CHECK TABLE语句来检查MyISAM表的健康，并用REPAIR TABLE语句修复一个损坏到MyISAM表。  
　　MyISAM的表还支持3种不同的存储格式：

* 静态\(固定长度\)表

* 动态表

* 压缩表

其中静态表是默认的存储格式。静态表中的字段都是非变长字段，这样每个记录都是固定长度的，这种存储方式的优点是存储非常迅速，容易缓存，出现故障容易恢复；缺点是占用的空间通常比动态表多。静态表在数据存储时会根据列定义的宽度定义补足空格，但是在访问的时候并不会得到这些空格，这些空格在返回给应用之前已经去掉。同时需要注意：在某些情况下可能需要返回字段后的空格，而使用这种格式时后面到空格会被自动处理掉。  
　　动态表包含变长字段，记录不是固定长度的，这样存储的优点是占用空间较少，但是频繁到更新删除记录会产生碎片，需要定期执行OPTIMIZE TABLE语句或myisamchk -r命令来改善性能，并且出现故障的时候恢复相对比较困难。  
　　压缩表由myisamchk工具创建，占据非常小的空间，因为每条记录都是被单独压缩的，所以只有非常小的访问开支。

##### 2.**InnoDB **

InnoDB是一个健壮的事务型存储引擎，这种存储引擎已经被很多互联网公司使用，为用户操作非常大的数据存储提供了一个强大的解决方案。我的电脑上安装的MySQL 5.6.13版，InnoDB就是作为默认的存储引擎。InnoDB还引入了行级锁定和外键约束，在以下场合下，使用InnoDB是最理想的选择：

1.更新密集的表。InnoDB存储引擎特别适合处理多重并发的更新请求。

2.事务。InnoDB存储引擎是支持事务的标准MySQL存储引擎。

3.自动灾难恢复。与其它存储引擎不同，InnoDB表能够自动从灾难中恢复。

4.外键约束。MySQL支持外键的存储引擎只有InnoDB。

5.支持自动增加列AUTO\_INCREMENT属性。

一般来说，如果需要事务支持，并且有较高的并发读取频率，InnoDB是不错的选择。

外键约束说明：

MySQL支持外键的存储引擎只有InnoDB，在创建外键的时候，父表必须有对应的索引，子表在创建外键的时候也会自动创建对应的索引。 在创建索引的时候，可以指定在删除、更新父表时，对子表进行的相应操作，包括restrict、cascade、set null和no action。其中restrict和no action相同，是指限制在子表有关联的情况下，父表不能更新；casecade表示父表在更新或删除时，更新或者删除子表对应的记录；set null 则表示父表在更新或者删除的时候，子表对应的字段被set null。当某个表被其它表创建了外键参照，那么该表对应的索引或主键被禁止删除。可以使用set foreign\_key\_checks=0;临时关闭外键约束，set foreign\_key\_checks=1;打开约束。

##### 3.**MEMORY**

使用MySQL Memory存储引擎的出发点是速度。为得到最快的响应时间，采用的逻辑存储介质是系统内存。虽然在内存中存储表数据确实会提供很高的性能，但当mysqld守护进程崩溃时，所有的Memory数据都会丢失。获得速度的同时也带来了一些缺陷。它要求存储在Memory数据表里的数据使用的是长度不变的格式，这意味着不能使用BLOB和TEXT这样的长度可变的数据类型，VARCHAR是一种长度可变的类型，但因为它在MySQL内部当做长度固定不变的CHAR类型，所以可以使用。

一般在以下几种情况下使用Memory存储引擎：

1.目标数据较小，而且被非常频繁地访问。在内存中存放数据，所以会造成内存的使用，可以通过参数max\_heap\_table\_size控制Memory表的大小，设置此参数，就可以限制Memory表的最大大小。

2.如果数据是临时的，而且要求必须立即可用，那么就可以存放在内存表中。

3.存储在Memory表中的数据如果突然丢失，不会对应用服务产生实质的负面影响。

Memory同时支持散列索引和B树索引。B树索引的优于散列索引的是，可以使用部分查询和通配查询，也可以使用&lt;、&gt;和&gt;=等操作符方便数据挖掘。散列索引进行“相等比较”非常快，但是对“范围比较”的速度就慢多了，因此散列索引值适合使用在=和&lt;&gt;的操作符中，不适合在&lt;或&gt;操作符中，也同样不适合用在order by子句中。

可以在表创建时利用USING子句指定要使用的版本。例如：

复制代码代码如下:

```
create table users
(
    id smallint unsigned not null auto_increment,
    username varchar(15) not null,
    pwd varchar(15) not null,
    index using hash (username),
    primary key (id)
)engine=memory;
```

上述代码创建了一个表，在username字段上使用了HASH散列索引。下面的代码就创建一个表，使用BTREE索引。

复制代码代码如下:

```
create table users
(
    id smallint unsigned not null auto_increment,
    username varchar(15) not null,
    pwd varchar(15) not null,
    index using btree (username),
    primary key (id)
)engine=memory;
```

##### 4.**MERGE**

merge存储引擎是一组MyISAM表的组合，这些MyISAM表结构必须完全相同，MERGE表中并没有数据，对MERGE类型的表可以进行查询、更新、删除的操作，这些操作实际上是对内部的MyISAM表进行操作。对于对MERGE表进行的插入操作，是根据INSERT\_METHOD子句定义的插入的表，可以有3个不同的值，first和last值使得插入操作被相应的作用在第一个或最后一个表上，不定义这个子句或者为NO，表示不能对这个MERGE表进行插入操作。可以对MERGE表进行drop操作，这个操作只是删除MERGE表的定义，对内部的表没有任何影响。MERGE在磁盘上保留2个以MERGE表名开头文件：.frm文件存储表的定义；.MRG文件包含组合表的信息，包括MERGE表由哪些表组成，插入数据时的依据。可以通过修改.MRG文件来修改MERGE表，但是修改后要通过flush table刷新。

| `createtableman_all(idint,namevarchar(20))engine=mergeunion=(man1,man2) insert_methos=last;` |
| :--- |




