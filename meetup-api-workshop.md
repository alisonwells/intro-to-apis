# Working with the Meetup API

## 1. Get an API key for the Meetup API
Go to https://secure.meetup.com/meetup_api/key/ and after logging in you should see your unique API key. As it says, protect this and don't share it.

## 2. Protect your key
A good way to protect your key is to keep it in a separate file. This allows you to share your main code and commit it to github without sharing your keys.  Create a file called ```config.py``` containing the following:
```python
MEETUP_KEY = your_meetup_key
```
### An alternative method
You can also export it to an environment value at the command line which the python package will automatically use. However I tend to find that this environment variable is easily lost and you still need to store the key somewhere.
```bash
$ export MEETUP_API_KEY=your_meetup_key
```
## 3. Install the meetup-api Python package
```python
pip install meetup-api
```

## 4. Connect to the Meetup API
Create a new file called ```meetup-events.py``` containing the following:
```python
import meetup.api
import config # Where your key is stored

client = meetup.api.Client()
client.api_key = config.MEETUP_KEY # Access the key from the config file
```
## 5. Get a list of events for a group
That has set up the access to the API. Now let's use that connection to look at the AI Club's events. Add the following code:
```python
events = client.GetEvents(group_urlname='ai-club') # Get all the events for AI Club
print(events.results)
```
You will see that you get a list of separate events with a dictionary of information for each event. When dealing with JSON, I always use a linter to help me read it - there are many online ones e.g. https://jsonlint.com
## 6. Look at an event in detail
One event looks like this:
```
{
'status': 'upcoming',
'utc_offset': 0,
'event_url': 'https://www.meetup.com/ai-club/events/244457090/',
'group': {
	'who': 'Techies',
	'name': 'Artificial Intelligence (AI) Club for Gender Minorities!',
	'group_lat': 51.52000045776367,
	'created': 1498916879000,
	'join_mode': 'approval',
	'group_lon': -0.10000000149011612,
	'urlname': 'ai-club',
	'id': 24782625
	},
'description': '<p><b>*****NOTE the now updated DAY &amp; VENUE for this event!! The workshop is now on Thurs 9th at Old St.</b> (instead of Wed 8th at Victoria due to issues with the original hosts)<b>*****</b></p> <p><b>-----------\xa0<br/></b></p> <p>Data Engineering Head Alison Wells\xa0will first explain what is an API, give examples of APIs and Python packages that make using them simple, and then she\'ll go through authentication. The second part will be a hands-on workshop where we\'ll work on pulling data from some popular APIs.</p> <p>-----------\xa0</p> <p><b>Requirements</b>:</p> <p>- bring a fully charged laptop</p> <p>-----------</p> <p><b>Schedule</b>:</p> <p>6.00pm Doors open</p> <p>6.30pm Talk/Workshop</p> <p>8.00pm Networking</p> <p>-----------</p> <p><b>New way of getting a spot</b>:</p> <p>You will notice that you will be on the waiting list when you register. We will move you to the main list if we believe you are less likely to be a no-show/someone who cancels last minute without sending/posting a message and prevents those on the waiting list from attending.</p> <p>In addition, you won\'t be promoted to the main list if you don\'t answer the question when you RSVP.</p> <p>We are experimenting with this new scheme as we have noticed some increasingly irresponsible meetup behaviour from some of our members. It is not respectful for our work, and puts our relationship with our sponsors at risk. You need to play your part to enable us to run these free &amp; highly impactful events,\xa0SO PLEASE CO-OPERATE!!</p> <p>-----------</p> <p><b>Many thanks to Venue Sponsors:</b></p> <p><img src="https://secure.meetupstatic.com/photos/event/9/1/a/9/600_465457289.jpeg" /></p>',
'created': 1508774741000,
'rsvp_limit': 4,
'venue': {
	'city': 'London',
	'name': 'Monzo',
	'country': 'gb',
	'lon': -0.093078,
	'localized_country_name': 'United Kingdom',
	'address_1': '230 City Road, EC1V 2QY',
	'repinned': False,
	'lat': 51.528488,
	'id': 25562918
	},
'updated': 1509733203000,
'visibility': 'public',
'yes_rsvp_count': 31,
'time': 1510250400000,
'duration': 9000000,
'waitlist_count': 19,
'headcount': 0,
'maybe_rsvp_count': 0,
'id': '244457090',
'announced': True,
'name': 'Intro to APIs workshop!'
}
```
As ```events.results``` is a list, you can iterate over it to pick out individual events, so for example to print out a list of just the event titles you could replace the original print statement with:
```python
for event in events.results:
  print(event['name'])
```
### Now try...
* Print out a list of event titles and the number of people for each event who have RSVPed 'Yes'
* Get a list of events for another group - e.g. rladies-london (You can get the right name for each group from the meetup.com URL after the www.meetup.com bit)
* Replace the ```group_urlname``` parameter with ```rsvp='Yes'``` to get a list of all the upcoming events you have RSVPed 'yes' to

### 7. Dates and Times
The timestamps in the event details are expressed as milliseconds *(why do we need millisecond precision for meetup times!)* after 1st January 1970. You can use the ```datetime``` library to convert them to human readable form.

This example takes the timestamp and turns it into a datetime object, then formats it into ISO 8601 style.

```python
from datetime import datetime
# Turn the timestamp from a string to an integer
intTime = int(event['time'])
# Turn the timestamp into seconds rather than milliseconds.
timeInSeconds = intTime/1000
# Convert to datetime object
datetimeObject = datetime.fromtimestamp(timeInSeconds)
# Format to ISO format
formattedDate = datetimeObject.strftime('%Y-%m-%dT%H:%M:%S'
print(formattedDate)
```
Also note that they are all in [UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) so you may need to consider the UTC offset information too.

### Now try...
* Convert the above code into a function and use it to print out a human readable list of event titles and start times
* Use the duration field to calculate the end time of each event
* Change the format of the date and time to be like "18:30, 9 November 2017"



###References
* [Meetup API - Documentation](https://www.meetup.com/meetup_api/)
* [meetup-api Python package - Documentation](http://meetup-api.readthedocs.io/en/latest/)
* [datetime Python package - Documentation](https://docs.python.org/2/library/datetime.html)
