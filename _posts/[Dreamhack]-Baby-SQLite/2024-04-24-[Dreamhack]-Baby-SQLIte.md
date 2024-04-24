---
layout: post
title:  "[Dreamhack]Baby SQLite"
date:   2024-04-24 00:29:20 +0700
categories: ctfwriteup
---

### Challenge Name : Baby SQLite

The challenge source code is below

```
#!/usr/bin/env python3
from flask import Flask, request, render_template, make_response, redirect, url_for, session, g
import urllib
import os
import sqlite3

app = Flask(__name__)
app.secret_key = os.urandom(32)
from flask import _app_ctx_stack

DATABASE = 'users.db'

def get_db():
    top = _app_ctx_stack.top
    if not hasattr(top, 'sqlite_db'):
        top.sqlite_db = sqlite3.connect(DATABASE)
    return top.sqlite_db


try:
    FLAG = open('./flag.txt', 'r').read()
except:
    FLAG = '[**FLAG**]'


@app.route('/')
def index():
    return render_template('index.html')


@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'GET':
        return render_template('login.html')

    uid = request.form.get('uid', '').lower()
    upw = request.form.get('upw', '').lower()
    level = request.form.get('level', '9').lower()

    sqli_filter = ['[', ']', ',', 'admin', 'select', '\'', '"', '\t', '\n', '\r', '\x08', '\x09', '\x00', '\x0b', '\x0d', ' ']
    for x in sqli_filter:
        if uid.find(x) != -1:
            return 'No Hack!'
        if upw.find(x) != -1:
            return 'No Hack!'
        if level.find(x) != -1:
            return 'No Hack!'

    
    with app.app_context():
        conn = get_db()
        query = f"SELECT uid FROM users WHERE uid='{uid}' and upw='{upw}' and level={level};"
        try:
            req = conn.execute(query)
            result = req.fetchone()

            if result is not None:
                uid = result[0]
                if uid == 'admin':
                    return FLAG
        except:
            return 'Error!'
    return 'Good!'


@app.teardown_appcontext
def close_connection(exception):
    top = _app_ctx_stack.top
    if hasattr(top, 'sqlite_db'):
        top.sqlite_db.close()


if __name__ == '__main__':
    os.system('rm -rf %s' % DATABASE)
    with app.app_context():
        conn = get_db()
        conn.execute('CREATE TABLE users (uid text, upw text, level integer);')
        conn.execute("INSERT INTO users VALUES ('dream','cometrue', 9);")
        conn.commit()

    app.run(host='0.0.0.0', port=8001)

```

Potential SQLite injection but <code>sqli_filter = ['[', ']', ',', 'admin', 'select', '\'', '"', '\t', '\n', '\r', '\x08', 

<code>'\x09', '\x00', '\x0b', '\x0d', ' ']</code> need to bypass these filters. [ Does not sound like a baby SQLite injection]

All the parameters are lower cased which means we cant bypass with <code>`SeLect - select`</code>[SQLi is case insensitive]

Also it seems we cant escape the upw and uid parameters due to the fact we cant bypass the quotes maybe we can try the injection in level parameter?

To bypass space we can use <code>`/**/`</code> ! after some failed attempts of crafting the injection ! i noticed the blunder i made.

The SQL database is getting deleted when the python script starts and also there is no entry of admin and only there is a entry of user named dream.

```

            if result is not None:
                uid = result[0]
                if uid == 'admin':
                    return FLAG
```

To get the flag we just need our injection to return the value admin and that value is checked not the original uid.

So i thought of using the payload <code>`union value`</code> to return the value we are giving.

We cant use `admin` directly, i thought of using char() function with ascii values of `admin` , here we cant use concat function like `concat(char(97),char(100),char(109),char(105),char(110))` since `,` is also blocked.

So our final payload depends on the use of concatenation operator <code>`||`</code>

<p>Using all these [ Space bypass , union value , char() , || ] we get the payload <code>`level=1/**/union/**/values(char(97)||char(100)||char(109)||char(105)||char(110))`</code> and this gives us the flag.


