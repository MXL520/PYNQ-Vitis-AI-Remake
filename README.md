月度项目 | 采用 PYNQ 和 Vitis AI 的智能办公解决方案 复现V1.0

表示文字部分
表示文件、地址和指令部分
表示网址部分
双删除线表示本人试错的废弃操作


目录
一、 使用 Vitis AI 升级到最新的PYNQ	1
二、 使用 Vitis AI 升级到最新的PYNQ(续)	3
三、 测试 USB 网络摄像头功能	4
四、 为 Ultra96-V2 编译 DPU-PYNQ	4
五、 从 Vitis 模型库编译 YoloV3	8
六、 Notebook 示例	12
七、 目前的总结	14

一、使用 Vitis AI 升级到最新的PYNQ
1.假如报unable to lock the administration错误的解决查看下面的网址
https://blog.csdn.net/gouxf_0219/article/details/80594554，杀死所有进程即可

2.现在想解决installing build dependencies的问题，会一直开在这里不动

①我想到到就是看dpu.py文件到import sys的问题(但感觉又不是，因为只是导入库而已)
或者卸载python2(已卸载)
问了学长，他给的建议是前面proxychain4(无用)
或者换一个源(未尝试)
②python pip命令安装pyinstaller失败提示Installing build dependencies ... error
https://blog.csdn.net/qingfengjuechen/article/details/102576515
或许我可以学习上面这篇文章进行手动安装
即：如何在Ubuntu系统下手动安装python第三方库
https://www.cnblogs.com/realwuxiong/p/13410291.html
直接运行python3 setup.py install
3.缺少了一个pynq.utils的文件，现在我直接拿读卡器在电脑上给它传过去，看看能不能行
pip安装第三方模块老是报错？多种常见错误，进来看看解决方案！
https://blog.csdn.net/weixin_46847476/article/details/105346569

4.有运行结果了说明可以，但是又报错error:invalid command ‘download overlays’
查了invalid command，说的是无效命令行，说明有问题的是‘download overlays’
①可能热点没开？但是连了热点也没有效果
②把老师给到DPU-PYNQ也换上来，发现也没有效果，再看以下DPU-PYNQ包里面到setup.py文件
③不知道咋搞，应该是要换Ultra96-V2的2.6版本到镜像看看？

5.make一直也卡在来xrt那里，现在准备换镜像了。


二、使用 Vitis AI 升级到最新的PYNQ(续)
1.想下ultra96 v2的2.6版本，但是始终打不开网页
https://blog.csdn.net/qq_38863842/article/details/119450384
现已在官网下好。

2.用balenaEtcher烧写SD卡。

3.在网址192.168.3.1按之前写的代码连接wifi。(感觉中午的网会好很多)，也可能是镜像版本(大概率)更新到问题，现在就很顺畅。
虽然中途有报错，但是最后还是成功地下载完成了。

三、测试 USB 网络摄像头功能
1.摄像头测试完成了，按照那个
https://mp.weixin.qq.com/s/CMeFr1Pg9D1g1jCF3qC9qA
上面说的指令。

四、为 Ultra96-V2 编译 DPU-PYNQ
1.开始编译
①在 boards 文件夹中，编辑 check_env.sh，已经不2019.2来，而是将2020.2改成2020.1
②component.xml的路径已经更改/home/wr/Documents/DPU-PYNQ/vitis-ai-git/dsa/DPU-TRD/dpu_ip/DPUCZDX8G_v3_3_0，可以见图片。在最后面将2020.2改成2020.1

③将RAM_USAGE_LOW改为RAM_USAGE_HIGH

④第一个source /opt/Xilinx/Vitis/2020.1/settings64.sh不可以，而是应该去找你下载Vitis的地方，我的是/home/wr/Vitis/Vitis/2020.1/settings64.sh


2.source ~/.bashrc修改bashrc，立即刷新
①发现出现WARNING: /bin/sh is not bash!
https://blog.csdn.net/qq_42870876/article/details/114666508
试试sudo dpkg-reconfigure dash，然后选择no
②WARNING: No tftp server found - please refer to "UG1144 2020.1 PetaLinux Tools Documentation Reference Guide" for its impact and solution，这个文章有详细地讲解https://www.cnblogs.com/likaiwei/p/10223354.html，所以需要我们查找PetaLinux2020.1的手册，发现No tftp server found，结果手册上说“联系什么系统管理员”，执行第③解决了。
③ubuntu开启TFTP服务和NFS服务
https://blog.csdn.net/viola_learing/article/details/116059080
https://blog.csdn.net/hahachenchen789/article/details/52494467
先sudo apt-get install tftpd-hpa，然后在/etc/default中找到tftpd-hpa，第二项说明了tftpboot的根目录在"/var/lib/tftpboot"，然后在/var/lib到终端执行sudo chmod 777 tftpboot

④第三项tftp地址是主机的ip地址，设置好保存即可。(但我不知道该如何设置，先放一放)，然后sudo service tftpd-hpa restart，然后再source ~/.bashrc，果然没有WARNING了。

4.在~/Documents/DPU-PYNQ/boards$ make BOARD=Ultra96，就是你DPU-PYNQ/boards下面执行该指令$ make BOARD=Ultra96之前需要一些步骤。
①/home/wr/Documents/DPU-PYNQ/boards/vitis_platform/dpu，将2020.2改成2020.1
②【FPGA】Vivado综合停滞、死机（PID Not Specified）解决方法
https://blog.csdn.net/weixin_59105807/article/details/119975287
它上面说重新安装Vivado，但是不现实，上次装一个花我5小时。再找找别的方法。
③注释#source /home/wr/Petalinux/settings.sh，看看有没有用(无用)






5.看看这个网址，这是别人一套到流程。
https://blog.csdn.net/u010879745/article/details/109477145
看看有什么错误，或者有什么启发。
①按照说明https://github.com/Xilinx/DPU-PYNQ/blob/master/boards/README.md
执行来如下操作还是无用：
cd DPU-PYNQ
git submodule init
git submodule update
②想到两个问题vivado的licence问题(试过了还是无用)、在他所构建到board里面比我多出来PYNQ-derivative-overlays包（不知道他哪里搞来的，但是应该就是这个）

6.开始一条条找warning。
①WARNING: [Vivado 12-818] No files matched '../../vitis-ai-git/dsa/DPU-TRD/dpu_ip/DPUCZDX8G_v3_3_0/ttcl/timing_impl_clocks_xdc.ttcl'
②WARNING: [IP_Flow 19-5661] Bus Interface 'ap_clk_2' does not have any bus interfaces associated with it.
WARNING: [IP_Flow 19-3157] Bus Interface 'ap_rst_n_2': Bus parameter POLARITY is ACTIVE_LOW but port 'ap_rst_n_2' is not *resetn - please double check the POLARITY setting.
由下述CSDN上说可以不用管它https://blog.csdn.net/Markus_xu/article/details/114388715（废弃，百度大部分搜不到）

7.开始下载PYNQ-derivative-overlays的包放到/home/wr/Documents/DPU-PYNQ/boards，再次编译$ make BOARD=Ultra96
①发现仍然是之前的结果，存放在了pynq_make_error2.txt当中
②意外发现，在INFO: [Vivado 12-6066] Finished running validate_dsa for file: './dpu.xsa'，看到它已经生成了dpu.xsa文件，所以网想看看这是什么，解压之后发现，在/home/wr/Documents/DPU-PYNQ/boards/vitis_platform/dpu里面存放有dpu.bit(/home/wr/Documents/DPU-PYNQ/boards/vitis_platform/dpu)、dpu.hwh(/home/wr/Documents/DPU-PYNQ/boards/vitis_platform/dpu/dpu/prj/sources_1/bd/dpu/hw_handoff)、dpu.xclbin(/home/wr/Documents/DPU-PYNQ/vitis-ai-git/dsa/DPU-for-RNN/xclbin/u25)，那死马当活马医，试试这三个可用否


五、从 Vitis 模型库编译 YoloV3
1.从 Vitis 模型库编译 YoloV3
①运行cp -rf ../vitis-ai-git/docker/PROMPT.txt docker时/home/wr/Documents/DPU-PYNQ/vitis-ai-git/setup/docker/docker中才有PROMPT.txt，复制这个过去就好
②运行./docker_run.sh xilinx/vitis-ai-cpu:latest，出现了无法读取的情况。

③按这个网址https://gitee.com/lirui2017/Vitis-AI走一遍。
④先按https://docs.docker.com/engine/install/ubuntu/把docker重新下载一遍


⑤再执行https://gitee.com/lirui2017/Vitis-AI上面到操作


Do you agree to the terms and wish to proceed [y/n]? y
find: ‘/dev/dri’: 没有那个文件或目录(握自己建了一个文件夹，但是与项目所给到图片不符)
⑥所有的./docker_run.sh xilinx/vitis-ai-cpu:latest前面必须加sudo(明明也按照说明给了权限，但就是不行)即sudo ./docker_run.sh xilinx/vitis-ai-cpu:latest
⑦它说我的/var没空间下载来，现在转移到/home里面
sudo cp -R /var/cache /home/cache
sudo cp -R /var/lib /home/lib
sudo ln -s /home/var /var(行)
sudo ln -s /var /home/var(不行)
⑧下次建议装系统的时候给/var多分布一点，我只给来10G
sudo rm -rf /var/log/journal/6868c4b083854ed7ad99204d475cf185,删除了好多日志文件
没有报存储不够到错误，但是报来这个：
docker: Error response from daemon: OCI runtime create failed: /var/lib/docker/overlay2/a2919a269cc25edac8a742b027fdab75c8f027dd1a0f7bbdde1b6c640427d4d6/merged is not an absolute path or is a symlink: unknown.
https://blog.csdn.net/weixin_30710457/article/details/95471146这个博客说需要重启docker，试一试。
service docker stop
service docker start
果然好了，只是有好多红色


2.它模型tf_yolov3_voc_416_416_65.63G_1.1也没有给我，去docunment找到了，/home/wr/Documents/DPU-PYNQ/vitis-ai-git/models/AI-Model-Zoo/model-list里面有tf_yolov3_voc_416_416_65.63G_1.3、/home/wr/Documents/xilinx/Vitis-AI/models/AI-Model-Zoo/model-list里面有tf_yolov3_voc_416_416_65.63G_1.4，这时我选择来1.3去编译，看看能否成功
①将tf_yolov3_voc_416_416_65.63G_1.3版本复制到host当中
之后是网打开compile.sh文件发现你没下到话通过编译好像会自动下载
②wr@wr-Lenovo-Legion-Y7000:~/Documents/DPU-PYNQ/host$ cp ../boards/Ultra96/dpu.hwh ./
③执行./compile.sh Ultra96 tf_yolov3_voc_416_416_65.63G_1.3，提示./compile.sh: 行 26: /etc/profile.d/conda.sh: 没有那个文件或目录
④突然发现我的WIN10的E盘里面有安装Anacoda，把里面到扒过来。
wr@wr-Lenovo-Legion-Y7000:/media/wr/Silence/Anaconda/etc/profile.d$ sudo cp conda.sh /etc/profile.d
wr@wr-Lenovo-Legion-Y7000:/media/wr/Silence/Anaconda/etc/profile.d$ sudo cp conda.csh /etc/profile.d
发现不行，里面的环境配置不相同。
⑤特别提及一点：请不要从Windows上直接发送到Ubuntu，会改变文件的md5。也不要用手机去下然后再传电脑上。
⑥开始下载anaconda
wget https://repo.anaconda.com/archive/Anaconda3-2020.02-Linux-x86_64.sh
sudo sh Anaconda3-2020.02-Linux-x86_64.sh安装完之后再把那个文件复制过去试试
⑦执行完Could not find conda environment: vitis-ai-tensorflow
这个时候就应该反思是之前第11点编译的问题，所以需要卸载anaconda环境
这个帖子有教如何卸载https://www.cnblogs.com/jisongxie/p/10053760.htm
⑧终于知道了，Vitis-AI /workspace > ./compile.sh Ultra96 tf_yolov3_voc_416_416_65.63G，要在那个工作空间当中运行，而且运行tf_yolov3_voc_416_416_65.63G_1.3和tf_yolov3_voc_416_416_65.63G_1.1均会报错，所以千万不要全信那篇文章。
⑨结果现在PYNQ更新导致并没有生成elf文件，而生成了xmodel文件。

⑩按照其说明upload到jupyter上。
六、Notebook 示例
1.那篇文章中说，pynq_dpu/dpu_yolo_v3.ipynb有个dpu_yolo_v3.ipynb示例，但是在最新版本的pynq当中已经没有了这个示例。
①模型elf文件变成了xmodel文件，而我在https://gitee.com/ss9701/DPU-PYNQ，这个网址中找到了dpu_yolo_v3.ipynb，下载并放至jupyter上。

②将里面的“dpu_tf_yolov3.elf”修改为“/home/xilinx/jupyter_notebooks/pynq-dpu-1/tf_yolov3.xmodel”，并且“run all”之后发现报错了。
(可能原因1：pynq与之前的版本不符，该程序已经不能用了)
(可能原因2：因为之前Vitis-AI运行就有红色，编译生成的xmodel不符合要求)

2.为了验证pynq-dpu当中的例程正确，选择了“pynq-dpu/dpu_tf_inceptionv1.ipynb”,修改运行程序，让其验证2张图片，结果均正确。





七、目前的总结
1.在第四节里面为 Ultra96-V2 编译 DPU-PYNQ失败未解决，不确定我自己搜索到的dpu.bit、dpu.hwh和dpu.xclbin文件是否正确，因为在生成的过程当中有所报错，详情见pynq_make_error2.txt；
2.在第六节里面未能实现dpu_yolo_v3.ipynb该程序未能实现。可能原因见P12；
3.能够实现dpu_tf_inceptionv1.ipynb，因为用的是已存在的模型，验证结果均正确。
