# Shelly nodes for node-red
[![Platform](https://img.shields.io/badge/platform-Node--RED-red)](https://nodered.org)
![License](https://img.shields.io/github/license/windkh/node-red-contrib-shelly.svg)
[![NPM](https://img.shields.io/npm/v/node-red-contrib-shelly?logo=npm)](https://www.npmjs.org/package/node-red-contrib-shelly)
[![Known Vulnerabilities](https://snyk.io/test/npm/node-red-contrib-shelly/badge.svg)](https://snyk.io/test/npm/node-red-contrib-shelly)
[![Downloads](https://img.shields.io/npm/dm/node-red-contrib-shelly.svg)](https://www.npmjs.com/package/node-red-contrib-shelly)
[![Total Downloads](https://img.shields.io/npm/dt/node-red-contrib-shelly.svg)](https://www.npmjs.com/package/node-red-contrib-shelly)
[![Package Quality](http://npm.packagequality.com/shield/node-red-contrib-shelly.png)](http://packagequality.com/#?package=node-red-contrib-shelly)
![Build](https://img.shields.io/github/workflow/status/windkh/node-red-contrib-shelly/Node.js%20CI?event=push)
[![Open Issues](https://img.shields.io/github/issues-raw/windkh/node-red-contrib-shelly.svg)](https://github.com/windkh/node-red-contrib-shelly/issues)
[![Closed Issues](https://img.shields.io/github/issues-closed-raw/windkh/node-red-contrib-shelly.svg)](https://github.com/windkh/node-red-contrib-shelly/issues?q=is%3Aissue+is%3Aclosed)
...

This package contains nodes for controlling shelly devices via the following API:
https://shelly-api-docs.shelly.cloud/


# Dependencies
This package depends on the following libraries
- Axios see https://github.com/axios/axios


# Supported Devices
This package is under construction. Right now the following devices are supported

Generation 1 devices:
- shelly switch 1 (PM), 2, L,  
- shelly plug plugS
- shelly 2.5 (roller shutter) 
- shelly dimmer / shelly bulb duo, Vintage
- shelly door / shelly door 2
- shelly RGBW2 / shelly bulb RGBW
- shelly motion
- shelly emeasure (EM, EM3)
- shelly UNI

Generation 2 devices:
- shelly 1 (PM) plus

Others may work but are not really tested so far.


# Thanks for your donation
If you want to support this free project. Any help is welcome. You can donate by clicking one of the following links:

<a target="blank" href="https://blockchain.com/btc/payment_request?address=1PBi7BoZ1mBLQx4ePbwh1MVoK2RaoiDsp5"><img src="https://img.shields.io/badge/Donate-Bitcoin-green.svg"/></a>
<a target="blank" href="https://www.paypal.me/windkh"><img src="https://img.shields.io/badge/Donate-PayPal-blue.svg"/></a>

<a href="https://www.buymeacoffee.com/windka" target="_blank"><img src="https://cdn.buymeacoffee.com/buttons/default-orange.png" alt="Buy Me A Coffee" height="41" width="174"></a>


# Credits
- gerdemann for providing the roller shutter code
- Gophlin for provider the Dimmer (SL) code
- minuq for providing pull request for issue #7
- Paul-Reed for enhancing the dimmer node see issue #21
- Docjones for providing the skeleton for ShellyRGBW node see pull request #15


# Changelog
Changes can be followed [here](/CHANGELOG.md)


# General
To check if the node is available in the network, a HTTP GET to /shelly is issued. This route returns a type string which is 
verified. You can configure an interval for the polling. The default is 5000ms, 0 is off = no polling. 
Most nodes will call HTTP GET to the /status route after a input is received from the node.
This answer is directly available at the output via msg.status. The msg.payload property 
contains a lightweight version of this status object.
The hostname can be left empty in the configuration node. This deactivates polling, too. In this case you must pass the hostname in
the msg.payload.hostname. This can be useful, if you want to use one node for several devices or if you want to make use of sub flows. 

```
{
    hostname : '192.168.178.250'
	...
}
```


# Shelly Switch Node (1, 2, 1PM, 1L, Plug, PlugS)
The node is able to turn on and turn off a shelly switch. It outputs the status of all relays after every interaction with the shelly device.
Turning on is done by sending the following payload into the input. The relay number is optional and defaults to 0.


```
{
    relay : 0,
	on : true
}
```

or you can make use of the alternative notation:


```
{
    relay : 0,
	turn : 'toggle'
}
```
turn can be one of the following: 'toggle', 'on', 'off'

Right after having sent the request to the shelly device a status request is done. The relays property of the response is output on output 1.

If you only want to get the current status of the switch without turning on or off you should leave the msg.payload blank.
This is useful, when you want to poll for the status cyclically.

The output of the node is an array of status objects for every relay of the switch:


```
[
    {
        ison : true,
	    ...
    },
    {
        ison : true,
	    ...
    },
]
```

Examples:  
[**shelly switch flow**](examples/switch.json) 


# Shelly Switch Node Generation 2 (1 Plus, 1 PM Plus)
The node is able to turn on and turn off a shelly switch. It outputs the status after every interaction with the shelly device.
Turning on is done by sending the following payload into the input. The relay number is optional and defaults to 0.


```
{
    id : 0,
	on : true
}
```

or you can make use of the alternative notation:


```
{
    id : 0,
	method : 'Switch.Toggle'
}
```

Right after having sent the request to the shelly device a status request is done.

If you only want to get the current status of the switch without turning on or off you should leave the msg.payload blank.
This is useful, when you want to poll for the status cyclically.

The output of the node is the full status object of the device.
See https://shelly-api-docs.shelly.cloud/gen2/Overview/CommonServices/Shelly

Examples:  
[**shelly switch gen 2 flow**](examples/switchplus.json) 



# Shelly Window/Door Node 1/2
The node is able to poll a shelly window/door sensor. It outputs the sensor status together with battery and lux sensor values on every change of the door status.
Note that the sensor sleeps when nothing is detected and is thus not accessible via REST.

The output of the node is as follows:


```
{
    sensor
	{
		state: 'close',
		is_valid: true
	},

	lux
	{
		value: 150,
		illumination: 'twilight',
		is_valid: true
	},

	bat
	{
		value: 100,
		voltage: 6.01
	}
}
```

Examples:  
[**shelly door flow**](examples/door.json)  


# Shelly Roller Shutter Node (Shelly 2.5)

A node that controls a shelly roller shutter device (Shelly 2.5).
The relay number is optional and defaults to 0. The following object for open or close the roller shutter:

```
{
    roller : 0,
	go : 'open'
}
```

```
{
    roller : 0,
	go : 'close'
}
```

```
{
    roller : 0,
	go : 'to_pos',
	roller_pos: 50
}
```


If device is in relay mode: Turning on is done by sending the following payload into the input. The relay number is optional and defaults to 0.

```
{
    relay : 0,
	on : true
}
```

or you can make use of the alternative notation:


```
{
    relay : 0,
	turn : 'toggle'
}
```
turn can be one of the following: 'toggle', 'on', 'off'

Right after having sent the request to the shelly device a status request is done. The roller property of the response is output on output 1.

If you only want to get the current status of the switch without turning on or off you should leave the msg.payload blank.
This is useful, when you want to poll for the status cyclically.

The output of the node is an array of status objects for every roller and every relay of the roller shutter:

```
{
rollers : [
        {
            state : 'open',
			current_pos: 50,
	        ...
        },
    ],
relays : [
        {
		    ison : true,
			...
		},
	]
```

Examples:  
[**shelly roller flow**](examples/roller.json)  
[**shelly roller2 flow**](examples/roller2.json)  
[**shelly roller3 flow**](examples/roller3.json)  


# Shelly Dimmer (SL) Node / Shelly Bulb Duo / Vintage
The node is able to turn on and turn off a shelly dimmer or shelly bulb duo and set its brightness. It outputs the status of all lights after every interaction with the shelly device.
Turning on is done by sending the following payload into the input. The light number is optional and defaults to 0. The brightness can be set between 0 and 100.


```
{
    light : 0, // default = 0 (not needed for bulb duo)
    on : true,
    brightness: 100,
	white: 100, // for bulb duo
	temp : 2700, // for bulb duo (same as white but in K 2700 - 6500)
	transition : 0 // for bulb duo (0..5000ms)
}
```

Like in the switch node you can replace on with turn and choose a value from the following: 'toggle', 'on', 'off'

You can also control the shelly state and brightness independently.

```
{
on: true
}
```

and

```
{
brightness: 100
}
```

Right after having sent the request to the shelly device a status request is done. The relays property of the response is output on output 1.  
This feature can optionally be disabled by unticking the `status` checkbox in the node configuration options.

If you only want to get the current status of the dimmer without turning on or off you should leave the msg.payload blank. This is useful, when you want to poll for the status cyclically.

The output of the node is an array of status objects for every light of the dimmer:


```
[
    {
        ison : true,
		brightness: 100
	    ...
    },
    {
        ison : true,
		brightness: 100
	    ...
    },
]
```

Examples:  
[**shelly dimmer flow**](examples/bulbduo.json)  
[**shelly bulb duo flow**](examples/dimmer.json)  


# Shelly RGBW2 Node / Shelly Bulb RGWB
The node is able to control a RGBW LED light strip or a bulb RGBW. 

If you only want to get the current status of the node without turning on or off you should leave the msg.payload blank. This is useful, when you want to poll for the status cyclically.

The node accepts the following input


```
{
    red : 0,
    green : 0,
	blue : 0,
	white : 0,
	temp : 5000, // bulb
	gain : 0,
	brightness : 0, // bulb
	effect : 1,
	on : true,
	timer : 0,
	transition : 0, // bulb
}
```

red, green, blue, white can be between 0 and 255. 
gain can be a value between 0 and 100.
brightness can be a value between 0 and 100.
effect = 0 means no effect. 
timer can be a value in seconds to flip back on/off
transition can be a value in milliseconds between 0 and 5000
temp can be a value in K between 3000 and 6500 for white


```
{
	light : 0,
    on : true,
    brightness: 100,
	timer : 0,
}
```

light 0 .. 3 is the number of the strip. 
brightness can be a value between 0 and 100. 
timer can be a value in seconds to flip back on/off

Examples:  
[**shelly RGBW2 color flow**](examples/rgbw2color.json)  
[**shelly RGBW2 color effect flow**](examples/rgbw2colorloop.json)  
[**shelly RGBW2 white flow**](examples/rgbw2white.json)  
[**shelly bulb RGBW flow**](examples/bulbrgbw.json)  


# Shelly Motion Node
The node is able to poll a shelly motion sensor. It outputs the sensor status together with battery and lux sensor values on every change of the motion status.
You can enable two extra outputs which are triggered exclusively only when either motion or vibration is detected.

The output of the node is as follows:


```
{
    sensor
	{
		motion: true,
		vibration: true,
		timestamp: 12345,
		active: true,
		is_valid: true
	},

	lux
	{
		value: 150,
		illumination: 'twilight',
		is_valid: true
	},

	bat
	{
		value: 100,
		voltage: 6.01
	}
}
```

Examples:  
[**shelly motion flow**](examples/motion.json)  


# Shelly EM(3) Node
The node is able to turn on and turn off a relay in a shelly EM node. It outputs the status of all measuring devices and the status of the relay after every interaction with the shelly device.
Turning on is done by sending the following payload into the input. The relay number is optional and defaults to 0.


```
{
    relay : 0,
	on : true
}
```

or you can make use of the alternative notation:


```
{
    relay : 0,
	turn : 'toggle'
}
```
turn can be one of the following: 'toggle', 'on', 'off'

Right after having sent the request to the shelly device a status request is done. The relays property of the response is output on output 1.

If you only want to get the current status of the switch without turning on or off you should leave the msg.payload blank.
This is useful, when you want to poll for the status cyclically.

The output of the node is an array of status objects for every relay of the switch:


```
[
    {
        ison : true,
	    ...
    },
    {
        ison : true,
	    ...
    },
]
```

The output of the node is as follows:


```
{
    'relays': [
        {
            'ison': false,
            'has_timer': false,
            'timer_started': 0,
            'timer_duration': 0,
            'timer_remaining': 0,
            'overpower': false,
            'is_valid': true,
            'source': 'http'
        }
    ],
    'emeters': [
        {
            'power': 0,
            'reactive': 0,
            'voltage': 0,
            'is_valid': true,
            'total': 0,
            'total_returned': 0
        },
        {
            'power': 0,
            'reactive': 0,
            'voltage': 0,
            'is_valid': true,
            'total': 0,
            'total_returned': 0
        }
    ]
}
```


Next to polling data you can also download the historical time series data which is stored inside the device as follows:

```
[
    {
        download : [0, 1, 2],
	    ...
    },
]
```

The download variable contains a list of channels that should be downloaded. This can be either one or more numbers. Note that downloading
from the device can take some seconds as it depends on the size of the gathered data.

Examples:  
[**shelly EM flow**](examples/emeasure.json)  
[**shelly EM download CSV flow**](examples/emeasure2.json)  


# Shelly UNI Node
The node is able to turn on and turn off a shelly UNI. It outputs the status of all relays, inputs and ADCs after every interaction with the shelly device.
Turning on is done by sending the following payload into the input. The relay number is optional and defaults to 0.


```
{
    relay : 0,
	on : true
}
```

or you can make use of the alternative notation:


```
{
    relay : 0,
	turn : 'toggle'
}
```
turn can be one of the following: 'toggle', 'on', 'off'

Right after having sent the request to the shelly device a status request is done. The relays property of the response is output on output 1.

If you only want to get the current status of the switch without turning on or off you should leave the msg.payload blank.
This is useful, when you want to poll for the status cyclically.

The output of the node is an array of status objects for every relay of the switch:


```
[
    {
        ison : true,
	    ...
    },
    {
        ison : true,
	    ...
    },
]
```

Examples:  
[**shelly uni flow**](examples/uni.json) 



# Shelly REST API
For a complete documentation see also
https://shelly-api-docs.shelly.cloud/#common-http-api



# License

Author: Karl-Heinz Wind

The MIT License (MIT)
Copyright (c) 2019 by Karl-Heinz Wind

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the "Software"), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NON-INFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
