### 描述

python自带的RotatingFileHandler，在windows中运行, 分割文件rename时会出问题。因为windows不支持rename正在使用的文件

### 解决办法

自定义handler, 按大小和日期切割文件

原理: handler写文件时调用emit, 其中shouldRollover判断是否要分割, doRollover进行分割. 重写这个两个函数就可以

```python
class BaseRotatingHandler(logging.FileHandler):

    def emit(self, record):
        """
        Emit a record.

        Output the record to the file, catering for rollover as described
        in doRollover().
        """
        try:
            if self.shouldRollover(record):
                self.doRollover()
            logging.FileHandler.emit(self, record)
        except Exception:
            self.handleError(record)
```

### 示例

```python

import os
import time
import datetime
import logging
from logging.handlers import RotatingFileHandler
from pathlib import Path


class AutumnRotatingFileHandler(RotatingFileHandler):
    """
    日志+大小+支持windows
    """

    def __init__(self, filename, mode='a', maxBytes=0, backupCount=99, encoding=None, delay=False, backupDayCount=30):
        filename = str(filename)
        self.date_format = "%Y%m%d"
        self.create_date = self._now_date()
        self.backupCount = backupCount

        # 保存原始文件名
        self.filename = str(Path(filename).absolute())
        self.backupDayCount = backupDayCount

        RotatingFileHandler.__init__(self, self.last_file_name(filename), mode=mode, maxBytes=maxBytes, backupCount=backupCount, encoding=encoding, delay=delay)

    def _now_date(self):
        return time.strftime(self.date_format, time.localtime())

    def doRollover(self):
        """
        Do a rollover, as described in __init__().
        """
        # 关闭当前文件
        if self.stream:
            self.stream.close()
            self.stream = None

        # 生成最新文件名
        suffix = "."+ self._now_date()
        self.baseFilename = str(self.filename) + suffix
        if self.backupCount > 0:
            for i in range(0, self.backupCount):
                number_suffix = "{:0>2d}".format(i)
                sfn = ".".join([self.baseFilename, number_suffix])
                if os.path.exists(sfn):
                    continue
                else:
                    break
            number_suffix = "{:0>2d}".format(i)
            self.baseFilename = ".".join([self.baseFilename, number_suffix])

        # 删除过期文件
        self.deleteExpiredFiles()

        # 打开
        self.stream = self._open()
        self.create_date = self._now_date()

    def shouldRollover(self, record):
        """
        Determine if rollover should occur.

        Basically, see if the supplied record would cause the file to exceed
        the size limit we have.
        """
        # 文件分割条件1: 日期变化
        if self._now_date() != self.create_date:
            return 1

        if self.stream is None:                 # delay was set...
            self.stream = self._open()

        # 文件分割条件1: 文件大小超过限制
        if self.maxBytes > 0:                   # are we rolling over?
            msg = "%s\n" % self.format(record)
            self.stream.seek(0, 2)  #due to non-posix-compliant Windows feature
            if self.stream.tell() + len(msg) >= self.maxBytes:
                return 1
        return 0

    def deleteExpiredFiles(self):
        """
        删除过期文件
        """
        dead_datetime = datetime.datetime.now() - datetime.timedelta(days=self.backupDayCount)

        filenames = []
        for i in range(1, 3):
            date = dead_datetime - datetime.timedelta(days=1)
            date_str = date.strftime(self.date_format)

            base_name = "%s.%s" % (self.filename, date_str)
            filenames.append(base_name)

            for j in range(1, self.backupCount):
                sfn = "%s.%d" % (base_name, j)
                filenames.append(sfn)

        # delete file
        for filename in filenames:
            if os.path.exists(filename):
                try:
                    os.remove(sfn)
                except:
                    pass

    def last_file_name(self, filename):
        """
        文件名
        """
        suffix = "."+self._now_date()
        base_filename = str(filename) + suffix
        if self.backupCount > 0:
            for i in reversed(range(0, self.backupCount)):
                number_suffix = "{:0>2d}".format(i)
                sfn = ".".join([base_filename, number_suffix])
                # 最近存在的文件
                if os.path.exists(sfn):
                    break
                else:
                    continue
            number_suffix = "{:0>2d}".format(i)
            base_filename = ".".join([base_filename, number_suffix])
        return base_filename

```

[佛說大乘無量壽莊嚴清淨平等覺經pdf](http://doc.sxjy360.top/book/佛說大乘無量壽莊嚴清淨平等覺經(難字注音).pdf)
[净土大经科注2014-doc](http://doc.sxjy360.top/book/净土大经科注2014-doc.zip)
[此生必看的科学实验-水知道答案](http://v.youku.com/v_show/id_XMjgzMzcwNDk4OA)
[印光大师十念法（胡小林主讲第1集）](http://v.youku.com/v_show/id_XMzUwMzc4NzY4NA)