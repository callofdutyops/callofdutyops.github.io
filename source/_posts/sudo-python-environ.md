---
title: sudo python environ 环境变量问题
date: 2017-06-15 16:25:05
tags:
- Linux
- Python
categories:
- tech
---

如果用`sudo`执行`python`命令的话，会发现：
```python
import os
os.getenv("PATH")
```
得到的变量和普通权限得到的不一致，即使是sudo加上-E参数也没有用。 这是sudoers的默认行为导致的，解决方案：
`sudo visudo`，`Defaults env_reset`改为`Defaults !env_reset`。 注意，加了!号。 同时注释掉`Defaults secure_path`，改完记得注销一下。

不需要以sudo执行python的时候记得改回来，防止出现权限安全问题。

这个奇葩的问题Google居然搜不到，看来是个神坑，分析过程如下： 

既然问题的根源是`sudo -E python`都无法继承当前的环境变量，那么首先`man sudo`搜索-E参数，发现是这么写的：
> -E, --preserve-env Indicates to the security policy that the user wishes to pre‐ serve their existing environment variables.
> The security policy may return an error if the user does not have permis‐ sion to preserve the environment. 

感觉这句
> The security policy may return an error if the user does not have permission to preserve the environment.

有问题，于是乎，`man sudoers`，搜索`PATH`，发现有这么一段：
> By default, the **env_reset** option is enabled. This causes commands to be executed with a new, minimal environment.
> On AIX (and Linux systems without PAM), the environment is initialized with the contents of the /etc/environment file.
> The new environment contains the TERM, PATH, HOME, MAIL, SHELL, LOGNAME, USER, USERNAME and SUDO_\* variables in addition
> to variables from the invoking process permitted by the env_check and env_keep options.
> This is effectively a whitelist for environment variables. Environment variables with a value beginning with () are removed
> unless both the name and value parts are matched by env_keep or env_check, as they will be interpreted as functions by
> older versions of the bash shell. Prior to version 1.8.11, such variables were always removed.

那肯定是`envreset`的锅跑不了了，继续搜索，发现`envreset`是默认参数行为，这就好办了，到`sudoers`文件里disable就好了。
因此，`sudo visudo`，`Defaults env_reset`改为`Defaults !env_reset`，保险起见，注释掉`Defaults secure_path`，注销后生效，问题解决！

p.s. 后来似乎搜到了，[PYTHONPATH not working for sudo on GNU/Linux (works for root)](https://stackoverflow.com/questions/7969540/pythonpath-not-working-for-sudo-on-gnu-linux-works-for-root)
