# 1、小组成员

网安一班 林若妍 201900460027



# 2、基本信息

项目名称：使用rho寻找SM3的碰撞

简介：随机化初始值并不断使用SM3求哈希值，使用哈希表保存每次哈希的结果。每求出一个哈希值就在哈希表中进行查找是否已经存在该哈希值，如果存在则发现碰撞，可利用哈希表输出相应的原像否则将其存入哈希表。

实验中使用该方法可以很快求解小于40bit的碰撞，其余的求解耗时取十次的均值如下表所示：

| **40bit** | 48bit | 56bit | 64bit | 72bit  |
| --------- | ----- | ----- | ----- | ------ |
| 1.3s      | 3.6s  | 18.7s | 56s   | 207.3s |

从执行效率上可以看出rho方法比直接的生日攻击方法快许多，和使用哈希表的生日攻击速度没有太大差异，理由是因为两种方法的输入都可以理解作“随机”的，且都是采用了哈希表，因此差距不大。





# 3、项目清单

- SM3.h：定义SM3实现函数与输入消息结构

- SM3.cpp：实现SM3与rho碰撞



# 4、说明

## A.项目代码说明

- 哈希表

```C++
typedef unordered_map<std::string, unsigned char*, StrHash, StrCompare> MyMap;
MyMap mymap;
```

- rho尝试碰撞

```C++
			hash_result = SM3::call_hash_sm3(buffer);
			hash_str = "";
			/*每次需要一个新的buffer来保存*/
			unsigned char* buffer1 = new unsigned char[64]; 

			/*以字符串形式保存到哈希表*/
			for (int i = 0; i < 32; i++) {
				std::ostringstream ss;
				ss << std::hex << std::setw(2) << std::setfill('0') << hash_result[i];
				hash_str += ss.str();

				/*将hash结果更新至buffer*/
				buffer1[2 * i] = static_cast<unsigned char>(ss.str()[0]);
				buffer1[2 * i + 1] = static_cast<unsigned char>(ss.str()[1]);
			}

			/*哈希值在哈希表出现过则为发现碰撞*/
			if (mymap[hash_str.substr(0, col_len)])
			{
				time(&end);
				cost = difftime(end, start);
				if (buffer == mymap[hash_str.substr(0, col_len)]) continue;
				cout << "collision buffer:" << endl;
				cout << buffer << endl;
				cout << mymap[hash_str.substr(0, col_len)] << std::endl << std::endl;

				cout << "collision hash:" << endl;
				for (int i = 0; i < 32; i++) {
					std::cout << std::hex << std::setw(2) << std::setfill('0') << hash_result[i];
				}
				std::cout << std::endl;

				hash_result = SM3::call_hash_sm3(mymap[hash_str.substr(0, col_len)]);
				for (int i = 0; i < 32; i++) {
					std::cout << std::hex << std::setw(2) << std::setfill('0') << hash_result[i];
				}
				std::cout << std::endl << std::endl;
				std::cout << "72bit collision needs " << cost << "s：" << std::endl << std::endl;
				break;
			}
			else {
				/*更新哈希表*/
				mymap[hash_str.substr(0, col_len)] = buffer;
				buffer = buffer1;
			}
```



## B.运行指导(跑不起来的不算成功)

头文件：

```
#include <string.h>
#include <stdio.h>
#include <iostream>
#include <vector>
#include <iomanip>
#include <memory>
#include <random>
#include <time.h>
#include <unordered_map>
#include <string>
#include <sstream>
#include "SM3.h"
```

SM3.h和SM3.cpp放在同一目录下即可执行，通过修改col_len值修改碰撞长度。



## C.代码运行全过程截图

**执行环境：**windows Intel(R) Core(TM) i5-8250U CPU @1.60G ，内存4GB

**16bit：**

![image-20220730154414793](https://cdn.jsdelivr.net/gh/Lynnrya/images@main/img/202207302112089.png)

![image-20220730154433342](https://cdn.jsdelivr.net/gh/Lynnrya/images@main/img/202207302112645.png)

![image-20220730154449644](https://cdn.jsdelivr.net/gh/Lynnrya/images@main/img/202207302112038.png)



**40bit：**

![image-20220730160050884](C:\Users\huinun\AppData\Roaming\Typora\typora-user-images\image-20220730160050884.png)

![image-20220730160059771](C:\Users\huinun\AppData\Roaming\Typora\typora-user-images\image-20220730160059771.png)

![image-20220730160121325](C:\Users\huinun\AppData\Roaming\Typora\typora-user-images\image-20220730160121325.png)



**48bit：**

![image-20220730155428601](C:\Users\huinun\AppData\Roaming\Typora\typora-user-images\image-20220730155428601.png)

![image-20220730155440628](C:\Users\huinun\AppData\Roaming\Typora\typora-user-images\image-20220730155440628.png)

![image-20220730155506372](C:\Users\huinun\AppData\Roaming\Typora\typora-user-images\image-20220730155506372.png)



**56bit:**

![image-20220730160653907](C:\Users\huinun\AppData\Roaming\Typora\typora-user-images\image-20220730160653907.png)

![image-20220730160706426](C:\Users\huinun\AppData\Roaming\Typora\typora-user-images\image-20220730160706426.png)

![image-20220730160724995](C:\Users\huinun\AppData\Roaming\Typora\typora-user-images\image-20220730160724995.png)



**64bit：**

![image-20220730162014276](C:\Users\huinun\AppData\Roaming\Typora\typora-user-images\image-20220730162014276.png)

![image-20220730162029281](C:\Users\huinun\AppData\Roaming\Typora\typora-user-images\image-20220730162029281.png)

![image-20220730162042840](C:\Users\huinun\AppData\Roaming\Typora\typora-user-images\image-20220730162042840.png)



**72bit：**

![image-20220730170414270](C:\Users\huinun\AppData\Roaming\Typora\typora-user-images\image-20220730170414270.png)

![image-20220730170431411](C:\Users\huinun\AppData\Roaming\Typora\typora-user-images\image-20220730170431411.png)

![image-20220730170444475](C:\Users\huinun\AppData\Roaming\Typora\typora-user-images\image-20220730170444475.png)



# 5、参考资料

https://blog.csdn.net/cg129054036/article/details/83032978

https://github.com/WHU-Cryptography/SM3



