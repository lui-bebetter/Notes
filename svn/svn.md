# SVN常用命令

- #### `svn ls [TARGET[@REV]...]` 

   远程仓库目录详情，若TARGET为本地目录，则自动转化为对应的URL

- #### `svn info [TARGET[@REV]...]`  

  本地`working copy`(path)、远程仓库目录信息(URL)

- #### `svn import [PATH] URL -m log` 

  上传文件到远程仓库

- #### `svn up [PATH...]`

  `--set-depth ARG`

  `-r --revision ARG` 指定版本

  `--parents`

  `--accept ARG`

- #### `svn add PATH...`

  `--depth ARG` 默认infinity

  `--force` 可用于添加目录下所有unversioned的文件

  `--parents`

- #### `svn del (PATH... | URL... -m log)`

  `--force` 

  `--keep-local` 不删除本地文件

- #### `svn cp SRC[@REV]... DST`

  `SRC` `DST`任一项均可为PATH或URL

  `-r --revision ARG`

- #### `svn mv SRC... DST`

  `SRC` 和`DST`可以都为PATH或URL

- #### `svn mkdir (PATH... | URL...) `

- #### `svn status [PATH...]`

   `svn status -u -v`

   默认只输出本地有改动的文件信息

   `-u --show-updates` 输出远程仓库有更新的文件信息

   `-r --revision` 基于远程仓库版本

   `-v --verbose` 所有文件的详细信息

   

   

