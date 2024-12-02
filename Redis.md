Redis**<font style="background-color:yellow;">持久化</font>**策略：**AOF日志，RDB快照混合模式**

1. 执行命令后写日志（防止命令出错，不会阻塞当前命令），写回策略（总是，每秒，系统控制）；AOF重写机制，减小日志体积，重写期间，主线程执行命令后会将命令写入**重写缓冲区**，以及**写时复制**（恢复数据较慢）。
2. 记录某个时间点的实际数据（记录频率不好设置）；记录时，主线程可以读写，基于**写时复制**（子进程拷贝内存）。

**<font style="background-color:yellow;">过期删除</font>**策略：惰性删除+定期删除（简单易用，兼顾内存占用和CPU占用）

**<font style="background-color:yellow;">内存淘汰</font>**策略：①不提供写入服务，②淘汰有过期时间的key（随机，时间，lru，lfu），③所有数据中淘汰（随机，lru（额外存储上次访问时间，删除时随机选择几个，时间最久的删除），lfu（存储上次访问时间以及访问次数））



**<font style="background-color:yellow;">缓存</font>****更新**策略：CacheAside（应用程序自己实现缓存和数据库交互问题）：先操作数据库，再删除缓存，因为缓存操作速度很快，一般比数据库操作快非常多，不一致性问题发生概率低；Write/ReadThrough（应用数据只与缓存交互，不操作数据库，读时缓存中有直接返回，没有由缓存组件查询数据库更新缓存，写时写缓存，若缓存没有则直接写数据库）；WriteBack（只更新缓存，由其他线程异步写回缓存数据）。

延迟双删：删除缓存，更新数据库，再过一段时间再删除一次缓存。（延迟时间不好设置，也存在问题）

1. 缓存**<font style="background-color:yellow;">穿透</font>**：请求数据在缓存中和数据库中都不存在，所有请求都打到数据库

解决思路：①缓存空对象：简单实现，内存消耗大，②布隆过滤（使用hash加二进制位（bitmap）判断请求合法性）：在缓存之前就判断请求合法性，可能误判（主要是由于hash冲突）（可以在每个哈希位统计次数，即可实现删除键）

1. 缓存**<font style="background-color:yellow;">雪崩</font>**：大量key同时失效或redis宕机（高可用），导致大量请求同时请求数据库

解决思路：①TTL增加随机项，②redis集群，③多级缓存，④降级限流策略

1. 缓存**<font style="background-color:yellow;">击穿</font>**：部分热点key或重建较复杂的key同时失效（缓存雪崩的子集），导致大量请求同时请求数据库

解决思路：①互斥锁（基于SETNX实现），保证只有一个线程可以写入缓存时，其他线程失败重试；②逻辑过期，不设置TTL而是在数据内加入一个过期时间，供于读取缓存时判断。



**<font style="background-color:yellow;">数据类型</font>**：String（缓存对象，分布式锁，共享session）；List（消息队列）；Hash（缓存对象（更省内存））；Set（集合操作（共同好友等））；Zset（排行榜）；Bitmap（签到统计）：二进制位；Hyperloglog（UV统计）：提供不精确的去重计数。

**<font style="background-color:yellow;">String</font>**：简单动态字符串，二进制格式，保存了长度字段；

<font style="background-color:yellow;">List</font>：双向链表+压缩列表->quicklist，压缩列表紧凑的格式节省了空间，但其基于连续内存且易发生连锁更新现象。Quicklist即使用链表结构把压缩列表分段后串在一起。

<font style="background-color:yellow;">Hash</font>：压缩列表+哈希表->listpack+哈希表，listpack没有记录前一个节点的大小，避免了连锁更新的问题。

<font style="background-color:yellow;">Set</font>：整数集合+哈希表，哈希表基于数组加链表，rehash基于渐进式hash，保存两个hash表，达到阈值时切换。

<font style="background-color:yellow;">ZSet</font>：压缩列表+跳表->listpack+跳表，跳表在创建节点时采用随机的办法来增加层数，为什么不用红黑树，跳表更加<font style="color:#FF0000;">简单</font>，更适合做范围查询，内存开销更小。为什么不用B+树，跳表更简单，增删更方便，单节点占用内存更小。



Redis**<font style="background-color:yellow;">为什么快</font>**：①主要基于内存操作；②单线程模式避免线程竞争；③采用了I/O多路复用机制（一个线程同时监听多个，select：遍历查找发生读写的socket，epoll：构建红黑树+事件触发模式）处理大量的客户端Socket请求。



Redis是**<font style="background-color:yellow;">单线程</font>**吗？单线程指的是接受请求解析处理数据读写由一个主线程来完成，但它还有后台线程，包括了处理关闭文件、AOF刷盘、异步释放内存的后台线程。



**<font style="background-color:yellow;">哈希分槽</font>**：对2^16取模，得到槽号，每个redis实例有多个哈希槽。优势：扩缩容方便，期间仍可提供服务，只需移动部分的槽即可，且可以更灵活地根据实例的硬件情况分配槽。而一致性哈希若某个节点挂了可能导致后续节点雪崩。

**<font style="background-color:yellow;">主从</font>****复制**：集群通常读写分离，第一次同步时，从服务器删除数据，并进行RDB全量同步；后续主服务器进行写操作，将同步命令异步发给从服务器（可能造成数据不一致）（长连接）。增量同步：环形缓冲区，维护offset字段

**<font style="background-color:yellow;">哨兵</font>****模式**：主从节点故障自动转移，哨兵检测主节点异常->发现的哨兵投票希望成为leader->进行主从切换（选新主->从节点指向新主->通知客户主节点切换->旧主切换为从节点）。

集群**<font style="background-color:yellow;">脑裂</font>**：主节点临时异常，导致新主节点出现，此时原主节点故障期间的数据操作丢失，主节点检测到异常（与从节点连接消失或上一次同步时间过去较久）时，限制数据操作，给客户端返回错误信息。



**<font style="background-color:yellow;">大Key</font>**问题：操作大key耗时较久，客户端超时阻塞；使用del删除大Key，阻塞工作线程；持久化时创建子进程复制页表时间增加，主线程修改大key时拷贝物理内存耗时。删除方案：①分批次删除，②异步删除unlink。

**<font style="background-color:yellow;">热Key</font>**问题：

监控：解析AOF日志，或是通过其他脚本统计key的访问次数；

解决：①建立Redis集群，分摊压力；②数据预热，在高并发之前，提前将热点数据加载进缓存；③	限流和降级，保护好后台服务；④使用多级缓存，一级缓存使用本地内存，缓存最近频繁访问的数据，速度快，容量小，当从二级缓存或后端数据库获取数据时同步更新本地缓存；二级缓存使用分布式缓存系统如redis，缓存相对频繁访问的数据，速度快容量大，当从后端数据库获取数据时，同步更新二级缓存；当数据在后端数据库中发生变化时，异步更新二级缓存。



| | 事务 | Lua脚本 | 管道 | Mget\Mset |
| --- | --- | :--- | :--- | :--- |
| 网络请求次数 | 多次请求 | 单次请求 | 单次请求 | 单次请求 |
| 响应次数 | 单次响应 | | | |
| 执行原子性 | 保证，阻塞其他请求 | 保证，阻塞其他请求 | 不保证，其他请求仍可执行 | 原生命令，保证 |
| 错误后续是否执行 | 仍执行 | 不执行 | 仍执行 | 原生命令 |
| 错误是否回滚 | 不回滚 | 不回滚 | 不回滚 | 原生命令、回滚 |
| 获得前面结果 | 不可以 | 可以 | 不可以 | |


Redis<font style="background-color:yellow;">事务</font>，一个事务在执行时会阻塞其他请求，保证一定的原子性，但不支持事务回滚，且事务中不能获取前面的执行结果。

<font style="background-color:yellow;">Lua</font>脚本，脚本内部可以获取中间命令的执行结果，且可以保证原子性，但lua脚本也会阻塞其他，尽量不要执行过大开销的操作。

<font style="background-color:yellow;">管道</font>技术，只需建立一次通信，批量发送请求与接收结果，缓解网络延迟的影响，不保证原子性（只能保证单个客户端内的串行执行）。

<font style="background-color:yellow;">Mget</font>，mget只能进行一种操作，即get，而管道中可以执行更多样的命令。Mget一次性执行后返回结果，而管道则依次执行后返回结果。Mget为为原生命令，可以保证原子性，管道不保证原子性。

面对hash分槽时无法保证原子性。



<font style="background-color:yellow;">分布式锁</font>，获取锁：set nx px；解锁：先判断是否为锁持有者，再删除锁（可重入锁将次数-1）





































