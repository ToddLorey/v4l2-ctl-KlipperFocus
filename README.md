# v4l2-ctl-KlipperFocus

I had tried to get my LMX179 USB webcam to set a manual focus level using the crowsnest.cfg file but it never seemed to work.  Manually sending a value in the command shell _did_ work, so I set about making a way that would automagically apply a focus value after Klipper starts up.

Essentially, this is just a "fancy" way of getting Klipper to manually send a command once it's up and running.  Also as a bonus, it lets to send commands on the fly via the Klipper console.

* Applicable camera module(s):
  https://a.co/d/a9iFTli (or probably anything similar)

* Prerequisites:
  *   Klipper gcode_shell_command extra module here:
     https://github.com/dw-0/kiauh/blob/master/docs/gcode_shell_command.md
  
  *  or, it might already be installed.  Mine was from somewhere.
  
# Installation steps:
1. Create a file that has a simple script to run when the macro is run.  I called mine focus.sh, and I put mine in ~/scripts.  From the command line, enter:
```
nano ~/scripts/focus.sh
```
2. Add the following contents to the file:
```
#/bin/sh

v4l2-ctl -d /dev/video9 -c focus_absolute=$1
echo "focus set to $1"

```
      NOTES:  
      - You may need to change the /dev/video9 to whatever your camera is identified as.  Use the same as what you used in your crowsnest.cfg file.
      - Your focus command might be different too.  Mine was "focus_absolute=_some number 1-1024_".  Check out your crowsnest.log file at ~/printer_data/logs/crowsnest.log.
      - Leave the "$1" alone, we need that to pass the parameter into the script)
      - Make sure you still have the #/bin/sh at the top of the file

3. Make any changes necessary as described above.  And save the file:
      Hit CTRL+X to exit and then Yes to save.

4. Make the script file executable:
```
chmod +x ~/scripts/focus.sh
```
5. Add these lines to your printer.cfg file in Klipper.  Or, if you know your way around, you can put them probalby anywhere that has an [include yourfavoritemacros.cfg] in your printer.cfg file.
```
[gcode_shell_command focus]
command: sh ~/scripts/focus.sh  # The shell command to call the script.  
timeout: 30.
verbose: False

[gcode_macro SET_FOCUS]
gcode:
    {% set focus_val = params.FOCUS|default("465")|int %}         # set default value 465 to whatever you like
    { action_respond_info("Focus set to: " "%s" % (focus_val)) }  # Echos command in klipper console
    RUN_SHELL_COMMAND CMD=focus PARAMS={focus_val}                # Run the gcode shell command 

[delayed_gcode INITIALIZE_FOCUS]
initial_duration: 10  # Run automatically after initial_duration seconds
gcode:
    SET_FOCUS FOCUS=465 # Set value for focus to suit your fancy

```
6. Restart Klipper firmware.  May as well reboot your host machine as well, for good measure.
# USAGE:
This scheme will automatically set the manual focus value (465 in my case), 10 seconds after Klipper starts up.

You can manually set a focus value by sending this from the console (which might be useful for finding what you want to use for your default):
```
SET_FOCUS FOCUS=<some number you want to try>
```
