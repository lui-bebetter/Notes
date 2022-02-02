# SVN常用命令

- #### `svn ls [TARGET[@REV]...]` 

   远程仓库目录详情，若TARGET为本地目录，则自动转化为对应的URL

- #### `svn cat TARGET[@REV]...`

   获取文件内容

   `-r --revision ARG`

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
  
- #### `svn diff`

   - `svn diff [PATH...]` 输出本地修改
   - `svn diff -c m [TARGET...]` 输出版本m的改动
   - `svn diff -r N[:M] [TARGET[@REV]...]` 两个版本之间的改动
   - `svn diff -r N[:M] --old=OLDTARGET --new=NEWTARGET` old@N 到new@M之间的差别

- #### `svn revert PATH...`

   undo, unschedule and resolve conflict

   `--depth ARG`

- #### `svn resolve PATH...`

   `--accept ARG` base, working, mine-conflict, theirs-conflict, mine-full, theirs-full

- #### `svn log `

   输出文件有变化的log信息

   - `svn log [PATH][@REV]`

      范围BASE:1

   - `svn log URL[@REV] [PATH...]`

      范围HEAD:1

      `-r --revision ARG`

      `-c --change ARG`

      `-v --verbose` 提交的具体文件信息

      `--diff` 显示diff

      `-l --limit ARG` log数

      `--stop-on-copy` 不显示copy history

      `--search PATTERN` match log

    `svn log -r BASE:HEAD foo`显示最新修改

- #### `svn blame [-r M:N] TARGET[@REV]...`

-  #### `svn propset`

   - `svn propset PROPNAME PROPVAL PATH...`

      设置文件或目录的属性

   - `svn propset --revprop -r REV PROPNAME PROPVAL [TARGET]`

      设置版本的属性，TARGET指定仓库路径

      `-F --file` 属性值从文件读取

- #### `svn propedit`

   - `svn propedit PROPNAME TARGET...`

      修改本地或远程文件的属性

   - `svn propedit --revprop -r REV PROPNAME [TARGET]`

      修改版本的属性
   
- #### `svn proplist`

   - `svn proplist [TARGET[@REV]...]`

      列举文件属性

   - `svn proplist --revprop -r REV [TARGET]`

      列举版本属性

      `-v --verbose` 输出属性值

- #### `svn propget`

   - `svn propget PROPNAME [TARGET[@REV]...]`
   - `svn propget PROPNAME --revprop -r REV [TARGET]`
   
- #### `svn propdel`

   - `svn propdel PROPNAME [PATH...]`
   - `svn propdel PROPNAME --revprop -r REV [TARGET]`

- #### `svn patch PATCHFILE [PATH]`

   - `--dry-run`
   - `--strip ARG` strip path
   - `--reverse-diff`
   - `--ignore-whitespace`	

- ## References

  [1]: https://svnbook.red-bean.com/en/1.7/index.html	"SVN Documentation"
  
    

