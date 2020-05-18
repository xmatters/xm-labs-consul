# Consul

This integration lets you alert users in xMatters when there is a critical check in [Consul](https://www.consul.io/).


---------

<kbd>
  <img src="https://github.com/xmatters/xMatters-Labs/raw/master/media/disclaimer.png">
</kbd>

---------

# Files

* [Consul.zip](Consul.zip) - Workflow zip file with the step and example flow
* [consul.png](/consul.png) - Consul logo

# How it works
The step is triggered by a [watch](https://www.consul.io/docs/agent/watches.html) in Consul. This watch finds if certain [checks](https://www.consul.io/docs/agent/checks.html) are critical in Consul.

# Installation

## Consul Setup
1. Create a [Watch](https://www.consul.io/docs/agent/watches.html) in Consul. This is an example.
```json
{
    "watches": [{
        "type": "checks",
        "state": "critical",
        "args": ["/bin/bash", "/home/user/watch.sh"]
    }]
}
```
Updating the `state` changes at which level it searches for checks.
In the args, point it to a script that will call xMatters with a payload.

2. Create a script for the watch
Here is an example for a script
```bash
OUTPUT=$(curl -G localhost:8500/v1/agent/checks --data-urlencode 'filter=Status != passing')

curl -X POST -H "Content-Type: application/json" -d "$OUTPUT" "https://instance.xmatters.com/api/integration/1/functions/UUID/triggers?apiKey=KEY"
```
The filter on the `OUTPUT` variable can be adjusted to change the sensitivity.
Make sure to change the URL on the curl request to point at your http trigger in xMatters.

## xMatters Setup
1. Download the [Consul.zip](Consul.zip) file onto your local computer
2. Navigate to the Workflows tab of your xMatters instance
3. Click Import, and select the zip file you just downloaded
4. Fill in the recipients in the `xMatters Create Event` step
5. Get the HTTP Trigger URL and put it in the script that the watch in Consul runs.


## Usage
The **Inbound from Consul** HTTP trigger will output the message sent from the script in Consul. This is sent into an xMatters Event.


## Troubleshooting
See if inside the Consul log it is sending requests to xMatters. These should look like: `{"requestId":"ID HERE"}`

Look in the Activity Log in xMatters to see if the steps are being run and succeeding.

