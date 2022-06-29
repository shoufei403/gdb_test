

我们使用`backward_ros`功能包来快速实现用`GDB`调试`ROS2`程序。



## `backward_ros`功能包介绍



`backward_ros`功能包是对`backward-cpp`包的`ROS2`封装，以便可以简单快速地使用`GDB`工具。



`backward-cpp`包的介绍可以查看其仓库：  

[https://github.com/bombela/backward-cpp](https://github.com/bombela/backward-cpp)



使用`backward_ros`功能包实现`GDB`调试`ROS2`程序只需下面三个步骤：

1. 添加`backward_ros`到`package.xml `文件。

```xml
<depend>backward_ros</depend>
```



2. 添加`backward_ros`到`CMakeLists.txt`文件中。

```cmake
find_package(backward_ros REQUIRED)
```



3. 使用`Debug `或者 `RelWithDebInfo`选项编译程序。

```bash
colcon build --cmake-args '-DCMAKE_BUILD_TYPE=RelWithDebInfo'
```



`backward_ros`功能包的`github`地址(`ROS2`程序使用`foxy-devel`分支)：  

[https://github.com/pal-robotics/backward_ros.git](https://github.com/pal-robotics/backward_ros.git)





## 运行示例程序

获取示例程序：  

```bash
git clone https://github.com/shoufei403/gdb_test.git
```



编译时选择`RelWithDebInfo`或`Debug`编译类型时可以看到是代码的哪一行出了问题

```bash
colcon build --cmake-args '-DCMAKE_BUILD_TYPE=RelWithDebInfo'
```



运行测试程序

```bash
source install/setup.bash
ros2 run gdb_test gdb_test
```



可以看到下面的堆栈信息

```bash
Stack trace (most recent call last):
#4    Object "", at 0xffffffffffffffff, in 
#3    Source "/home/kevin/gdb_test/src/gdb_test/gdb_test.cpp", line 50, in _start [0x56060876e91d]
         47:   rclcpp::spin(std::make_shared<MinimalSubscriber>());
         48:   rclcpp::shutdown();
         49:   return 0;
      >  50: }
#2    Source "../csu/libc-start.c", line 308, in __libc_start_main [0x7f1b2f271082]
#1  | Source "/home/kevin/gdb_test/src/gdb_test/gdb_test.cpp", line 47, in make_shared<MinimalSubscriber>
    |    45: {
    |    46:   rclcpp::init(argc, argv);
    | >  47:   rclcpp::spin(std::make_shared<MinimalSubscriber>());
    |    48:   rclcpp::shutdown();
    |    49:   return 0;
    | Source "/usr/include/c++/9/bits/shared_ptr.h", line 718, in allocate_shared<MinimalSubscriber, std::allocator<MinimalSubscriber> >
    |   716:       typedef typename std::remove_cv<_Tp>::type _Tp_nc;
    |   717:       return std::allocate_shared<_Tp>(std::allocator<_Tp_nc>(),
    | > 718: 				       std::forward<_Args>(__args)...);
    |   719:     }
    | Source "/usr/include/c++/9/bits/shared_ptr.h", line 702, in shared_ptr<std::allocator<MinimalSubscriber> >
    |   700:     {
    |   701:       return shared_ptr<_Tp>(_Sp_alloc_shared_tag<_Alloc>{__a},
    | > 702: 			     std::forward<_Args>(__args)...);
    |   703:     }
    | Source "/usr/include/c++/9/bits/shared_ptr.h", line 359, in __shared_ptr<std::allocator<MinimalSubscriber> >
    |   357:       template<typename _Alloc, typename... _Args>
    |   358: 	shared_ptr(_Sp_alloc_shared_tag<_Alloc> __tag, _Args&&... __args)
    | > 359: 	: __shared_ptr<_Tp>(__tag, std::forward<_Args>(__args)...)
    |   360: 	{ }
    | Source "/usr/include/c++/9/bits/shared_ptr_base.h", line 1344, in __shared_count<MinimalSubscriber, std::allocator<MinimalSubscriber> >
    |  1342:       template<typename _Alloc, typename... _Args>
    |  1343: 	__shared_ptr(_Sp_alloc_shared_tag<_Alloc> __tag, _Args&&... __args)
    | >1344: 	: _M_ptr(), _M_refcount(_M_ptr, __tag, std::forward<_Args>(__args)...)
    |  1345: 	{ _M_enable_shared_from_this_with(_M_ptr); }
    | Source "/usr/include/c++/9/bits/shared_ptr_base.h", line 679, in _Sp_counted_ptr_inplace<>
    |   677: 	  auto __guard = std::__allocate_guarded(__a2);
    |   678: 	  _Sp_cp_type* __mem = __guard.get();
    | > 679: 	  auto __pi = ::new (__mem)
    |   680: 	    _Sp_cp_type(__a._M_a, std::forward<_Args>(__args)...);
    |   681: 	  __guard = nullptr;
    | Source "/usr/include/c++/9/bits/shared_ptr_base.h", line 548, in construct<MinimalSubscriber>
    |   546: 	  // _GLIBCXX_RESOLVE_LIB_DEFECTS
    |   547: 	  // 2070.  allocate_shared should use allocator_traits<A>::construct
    | > 548: 	  allocator_traits<_Alloc>::construct(__a, _M_ptr(),
    |   549: 	      std::forward<_Args>(__args)...); // might throw
    |   550: 	}
    | Source "/usr/include/c++/9/bits/alloc_traits.h", line 483, in construct<MinimalSubscriber>
    |   481: 	construct(allocator_type& __a, _Up* __p, _Args&&... __args)
    |   482: 	noexcept(std::is_nothrow_constructible<_Up, _Args...>::value)
    | > 483: 	{ __a.construct(__p, std::forward<_Args>(__args)...); }
    |   484: 
    |   485:       /**
      Source "/usr/include/c++/9/new", line 174, in main [0x56060876e7b1]
        172: // Default placement versions of operator new.
        173: _GLIBCXX_NODISCARD inline void* operator new(std::size_t, void* __p) _GLIBCXX_USE_NOEXCEPT
      > 174: { return __p; }
        175: _GLIBCXX_NODISCARD inline void* operator new[](std::size_t, void* __p) _GLIBCXX_USE_NOEXCEPT
        176: { return __p; }
#0    Source "/home/kevin/gdb_test/src/gdb_test/gdb_test.cpp", line 33, in MinimalSubscriber [0x56060878c1a8]
         30:       "topic", 10, std::bind(&MinimalSubscriber::topic_callback, this, _1));
         31: 
         32:     int * p = nullptr;
      >  33:     *p = 1;//制造断错误
         34:   }
         35: 
         36: private:
Segmentation fault (Address not mapped to object [(nil)])

```



其他使用GDB的方法可以参考此文档

[https://navigation.ros.org/tutorials/docs/get_backtrace.html#preliminaries](https://navigation.ros.org/tutorials/docs/get_backtrace.html#preliminaries)




