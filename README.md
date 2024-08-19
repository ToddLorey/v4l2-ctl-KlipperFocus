# v4l2-ctl-KlipperFocus

I had tried to get my Arducam to set a manual focus level using the crowsnest.cfg file but it never seemed to work.  Manually sending a value in the command shell did work, so I set about making a way that would automagically apply a focus value after Klipper starts up.

Essentially, this is just a "fancy" way of getting Klipper to manually send a command once it's up and running.  It also as a bonus, lets to send commands on the fly via the Klipper console.
