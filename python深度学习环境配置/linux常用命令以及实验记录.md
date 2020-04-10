# linux 常用命令以及实验记录

```
# 挂载硬盘方法
# 查看硬盘信息
sudo fdisk -l
# /dev/sdb1 3.7T Linux filesystem

# 将硬盘挂载到 /media/sata4t 目录下
sudo mount /dev/sdb1 /media/sata4t

# 创建软连接
ln -s /media/sata4t ~/
```

```
# 指定 8888 端口并后台启动 jupyter notebook
nohup jupyter notebook --port 8888 &

# 实时查看 log
tail -f nohup.out

# 查看进程
ps ux | grep jupyter

ahtcm 4985 1.6 0.1 246532 66180 pts/2 Sl 12:25 0:01 /home/ahtcm/anaconda3/bin/python /home/ahtcm/anaconda3/bin/jupyter-notebook

# 关闭 jupyter notebook PID 进程
kill -9 4985
```

```
# 查看 GPU 显存使用情况，杀掉指定 GPU 进程
kill -9 PIDNum

# 实时查看 GPU 使用率：
watch -n 1 nvidia-smi
```

```
# 将本地文件拷贝到远程
scp (-r) 本地文件 远程服务器用户名@远程服务器ip地址:指定拷贝到远程服务器的路径

# 将远程文件拷贝到本地
scp (-r) 远程服务器用户名@远程服务器ip地址:远程服务器文件 指定拷贝到本地电脑的路径
```


```
# zip 压缩命令
zip (-r) test.zip test/
```


```
# 查看小文件
cat info.txt
# 查看多屏大文件
more info.txt
```


```
# 连续创建多个目录，-p 表示创建所依赖的文件夹
mkdir -p python_pro/com/winston/

# 以文件树的形式展示目录结构
tree test
```

more 查看大文件的操作键说明：

| 操作键 | 说明           |
| ------ | -------------- |
| 回车   | 显示下一行信息 |
| 空格 或 d   | 显示下一屏信息 |
| b      | 显示上一屏信息 |
| q      | 退出           |


vim 操作快捷键：
| 操作键 | 说明           |
| ------ | -------------- |
| G(shift + g)   | 跳转到最后一行 |
| shift + 4  | 跳转到当前行的最后一个字符 |
| shift + z + z  | 保存并退出 |

---
实验记录：

```
多 GPU 训练报错提示：
RuntimeError: It looks like you are subclassing `Model`
and you forgot to call `super(YourClass, self).__init__()`.

Solved by changing code in mrcnn/parallel_model.py as the following：
class ParallelModel(KM.Model):
    def __init__(self, keras_model, gpu_count):
        """Class constructor.
        keras_model: The Keras model to parallelize
        gpu_count: Number of GPUs. Must be > 1
        """
        super(ParallelModel, self).__init__()
        self.inner_model = keras_model
        self.gpu_count = gpu_count
        merged_outputs = self.make_parallel()
        super(ParallelModel, self).__init__(inputs=self.inner_model.inputs,
                                            outputs=merged_outputs)
```

