- # `diff`

  - ## 几种输出格式

    - ### normal format

      默认格式

    - ### context format

      `--context=[lines] | -C lines | -c `

    - ### unified format

      `--unified=[lines] | -U lines | -u`

    - ### side by side

      `--side-by-side | -y`

  - ## 一些参数

    - ### `--show-function-line | -F`
    
  - ## 目录比较
  
    - ### `-s --report-identical-files`
  
      无变化文件默认无输出，开启本选项后会有输出
  
    - ### `-r --recursive`
  
      默认子目录不递归，开启本选项后递归
  
    - ### `--new-file -N`
  
    - ### `--unidirectional-new-file`
  
    - ### `--exclude=pattern -x pattern`
  
    - ### `exclude-from=file - X file`
  
    - ### `--ignore-file-name-case`
  
    - ### `-- starting-file=file -S file`
  
      只比较file和按字母顺序在file之后的文件`
  
- # `diff3`
  
  - ## `diff3 -e mine older yours`
  
    mine + yours - older
  
  - ## `diff3 -3 mine older yours`
  
    mine + yours - older，only加上重叠的改动
  
  - ## `diff3 -x mine older yours`
  
    mine + yours - older，only加上不重叠的改动
  
- # patch
  
  `patch options [origfile [patchfile]]`
  
  ## options:
  
  - ### `-R --reverse`    
  
    `[-hunk for hunk in patch]`
    
  - ### `-b --backup`
  
    备份源文件
  
  - ### `-z backup-suffix --suffix=backup-suffix`
  
    备份文件后缀
  
  - ### `-l --ignore-white-space`
  
    any nonempty sequence of blanks in the patch file matches any nonempty sequence of blanks
    
  - ### `-F --fuzz=n`
  
    ignore the first n and last n lines of context
    
  - ### `--dry-run`
  
    只输出结果到标准输出，不实际改变文件，可用于测试
  
  - ### `--verbose`
  
    输出详细信息
    
  - ### `--set-utc -Z`
  
    默认设置文件更新时间为当前时间，设置本选项后设置为diff header中指定更新时间
  
- # 一般使用场景
  
  - `diff -Naur old new`
  - `patch -p1 -i patch`
  
- # References

  [1]: https://www.gnu.org/software/diffutils/manual/html_node/index.html#SEC_Contents	"Comparing and Merging Files"

