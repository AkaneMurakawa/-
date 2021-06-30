# 4 自动审核脚本

## 前言

记得开始学Python的时候，不知有什么用途，听别人说可以爬虫，就跑去学了基本语法。结果真正去爬取东西的时候 ，就一脸懵逼了，根本不知道为什么要这样做。到后来也没什么用得到Python的地方，直到最近需要编写一个Archery自动审核的脚本，我才想起了Python。

当然，脚本是已经好了，只不过是Perl语言写的，我只是简单的翻译成Python。本以为过程很简单，但是中间却也遇到了一些问题。

## 过程和遇到的问题

实现的思路很简单，就是模拟请求，分以下几个步骤：

1. 登录
2. 查询
3. 审核
4. 执行
5. 发送钉钉通知

细节部分就是，访问登录页，获取cookie，进行登录。登录后需获取seesionid，每次访问在请求head中带上cookie和seesion。

在进行审核和执行时，需带上密钥`csrfmiddlewaretoken`，密钥在详情页面获取。

遇到的问题：

1. 需要从返回的响应报文中解析cookie、token、密钥，在这里我用最笨的方法：split。本想用正则，但是试了几次没成功，由于时间关系就用了最笨却有用的办法。
2. 需设置请求行、请求头、请求体。在这里请求体中参数有个密钥`csrfmiddlewaretoken`，密钥从何而来是比较难找的地方。
3. 调试困难，由于没有搭建本地Archery环境，当调试多次后就被限制了。
4. 钉钉通知这里，@某人遇到了坑，好在看官方文档马上解决了。必须在text中设置@mobile

优化：

1. 在这里设置为十分钟执行一次，是通过Python的sleep，可以改进用定时任务去执行。

### 示例

```python
# !/usr/bin/python3
# -*- coding: utf-8 -*-

"""
auto archery

:copyright: (c) 2020 by AkaneMurakwa.
:date: 2020-11-27
"""

import requests
import datetime
import time
import json

# demo
# url: https://demo.archerydms.com
# username/password: archer / archer
SERVER_URL = 'https://demo.archerydms.com'
USERNAME = 'archer'
PASSWORD = 'archer'
DING_TALK_URL = 'https://oapi.dingtalk.com/robot/send?access_token=xxxxxx'
MOBILE = {
    'xxxxx': '12345678991'
}


# constant variable
LOGIN_URL = SERVER_URL + '/login/'
Referer = SERVER_URL + "/sqlworkflow/"
AUTHENTICATE_URL = SERVER_URL + '/authenticate/'
SQL_WORK_FLOW_URL = SERVER_URL + '/sqlworkflow_list/'
SQL_DETAIL_URL = SERVER_URL + '/detail/'
SQL_AUDIT_URL = SERVER_URL + '/passed/'
SQL_EXECUTE_URL = SERVER_URL + '/execute/'
LIMIT = 20


def run():
    login()


def login():
    """
    login to archery
    :return:
    """
    print('============================================Auto Archery===========================================')
    print('start login ', SERVER_URL)
    login_response = requests.get(LOGIN_URL)
    print('login response : ', login_response)

    headers = {
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/80.0.3987.132 Safari/537.36',
        'Content-Type': 'application/x-www-form-urlencoded',
        'Connection': 'keep-alive',
        'X-Requested-With': 'XMLHttpRequest',
        'Sec-Fetch-Dest': 'empty',
        'Sec-Fetch-Mode': 'cors',
        'Referer': Referer
    }
    data = {'username': USERNAME, 'password': PASSWORD}
    set_cookie(headers, login_response)
    response = requests.post(AUTHENTICATE_URL, headers=headers, data=data)
    response.raise_for_status()
    print(response.text)
    if response.text.split(',')[0].split(':')[1].strip() == '1':
        print('login fail, msg: ', response.content.get('msg'))
        return

    set_cookie_token(headers, response)
    query(headers, 0)


def set_cookie(headers, response):
    """
    get cookie and resolve CSRFToken for request headers
    :param headers:
    :param response:
    :return:
    """
    cookie = response.headers.get('Set-Cookie').split(";")[0]
    csrftoken = cookie.split('=')[1]
    headers['X-CSRFToken'] = csrftoken
    headers['Cookie'] = cookie


def set_cookie_token(headers, response):
    """
    get cookie (contains session id) and resolve CSRFToken for request headers
    :param headers:
    :param response:
    :return:
    """
    cookie = response.headers.get('Set-Cookie').split(";")[0]
    sessionid = ''
    if len(response.headers.get('Set-Cookie').split(';')) > 3:
        sessionid = '; ' + response.headers.get('Set-Cookie').split(';')[3].split(',')[1].strip()
    csrftoken = cookie.split('=')[1]
    headers['X-CSRFToken'] = csrftoken
    headers['Cookie'] = cookie + sessionid


def query(headers, offset):
    """
    only query today sql workflow
    something you know:
        param 'search' is keyword
        param 'navStatus' is status about sql workflow, 'workflow_manreviewing' means 'wait to audit'
    :param headers:
    :param offset: it means pageNum
    :return:
    """
    date_time = datetime.date.today()
    data = {
        'limit': LIMIT,
        'offset': offset,
        'navStatus': 'workflow_manreviewing',
        'instance_id': '',
        'group_id': '',
        'start_date': date_time,
        'end_date': date_time,
        'search': '',
    }
    headers['Sec-Fetch-Site'] = 'same-origin'
    response = requests.post(SQL_WORK_FLOW_URL, headers=headers, data=data)
    try: 
        total = response.json().get('total')
    except:
        print('err: ', response.text)
        return
    print('query workflow list, total:', total)
    if total == 0:
        print('query result, total 0')
        return

    rows = response.json().get('rows')
    for row in rows:
        set_cookie_token(headers, response)
        detail(headers, row)

    # next page
    if (offset+1) * LIMIT <= total:
        time.sleep(2)
        set_cookie_token(headers, response)
        query(headers, offset+1)


def detail(headers, row):
    """
    the detail of workflow id
    :param headers:
    :param row:
    :return:
    """
    # check something, syntax_type == 2 means DML, syntax_type == 1 means DDL, syntax_type == 0 means other
    if row['syntax_type'] != 2:
        print('unsupported audit type')
        return
    if row['engineer_display'] not in MOBILE:
        print('user not in the auto audit list')
        return

    # query detail for setting csrfmiddlewaretoken
    workflow_id = row['id']
    response = requests.get(SQL_DETAIL_URL + str(workflow_id), headers=headers)
    csrfmiddlewaretoken = response.text.split('form')[2].replace(' ', '').split('csrfmiddlewaretoken')[1].split('\'/>')[0].split('value=\'')[1]

    set_cookie_token(headers, response)
    audit(headers, csrfmiddlewaretoken, row)


def audit(headers, csrfmiddlewaretoken, row):
    """
    audit sql
    :param headers:
    :param csrfmiddlewaretoken:
    :param row:
    :return:
    """
    workflow_id = row['id']
    data = {
        'csrfmiddlewaretoken': csrfmiddlewaretoken,
        'workflow_id': workflow_id,
        'audit_remark': 'auto audit by python3',
        'btnPass': ''
    }
    try:
        response = requests.post(SQL_AUDIT_URL, headers=headers, data=data)
        csrfmiddlewaretoken = response.text.split('form')[2].replace(' ', '').split('csrfmiddlewaretoken')[1].split('\'/>')[0].split('value=\'')[1]
    except:
        print('SQL audit fail')
        return
    set_cookie_token(headers, response)
    execute(headers, csrfmiddlewaretoken, row)


def execute(headers, csrfmiddlewaretoken, row):
    """
    execute sql
    :param headers:
    :param csrfmiddlewaretoken:
    :param row:
    :return:
    """
    workflow_id = row['id']
    data = {
        'csrfmiddlewaretoken': csrfmiddlewaretoken,
        'mode': 'auto',
        'workflow_id': workflow_id
    }
    try:
        response = requests.post(SQL_EXECUTE_URL, headers=headers, data=data)
        response.raise_for_status()
        print('execute', response)
    except:
        print('SQL execute fail')
    finally:
        send_dingtalk(row)


def send_dingtalk(row):
    """
    send the result to ding talk, if you need @some, you should set atMobiles and the text content should include @mobile
    :param row:
    :return:
    """
    headers = {
        'Accept-Encoding': '',
        'Accept-Language': 'zh-CN,zh;q=0.9',
        'Content-Type': 'application/json',
        'Accept': 'application/json, text/javascript, */*; q=0.01',
    }

    name = row['engineer_display']
    mobile = MOBILE[name]
    text = '### 工单执行中：' + row['workflow_name'] + '\n<br/><br/>' + \
           '用户：' + name + ' ' + row['instance__instance_name'] + ' ' + row['db_name'] + '\n\n<br/>' + \
           '时间：' + row['create_time'] + \
           ' @' + mobile
    title = 'Archery Auto Audit'
    data = {
        'msgtype': 'markdown',
        'markdown': {
            'title': title,
            'text': text
        },
        'at': {
            'atMobiles': [
                mobile
            ],
            "isAtAll": False
        }
    }
    try:
        response = requests.post(DING_TALK_URL, headers=headers, data=json.dumps(data))
        response.raise_for_status()
    except:
        print('send ding talk fail')
    # wait a time
    time.sleep(2)


if __name__ == '__main__':
    while True:
        try:
            run()
            # try again after 10m
            print('sleep 10 minutes, now time:', datetime.datetime.now().strftime('%Y-%m-%d %H:%M:%S'))
            time.sleep(10 * 60)
        except Exception as err:
            print('something error', err)
```

### 结果

```text
============================================Auto Archery===========================================
start login  https://demo.archerydms.com
login response :  <Response [200]>
{"status": 0, "msg": "ok", "data": null}
query workflow list, total: 0
query result, total 0
sleep 10 minutes, now time: 2020-11-30 08:51:14
```

