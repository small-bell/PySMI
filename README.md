# PySMI 源码解析

## 介绍

PySMI 是一个用于处理SNMP协议的信息库Mib文件的包，包含文件读取，语法树构建与解析，目标代码生成三个部分。

其实是工作中用到了，需要自定义一个MIB词法语法解析器。

## 目录结构

---pysmi
    |   compat.py ：utf-8编解码类
    |   compiler.py：Mib编译器，会初始化reader读取文件，searcher搜索.mib文件存入缓存，并且用parser lex编译成语法树，交给codegen模块生成目标代码。
    |   config.py：是否严格模式的配置
    |   debug.py：日志打印工具
    |   error.py：定义了一些错误
    |   mibinfo.py：定义了Mib文件对象，包含文件的信息，不包含内容
    |   README.md
    |   __init__.py：
    |
    +---borrower
    |       anyfile.py：AnyFileBorrower：空实现
    |       base.py：AbstractBorrower 浏览器基类，调用自己的reader去读取文件内容
    |       pyfile.py：PyFileBorrower，用来生成Py格式的mib，无实现
    |       __init__.py
    |
    +---codegen：接收perser传来的ast抽象语法树，在调用symtable静态语法表处理词法语法，调用模板生成目标文档
    |   |   base.py
    |   |   intermediate.py
    |   |   jfilters.py
    |   |   jsondoc.py
    |   |   null.py
    |   |   pysnmp.py
    |   |   symtable.py
    |   |   __init__.py
    |   |
    |   \---templates： 代码生成模板，会再codegen模块填充模板
    |       +---jsondoc
    |       |       base.j2
    |       |
    |       \---pysnmp
    |           |   base.j2
    |           |   managed-objects-instances.j2
    |           |   mib-definitions.j2
    |           |
    |           \---mib-instrumentation
    |                   managed-objects-instances.j2
    |                   managed-objects.j2
    |
    +---lexer：定义了Mib的词法语法
    |       base.py
    |       smi.py
    |       __init__.py
    |
    +---parser：其实其他的都是工厂模式，真正实现在smi.py,调用了lexer的smi词法语法解析器，进行解析和换行，添加V1V2V3的三种语法解析器
    |       base.py
    |       dialect.py
    |       null.py
    |       smi.py
    |       smiv1.py
    |       smiv1compat.py
    |       smiv2.py
    |       __init__.py
    |
    +---reader： 包含了从指定URI读取文件的类，如自定义，http，本地文件，压缩文件
    |       base.py
    |       callback.py
    |       httpclient.py
    |       localfile.py
    |       url.py
    |       zipreader.py
    |       __init__.py
    |
    +---scripts
    |       mibcopy.py
    |       mibdump.py：可以用来mib转json
    |
    +---searcher
    |       anyfile.py：AnyFileSearcher实现了是否有本地文件
    |       base.py：AbstractSearcher，Searcher基类，用来搜素目录的
    |       pyfile.py：搜索大小写和原来的文件名加上py后缀
    |       pypackage.py：搜索py egg文件包
    |       stub.py
    |       __init__.py
    |
    \---writer：文件写入工具类
            base.py：AbstractWriter接口
            callback.py：一个可以用户实现的Writer，用户实现后会调用用户的putdata,getdata
            localfile.py：FileWriter 本地文件Writter
            pyfile.py：用py_compile原生生成python字节码存储
            __init__.py
