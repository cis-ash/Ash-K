# Sending data from Godot to google sheets

Pretty useful thing if you want to lower the amount of effort needed to submit some information/bug report. Also could be good as a free and cheap way of doing telemetry if you don't need to send a whole lot of data non stop.

### 1. Create a new form with google forms

It doesn't need to be fancy as it will not be seen by players. I believe paragraph fields work best for what we are going to do. Remember to set it to output responses into a google sheets document.

### 2. Find the response key

We will be tricking google into thinking someone filled out a form with the information we provided. To do that we need to send a request to the response page that looks like the one you would send after filling out a form normally.

Enable your browsers developer tools functionality and go to the Network tab. Now fill out the form and send your response. You should see a message pop up in the network tab, this is the request we will be imitating.

![](https://cdn.discordapp.com/attachments/814658514328354867/861999723769626634/unknown.png)

These are the magic numbers we are looking for

![](https://cdn.discordapp.com/attachments/814658514328354867/862000012866224148/unknown.png)

### 3. Sending Data from Godot

To do that we will be using the HTTPRequest node with a script attached. You can copy the code below and change the necessary parts to fit your needs.

Change my_full_url to the link you are directed after clicking Submit, it should end with /formResponse.

Change entry.322097243 to entry.[magic numbers we found in the previous step]

```python
extends HTTPRequest

var my_full_url = "https://docs.google.com/forms/u/0/d/e/1FAIpQLSddxo2K4qTU8-Wey-IibJRdHzitcf-7oam-_eniy88MNl2xMw/formResponse"
onready var my_data = {"entry.322097243" : "default data to send, seeing this means somethign went wrong"}
var headers = ["Content-Type: application/x-www-form-urlencoded"]
var http = HTTPClient.new()


func send_data(text):
	my_data = {"entry.322097243" : text}
	# Transform data so it can be sent:
	var headers_pool = PoolStringArray(headers)
	var my_data_ready = http.query_string_from_dict(my_data)
	#Send data!!!!
	var result = self.request(my_full_url, headers_pool, false, http.METHOD_POST, my_data_ready)

```

Now just call the send_data method on this node and check the sheets for the response. It may take up to a minute for the data to appear. It will usually be done in just a few seconds though.

It should be possible to do this with multiple fields in the form, where you would need to find the keys to each one and include them all in your my_data dictionary.

Hope you find this explanation useful. If you run into any issues you can send me a message on twitter, discord, or shoot me an email.