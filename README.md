# MKS-80_M4L
Maxforlive device for controlling the Roland MKS-80 from Ableton / Max for Live

This is a maxforlive device for the Roland MKS-80. With it you can control (nearly) every aspect of the, normally Sysex only, MKS-80 from Live.
So you can now automate filter cutoff, tweak Envolopes, modulate LFO and fine tune etc.
Please be aware that Sysex was not really supposed to do real-time editing. So while you can certainly sweep frequencies etc while playing midi notes, the MKS-80 sometimes stumbles a bit when receiving too much Midi, which can result in a nice swing-y feel, but might not always be intended ;-).

I have put in a couple of annotations (the lower left window in Live), especially where a parameter is not immediately understandable (I had to abbreviate a lot of parameters like "V2>V1 XMOD depth"). These annotations are copied from the manual and sometimes edited for clarity.
Any parameter that starts with "F" is for the filter, so for example "F ENV Src" is the parameter to select the source for the filter envelope modulation. Anything with "A" is for the VCA/Amp.
ALL parameters are mapped and can be automated. I have also put them in the proper order.
You can select UPPER or LOWER tone by choosing UPPER etc and then changing the parameter.

AFAIK this is the first m4l device for the MKS-80 and I love using it with my rev 5.

Changelog:
1.0 initial release (internal v08)
1.1 added Volume control in the GLOBAL section, minor cosmetic fixes, removed most of the initial values. (internal v10)

Known issues:
1. VCO2 Range is weirdly done by Roland (0,36-84, 100), which means I didn't know how to implement this with nice visual range values. So value shown 0=low, 1-35 =does nothing, 36-84 = 32C - 2C, 85-99=does nothing, 100=high)
If you know how to do this nicely, please let me know! This also goes for any further hints, tips, errors you find etc! :-) (this is my first m4l device I built from scratch).
2. "Tone control" doesn't change tones. If I am not mistaken, tone changes only work when the MKS-80 is in Midi mode II, while if you want to edit things, you need to be in Midi mode III. All other editors I tested have the same problem.
3. "Tune request" does not work. It is implemented but hidden in the GUI. Reason: Ableton Live actively filters out all "System Common" messages (known bug) and tune request is a system common message.

Future Plans:
Ideally I would like to implement patch parsing and presets as the next step, however I cannot find a sysex command for "request program/edit buffer", so if you know how to do this: Let me know.
This also means, that this device is unidirectional (Live ==> MKS-80) at the moment. So the dials will not represent the actual current values. Additionally this means that loading the m4l device WILL change your current selected patch sound! (not overwrite, just temporarily change it). Also if you switch from UPPER to LOWER the parameter values will not change.


If you like the device, you can support me by listening to my music at
https://distrokid.com/hyperfollow/markusschloesser/personality-development
https://distrokid.com/hyperfollow/markusschloesser/tag-und-nacht
