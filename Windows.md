

# 异常处理

## Windows结构化异常处理机制

* Windows核心编程
  * SEH - Structured Exception Handling
  * WER - Windows Error Reporting 
    * msinfo32.exe
* [A Crash Course on the Depths of Win32 Structured Exception Handling](http://bytepointer.com/resources/pietrek_crash_course_depths_of_win32_seh.htm)
  * 结构化异常处理是操作系统提供的服务
  * 结构化异常处理是基于线程的
  * EXCEPTION_REGISTRATION链表结构
* [闲聊Windows系统日志](https://www.freebuf.com/vuls/175560.html)

## 生成/调试Windows转储文件

* assert - 程序退出(abort)，无异常抛出，用于程序开发调试阶段

  * outputs implementation-specific diagnostic information on the standard error output and calls [std::abort](https://en.cppreference.com/w/cpp/utility/program/abort). 

* DebugBreak

  * [In most cases, this causes the calling process to terminate because of an unhandled breakpoint exception](https://docs.microsoft.com/zh-cn/windows/win32/api/debugapi/nf-debugapi-debugbreak?redirectedfrom=MSDN)

* [Just in time debugger](https://docs.microsoft.com/en-us/visualstudio/debugger/debug-using-the-just-in-time-debugger?view=vs-2019#jit_errors)

* SetUnhandledExceptionFilter 

  - Enables an application to supersede the top-level exception handler of each thread of a process.

    After calling this function, if an exception occurs in a process that is not being debugged, and the exception makes it to the unhandled exception filter, that filter will call the exception filter function specified by the *lpTopLevelExceptionFilter* parameter.

* MiniDumpWriteDump

  * Writes user-mode minidump information to the specified file.

* _set_se_translator  

  - Set a per-thread callback function to translate Win32 exceptions (C structured exceptions) into C++ typed exceptions.



# Windbg

* [Dispaly exception context record](https://docs.microsoft.com/en-us/windows-hardware/drivers/debugger/-ecxr--display-exception-context-record-)



# Remote Debug Tools

* [Remote debugging a C++ project in Visual Studio](https://docs.microsoft.com/en-us/visualstudio/debugger/remote-debugging-cpp?view=vs-2019)





























