# 关闭windows启动的tomcat

首先通过快捷键**windows+R**进入运行面板，其次输入**cmd**打开命令行界面，接着输入**netstat -ano|findstr xxxx**查找端口被占用的进程，最后输入**taskkill /F /PID xxxxx**来杀死进程，即可释放端口。
