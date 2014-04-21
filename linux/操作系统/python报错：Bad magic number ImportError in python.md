是源码编译里面版本不对，删除掉源码pyc然后重新编译就可以了

>    find .-name '*.pyc'-delete
>    
>    python -m compileall .