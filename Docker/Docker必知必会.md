## 联合文件系统

联合文件系统（[UnionFS](https://en.wikipedia.org/wiki/UnionFS)）是一种分层、轻量级并且高性能的文件系统，它支持对文件系统的修改作为一次提交来一层层的叠加，同时可以将不同目录挂载到同一个虚拟文件系统下(unite several directories into a single virtual filesystem)。

联合文件系统是 Docker 镜像的基础。镜像可以通过分层来进行继承，基于基础镜像（没有父镜像），可以制作各种具体的应用镜像。

另外，不同 Docker 容器就可以共享一些基础的文件系统层，同时再加上自己独有的改动层，大大提高了存储的效率。

## Docker数据管理

默认情况下，在容器内创建的所有文件都存储在可写容器层上。这意味着：

- 当该容器不再存在时，数据不会持久存在，并且如果另一个进程需要数据，则很难从容器中取出数据。

- 容器的可写层与运行容器的主机紧密耦合。您无法轻松地将数据移动到其他地方。

- 写入容器的可写层需要存储驱动程序来管理文件系统。 存储驱动程序使用 Linux 内核提供联合文件系统。 与使用直接写入主机文件系统的数据卷相比，这种额外的抽象降低了性能。

Docker 有两个选项供容器在主机上存储文件，以便即使在容器停止后文件也能持久保存：卷(*volumes*)和绑定挂载(*bind mounts*)。

Docker 还支持在主机内存中存储文件的容器。 此类文件不会持久保存。

### 选择正确的挂载方式

无论您选择使用哪种类型的挂载，数据在容器内看起来都是一样的。它在容器的文件系统中作为目录或单个文件公开。

可视化volumes, bind mounts, 和tmpfs挂载之间差异的一种简单方法是考虑数据在 Docker 主机上的位置。

![Types of mounts and where they live on the Docker host](asserts/types-of-mounts.png)

- **Volumes** 存储在由 Docker 管理的主机文件系统的一部分中（在 Linux 上为/var/lib/docker/volumes/）。非 Docker 进程不应修改文件系统的这一部分。**Volumes** 是在 Docker 中持久保存数据的最佳方式。

- **Bind mounts**可以存储在主机系统的任何位置。它们甚至可能是重要的系统文件或目录。 Docker 主机或 Docker 容器上的非 Docker 进程可以随时修改它们。

- **tmpfs mounts**挂载仅存储在主机系统的内存中，永远不会写入主机系统的文件系统。

