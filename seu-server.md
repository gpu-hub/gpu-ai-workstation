## 学生账号审批流程
### 学生开通账户（导师已开通）
   
   1. **登录**  
      校园网/VPN 打开 [`https://sc.seu.edu.cn/`](https://sc.seu.edu.cn/) → 用 **统一身份认证** 登录。
   
   2. **申请账户**  
      登录后 → 搜索导师姓名 → 提交 **学生账户申请**。
   
### 导师审批
   
   1. **登录**  
      校园网/VPN → [`https://sc.seu.edu.cn/`](https://sc.seu.edu.cn/) → **一卡通账户** 登录。
   
   2. **审批**  
      驾驶舱 → **我的待办审批** → 审核学生账户申请。
   
   ## 相关链接
   - 登陆平台：https://sc.seu.edu.cn/usr/login
   - 提交工单：https://mcnta95cacii.feishu.cn/share/base/form/
   shrcn7bzrP8qDtLyDOdov0siQch
   - 收费标准：http://bdcc.seu.edu.cn/204/11/2/news.html
   - 申请账户：https://bdcc.seu.edu.cn/4/23/list.html
   - 用户手册： https://sc.seu.edu.cn/docs/
   - 作业平台教程：
   https://www.bilibili.com/video/BV1enCEYWENy/
   
   - 常用软件下载链接
      - FreeOTP：https://freeotp.github.io/
      - xshell：https://www.xshell.com/zh/free-for-home-school/
      
## 使用教程
### conda 创建并激活自己的conda环境
   ```bash
   module load anaconda3-2024.10-1    #加载anaconda3环境变量
   conda create --name XXX python=XXX   #创建环境并指定python版本
   source activate XXX      #激活创建的环境
   ```
   
   conda create --name pytorch python=3.9
   
   GPU任务推荐使用/seu_nvme目录（在终端中可以使用sd命令快速进入），每组限额500G