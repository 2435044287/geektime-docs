你好，我是海纳。

上一节课，我们使用 Python 源码实现了 Exception 类，从而构建起了异常对象的继承体系，同时也实现了异常对象的匹配功能。在这个基础上，我们进一步实现了用于处理异常控制流的几条字节码。

第一条重要的字节码是 SETUP\_FINALLY，它的作用是创建一个 Block，指定了如果在执行 try 语句的过程中发生异常应该跳转到哪里执行。第二条是 CALL\_FINALLY，它的作用是如果 try 语句正常结束了，就跳转进 finally 子句执行。这节课，我们就来实现整个异常控制流的最后一条重要的指令，就是 **END\_FINALLY**，我们要在 END\_FINALLY 中增加恢复异常的逻辑。从而让解释器可以在退出函数栈帧的时候还能正确地维护异常对象。

## 实现 END\_FINALLY

就像之前分析的，END\_FINALLY 的主要作用是结束 finally 子句的执行。根据上节课说的进入 finally 子句的三种情况，我们这里分别做了处理，你可以看一下具体实现。

```c++
void Interpreter::eval_frame() {
    ...
    while (_frame->has_more_codes()) {
	    unsigned char op_code = _frame->get_op_code();
        ...
        switch (op_code) {
		...
            case ByteCode::END_FINALLY: {
                v = POP();
                long long t = (long long)v();
                if (t == 0) {
                    // do nothing.
                }
                else if (t & 0x1) {
                    _frame->set_pc(t >> 1);
                }
                else {
                    _exception_class = v;
                    _pending_exception = POP();
                    _trace_back = POP();
                    _int_status = IS_EXCEPTION;
                }
                break;
            }
        ...
        }
    }
}
```
<div><strong>精选留言（1）</strong></div><ul>
<li><img src="https://static001.geekbang.org/account/avatar/00/26/eb/d7/90391376.jpg" width="30px"><span>ifelse</span> 👍（0） 💬（0）<div>学习打卡</div>2024-11-12</li><br/>
</ul>