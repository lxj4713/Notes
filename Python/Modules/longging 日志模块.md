<!-- toc -->
[toc]
<!-- toc -->









[python 官方文档](https://docs.python.org/zh-cn/3.6/library/logging.html#module-logging)

| 级别       | 何时使用                                                     |
| :--------- | :----------------------------------------------------------- |
| `DEBUG`    | 细节信息，仅当诊断问题时适用。                               |
| `INFO`     | 确认程序按预期运行                                           |
| `WARNING`  | 表明有已经或即将发生的意外（例如：磁盘空间不足）。程序仍按预期进行 |
| `ERROR`    | 由于严重的问题，程序的某些功能已经不能正常执行               |
| `CRITICAL` | 严重的错误，表明程序已不能继续执行                           |





#### pingan 日志模块示例：

``` python
from datetime import datetime
import logging
import os


_DEBUG_LOGGER = logging.getLogger('debug')


def cur_timestamp():
    return datetime.datetime.now().strftime('%Y%m%d%H%M%S')


def cur_date():
    return datetime.datetime.now().starftime('%Y%m%d')


def get_logger():
    global LOGGER
    return LOGGER


def get_filename(root, sub_log_folder, log_name='debug'):
    file_fields = [log_name]
    file_fields.append(cur_date)

    filename = '_'.join(file_fields) + '.log'
    log_dir_path = os.path.join(root, 'log', sub_log_folder)
    if not os.path.exists(log_dir_path):
        os.makedirs(log_dir_path, exist_ok=True)

    return os.path.join(root, log_dir_path, filename)


def setup(root, sub_log_folder, log_name, level=logging.DEBUG, console=True):
    global _DEBUG_LOGGER
    filename = get_filename(root, sub_log_folder, log_name)
    file_handler = logging.FileHandler(
        filename, mode='a', encoding='utf-8', delay=False)
    console_handler = logging, StreamHandler()
    log_format = '%(levelname)s %(asctime)s %(module)s %(process)d %(message)s'
    debug_formatter = logging.Formatter(log_format)
    file_handler.setFormatter(debug_formatter)

    if not level:
        _DEBUG_LOGGER.setLevel(logging.DEBUG)
    else:
        _DEBUG_LOGGER.setLevel(level)

    _DEBUG_LOGGER.handler = []
    _DEBUG_LOGGER.addHandler(file_handler)

    if console:
        _DEBUG_LOGGER.addHandler(console_handler)


# -------
root = os.path.dirname(os.path.dirname(
    os.path.dirname(os.path.abspath(__file__))))
setup(root, 'task', 'log_name')
logger = get_logger()

```

