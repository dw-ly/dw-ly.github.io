##### 1.常用方法

```c++
//使用时注意包含头文件<set>    std::set and std::multiset associative containers
 std::set<int> s;
s.begin();     　 //返回set容器的第一个元素
s.end(); 　　　　  //返回set容器的最后一个元素
s.clear();       //删除set容器中的所有的元素
s.empty();　　　  //判断set容器是否为空
s.insert();      //插入一个元素
s.erase();       //删除一个元素
s.size(); 　　　　//返回当前set容器中的元素个数
```

##### 2.使用

###### 2.1创建

```c++
#include <iostream>
#include <set>
#include <functional>
using namespace std;
set<int> s;
 
int main(){
   set<int> seta; //默认是小于比较器less<int>的set
   set<int, greater<int>> setb; //创建一个带大于比较器的set，需包含头文件functional
   int a[5] = {1,2,3,4,5};
   set<int> setc(a,a+5); //数组a初始化一个set；
   set<int> setd(setc.begin(),setc.end()); //setc初始化一个set
   //上述两例均为区间初始化
   set<int> sete(setd); //拷贝构造创建set
   return 0;
}
```



###### 2.2插入

```c++
#include <iostream>
#include <set>
using namespace std;
set<int> s;
void setprint(int cnt){
    cout << "Test output :" << cnt << ":" << endl;
    for(set<int>::iterator it = s.begin(); it!= s.end(); it++)
        cout << *it << " ";
    puts("");
    return ;
}
int main(){
    int cnt = 1;
    s.insert(1);
    s.insert(2);
    s.insert(5);
    setprint(cnt++);
 
    s.insert(2); //set只允许用一个值出现一次，要插入相同元素请用multiset
    setprint(cnt++);
 
    int a[4] = {11,12,13,14};
    s.insert(a,a+4); //将区间[a, a+4]里的元素插入容器
    setprint(cnt++);
 
    return 0;
}
```



###### 2.3修改

> 不能直接修改容器内数据，所以只能删除某元素再插入要修改的数值。

###### 2.4删除

```c++
//s.erase()       删除一个元素 
//s.clear()       清空set容器中的所有的元素
#include <iostream>
#include <set>
using namespace std;
set<int >s;
void setprint(int cnt){
    cout << "Test output :" << cnt << ":" << endl;
    for(set<int>::iterator it = s.begin(); it!= s.end(); it++)
        cout << *it << " ";
    puts("");
    return ;
}
 
int main(){
    int cnt = 1;
    for(int i = 1; i < 11; i++){
        s.insert(i);
    }
    setprint(cnt++);
 
    s.erase(9); //根据元素删除
    setprint(cnt++);
 
    set<int>::iterator ita = s.begin();
    set<int>::iterator itb = s.begin();
    s.erase(ita);  //删除迭代器指向位置的元素
    setprint(cnt++);
 
    ita = s.begin();
    itb = s.begin();
    itb++;itb++;
    s.erase(ita,itb); //删除区间[ita,itb)的元素
    setprint(cnt);
    s.clear();
    return 0;
}
```

###### 2.5查找

```c++
...
set<int> s
...
if(s.find() == s.end())
{
	printf("不存在");
}
else
{
	printf("存在");
}
```

###### 2.6其他

```c++
...
if(s.empty()) cout << "容器为空" << endl;
if(s.count(1)) cout << "1在容器中" << endl
```

参考：[C++ STL set容器常用用法](https://blog.csdn.net/sinat_37158899/article/details/79328104)