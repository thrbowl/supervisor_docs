配置文件
========


*Supervisor* 的配置文件叫做 ``supervisord.conf``。 *supervisord* 和 *supervisorctl* 都使用该配置文件。
不论是使用-c选项（这个选项告诉这个应用要使用的配置文件名）启动应用，或者按照下面的顺序查找配置文件（ ``supervisord.conf`` ）。
找到的第一个配置文件被使用。

1. ``$CWD/supervisord.conf``
#. ``$CWD/etc/supervisord.conf``
#. ``/etc/supervisord.conf``
#. ``../etc/supervisord.conf`` (相对于可执行文件)
#. ``../supervisord.conf`` (相对于可执行文件)

.. note::

    一些发行版已经打包了自定义的Supervisor。这些修改的版本可能从本地加载配置文件。
    另外, Ubuntu 是从 ``/etc/supervisor/supervisord.conf`` 加载配置。


文件格式
--------

``supervisord.conf`` 是一个 Windows INI 样式（Python ConfigParser）文件。
它由一个或多个部分（一个[header]作为一个部分）组成，同时每个部分有各自的key/value键值对配置项。
下面是所有可以使用的配置项。


环境变量
--------

环境变量是当supervisord启动时，配置文件中能使用的Python字符串表达式, 语法为 %(ENV_X)s:


.. code-block:: ini

    [program:example]
    command=/usr/bin/example --loglevel=%(ENV_LOGLEVEL)s

在上面的例子中，表达式 ``%(ENV_LOGLEVEL)s`` 使用环境变量 ``LOGLEVEL`` 。

.. note::

    Supervisor 3.2及以后版本， 所有的可选项都会支持%(ENV_X)s表达式，但是以前的版本，只有部分支持，大部分都是不支持的。
    具体的参考下面的可选项文档。


``[unix_http_server]`` 部分
-----------------------------

``[unix_http_server]`` 会创建一个UNIX socket, 用于操作管理superisord进程。


**file**

UNIX socket创建路径（例如：/tmp/supervisord.sock），supervisorctl使用XML-RPC通过这个socket去与supervisord进行通讯，
这个选项可以包含%(here)s，指代配置文件所在目录。

``Default``: None

``Required``: No

``Introduced``: 3.0


**chmod**

设置UNIX socket启动后的默认权限

``Default``: ``0700``

``Required``: No

``Introduced``: 3.0


**chown**

设置UNIX socket启动后的所属用户和用户组(例如 chrism 或者 chrism:wheel)

``Default``: 启动supervisord的用户和用户组

``Required``: No

``Introduced``: 3.0


**username**

http服务器验证使用的用户名

``Default``: None

``Required``: No

``Introduced``: 3.0


**password**

http服务器验证使用的密码. 可以使用纯文本，也可以使用加密值，
例如使用SHA-1哈希，{SHA}82ab876d1387bfafe46cc1c8a2ef074eae50cb1d对应‘thepassword’。

``Default``: None

``Required``: No

``Introduced``: 3.0


*Example*

.. code-block:: ini

    [unix_http_server]
    file = /tmp/supervisor.sock
    chmod = 0777
    chown= nobody:nogroup
    username = user
    password = 123


``[inet_http_server]`` 部分
----------------------------

``[unix_http_server]`` 会创建一个TCP socket, 用于操作管理superisord进程。


**port**

一个TCP host:port值，supervisorctl使用XML-RPC通过这个端口去与supervisord进行通讯。

``Default``: No default.

``Required``: Yes.

``Introduced``: 3.0


**username**

http服务器验证使用的用户名

``Default``: None

``Required``: No

``Introduced``: 3.0


**password**

http服务器验证使用的密码. 可以使用纯文本，也可以使用加密值，
例如使用SHA-1哈希，{SHA}82ab876d1387bfafe46cc1c8a2ef074eae50cb1d对应‘thepassword’。

``Default``: None

``Required``: No

``Introduced``: 3.0


*Example*

.. code-block:: ini

    [inet_http_server]
    port = 127.0.0.1:9001
    username = user
    password = 123


``[supervisord]`` 部分
------------------------

``supervisord`` 相关的配置项


**logfile**

``supervisord`` 进程的log存放路径

``Default``: ``$CWD/supervisord.log``

``Required``: No

``Introduced``: 3.0


**logfile_maxbytes**

supervisord log的最大字节数（超过就会被切割），后缀可以是“KB”, “MB”, 和 “GB”，如果是0表示不限制大小。

``Default``: 50MB

``Required``: No.

``Introduced``: 3.0


**logfile_backups**

切割文件的保留数目，如果设置为0，不会保留任何备份。

``Default``: 10

``Required``: No.

``Introduced``: 3.0


**loglevel**

logging的信息级别，指定哪些信息写到log中。使用值：critical, error, warn, info, debug, trace, 或者 blather。
如果使用debug级别，supervisord log文件会记录它子进程的stderr/stdout输出和进程状态的扩展信息，
这对于调试不是正常启动的进程很有用。

``Default``: info

``Required``: No.

``Introduced``: 3.0


**pidfile**

supervisord的pid文件存放位置。这个选项可以包含%(here)s，指代配置文件所在目录。

``Default``: ``$CWD/supervisord.pid``

``Required``: No.

``Introduced``: 3.0


**umask**

用户掩码(user mask)的缩写, 用于设置当前进程(supervisord)所创建文件的模式(mode), 详见：http://en.wikipedia.org/wiki/Umask.

``Default``: ``022``

``Required``: No.

``Introduced``: 3.0


**nodaemon**

如果为true，supervisord将会在前台方式启动，代替守护进程方式启动。

``Default``: false

``Required``: No.

``Introduced``: 3.0


**minfds**

supervisord开始启动前，必须有效的最小文件描述符数目。调用setrlimit指令去设置supervisord进程的软/硬限制去满足 ``minfds``。
硬限制可能只在以root运行supervisord的时候有效。supervisord不限制的使用文件描述符，一旦无法从操作系统产生一个新的将会进入失败模式。
所以指定一个最小描述符数目是有用的去确保运行期间不超出范围。这个选项实际对Solaris很有效，因为其每个进程默认有很低的fd限制。

``Default``: 1024

``Required``: No.

``Introduced``: 3.0


**minprocs**

supervisord开始启动前，必须有效的最小进程描述符数目。调用setrlimit指令去设置supervisord进程的软/硬限制去满足 ``minprocs``。
硬限制可能只在以root运行supervisord的时候有效。supervisord不限制的使用进程描述符，一旦无法从操作系统产生一个新的将会进入失败模式。
所以指定一个最小描述符数目是有用的去确保运行期间不超出范围。

``Default``: 200

``Required``: No.

``Introduced``: 3.0


**nocleanup**

阻止supervisord启动时自动清除存在的子进程log。对于调试很有用。

``Default``: false

``Required``: No.

``Introduced``: 3.0


**childlogdir**

自动生成的子进程日志文件存放目录. 这个选项可以包含%(here)s，指代配置文件所在目录。

``Default``: value of Python’s ``tempfile.get_tempdir()``

``Required``: No.

``Introduced``: 3.0


**user**

让supervisord在进行任何操作前切换到该用户帐号。只有以root运行supervisord的时候才可以进行切换。
如果不能切换，仍然会继续执行，但是会在日志中输入一条致命错误日志去提示不能放弃权限。

``Default``: do not switch users

``Required``: No.

``Introduced``: 3.0


**directory**

当supervisord已守护进程模式启动，切换到该目录作为工作目录。这个选项可以包含%(here)s，指代配置文件所在目录。

``Default``: do not cd

``Required``: No.

``Introduced``: 3.0


**strip_ansi**

从所有的子进程日志文件中删除ANSI转义序列

``Default``: false

``Required``: No.

``Introduced``: 3.0


**environment**

一个键/值对格式（KEY="val",KEY2="val2"）列表，作为supervisord进程中的环境变量（以及之进程中的环境变量）。
这个选项可以包含%(here)s，指代配置文件所在目录。包含非字母数字的字符应该使用引号括起来，否则引号是可选，但是推荐的。
使用两个%%去表示%。同时自进程会继承父进程的环境变量。

``Default``: no values

``Required``: No.

``Introduced``: 3.0


**identifier**

supervisor进程的标识符，用于RPC接口。

``Default``: supervisor

``Required``: No.

``Introduced``: 3.0


*Example*

.. code-block:: ini

    [supervisord]
    logfile = /tmp/supervisord.log
    logfile_maxbytes = 50MB
    logfile_backups=10
    loglevel = info
    pidfile = /tmp/supervisord.pid
    nodaemon = false
    minfds = 1024
    minprocs = 200
    umask = 022
    user = chrism
    identifier = supervisor
    directory = /tmp
    nocleanup = true
    childlogdir = /tmp
    strip_ansi = false
    environment = KEY1="value1",KEY2="value2"


``[supervisorctl]`` 部分
--------------------------

交互shell ``supervisorct`` 的配置项


**serverurl**

访问supervisord服务的URL（http://localhost:9001）或者UNIX socket（unix:///absolute/path/to/file.sock）。

``Default``: ``http://localhost:9001``

``Required``: No.

``Introduced``: 3.0


**username**

验证supervisord服务权限的用户名。这用户名应该于supervisord服务的端口或者UNIX socket用户名一样。

``Default``: No username

``Required``: No.

``Introduced``: 3.0


**password**

验证supervisord服务权限的密码。这个密码不像其它密码，这里只能使用纯文本。

``Default``: No password

``Required``: No.

``Introduced``: 3.0


**prompt**

supervisorctl的提示信息

``Default``: supervisor

``Required``: No.

``Introduced``: 3.0


**history_file**

记录supervisorctl命令操作历史的文件路径

``Default``: No file

``Required``: No.

``Introduced``: 3.0a5


*Example*

.. code-block:: ini

    [supervisorctl]
    serverurl = unix:///tmp/supervisor.sock
    username = chris
    password = 123
    prompt = mysupervisor


``[program:x]`` 部分
----------------------

配置文件必须包含一个或多个 ``program`` 部分，让 ``supervisord`` 知道如何启动和控制程序。
这个header是一个组合值（``program:`` 加上程序名），格式 ``[program:foo]`` 表示foo程序。
这个名字被用来设置进程的属性值。如果名字没有设置会抛出一个错误。这个名字可以在配置项值里使用%(program_name)s来代替。

.. note::

    ``[program:x]`` 对于 ``supervisor`` 代表一个 *同源进程租* 。
    这个组的成员由进程数（``numprocs``）和进程名（``process_name``）合成。
    如果进程数（``numprocs``）和进程名（``process_name``）采用默认值，进程组的名字就是 *x* , 里面只有一个进程 *x* 。
    这个保证了与旧 ``supervisor`` 版本的兼容（没有把 ``program`` 部分作为 *同源进程租* ）

    但是作为运行实例，如果 ``[program:foo]`` 有 ``numprocs=3, process_name=%(program_name)s_%(process_num)02d``,
    foo进程组会包含3个进程（foo_00, foo_01, and foo_02）。这个方式可以使用一个 ``[program:x]`` 配置一组类似的进程。
    所有的日志文件名称、环境变量和程序命令都可以包含类似的Python表达式，为每个进程传递不同的参数。


**command**

程序运行的命令。这个命令可以是命令的绝对路径（例如：/path/to/programname），也可以是相对路径（例如：programname）。
如果使用相对路径，将会在 ``supervisord`` 的环境$PATH中搜索改命令。程序可以接受参数（例如：/path/to/program foo bar）。
命令行可以使用双引号去传递使用空格分开的组参数（例如：/path/to/program/name -p "foo bar"）。

.. warning::
    命令行可以包含Python表达式（例如：/path/to/programname --port=80%(process_num)02d）会在运行时扩展成（/path/to/programname --port=8000）。
    表达式会使用一个字典（group_name, host_node_name, process_num, program_name, here（ ``supervisord`` 配置文件目录））和所有的已ENV_开头的 ``supervisord`` 环境变量来计算字符串。
    被控制的程序不能使用守护进程方式启动，这样 ``supervisord`` 才能接管该程序。

``Default``: No default.

``Required``: Yes.

``Introduced``: 3.0


**process_name**

使用Python表达式作为进程名。除非你修改 ``numprocs``, 你通常不需要关心。
表达式会使用一个字典（group_name, host_node_name, process_num, program_name, here（ ``supervisord`` 配置文件目录））来计算字符串。

``Default``: ``%(program_name)s``

``Required``: No.

``Introduced``: 3.0


**numprocs**

Supervisor在进程开始的时候会启动 ``numprocs`` 个进程。

.. danger::

    如果numprocs>1, 表达式必须包括 ``%(process_num)s``（或者任何有效的包含 ``%(process_num)s`` 的Python表达式）。

``Default``: 1

``Required``: No.

``Introduced``: 3.0


**numprocs_start**

``numprocs`` 的起始值。

``Default``: 0

``Required``: No.

``Introduced``: 3.0


**priority**

程序启动和关闭的优先级顺序。低优先级表示在运行客户端命令（例如：start all”/”stop all）是先启动后关闭。高优先级表示程序后启动先关闭。

``Default``: 999

``Required``: No.

``Introduced``: 3.0


**autostart**

如果为true, 程序会在supervisord启动的时候自动启动。

``Default``: true

``Required``: No.

``Introduced``: 3.0


**startsecs**

认为程序启动成功（从程序STARTING状态改变成RUNNING状态）的程序运行持续秒数。0表示不需要程序持续运行。

.. note::

    尽管程序使用一个 ``expected``退出码 退出。但是如果程序比设置的 ``startsecs`` 更快的退出，仍然会认为是失败。

``Default``: 1

``Required``: No.

``Introduced``: 3.0


**startretries**

在设置进程运行状态为失败之前，程序重试的次数。

``Default``: 3

``Required``: No.

``Introduced``: 3.0


**autorestart**

如果 ``supervisord`` 会在一个进程退出（退出前是 ``RUNNING`` 状态）后，自动重启该进程，需要指定该选项。
可以是 ``one`` , ``false`` , ``unexpected``, ``true`` 中的一个。

如果是 ``false`` ,进程不会自动重启。

如果是 ``unexpected`` , 进程的退出码如果没有和进程设置的退出码集合匹配会自动重启。

如果是 ``true`` ,进程会无条件的重启。

.. note::

    ``autorestart`` 控制的是进程由 ``RUNNING`` 状态退出的重启机制。

    如果进程是第一次启动（ ``STARTING`` 状态），重启机制通过 ``startsecs`` , ``startretries``控制。

``Default``: unexpected

``Required``: No.

``Introduced``: 3.0


**exitcodes**

``autorestart`` 使用的 ``expected`` 退出码列表。
如果 ``autorestart`` 选项设置为 ``unexpected`` , 进程如果不是因为 ``supervisor`` 停止请求而退出的话，
同时进程的退出码没有在这个列表里， ``supervisord`` 会自动重启该进程。

``Default``: 0,2

``Required``: No.

``Introduced``: 3.0


**stopsignal**

停止进程时发送的信号，可以使用 ``TERM, HUP, INT, QUIT, KILL, USR1, 或者 USR2`` 。

``Default``: ``TERM``

``Required``: No.

``Introduced``: 3.0


**stopwaitsecs**

在发送一个停止信号后，操作系统等待 ``stopwaitsecs`` 秒，然后返回一个 ``SIGCHILD`` 信号给supervisord。
如果 ``supervisord`` 超过 ``stopwaitsecs`` 秒后，还没有收到 ``SIGCHILD`` ，``supervisord`` 会再发送一个 ``SIGKILL`` 信号给进程。

``Default``: 10

``Required``: No.

``Introduced``: 3.0


**stopasgroup**

如果是true， 这个选项会让supervisor发送一个停止信号给整个进程组。
例如Flask运行在debug模式，不会发送停止信号给它的子进程，这时候这个选项是有用的。

``Default``: false

``Required``: No.

``Introduced``: 3.0b1


**killasgroup**

如果为true，会把只发送 ``SIGKILL`` 到单个进程，转化为发送到整个进程租。
这个对于存在子进程的程序是有用的，例如使用 ``multiprocessing`` 启动的程序。

``Default``: false

``Required``: No.

``Introduced``: 3.0a11


**user**

这个选项指定了程序运行时使用的帐号，一般只能以root权限运行 ``supervisord`` 的时候才可以设置。
如果 ``supervisord`` 设置用户失败，程序不会启动。

.. note::

    这个用户使用 ``setuid`` 来设置。不会启动一个登录shell，也不会改变环境变量（比如 USER，HOME）。

``Default``: Do not switch users

``Required``: No.

``Introduced``: 3.0


**redirect_stderr**

如果选项为true, 程序的标准错误会被写到 ``supervisord`` 的标准输出中。（在UNIX中，等于执行：/the/program 2>&1）

.. note::

    在 ``[eventlistener:x]`` 中不要将该选项设置为true。Eventlisteners通过stdout和stdin于 ``supervisord`` 通讯。
    如果stderr被重定向，从stderr的输出将会扰乱eventlistener协议。

``Default``: false

``Required``: No.

``Introduced``: 3.0, replaces 2.0’s log_stdout and log_stderr


**stdout_logfile**

程序stdout存放的文件路径（如果 ``redirect_stderr`` 是true，stderr输出也会到该文件中）。
如果 ``stdout_logfile`` 没有设置，或者设置为 ``AUTO``, supervisor会自动选择一个文件路径。
如果设置为 ``None`` `, supervisor不会生成log文件。通过 ``AUTO`` 创建的log文件和备份会在supervisord重启时删除。
``stdout_logfile`` 可以使用Python表达式。表达式会使用一个字典（group_name, host_node_name, process_num, program_name,
here（ supervisord 配置文件目录））来计算字符串。

.. note::

    在rotation（stdout_logfile_maxbytes）启用时，不可以多个进程共享一个日志文件。因为会打断日志内容。

``Default``: AUTO

``Required``: No.

``Introduced``: 3.0, replaces 2.0’s logfile


**stdout_logfile_maxbytes**

``stdout_logfile`` 的最大字节数（超过就会被切割），后缀可以是“KB”, “MB”, 和 “GB”，如果是0表示不限制大小。

``Default``: 50MB

``Required``: No.

``Introduced``: 3.0, replaces 2.0’s logfile_maxbytes


**stdout_logfile_backups**


切割文件的保留数目，如果设置为0，不会保留任何备份。

``Default``: 10

``Required``: No.

``Introduced``: 3.0, replaces 2.0’s logfile_backups


**stdout_capture_maxbytes**

``stdout capture mode``下，FIFO中的最大字节数。后缀可以是“KB”, “MB”, 和 “GB”，如果是0表示不限制大小。

``Default``: 0

``Required``: No.

``Introduced``: 3.0


**stderr_events_enabled**

如果选项值为true，在进程写它的stderr时，会触发 ``PROCESS_LOG_STDERR`` 事件。
这个事件只在 ``capture`` 模式下，接受到数据时会被触发。

``Default``: false

``Required``: No.

``Introduced``: 3.0a7


**environment**

一个包含一个或多个的键值对列表，作为子进程的环境变量。这个变量会使用一个字典（group_name, host_node_name, process_num,
program_name, here（ supervisord 配置文件目录））计算字符串。
包含非字母数字的字符应该使用引号括起来，否则引号是可选，但是推荐的。 使用两个%%去表示%。同时自进程会继承父进程的环境变量。

``Default``: no values

``Required``: No.

``Introduced``: 3.0


**directory**

在执行程序前，``chdir`` 到该目录

``Default``: No chdir (inherit supervisor’s)

``Required``: No.

``Introduced``: 3.0


**umask**

用户掩码(user mask)的缩写, 用于设置当前进程(x)所创建文件的模式(mode), 详见：http://en.wikipedia.org/wiki/Umask.

``Default``: No special umask (inherit supervisor’s)

``Required``: No.

``Introduced``: 3.0


**serverurl**

传递给环境变量 ``SUPERVISOR_SERVER_URL`` 的URL，通过这个值子进程可以容易的与supervisord内部的HTTP Server通讯。
如果提供该值，应该与 ``[supervisorctl]`` 部分，同名的选项有同样的语法和结构。
如果设置为 ``AUTO`` ,或者不设置， supervisor会自动生成一个URL。

``Default``: AUTO

``Required``: No.

``Introduced``: 3.0


*Example*

.. code-block:: ini

    [program:cat]
    command=/bin/cat
    process_name=%(program_name)s
    numprocs=1
    directory=/tmp
    umask=022
    priority=999
    autostart=true
    autorestart=unexpected
    startsecs=10
    startretries=3
    exitcodes=0,2
    stopsignal=TERM
    stopwaitsecs=10
    stopasgroup=false
    killasgroup=false
    user=chrism
    redirect_stderr=false
    stdout_logfile=/a/path
    stdout_logfile_maxbytes=1MB
    stdout_logfile_backups=10
    stdout_capture_maxbytes=1MB
    stdout_events_enabled=false
    stderr_logfile=/a/path
    stderr_logfile_maxbytes=1MB
    stderr_logfile_backups=10
    stderr_capture_maxbytes=1MB
    stderr_events_enabled=false
    environment=A="1",B="2"
    serverurl=AUTO


``[include]`` 部分
-------------------

``[include]`` 包括一个单一的files键，它的值是包含配置内容的文件路径。


**files**

一个空格分割的文件表达式。每个文件表达式可以是绝对路径，也可以是相对路径。
如果是相对路径，是相对于配置文件来说的。一个 ``glob`` 是正则表达式对应Unix shell的匹配规则。
可以使用*，？，[]等匹配符。递归包含是不支持的。

``Default``: No default (required)

``Required``: Yes.

``Introduced``: 3.0


*Example*

.. code-block:: ini

    [include]
    files = /an/absolute/filename.conf /an/absolute/*.conf foo.conf config??.conf


``[group:x]`` 部分
-------------------

有时候会把多个进程作为一个进程组，这样可以把他们作为一个整体，通过Supervisor的接口进行操作。

你使用 ``[group:x]`` 来定义进程组，要使用进程组必须有一个或多个进程配置( ``[program:x]`` ), 然后在进程组中使用进程名称进行配置。

.. code-block:: ini

    [group:foo]
    programs=bar,baz
    priority=999

如果使用进程组将不能再直接操作单个进程。


**programs**

一个逗号分割的程序名称列表。这些程序作为进程组的成员。

``Default``: No default (required)

``Required``: Yes.

``Introduced``: 3.0


**priority**

进程组的优先级

``Default``: 999

``Required``: No.

``Introduced``: 3.0


*Example*

.. code-block:: ini

    [group:foo]
    programs=bar,baz
    priority=999


``[fcgi-program:x]`` 部分
--------------------------

Supervisor可以管理监听相同socket的一组进程。至今，部署可扩展的FastCGI进程依然是被限制的。
你可以用Apache的mod_fastcgi模块来部署，但是Apache低效率的并发模型（一个链接一个进程/线程）会限制你的程序。
另外对于要求多CPU和多内存的应用，Apache的（一个链接一个进程/线程）并发模型会很快的被慢请求耗尽资源，这样就不能为其他的请求服务。
如果你使用新的事件驱动的web服务器（例如:lighttpd/nginx）, 但是他们没有自带进程管理功能，你就必须使用类似（cgi-fcgi/spawn-fcgi）的脚本。
他们可以与一个进程管理器（例如: supervisord/daemontools）一起使用，但是可能要求每个FastCGI子进程都绑定自己的socket。

这样的缺点是：

* 复杂的web服务器配置
* 不优雅的启动方式
* 减弱的容错

不配置多个sockets，如果web服务器使用共享socket，配置文件会大大简化。因为scoket会一直绑定父进程，直到每一个子进程都被重启，
所以共享sockets可以优雅的重启。最后共享sockets会更有效的容错，因为如果一个进程失败，其他的进程会去接替服务。

使用集成的FastCGI支持，Supervisor拥有两个优势：

1. 通过共享socket而不是与实际份额web服务器捆绑，方便拥有完整的进程管理功能。
2. 独立的配置，让web服务器和进程管理器各自发挥自己的优势。

.. note::

    FastCGI进程是Supervisor socket管理器原生支持的，但是可以没有任何限制的使用。不要求特定的配置，其它的协议也可以很方便的使用。
    任何程序都可以通过文件描述符（比如：Python的socket.fromfd）来使用socket管理器。Supervisor在进程组里的第一个子进程被fork前，
    会自动创建socket，并并定，监听。这个socket会作为0文件描述符传递到每个子进程中，当最后一个子进程退出，Supervisor会关闭这个socket.

所有 ``[program:x]`` 部分的选项，都可以在 ``fcgi-program`` 中使用。


**socket**

程序使用的FactCGI socket，可以是一个TCP socket，也可以是一个UNIX socket。如果是TCP socket，格式为 ``tcp://localhost:9002`` 。
如果是UNIX socket, 格式为 ``unix:///absolute/path/to/file.sock`` 。Python表达式会使用 ``program_name`` ,
``here`` （supervisor的配置文件目录） 计算真正的路径。

``Default``: No default.

``Required``: Yes.

``Introduced``: 3.0


**socket_owner**

指定FastCGI socket的用户和组，可以是一个用户名（例如：chrism），也可以是一个冒号分割的用户名和组名（例如：chrism:wheel）。

``Default``: Uses the user and group set for the fcgi-program

``Required``: No.

``Introduced``: 3.0


**socket_mode**

UNIX socket的权限模式

``Default``: 0700

``Required``: No.

``Introduced``: 3.0


*Example*

.. code-block:: ini

    [fcgi-program:fcgiprogramname]
    command=/usr/bin/example.fcgi
    socket=unix:///var/run/supervisor/%(program_name)s.sock
    socket_owner=chrism
    socket_mode=0700
    process_name=%(program_name)s_%(process_num)02d
    numprocs=5
    directory=/tmp
    umask=022
    priority=999
    autostart=true
    autorestart=unexpected
    startsecs=1
    startretries=3
    exitcodes=0,2
    stopsignal=QUIT
    stopasgroup=false
    killasgroup=false
    stopwaitsecs=10
    user=chrism
    redirect_stderr=true
    stdout_logfile=/a/path
    stdout_logfile_maxbytes=1MB
    stdout_logfile_backups=10
    stdout_events_enabled=false
    stderr_logfile=/a/path
    stderr_logfile_maxbytes=1MB
    stderr_logfile_backups=10
    stderr_events_enabled=false
    environment=A="1",B="2"
    serverurl=AUTO


``[eventlistener:x]`` 部分
-----------------------------

Supervisor允许在配置文件里定义特定的进程组（“event listener pools”）。这些监听池包含接受和响应supervisor事件系统通知的进程。

除了 ``stdout_capture_maxbytes`` , ``stderr_capture_maxbytes`` ， 在 ``[program:x]`` 中的其它选项都可以在这里使用。


**buffer_size**

事件监听池的事件队列缓冲区大小。当缓冲区是溢出，最早进入的事件被抛弃。


**events**

一个逗号分割的事件列表，包含所有程序“有兴趣”的事件，当这些事件发生时，收到supervisor的通知。


**result_handler**

一个pkg_resources的入口点对应一个python的回调。默认值是 ``supervisor.dispatchers:default_handler`` 。


*Example*

.. code-block:: ini

    [eventlistener:theeventlistenername]
    command=/bin/eventlistener
    process_name=%(program_name)s_%(process_num)02d
    numprocs=5
    events=PROCESS_STATE
    buffer_size=10
    directory=/tmp
    umask=022
    priority=-1
    autostart=true
    autorestart=unexpected
    startsecs=1
    startretries=3
    exitcodes=0,2
    stopsignal=QUIT
    stopwaitsecs=10
    stopasgroup=false
    killasgroup=false
    user=chrism
    redirect_stderr=false
    stdout_logfile=/a/path
    stdout_logfile_maxbytes=1MB
    stdout_logfile_backups=10
    stdout_events_enabled=false
    stderr_logfile=/a/path
    stderr_logfile_maxbytes=1MB
    stderr_logfile_backups=10
    stderr_events_enabled=false
    environment=A="1",B="2"
    serverurl=AUTO


``[rpcinterface:x]`` 部分
----------------------------

``rpcinterface:x`` 只有在用户希望扩展supervisor，添加自定义行为的时候会被使用。

.. code-block:: ini

    [rpcinterface:supervisor]
    supervisor.rpcinterface_factory = supervisor.rpcinterface:make_main_rpcinterface


``[rpcinterface:supervisor]`` 这个必须在配置文件中。 如果你没有什么额外的处理需要supervisor实现，不需要修改。

但是如果你想为自定义行为添加一个rpc接口命名空间，你可以添加 ``[rpcinterface:foo]`` , foo表示这个命名空间的接口，
这个值（ ``supervisor.rpcinterface_factory`` ）是一个只接受单一位置参数和多个关键字参数的可回调的函数工厂。
任何额外定义的键值对都会作为工厂的keyword参数传入函数。

下面是一个工厂函数:

.. code-block:: python

    from my.package.rpcinterface import AnotherRPCInterface

    def make_another_rpcinterface(supervisord, **config):
        retries = int(config.get('retries', 0))
        another_rpc_interface = AnotherRPCInterface(supervisord, retries)
        return another_rpc_interface

在配置文件中使用：

.. code-block:: ini

    [rpcinterface:another]
    supervisor.rpcinterface_factory = my.package:make_another_rpcinterface
    retries = 1


**pkg_resources**

RPC接口工厂函数的入口点

``Default``: N/A

``Required``: No.

``Introduced``: 3.0


*Example*

.. code-block:: ini

    [rpcinterface:another]
    supervisor.rpcinterface_factory = my.package:make_another_rpcinterface
    retries = 1

