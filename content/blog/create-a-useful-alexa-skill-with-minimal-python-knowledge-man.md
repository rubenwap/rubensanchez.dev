---
stackbit_url_path: posts/create-a-useful-alexa-skill-with-minimal-python-knowledge-man
title: Create a *useful* Alexa skill with minimal Python knowledge
date: '2020-12-08T15:18:11.472Z'
excerpt: >-
  I am pretty disappointed with the terrible quality of most of the "custom"
  stuff found in the Alexa s...
thumb_img_path: >-
  https://res.cloudinary.com/practicaldev/image/fetch/s--U7lpC5pA--/c_imagga_scale,f_auto,fl_progressive,h_420,q_auto,w_1000/https://dev-to-uploads.s3.amazonaws.com/i/zwkr8k11kjtb3vzsry6w.png
comments_count: 0
positive_reactions_count: 6
tags:
  - python
  - alexa
  - tutorial
canonical_url: >-
  https://dev.to/rubenwap/create-a-useful-alexa-skill-with-minimal-python-knowledge-man
template: post
---
I am pretty disappointed with the terrible quality of most of the "custom" stuff found in the Alexa skills shop. Lots of "tell me a joke", "tell me a fun fact", "tell me random facts", "play forest sounds", "play sea sounds"... So at the end, my Echo unit ended up being used just to turn Netflix on and off and manipulate the heating temperature. 

That's no more! It's very easy to create custom skills with Python (or Node.js) which can actually be useful so you find yourself triggering them often. Because after all, how many times can you play "random facts"?

## How to find out what is actually useful

In my case, I focused on trying to remember small actions that I might have repeated day after day using either my phone or the computer. Mechanical tasks that might not be pleasurable or be considered as leisure, but you need to do them anyhow. Consider if you find yourself often checking the same bit of information.

## Bike hire status

I live in Barcelona and here we have a public bike hire system called Bicing. Nowadays with working from home I don't use it that much, but when I need it, I used to open the app in my phone before leaving home in order to find out whether there are available bikes in the station near my home. Bicing has a [live status page in JSON format](https://api.bsmsa.eu/ext/api/bsm/gbfs/v2/en/station_status), so that was the perfect candidate for my Alexa Skill.

## Getting started

First you must create an free account if you don't already have one, and log in to the [Skills dashboard](https://developer.amazon.com/alexa/console/ask)

![Captura de pantalla 2020-12-08 a las 15.09.07](https://dev-to-uploads.s3.amazonaws.com/i/6dpwg32o0kjnlevdaihm.png)
 

Click on "Create Skill" when you are ready and follow the steps to create a custom skill from scratch with Python (you can select Node.js, although the example on this tutorial will be Python)

You should end up with an empty skill in your dashboard

![Captura de pantalla 2020-12-08 a las 15.15.04](https://dev-to-uploads.s3.amazonaws.com/i/2hotmiei4c0gjsppoefk.png)
 
## Configuring the skill

There are two critical items you should understand:

* INVOCATION

Invocation is what name or phrase should you tell Alexa so she understands that you want to communicate with your skill.

For example, I have a skill to control my heating temperature, so in the sentence "Alexa, tell *the heating* to lower the temperature one degree", *the heating* (or *heating*, since Alexa seems pretty flexible with grammar particles) is the Invocation. Or "Alexa, tell Xbox to play Grand Theft Auto". *Xbox* would be the Invocation. 

![Captura de pantalla 2020-12-08 a las 15.15.25](https://dev-to-uploads.s3.amazonaws.com/i/qtdc42i8yn0wkav54zc9.png)
 
So in my case I am using *Estación de bicing*, which translates to "bicing station" (I am creating the skill in Spanish). 

* INTENT

So, how many actions do you intend your skill to handle? example intents would be (for my heating) "set temperature", "switch on and off", "change mode"... For my bike hire status skill I am just going to do one intent "AreThereBikes"?

Add as many intents as you want in the dashboard 

![Captura de pantalla 2020-12-08 a las 15.15.37](https://dev-to-uploads.s3.amazonaws.com/i/k8azy687lq5gwpsjc2cw.png)
 
And for every intent, you need to add the sentences that would trigger it. In my case I have put a list of sentences such as "Are there bikes", "are there bicycles", "are there available bikes"... and son on. Think of the possibilities.  

![Captura de pantalla 2020-12-08 a las 15.15.45](https://dev-to-uploads.s3.amazonaws.com/i/5i79c0egqbn1aebigdfa.png)
 
## Adding your code

Once your code is configured with Invocation and Intent, do save and build the language model. You can find those buttons in the Intent screen. You need to rebuild every time you make changes to it. 

Click on the "Code" tab in the upper part of the page. You can edit your code here. You can also do it in VSCode or your favourite local editor, but the set up is a bit longer, so we will stick here. 

I have added this class to the 
`lambda_function.py`
 file:


```python
class HayBicisIntentHandler(AbstractRequestHandler):
    """Handler for HayBicis Intent."""
    def can_handle(self, handler_input):
        # type: (HandlerInput) -> bool
        return ask_utils.is_intent_name("HayBicis")(handler_input)

    def handle(self, handler_input):
        # type: (HandlerInput) -> Response
        bikes_available = get_bikes(244)
        speak_output = bikes_available

        return (
            handler_input.response_builder
                .speak(speak_output)
                .response
        )

```


The structure of the class will be done for you automatically. You just need to change a couple of things. In my case, I have put the proper intent name:


```
return ask_utils.is_intent_name("HayBicis")
```


and have defined that my 
`handle`
 method should respond with the 
`speak_output`
 created by:


```python
bikes_available = get_bikes(244)
speak_output = bikes_available
```


So what is this 
`get_bikes(244)`
 function? This is the very simple custom code I have included in the skill. It makes a request to the Bicing JSON live status and returns a string with the information I want Alexa to repeat. 


```python
def get_bikes(station_id):
    resp = requests.get("https://api.bsmsa.eu/ext/api/bsm/gbfs/v2/en/station_status")
    available_bikes = list(filter(lambda item: item["station_id"] == station_id, resp.json()["data"]["stations"]))[0]["num_bikes_available_types"]
    return f"""Hay {available_bikes["mechanical"]} bicis mecánicas y {available_bikes["ebike"]} eléctricas."""

```


The code retrieves the json and then, filters the response so it only uses the stations where the station_id matches with the id we have provided. Out of that selected station, it grabs the amount of available bikes. 

Last point, don't forget to register your intent at the bottom of the file, as such:


```python
sb = SkillBuilder()

sb.add_request_handler(LaunchRequestHandler())
sb.add_request_handler(HayBicisIntentHandler())
sb.add_request_handler(HelpIntentHandler())
sb.add_request_handler(CancelOrStopIntentHandler())
sb.add_request_handler(SessionEndedRequestHandler())
```


## Testing

So that's the full story. Of course we still need to add some other niceties, such as persisting user settings so anyone could select their own station (right now it's hardcoded to the station where I live!) But we can go step by step in future tutorials. 

Now is time to test. After you have saved your code, make sure you click on the "Deploy" button in the upper right side of the code editor, and then click on the "Test" tab. 

Here you can choose whether using your microphone or simply type the questions. With this testing you will see if Alexa reacts as you would expect:

![Captura de pantalla 2020-12-08 a las 15.38.29](https://dev-to-uploads.s3.amazonaws.com/i/gd744780qpefeqnz8rz7.png)
 
Great! That is exactly the outcome we wanted. We now have a working Alexa skill which we have made in barely a few minutes.


## Deploying to the Alexa store

I won't deploy this project to the store yet, as it's missing a crucial part for it to be useful for someone else (setting your own station). But if you were inclined to deploy your skills,  you can click on the "Distribution" tab and get started with the process. Good luck! 

## GitHub repo
https://github.com/rubenwap/haybicis


*[This post is also available on DEV.](https://dev.to/rubenwap/create-a-useful-alexa-skill-with-minimal-python-knowledge-man)*


<script>
const parent = document.getElementsByTagName('head')[0];
const script = document.createElement('script');
script.type = 'text/javascript';
script.src = 'https://cdnjs.cloudflare.com/ajax/libs/iframe-resizer/4.1.1/iframeResizer.min.js';
script.charset = 'utf-8';
script.onload = function() {
    window.iFrameResize({}, '.liquidTag');
};
parent.appendChild(script);
</script>    
