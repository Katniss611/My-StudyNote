# MySQL

[TOC]



#### MySQL的架构？

* 连接层
* 服务层
* 引擎层
* 存储层

#### MySQL执行一条SQL语句的流程？

<img src="https://s2.loli.net/2022/05/07/BLvK89g7cm5IPMj.png" alt="image-20220507211341859" style="zoom:50%;" />

#### 说说MySQL有哪些存储引擎？都有哪些区别？

常见的存储引擎有：MyISAM、InnoDB、MEMORY、NDB。

##### 存储引擎的对比

######  文件存储结构对比

​	**frm文件：** 在MySQL中建立任何一张数据表在其对应的数据库目录下都有对应该表的.frm文件来保存每个数据表的**元数据(meta)**信息，包括**表的结构和定义等**。

​	与存储引擎无关，任何数据引擎的数据表都必须有.frm文件。`表名.frm`

**MyISAM的文件结构**

​	------------.frm文件

​	------------.MYD(MYData)文件：MyISAM存储引擎专用，用于存储表的数据

​	------------.MYI(MYIndex)文件：MyISAM存储引擎专用，存储表的索引

**InnoDB的文件结构**

​	------------.frm文件

​	------------.ibd文件或.ibdata文件： 这两种文件都是存放 InnoDB 数据的文件。使用**共享表**(.ibdata，所有表共享一个或者通过配置使用多个.ibdata文件)空间还是**独享表**(.ibd，每个表一个.ibd文件)空间的数据存储方式的不同配置。



#### 索引

> 索引所采用的数据结构，以及为什么要这样设计？
>
> 在数据库中创建索引的原则？
>
> BTree 索引和 Hash 索引的适用范围。

1. 定义：索引是帮助MySQL高效获取数据的**数据结构**。
2. 目的：提高查询效率，类比字典、图书目录、火车站车次表等。
3. 特点：

* 索引本身很大不可能全部存储在内存中，一般以**索引文件**的形式存储在磁盘上。
* 一般索引即指**B+树**（多路搜索树）结构组织的索引。其中聚集索引，次要索引，覆盖索引，复合索引，前缀索引，唯一索引默认都是使用B+树索引，统称索引。此外还有哈希索引等。

##### 索引底层实现原理

​	数据库索引存储在磁盘上，当数据量大时，将索引加载到内存中是不现实的，只能逐一加载每一个磁盘块（block，对应索引树的节点），索引树越低，越矮胖，磁盘IO次数就少。

​	MySQL支持两种索引，B-树索引和哈希索引。B-树和哈希表在数据查询时的效率是非常高的。

##### **索引分类**

###### BST(二叉搜索树)

​	性质：左子树的键值小于根的键值，右子树的键值大于根的键值。

​	二叉查找树可以任意地构造，但**若想二叉树的查询效率尽可能高，需要这棵二叉树是平衡的**，从而引出新的定义——平衡二叉树，或称AVL树。

###### AVL(平衡二叉树)

​	AVL在符合BST的条件下还满足以下特点

* 左右子树高度差只能为-1，0，1。
* 树中的每个左子树和右子树都是AVL树

###### 1. **B-Tree(多路平衡查找树)**

​	B-Tree是为磁盘等外存储设备设计的一种平衡查找树，**叶子节点都在同一层**。B-Tree相对于AVLTree缩减了节点个数，使每次磁盘I/O取到内存的数据都发挥了作用，从而提高了查询效率。

​	磁盘的读取是按**block块**操作的（内存是按**page**页面操作的，page是其磁盘管理的最小单位），因此B-树的节点大小一般设置为和磁盘块大小一致，这样一个B-树节点，就可以通过一次磁盘I/O把一个磁盘块的数据全部存储下来，所以当使用B-树存储索引的时候，**磁盘I/O的操作次数是最少的**（MySQL的读写效率，主要集中在磁盘I/O上）。

​	**定义：**B树中所有结点的孩子结点数的最大值称为B树的阶，通常用m表示。

​	**特点：**一棵m阶B树或者空树，或为满足如下特征的m叉树：

* 树中每个结点至多有m棵子树(即至多有m-1个关键字)
* 除了根节点和叶子节点外，其它每个节点至少有Ceil(m/2)个孩子
* 若根节点不是叶子节点，则至少有2个孩子
* 所有叶子节点都在同一层，且不包含其它关键字信息
* 每个非终端节点包含n个关键字信息（P0,P1,…Pn, k1,…kn）
* 关键字的个数n满足：ceil(m/2)-1 <= n <= m-1
* ki(i=1,…n)为关键字，且关键字**升序排序**
* Pi(i=1,…n)为指向**子树根节点的指针**。P(i-1)指向的子树的所有节点关键字均小于ki，但都大于k(i-1)

​	**其目的，也是尽可能的减少IO次数。**

![image-20220508160840029](https://s2.loli.net/2023/02/12/dGj1prJaxHNLmKX.png)

###### **2. B+-Tree**	

​	是应数据库所需而出现的一种B树的变形树，使其更适合实现外存储索引结构，InnoDB 存储引擎就是用 B+Tree 实现其索引结构。

![image-20220508160927440](https://s2.loli.net/2022/05/08/REm78Web5zHL4wj.png)

​		跟B树相比，B+树的**叶子结点是一条链表**，会将搜有的数据连接在一起，更加适合范围查询，并且每个非叶子节点只存放key值，这样一个节点就能存放更多的key值，**从而降低树的层高**，叶子结点会复现非叶子结点的key。

**B+-Tree的性质：**

* B+树要求把真实的数据放到叶子节点而不是内层节点(一旦放到内层节点，磁盘块的数据项会大幅度下降，导致树增高。当数据项等于1时将会退化成线性表。)
* **索引的最左匹配特性**：B+树是按照从左到右的顺序来建立搜索树的

**B-Tree 和 B+-Tree的比较**

​	相较于B-Tree, B+-Tree有以下几点

1. 非叶子节点只存储键值信息
2. 所有的叶子节点间有一个链指针
3. 数据记录都放在叶子节点中

> 为什么Mysql索引要用B+树不是B树？



##### MyISAM的索引结构

![image-20220509123750306](https://s2.loli.net/2022/05/09/WHnVeOEdbS12JTY.png)

​	**MyISAM引擎的索引文件和数据文件是分离的**，这样的索引叫做“**非聚簇索引**”。

​	索引(含叶子节点)存放在单独的.myi文件中，其中叶子节点存放的是数据的物理地址(通过地址偏移量的访问实现随机访问，速度很快)。

* 主索引为主键索引，键值不能重复；辅助索引为普通索引，键值可重复。
* 查找数据的流程：
  * 从索引文件中找到索引节点，拿到数据的文件指针
  * 再通过文件指针在数据文件中定位具体的数据

##### **InnoDB主键索引与辅助索引的结构**

**主键索引**

![image-20220509122731052](https://s2.loli.net/2022/05/09/KkoRTfMwh3Zenjc.png)

​	**InnoDB引擎索引结构的叶子节点的数据域，存放的就是实际的数据记录**（对于主索引，此处会存放表中所有的数据记录；对于辅助索引此处会引用主键，检索的时候通过主键到主键索引中找到对应数据行。因此，**InnoDB的数据文件本身就是主键索引文件**，这样的索引被称为"“**聚簇索引**”，因为无法把相同的数据行存放在不同的地方，所以一个表只能有**一个**聚簇索引。

​	**InnoDB没有指定主键时**，系统会检查是否有唯一字段，有则默认设置其为主键，否则将自动创建一个**6Byte的自增型主键**。

**辅助索引**

![image-20220509123036199](https://s2.loli.net/2022/05/09/FY8jl51TxgDE3sb.png)

​	它的索引结构跟主键索引的结构有很大差别，在最底层的叶子结点有两行数据，第一行的字符串是辅助索引，按照ASCII码进行排序，第二行的整数是主键的值。

​	辅助索引的步骤如下：

1. 在辅助索引上检索name，到达其叶子节点获取对应的主键
2. 使用主键在主索引上再进行对应的检索操作

​	这一过程也被称作**回表查询**。

**InnoDB索引结构的特点**

* 数据文件本身就是索引文件
* 表数据文件本身为按B+-Tree组织的一个索引结构文件
* 聚集索引中叶节点包含了完整的数据记录
* InnoDB 表**必须要有主键**，并且推荐使用整型自增主键

##### 3. **Hash索引**

![image-20220510122241351](https://s2.loli.net/2022/05/10/jBGAxLeNcnW2ltV.png)

​	**定义：**使用哈希算法将**数据库字段**数据转换成定长的Hash值，与这条数据的行指针一并存入Hash表的对应位置。 由于索引只存储哈希码及行指针，所以索引的数据结构非常的紧凑，这也让哈希索引**查找速度非常快**。

​	索引比起B和B+索引，是不同的思路，因为它可以完全不用从根去找数据，而树是必须从根去索引到值的。所以，**Hash索引的IO次数就更少了，理论上有潜力比B+树的效率更高。**

​	哈希冲突常用链地址法，即将冲突数据放在链表中。

* jdk1.8之前hashmap
* jdk1.8之后引入了红黑树

###### 自定义哈希索引

​	InnoDB有`自适应哈希索引`的功能。

​	某些索引值被使用的非常频繁的时候，它会在内存中基于B+Tree的基础上再创建一个哈希索引，使其不必要在从根节点就行查找。这是完全自动的内部行为，用户无法配置或更改。

​	使用场景：为超长的键创建哈希索引

###### **Hash索引的缺点**

1. 无法用于排序和分组(因为数据经过哈希算法后就是随机非连续的)
2. 仅仅能满足 “=”查询和“IN”查询，不能自定义一个范围去查询。只支持精确查找，无法用于顺序查找和范围查找（**对数据库来说是重大缺陷**）
2. 不能利用部分索引键查询
2. 不能避免表扫描(由于无序)
2. 遇到大量Hash值相等的情况后，性能并不一定就会比B+Tree高

> **为什么不采用哈希索引？**



##### 4. full-text全文索引

​	全文索引同样使用**B-Tree**存放索引数据。

​	MyISAM引擎支持全文索引，用于查找文本中的关键词，替代了效率较低的LIKE模糊匹配操作，而且可以通过多字段组合的全文索引一次性全模糊匹配多个字段。

​	InnoDB从MYSQL5.6版本提供对全文索引的支持。



##### 5. R-Tree空间索引

​	空间索引是MyISAM的一种特殊索引类型，主要用于地理空间数据类型。和B+Tree索引不同，这类索引无需前缀查询。空间索引从所有维度索引数据。



##### 哪些情况要创建索引

1. 主键自动建立唯一索引
2. 频繁作为**查询条件**的字段
3. 查询中与其他表关联的字段，**外键关系**建立索引
4. 单键/组合索引的选择问题，高并发下倾向创建组合索引
5. 查询中排序的字段，**排序字段**通过索引访问可以极大提高排序速度
6. 查询中**统计或分组**的字段

##### 哪些情况不创建索引

1. 表记录太少
2. 经常增删改的表
3. 数据重复且分布均匀的表字段(一般只会为最经常查询和排序的数据建立索引，若某个数据类包含太多重复数据，建立索引则没有太大意义)
4. where条件里用不到的字段不用创建索引



#### MySQL高效索引

##### 覆盖索引(covering index)

​	如果一个索引包含所有需要查询的字段的值(**查询列要被所建的索引覆盖**)称之为覆盖索引，可以直接根据索引查询数据，**无需回表**。



#### **MySQL事务**

​	事务是由一组SQL语句组成的逻辑处理单元，是一个操作序列，也是不可分割的工作单位，以BEGIN TRANSACTION开始，以ROLLBACK/COMMIT结束。

​	MySQL事务主要用于处理操作量大，复杂度高的数据。

##### **ACID**

* Atomicity(原子性)：整个事务的所有操作要么全部完成，要么就不执行，不会停留在中间状态。若事务在执行过程中发生错误，会被**回滚**(rollback)到事务之前的状态。
* Consistency(一致性)：在事务开始之前和事务结束以后，**数据库的完整性约束**没有被破坏。**在一致性状态下，所有事务对一个数据的读取结果都是相同的**。
  * 数据的完整性：所有相关的数据规则都必须应用于事务的修改
  * 数据结构的正确性：事务结束时所有的内部数据结构(例如B树索引或双向链表)

* Isolation(隔离性)：一个事务的执行不能被其他事务干扰。即一个事务内部的操作及使用的数据对其它并发事务是隔离的，并发执行的各个事务之间不能互相干扰。
* Durability(持久性)：在事务完成以后，该事务对数据库所作的更改将持久地保存在数据库中且不会被回滚。即使系统出现故障也能够保持。

##### **并发事务带来的一致性问题**

* 更新丢失(lost update)：两个事务在不知道彼此存在的情况下基于同一行的最初值进行更新时就会发生丢失更新问题。

* 脏读(dirty reads)：事务A读取了事务B的更新的数据后，B又进行了回滚操作，则A读到的数据发生了脏读

* 不可重复读(Non-repeatable reads)：事务A多次读取同一数据时，事务B在其中对数据进行了更新并提交，导致A多次读取同一数据的结果不一致。

* 幻读(phantom reads)：一个事务按相同的查询条件重新读取之前检索过的数据，却发现有其他事务插入的满足了其查询条件的新数据，这种现象称为幻读。

  与不可重复读类似，例如事务A读取几行数据后，另一个并发事务B**插入**了新的数据，则在随后的查询中事务A就会发现多出来的新纪录，仿佛产生了幻觉一样。

##### **不可重复读和幻读的区别**

1. 不可重复读的重点在于**修改**(update)：两次读的数据不一样，中间有其他事务提交了修改。
2. 幻读的重点在于**新增或删除**(insert, delete)：两次读的记录不一样，中间有其他事务提交了插入/删除。

​	因此，解决不可重复读的问题只需锁住满足条件的行，解决幻读需要锁表。

##### **事务隔离级别**

​	“幻读”、“重复读”、“脏读”本质上都是数据库读一致性问题，必须由数据库提供一定的事务隔离机制来解决。数据库实现事务隔离的方式，基本可分为以下两种：

* 在读取数据前对其加锁，阻止其他事务对数据进行修改。

* 另一种不用加任何锁，而是通过一定机制生成一个数据请求时间点的一致性数据快照(snapshot)，并用这个快照来提供一定级别(语句级或事务级)的一致性读取。

  因此，从用户角度看来就是数据库可以提供同一数据的多个版本，该种技术被叫做数据多版本并发控制(MVCC或MCC)，也经常称为多版本数据库。

###### **4种事务隔离级别**

1. 未提交读(read uncommitted)：在一个事务提交之前，它的执行结果对其他事务也是可见的。会导致脏读、不可重复读、幻读。
2. 提交读(read commited)：一个事务只能看见已经提交的事务作出的改变。可以**避免脏读**。
3. 可重复读(repeatable read, MySQL InnoDB 存储引擎的默认隔离级别)：可以确保同一事务在多次读取相同的数据时得到相同的结果。可避免不可重复读。
4. 可串行化(serializable)：最高的隔离级别，完全服从ACID的隔离级别。强制事务串行化执行，会在读取的**每一行数据都加上锁。**由此，解决了事务间冲突的以及幻读现象，但可能导致**超时和锁竞争**，实际很少使用。

​	由此可以看出隔离级别越低，则事务请求的锁越少，所以大部分数据库系统的隔离级别都是**READ-COMMITTED(读已提交):**，但是InnoDB 存储引擎默认使用 **REPEATABLE-READ（可重读）**且并不会有很大的性能损失。

​	查看当前数据库的事务隔离级别：

​	`show variables like 'tx_isolation'`

<img src="https://s2.loli.net/2022/05/12/AsjJPe3x18YGzkR.png" alt="image-20220512111329831" style="zoom:80%;" />



##### MVCC

​	多版本并发控制（Multi-Version Concurrency Control, MVCC）。

​	MVCC在每行记录后面都保存有两个隐藏的列，用来存储**创建版本号**和**删除版本号**。

* 创建版本号：创建一个数据行时的事务版本号。
  * **事务版本号**：事务开始时的系统版本号；
  * 系统版本号：每开始一个新的事务，系统版本号就会自动递增；
* 删除版本号：删除操作时的事务版本号
* 操作
  * 插入操作时，记录创建版本号；
  * 删除操作时，记录删除版本号；
  * 更新操作时，先记录删除版本号，再新增一行记录创建版本号；
  * 查询操作时，要符合以下条件才能被查询出来：
    * 删除版本号未定义或大于当前事务版本号（即删除操作是在当前事务启动之后做的）
    * 创建版本号小于或等于当前事务版本号（即创建操作是事务完成或者在事务启动之前完成）

###### 版本号的作用

1. 通过版本号减少了锁的争用，**提高了系统性能**；
1. 可以实现**提交读**和**可重复读**两种隔离级别，未提交读无需使用MVCC

###### 快照读与当前读

​	使用 MVCC 读取的是快照中的数据，这样可以减少加锁所带来的开销：

> select * from table ...;

​	当前读读取的是最新的数据，需要加锁。以下第一个语句需要加 S 锁，其它都需要加 X 锁：

> select * from table where ? lock in share mode;
> select * from table where ? for update;
> insert;
> update;
> delete;

#### MySQL锁机制

​	为了保证在多用户环境下数据库的一致性和完整性，需要加锁。

##### 从数据操作类型分类

###### 	读锁(共享锁)：

​	对于同一份数据多个读操作可以同时进行，不会互相影响。

###### 	写锁(排他锁)：：

​	当前写操作没有完成前，它会阻断其他写锁和读锁。

##### 从数据操作的粒度分类(Lock granularity)

​	为了尽可能提高数据库的并发度，每次锁定的数据范围越小越好，理论上每次只锁定当前操作的数据的方案会得到最大的并发度，但是管理锁是很耗资源的事情（涉及获取，检查，释放锁等动作），因此数据库系统需要在高并发响应和系统性能两方面进行平衡，这样就产生了“锁粒度（Lock granularity）”的概念。

###### 	表级锁(MyISAM 和 MEMORY)：

1. 开销小，加锁快；
1. 不会出现死锁；
1. 锁定粒度大，发生锁冲突的概率最高，并发度最低

因此，表级锁更适合于**以查询为主**，只有少量按索引条件更新数据的应用，如Web应用。

###### 	**行级锁**(InnoDB)

​	可以通过检查`InonoDB_row_lock`状态变量来分析系统上的行锁争夺情况。

<img src="https://s2.loli.net/2022/07/05/2cEOG8Wzg5h6S1i.png" alt="image-20220705214647867" style="zoom:67%;" />

1. 开销大，加锁慢；
1. 会出现死锁；
1. 锁定粒度最小，发生锁冲突的概率最低，并发度也最高（**InnoDB** 存储引擎既支持行级锁也支持表级锁，但默认情况下是采用行级锁）；

因此，行级锁更适合于有大量按索引条件并发更新少量不同数据，同时又有并发查询的应用，如一些在线事务处理(OLTP)系统。

###### 	※页面锁(BDB引擎，了解)

​	页面锁一次锁定相邻的一组记录。BDB支持页级锁。

​	开销和加锁时间界于表锁和行锁之间；会出现死锁；锁定粒度界于表锁和行锁之间，并发度一般。

<img src="https://s2.loli.net/2022/06/28/UWxIuFHy49PeBl6.png" alt="image-20220628232820810" style="zoom:67%;" />

##### MyISAM表锁

<img src="https://s2.loli.net/2022/07/05/Ala37JV4IcXiUjF.png" alt="image-20220705192118350" style="zoom: 50%;" />

###### 特点

* MyISAM在执行查询语句(SELECT)前会自动给涉及的表加读锁，执行更新操作前(UPDATE、DELETE、INSERT等)前会自动给相关表加写锁，因此一般不需要用户使用`LOCK TABLE`命令给MyISAM表显示加锁。
* MyISAM总是一次性获得SQL语句所需要的全部锁，这也是MyISAM表中不会出现死锁的原因。

###### 表共享读锁(table read lock)

​	不阻塞其他用户对同一表的读请求，阻塞其写请求。

###### 表独占写锁(table write lock)

​	阻塞其他用户对同一表的读、写操作。

​	MyISAM 表的读操作与写操作之间，以及写操作之间是串行的。当一个线程获得对一个表的写锁后， 只有持有锁的线程可以对表进行更新操作。 其他线程的读、 写操作都会等待，直到锁被释放为止。

​	默认情况下，写锁比读锁具有更高的优先级：当一个锁释放时，这个锁会优先给写锁队列中等候的获取锁请求，然后再给读锁队列中等候的获取锁请求。

###### 并发插入(concurrent inserts)

​	MyISAM表的读写操作总体而言是串行的，但在一定条件下，MyISAM表也支持并发的查询和插入操作。

<img src="https://s2.loli.net/2022/07/05/scTO517vIjSCwKR.png" alt="image-20220705210448019" style="zoom:67%;" />

​	因此，可以利用MyISAM存储引擎的并发插入特性来解决应用中对同一表查询和插入的锁争用。(将concurrent_insert设置为2等...)

##### InnoDB行锁

​	InnoDB与MyISAM最大的两点不同在于：

1. InnoDB支持事务
2. InnoDB支持行级锁和表级锁，默认行锁。

​	InnoDB实现了以下两种类型的行锁：

###### 	共享锁(S):

​	又叫读锁， 当用户要进行数据的读取时，对数据加上共享锁。共享锁可以同时加上多个。

​	作用：允许一个事务去读一行，阻止其他事务获得相同数据集的排他锁。

###### 	排他锁(X):

​	又叫做写锁。 当用户要进行数据的写入时，对数据加上排他锁。排他锁只可以加**一个**。

​	作用：允许获得排他锁的事务更新数据，阻止其他事务取得相同数据集的共享读锁和排他写锁。

###### 意向锁

​	为了允许行锁和表锁共存，实现多粒度锁机制，InnoDB 还有两种内部使用的意向锁（Intention Locks），这两种意向锁都是**表锁**：

* 意向共享锁（IS）：事务打算给数据行加行共享锁，事务在给一个数据行加共享锁前必须先取得该表的 IS 锁。
* 意向排他锁（IX）：事务打算给数据行加行排他锁，事务在给一个数据行加排他锁前必须先取得该表的 IX 锁。

<img src="https://s2.loli.net/2022/07/05/irmpT7jXDshCSA9.png" alt="image-20220705215258025" style="zoom:67%;" />

###### 	加锁规则

1. 意向锁是InnoDB自动加的，不需要用户干预。对于更新操作，InnoDB会自动给相关数据集加排他锁(X)
2. 对于普通SELECT语句，InnoDB不回家任何锁

<img src="https://s2.loli.net/2022/07/05/tVop9iewugG7jqC.png" alt="image-20220705215700988" style="zoom:67%;" />

###### InnoDB引擎的行锁是怎么实现的？

​	**InnoDB是基于索引来完成行锁。**

> 例: select * from tab_with_index where id = 1 for update;
>
> for update 可以根据条件来完成行锁锁定，并且 id 是有索引键的列，如果 id 不是索引键那么InnoDB将完成表锁，并发将无从谈起。
>
> 注：锁住指定的一行，如果进行更新操作就是 ... FOR UPDATE，删除操作就是 ... FOR DELETE 

**索引失效会导致行锁变表锁**。当索引失效后即使多个session操作的不是同一条记录，若操作未提交，其他session也会进入阻塞。比如 varchar 查询不写单引号的情况。

##### 加锁机制

​	乐观锁和悲观锁是两种并发控制的思想，可用于解决**丢失更新**问题。

> SELECT ... LOCK In SHARE MODE;
> SELECT ... FOR UPDATE;

###### 乐观锁

​	在更新数据时再判断有没有其他事务更新过该数据，若已被更新过则失败重试，适用于**读多写少**的场景。乐观锁的实现方式有以下：

* 增加一个版本号或时间戳字段，每次更新数据时同步更新该字段
* 先读取想要更新的字段或者所有字段，更新时判断只有字段没有发生过变化的才进行更新	

###### 悲观锁

​	认为数据随时会被修改，因此为了防止被其他事务读取或修改数据，**每次**读取数据前都会加锁，适用于**数据更新比较频繁**的场景。

​	另外与乐观锁相对应的，**悲观锁是由数据库默认实现了的，我们可以直接调用数据库的相关语句。**

##### 锁模式(InnoDB有3种行锁的算法)

​	InnoDB行锁通过给索引上的**索引项**加锁实现，如果没有索引，InonoDB将通过隐藏的聚簇索引来对记录加锁。

​	InnoDB行锁有以下3种形式，而它们的实现特点意味着如果不通过索引条件检索数据，那么InnoDB将对表中的所有记录加锁，效果等同于**表锁**。

###### 记录锁(Record Locks)

​	 单个行记录上的锁。对索引项加锁，锁定符合条件的行。其他事务不能修改和删除加锁项。

###### 间隙锁（Gap Locks）

​	当我们使用**范围条件**而不是相等条件检索数据，并请求共享或排他锁时，InnoDB会给**符合条件的已有数据**记录的索引项加锁。

​	**间隙**：对于**键值在条件范围内但并不存在的记录**，叫做“间隙”。

​	InnoDB 也会对这个“间隙”加锁，这种锁机制就是所谓的间隙锁。有以下特点：

* 加间隙锁后其他事务不能在锁范围内插入数据

  * 对索引项之间的“间隙”加锁，锁定记录的范围（对第一条记录前的间隙或最后一条记录后的间隙加锁），不包含索引项本身。其他事务不能在锁范围内插入数据，这样就防止了别的事务新增幻影行。

* 间隙锁基于非唯一索引，它锁定**一段范围内**的索引记录

  * 间隙锁基于Next-Key Locking 算法，请务必牢记：**使用间隙锁锁住的是一个区间，而不仅仅是这个区间中的每一条数据**。

    ```sql
    SELECT * FROMtableWHEREid BETWEN 1 AND 10 FORUPDATE; 
    / ** 即所有在（1，10）区间内的记录行都会被锁住，所有id 为 2、3、4、5、6、7、8、9 的数据行的插入会被阻塞，但是 1 和 10 两条记录行并不会被锁住。**/
    ```

* 间隙锁的目的是为了防止同一事务的两次当前读，出现**幻读**的情况。

###### 间隙锁的缺点

​	InnoDB在使用范围条件检索并锁定记录时，间隙锁机制会阻塞符合条件范围内的键值的并发插入，这往往会造成严重的锁等待。

​	因此，在实际开发中，尤其是并发插入比较多的应用我们要尽量优化业务逻辑，尽量使用相等条件来访问更新数据，避免使用范围条件。

​	**注：**InnoDb除了通过范围条件加锁时会使用Next-key锁外，如果使用相等的条件请求给一个不存在的记录加锁，也会使用Next-Key锁！

###### **临键锁(Next-key Locks)**：

**临键锁**是记录锁与间隙锁的组合。它的封锁范围既包含**索引记录**，又包含**索引区间**。

​	临键锁的主要目的也是为了避免**幻读**(Phantom Read)。如果把事务的隔离级别降级为RC(提交读read commited)，临键锁也会失效。

* Next-Key 可以理解为一种特殊的**间隙锁**，也可以理解为一种特殊的**算法**。通过**临建锁**可以解决幻读的问题。 
  * 每个数据行上的非唯一索引列上都会存在一把临键锁，当某个事务持有该数据行的临键锁时，会锁住一段左开右闭区间的数据。
  * 需要强调的一点是，InnoDB 中行级锁是基于索引实现的，临键锁只与**非唯一索引列**有关，在唯一索引列（包括主键列）上不存在临键锁。
* 对于行的查询，都是采用该方法，主要目的是解决**幻读**的问题。

###### 什么时候用表锁？

​	在绝大部分情况下，对于InnoDB表都应该使用行级锁，因为事务和行锁往往是我们选择InnoDB的理由。但在个别特殊事务中，可以考虑使用表级锁：

* **事务需要更新大部分或全部数据，且表比较大**：如果此时使用默认的行锁不仅该事务的执行效率低，还可能造成其他事务长时间锁等待和锁冲突。因此，在这种情况下可以考虑使用表锁来提高该事务的执行速度。
* **事务涉及多个表，比较复杂，很可能引起死锁，造成大量事务回滚**：该情况可以考虑一次性锁定事务涉及的表，从而避免死锁，减少数据库因事务回滚带来的开销。

<img src="https://s2.loli.net/2022/07/07/U4NsHLZWTtwKe8a.png" alt="image-20220707171413722" style="zoom: 50%;" />

##### 死锁(p365)

<img src="https://s2.loli.net/2022/07/05/F3VOid7eqZacKms.png" alt="image-20220705233417707" style="zoom:67%;" />

###### 特点

* 死锁是指两个或多个事务在同一资源上相互占用，并请求锁定对方占用的资源，从而导致都陷入等待的恶性循环。
* 当事务试图以不同的顺序锁定资源时，就可能产生死锁。多个事务同时锁定同一个资源时也可能会产生死锁。
* 锁的行为和顺序和**存储引擎**相关。以同样的顺序执行语句，有些存储引擎会产生死锁有些不会。
  * 因此死锁有双重原因：数据冲突；存储引擎的实现方式。

###### 检测死锁

​	数据库系统实现了各种死锁检测和死锁超时的机制。

​	**InnoDB**存储引擎能检测到死锁的循环依赖并立即返回一个错误。

###### 死锁恢复

​	死锁发生以后，只有部分或完全回滚其中一个事务，才能打破死锁。

​	**InnoDB处理死锁的方法：**将持有最少行级排他锁的事务进行回滚。所以事务型应用程序在设计时必须考虑如何处理死锁，多数情况下只需要重新执行因死锁回滚的事务即可。

###### 外部锁的死锁检测

​	发生死锁后，InnoDB 一般都能自动检测到，并使一个事务释放锁并回退，另一个事务获得锁，继续完成事务。

​	但在涉及**外部锁，或涉及表锁**的情况下，InnoDB 并不能完全自动检测到死锁， 这需要通过**设置锁等待超时参数** innodb_lock_wait_timeout 来解决。

###### 死锁的性能影响

​	死锁一般不会产生严重错误，例如InnoDB会自动检测死锁状况并回滚其中一个受影响的事务，但它会影响

性能。

> ​	例如：在高并发系统上，当许多线程等待同一个锁时，死锁检测可能导致速度变慢。 有时当发生死锁时，禁用死锁检测（使用innodb_deadlock_detect配置选项）可能会更有效，这时可以依赖innodb_lock_wait_timeout设置进行事务回滚。

###### MyISAM避免死锁

​	在自动加锁的情况下，MyISAM 总是一次获得 SQL 语句所需要的全部锁，所以 MyISAM 表**不会出现死锁**。

###### InnoDB避免死锁

* 为了在单个InnoDB表上执行多个并发写入操作时避免死锁，可以在事务开始时通过为预期要修改的每个元祖（行）使用**SELECT ... FOR UPDATE**语句来获取必要的锁，即使这些行的更改语句是在之后才执行的。
* 在事务中，**如果要更新记录，应该直接申请足够级别的锁，即排他锁**，而不应先申请共享锁、更新时再申请排他锁，因为这时候当用户再申请排他锁时，其他事务可能又已经获得了相同记录的共享锁，从而造成锁冲突，甚至死锁。
* 如果事务需要修改或锁定多个表，则应在每个事务中**以相同的顺序使用加锁语句**。 在应用中，如果不同的程序会并发存取多个表，应尽量约定以相同的顺序来访问表，这样可以大大降低产生死锁的机会。
* 通过SELECT ... LOCK IN SHARE MODE获取行的读锁后，如果当前事务再需要对该记录进行更新操作，则很有可能造成死锁。
* 改变事务隔离级别

​	如果出现死锁，可以用 `show engine innodb status`; 命令来确定最后一个死锁产生的原因。返回结果中包括死锁相关事务的详细信息：

* 如引发死锁的 SQL 语句，
* 事务已经获得的锁，正在等待什么锁，以及被回滚的事务等。

​	据此可以分析死锁产生的原因和改进措施。



#### **如何优化数据库**？

##### SQL语句的优化

###### 1. 分析慢查询日志

​	记录了在MySQL中响应时间超过阀值long_query_time的SQL语句，通过日志去找出IO大的SQL以及发现未命中索引的SQL。

###### 2. 查询当前MySQL执行的线程

​	慢查询日志在查询结束后才记录所以在应用反映执行效率出现问题时，慢查询日志并不能定位问题，此时可以使用`show processlist`命令查看MySQL执行的线程的状态，是否锁表等信息，可以实时地查看SQL的执行情况同时也可以对一些锁表操作进行优化。

###### 3. 查询性能的优化

###### **用Explain进行分析**

​	用Explain可以模拟优化器执行SQL查询语句，用来分析**SELECT**查询语句或表结构的性能瓶颈，开发人员可以通过分析Explain结果来优化查询语句。

###### Explain能做什么？

* 得到表的读取顺序
* 数据读取操作的操作类型
* 哪些索引可以使用
* **哪些索引被实际使用**
* 表之间的引用
* 每张表有多少行被优化器查询(被扫描的行数)

​	其中比较重要的字段有：

* select_type：查询类型。常见取值有简单查询(SIMPLE，即不使用表连接或者子查询)、主查询(PRIMARY,即外层的查询)联合查询(UNION)和子查询(SUBQUERY)等
* key：使用的索引
* rows：扫描的行数

###### 通过show profile分析SQL

​	有时仅仅通过explain分析执行计划并不能很快地定位SQL的问题，这时就可以选择profile联合分析。

​	**作用：**show profile能在做SQL优化时帮助我们了解时间具体耗费到哪里去了。例如：InnoDB的表执行count(*) 时经历了 `Sending data` 状态(时间主要消耗在CPU上)而相同数据下的MyISAM表由于有元数据，因此在`executing`后就结束查询，不需要访问数据。

###### 通过trace分析优化器选择执行计划

​	<img src="https://s2.loli.net/2022/05/18/BXrEyLkIxuZNDSH.png" alt="image-20220518142737061" style="zoom:80%;" />

​	执行步骤：

```
1. 打开trace
1. 执行SQL语句
1. 检查`information_schema.optimizer_trace`结果，得到跟踪文件
```

<img src="https://s2.loli.net/2022/05/18/pOn5YTkV3ioxqQC.png" alt="image-20220518142928090" style="zoom: 50%;" />



###### **优化数据访问**

1. 减少请求的数据量：

* 只返回必要的列：最好不用SELECT*语句
* 只返回必要的行：使用LIMIT语句来限制返回的数据
* 缓存被重复查询的数据：使用缓存可以避免数据库频繁查询重复的数据，该情况下缓存给查询性能带来了显著的提升

2. 减少服务器端扫描的行数：

​	最有效的方式是使用索引来覆盖查询。因此，应尽量避免在 where 子句中使用`!=`、`<`、`>`操作符或对字段进行null值判断，否则将引擎放弃使用索引而进行全表扫描；

**注：**MyISAM引擎有元数据的缓存(例如行数，即COUNT(*)值 )，因此对其来说COUNT(*)不会消耗太多资源而InnoDB则没有这种元数据缓存,执行COUNT(*)会比较慢。

###### 4. 重构查询方式

一、切分大查询

​	一个大查询如果一次性执行的话，可能锁住很多数据，占满占满整个事务日志、耗尽系统资源、阻塞很多较小但重要的查询。

二、分解大连接查询

​	将一个大连接查询分解成对每一个表进行一次单表查询，然后在应用程序中进行关联，这样做的好处如下：

1. 缓存更高效：若一个表发生变化，对于连接查询而言整个查询缓存就会无法使用，而分解后的多个查询即使其中一个表发生变化，其他表的查询缓存仍然可以使用。
2. 减少冗余记录的查询：分解成多个单表查询后，这些单表查询的缓存结果更可能被其他查询使用到。
3. 减少锁竞争。
4. 高性能和可伸缩：在应用层进行连接可以更容易地对数据库进行拆分。
5. 查询效率也可能有所提升：例如使用IN()代替连接查询可以让MySQL按字段的顺序进行查询，使其比随机的连接查询更高效。

##### 索引的优化

​	注意引起索引失效的情况以及在选择合适的索引。

###### **MySQL索引在哪些情况下会失效**

<img src="https://s2.loli.net/2022/05/18/YvoiNBL9THfJsI2.png" alt="image-20220518163258320" style="zoom: 67%;" />

* 在索引的字段上做任何操作(函数、计算等)。

  `EXPLAIN SELECT * FROM user WHERE age = age+1`

* 索引范围条件右边的索引列会失效。

  `EXPLAIN SELECT * FROM user WHERE name = 'juejin' and age > 18`

* 使用不等于操作符(!=、<>)使索引失效。

  `EXPLAIN SELECT * FROM user WHERE age != 18`

* 使用like时进行模糊查询时其中“%”在查询数据的左侧时，使索引失效例如：“%小明”，“%掘金”，在查询条件的右侧可以使用索引例如：“掘金%”、“小明%”。

  `EXPLAIN SELECT * FROM user WHERE name like "%小"`

* 在查询语句中使用or连接之后，会使索引失效。哪怕是左右两个都是有索引的字段也会使索引失效

  `EXPLAIN SELECT * FROM user WHERE age=18  or name="小明"`

*  在查询中存在类型转换，会导致索引失效。

  `EXPLAIN SELECT * FROM user WHERE userId="18"`

*  违反最左前缀法则。在查询语句中如果索引有多列，要遵守查询从索引的最左前列开始并且不跳过索引中的列。其中左侧的列可以使用索引，未加索引列之后的所有列索引失效。

* 在使用 group by 和order by的时候，会影响索引的查询效率。

###### 索引的分类

* B-TREE：最常见的。大部分引擎都支持。
* HASH：只有MEMORY引擎支持，使用场景简单。
* R-TREE：空间索引，是MyISAM的一个特殊索引，通常使用较少。
* Full-text：也是MyISAM的一个特殊索引类型，主要用于全文索引，InnoDB从MySQL5.6开始支持。

###### **前缀索引**

​	MySQL不支持函数索引但可以对列的前面某一部分进行索引，例如标题字段，可以只取标题的前10个字符进行索引，大大地缩小了索引文件的大小，但在**排序Order By和分组Group By操作时就无法使用前缀索引**了。

<img src="https://s2.loli.net/2022/05/16/U3bv1zBDYrWSy7j.png" alt="image-20220516162223521" style="zoom: 50%;" />

##### 数据库表结构的优化

* 设计表时遵循**三大范式**
* 选择合适的**数据类型**：尽可能避免存储NULL字段，使用简单的数据类型(int, varchar/text)
* 表的**水平切分**(sharding)：将同一个表中的记录拆分到多个结构相同的表中当一个表的数据不断增多时Sharding可以将数据分布到集群的不同节点上，从而缓解了单个数据库的压力。
  * Sharding策略：
    * 哈希取模：hash(key) % N
    * 范围：可以是ID范围或时间范围
    * 映射表：使用单独的一个数据库来存储映射关系
  * Sharding存在的问题：
    * 事务问题：使用分布式事务来解决，例如XA接口。
    * 连接：可将原来的连接分解成多个单表查询然后在用户程序中连接
    * ID唯一性：
      1. 使用全局唯一ID(GUID)
      2. 为每个分片指定一个ID范围
      3. 分布式ID生成器(Twitter 的 Snowflake 算法)

<img src="https://s2.loli.net/2022/05/13/eNAH5XwirlSaC7m.png" alt="image-20220513121248439" style="zoom:80%;" />



* 表的垂直切分：即将一张表按列切分为多个表(通常按照**列的关系密集程度**进行切分)。垂直切分可以使业务更加清晰，系统之间整合或扩展容易，数据更易于维护。
  * 将不常用的字段单独放同一个表中
  * 把大字段独立放入一个表中
  * 把经常使用的字段(关系密切的)放在同一章表中

<img src="https://s2.loli.net/2022/05/13/i3sBHbj8fdKLWtD.png" alt="image-20220513121340112" style="zoom: 50%;" />

##### 系统配置的优化

* 操作系统：增加**TCP**支持的队列数
* MySQL配置文件优化：**缓存池**大小和个数设置

##### 硬件的优化

* 磁盘性能：使用固态硬盘
* CPU：多核且高频
* 内存：增大内存

#### 主从复制

<img src="https://s2.loli.net/2023/02/12/VEWzxFCGhyqHvOT.png" alt="image-20220820155038377" style="zoom:67%;" />

- 主库接受增删改操作，把增删改操作的**binlog**日志写入本地文件
- 从库上有一个**IO线程**，这个IO线程会负责跟主库建立一个TCP连接，接着请求主库传输binlog日志给自己，这个时候主库上有一个**IO dump**线程，就会负责通过这个TCP连接把binlog日志传输给从库的 IO线程
- 从库的IO线程会把读取到的binlog日志数据写入到自己本地的**relay**中继日志文件中去，然后从库上另外 有一个**SQL线程**会读取relay日志里的内容，进行日志重做，把所有在主库执行过的增删改操作，在从库 上做一遍，达到一个还原数据的过程

> 问题与思考：
>
> （1）MySQL 的主从复制原理。 
>
> （2）MySQL 的三种复制方式。 
>
> （3）MySQL 的异步复制和半同步复制。
>
> （4）如何提高复制的性能。

##### 什么是主从复制？实现原理是什么？

​	**主从复制（Replication）是指数据可以从一个MySQL数据库主服务器复制到一个或多个从服务器，从服务器可以复制主服务器中的所有数据库或者特定的数据库，或者特定的表。默认采用异步模式**。

<img src="https://s2.loli.net/2023/02/12/bJ1KjnF4xwXRQEr.png" alt="image-20220820185023479" style="zoom:50%;" />

##### 实现原理：

- 主服务器 **binary log dump 线程**：将主服务器中的数据更改（增删改）日志写入 Binary log(二进制日志文件) 中；
- 从服务器 **I/O 线程**：负责从主服务器读取binary log，并写入本地的 Relay log；
- 从服务器 **SQL 线程**：负责读取 Relay log，解析出主服务器已经执行的数据更改，并在从服务器中重新执行（Replay），保证主从数据的一致性

##### 为什么要主从复制？

- **读写分离**：主服务器负责写，从服务器负责读
  - 缓解了锁的争用，即使主服务器中加了锁，依然可以进行读操作；
  - 从服务器可以使用 MyISAM，提升查询性能以及节约系统开销；
  - 增加冗余，提高可用性
- 数据实时备份，当系统中某个节点发生故障时，可以方便的故障切换
- 降低单个服务器磁盘I/O访问的频率，提高单个机器的**I/O性能**

##### MySQL 的三种复制方式

###### 一主多从复制架构

​	一主多从的架构能够解决大部分**请求压力**特别大的场景需求。

<img src="https://s2.loli.net/2023/02/12/Qyi3OqZU2mfa9El.png" alt="image-20220820185222081" style="zoom:50%;" />

###### 多级复制架构

<img src="https://s2.loli.net/2023/02/12/XDfp645aGklgjO8.png" alt="image-20220820185408508" style="zoom:50%;" />

**黑洞引擎**

​	**问题场景**：多级复制解决了一主多从场景下主库的I/O负载和网络压力，

​	而缺点是：由于MySQL的复制是异步复制，多级复制场景下主库的数据会经过多次复制才到达从库，导致期间的**延时**大于主从复制只经过一次复制的情况。

​	**解决方案**：

<img src="https://s2.loli.net/2023/02/12/qko6idy2YADmtO3.png" alt="image-20220820185851289" style="zoom:50%;" />

* BLACKHOLE是一个**空表**
* 写入黑洞引擎的数据不会谢晖到磁盘上
* **黑洞引擎的master库并不承担读写请求，仅仅负责将Binlog日志尽快传送给从库。**

###### 双主复制/Dual Master

<img src="https://s2.loli.net/2023/02/12/x83eO1Qt7lwBJFo.png" alt="image-20220820190126967" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/02/12/p1XIfhvEAL2UJjd.png" alt="image-20220820190255354" style="zoom:50%;" />

###### 双主复制和主从复制联合使用

​	**可以做到在DBA做维护的同时避免重建从库的额外工作**

<img src="https://s2.loli.net/2023/02/12/fRv7mO2jgANGlkU.png" alt="image-20220820191214485" style="zoom:50%;" />

##### 异步复制和半同步复制

###### 异步复制

![image-20220820184229313](https://s2.loli.net/2023/02/12/2YanCkLrAEKJRs5.png)

​	主库和从库之间存在一定的延迟。

* 主库执行完commit提交操作后在主库写入Binlog日志后即可成功返回客户端，无需等待将Binlog日志传给从库
  * 存在数据不一致的隐患：在主库写入事务并提交成功，而从库还未获取到主库的Binlog时主库发生了宕机，可能出现磁盘损坏、内存故障等故障造成主库的Binlog日志丢失，此时从库就可能损失了这个事务。

###### 半同步复制

<img src="https://s2.loli.net/2023/02/12/WyOrTEIjzbv29KN.png" alt="image-20220820184029382" style="zoom:67%;" />

​	为保证主库上的每一个Binlog事务都能够被可靠的复制到从库上，主库在每次事务成功提交时等待其中一个从库也受到Binlog事务并成功写入中继日志之后，主库才返回commit操作成功给客户端。

* 半同步复制保证了事务成功提交后至少存在两份日志记录，一份在主库的Binlog日志上，另一份在其中一个从库的中继日志relay log上，从而进一步地保证了数据的完整性。
* MySQL5.5的一个插件实现，主库和从库使用不同的插件。

##### 如何提高复制的性能

###### 拆分database复制

**1.一主多从架构**

![image-20220820191430262](https://s2.loli.net/2023/02/12/lEjYtPHA3O1mByZ.png)

**2.多级主从架构**

![image-20220820191704141](https://s2.loli.net/2023/02/12/AbHo9lFIVfRnmJU.png)

**总结：**

* 自由拆分从库可以方便地将热点数据分散开来，但也增加了维护难度
* 拆分主库使得从库上都没有完整的数据，若主库宕机则处理较为麻烦，需要提前做好异常处理的解决方案

###### 基于Schema的多线程复制

<img src="https://s2.loli.net/2023/02/12/6bmVfztD5CL4aMG.png" alt="image-20220820192104454" style="zoom:50%;" />

<img src="https://s2.loli.net/2023/02/12/8yjlDBAftcd5x7a.png" alt="image-20220820192021513" style="zoom:50%;" />
