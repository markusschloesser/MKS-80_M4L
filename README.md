# MKS-80_M4L
Maxforlive device for controlling the Roland MKS-80 from Ableton / Max for Live
<img width="1802" alt="image" src="https://github.com/markusschloesser/MKS-80_M4L/assets/59286549/21d20d5c-5b4e-4726-807e-ae2dab0960ef">
<img width="639" alt="image" src="https://github.com/markusschloesser/MKS-80_M4L/assets/59286549/e271a6ef-a744-416d-8083-56cc23bcd391">


This is a maxforlive device for the Roland MKS-80. With it you can control (nearly) every aspect of the, normally Sysex only, MKS-80 from Live.
So you can now automate filter cutoff, tweak Envelopes, modulate LFO and fine tune etc.
Please be aware that Sysex was not really supposed to do real-time editing. So while you can certainly sweep frequencies etc while playing midi notes, the MKS-80 sometimes stumbles a bit when receiving too much Midi, which can result in a nice swing-y feel, but might not always be intended ;-).

I have put in a lot of annotations (the lower left window in Live), especially where a parameter is not immediately understandable (I had to abbreviate a lot of parameters like "V2>V1 XMOD depth"). These annotations are copied from the manual and sometimes edited for clarity. Some information would be too much for the annotation window, in this case I used the pop-up hint to explain, for example for the different Key Modes or Assign Modes.

Any parameter that starts with "F" is for the filter, so for example "F ENV Src" is the parameter to select the source for the filter envelope modulation. Anything with "A" is for the VCA/Amp.
ALL parameters are mapped, properly numbered and can be automated. I have also put them in the proper order.

AFAIK this is the first m4l device for the MKS-80 and I love using it with my rev 5.

# Changelog:

1.0 initial release (internal v08)

1.1 added Volume control in the GLOBAL section, minor cosmetic fixes, removed most of the initial values. (internal v10)

1.2 fixed VCO2 range visualization (thanks to Source Audio from the Cycling forum!), fixed F ENV Polarity, it had the wrong parameter (v14)

from here on, I'll use the file version, the newest one being

v14: fixed known VCO2 Range and and fixed wrong F ENV Polarity assignment

v32: now bidirectional!!! :-)

Now you can receive a preset from the MKS80, for that the Track the device is on, needs to be armed and of course the Midi input device needs to be set to the MKS80. 
To receive a patch, enable the "enable Sysex receive from MKS80" button, and then select a preset ON THE MKS80. 
After the patch is received, the button will turn itself off again in order to not run into Midi Loops. 
I would also strongly recommend to disable "enable sysex output" while receiving. The devices Dials and controls will now reflect the state of the MKS80.

This is a huge advancement but also a little bit weird to use because of the way the MKS80 does(doesn't) support things.
Unfortunately the MKS80 does not have an "edit buffer request" neither a way to ask it to send the parameters of a patch when a program change is send. So that's why you have to need to physically go to the MKS80.


ATTENTION: to be able to use individual dials etc you need to enable "enable sysex output".

HUGE thanks to "Source Audio" on the Cycling forum for all the help and patience with the device!! Really grateful!

For further issues/plans, check https://github.com/markusschloesser/MKS-80_M4L/issues

If you like the device, you can support me by listening to my music at
https://distrokid.com/hyperfollow/markusschloesser/personality-development
https://distrokid.com/hyperfollow/markusschloesser/tag-und-nacht
