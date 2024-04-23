---
layout: post
title:  "[Dreamhack]Simple-SQLI"
date:   2024-04-23 00:29:20 +0700
categories: ctfwriteup
---

### Challenge Name : Simple-SQLI

<p>The challenge invloves solving a simple sqlite injection and the source code for the challenge is given below!</p>

```
#!/usr/bin/python3
from flask import Flask, request, render_template, g
import sqlite3
import os
import binascii

app = Flask(__name__)
app.secret_key = os.urandom(32)

try:
    FLAG = open('./flag.txt', 'r').read()
except:
    FLAG = '[**FLAG**]'

DATABASE = "database.db"
if os.path.exists(DATABASE) == False:
    db = sqlite3.connect(DATABASE)
    db.execute('create table users(userid char(100), userpassword char(100));')
    db.execute(f'insert into users(userid, userpassword) values ("guest", "guest"), ("admin", "{binascii.hexlify(os.urandom(16)).decode("utf8")}");')
    db.commit()
    db.close()

def get_db():
    db = getattr(g, '_database', None)
    if db is None:
        db = g._database = sqlite3.connect(DATABASE)
    db.row_factory = sqlite3.Row
    return db

def query_db(query, one=True):
    cur = get_db().execute(query)
    rv = cur.fetchall()
    cur.close()
    return (rv[0] if rv else None) if one else rv

@app.teardown_appcontext
def close_connection(exception):
    db = getattr(g, '_database', None)
    if db is not None:
        db.close()

@app.route('/')
def index():
    return render_template('index.html')

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'GET':
        return render_template('login.html')
    else:
        userid = request.form.get('userid')
        userpassword = request.form.get('userpassword')
        res = query_db(f'select * from users where userid="{userid}" and userpassword="{userpassword}"')
        if res:
            userid = res[0]
            if userid == 'admin':
                return f'hello {userid} flag is {FLAG}'
            return f'<script>alert("hello {userid}");history.go(-1);</script>'
        return '<script>alert("wrong");history.go(-1);</script>'

app.run(host='0.0.0.0', port=8000)
```

<p>It easier to notice, our user input is directly appened to the sql query and we can simply craft a login bypass payload to solve this?

<p>Using this in userid `admin" or 1=1 -- -` will help us login! Lets try...

<p>We are logged in as guest but we supplied userid as admin :0 , the catch here is `userid = res[0]` [ Seems like it checking the response of 0th row and in 0th row we are having the userid `guest`]

<p>To bypass this we can use `LIMIT 1 OFFSET 1` -limits the result to 1 row (LIMIT 1), and skips the first row, effectively returning the second row (OFFSET 1).

<p>This will return the userid `admin` and we get the flag!