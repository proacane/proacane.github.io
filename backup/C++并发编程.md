# std::thread的基本操作
  在定义一个 std::thread 变量时，可以在该线程中指定要执行的函数
```c++
void thread_work1(std::string str){
    std::cout<<"Thread work 1 start"<<std::endl;
    std::cout<<"str is "<<str<<std::endl;
}

int main() {
    std::thread t1(thread_work1,"Hello, World!");
    t1.join();
    return 0;
}
```