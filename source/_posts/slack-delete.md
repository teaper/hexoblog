---
title: 清理 Slack 空间
categories: [Slack]
tags: [Slack,Python3]
date: 2019-11-09 21:31:47
---
[Slack](https://nut-edu.slack.com/plans?ui_step=27&ui_element=11) 免费版工作区存储只有 5GB，一不小心就满了，特别是有大量图文信息发送之后！  
那么我们通过一个 Python3 脚本来实现批量文件清理，创建一个 `slack_delete.py` 文件，内容如下
```python
from urllib.parse import urlencode
from urllib.request import urlopen
import time
import json
import codecs
import datetime
from collections import OrderedDict

reader = codecs.getreader("utf-8")

# 在这里获取 Token 信息: https://api.slack.com/custom-integrations/legacy-tokens
token = '' 

# 设置它仅删除该用户的文件。 如果您不是管理员，则非常方便。
member_id= ''

# 文件列表的参数。 此处提供更多信息： https://api.slack.com/methods/files.list

# 删除早于此的文件：
days = 30
ts_to = int(time.time()) - days * 24 * 60 * 60

# 多少？（最大值为1000，否则默认为100）
count = 1000

# 要删除的文件类型，all 是全部
types = 'all'
# types = 'spaces,snippets,images,gdocs,zips,pdfs'
# types = 'zips'

def list_files(user= ''):
    params = {
        'token': token,
        'ts_to': ts_to,
        'count': count,
        'types': types,
        'user': user,
    }
    uri = 'https://slack.com/api/files.list'
    response = reader(urlopen(uri + '?' + urlencode(params)))
    return json.load(response)['files']

def greater_mb(file, mb):
    return file['size'] / 1000000 >= mb

def smaller_mb(file, mb):
    return file['size'] / 1000000 < mb

def filter_by_size(files, greater_or_smaller, mb):
    return [file for file in files if greater_or_smaller(file, mb)]

def info(file):
    order = ['Title', 'Name', 'Created', 'Size', 'Filetype',
             'Comment', 'Permalink', 'Download', 'User', 'Channels']
    info = {
        'Title': file['title'],
        'Name': file['name'],
        'Created': datetime.datetime.utcfromtimestamp(file['created']).strftime('%B %d, %Y %H:%M:%S'),
        'Size': str(file['size'] / 1000000) + ' MB',
        'Filetype': file['filetype'],
        'Comment': file['initial_comment'] if 'initial_comment' in file else '',
        'Permalink': file['permalink'],
        'Download': file['url_private'],
        'User': file['user'],
        'Channels': file['channels']
    }
    return OrderedDict((key, info[key]) for key in order)

def delete_files(files):
    num_files = len(files)
    file_ids = map(lambda f: f['id'], files)
    print('Deleting %i files'%num_files)
    for index, file_id in enumerate(file_ids):
        params = {
            'token': token,
            'file': file_id
        }
        uri = 'https://slack.com/api/files.delete'
        response = reader(urlopen(uri + '?' + urlencode(params)))
        print((index + 1, "of", num_files, "-",
               file_id, json.load(response)['ok']))

print('Retrieving files older than %s days'%(days))			   
			   
files = list_files(member_id)

print('Total %i files'%len(files))

files = filter_by_size(files, greater_mb, 50)   #匹配 50 M的文件

print('Match size %i files'%len(files))

#delete_files(files)  # 已注释掉，因此您不会意外运行它
```
这个脚本直接是不能的，需要修改一些参数，首先是 Slack 工作区的令牌，在 [https://api.slack.com/custom-integrations/legacy-tokens](https://api.slack.com/custom-integrations/legacy-tokens) 中创建一个 `Token`，复制过来，粘贴到脚本里
  
![](http://ww1.sinaimg.cn/large/006kWbIoly1g7mk6m8jvhj30hw05xt93.jpg)  
  
```python
token = 'xoxp-613920254549-603568988866-784157202429-b1cc470bc0a1d54248687a87dbb6a7ac'
```
设置删除多少天之前的文件，默认 30 天之前  
```bash
days = 30
```
选择要删除的类型，一般就是 `All` 全部，当然还有 `spaces`、`snippets`、`images`、`gdocs`、`zips`、`pdfs` 这些类型，我主要是清理图文信息，所以基本是清理图片，那就选择 `images`  
```python
types = 'images'
```
匹配的要删除的文件大小是多大，默认 50 M 以上  
```bash
files = filter_by_size(files, greater_mb, 0)       #建议改成 0
```
然后就是运行此脚本了，由于我是工作区所有者，所以我可以删除我和其他人的图片，如果你不是管理员，那就只能删除自己文件  
我是 Linux 系统，默认 Python 3 的版本，所以直接用命令运行脚本即可，Windows 用户请看[【运行python代码的指南】](http://stackoverflow.com/a/1527012/662761)  
```bash
python3 slack_delete.py     #运行命令

#--------------------输出日志------------------
Retrieving files older than 30 days  #检索超过30天的文件
Total 73 files      #共4个文件
Match size 73 files     #匹配大73个文件
Deleting 73 files       #删除73个文件
(1, 'of', 73, '-', 'FQ77N7YHK', True)
(2, 'of', 73, '-', 'FQ8RCPV8V', True)
(3, 'of', 73, '-', 'FQ94SHE3H', True)
(4, 'of', 73, '-', 'FPQBV1G93', True)
(5, 'of', 73, '-', 'FQ5PXFMP1', True)
...
```
确认无误之后，取消最后一行的注释，再运行一下脚本
