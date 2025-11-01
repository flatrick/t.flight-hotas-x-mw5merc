# Fatal's insane guide to DUAL JOYSTICKS ON LINUX

source: [Reddit - Fatal's insane guide to DUAL JOYSTICKS ON LINUX](https://www.reddit.com/r/Mechwarrior5/comments/12gmlr0/fatals_insane_guide_to_dual_joysticks_on_linux/)

_The reason I've saved this guide in this repo is just in case his guide disappears._
_I hope I'll get around to adjusting it for the Thrustmaster T.Flight HOTAS X v2 that I own and making my own instructions here for it.)_

Do you use Linux?
Do you want to play Mechwarrior5 like a real Mech Warrior, administering battlefield destruction through your two hands on two joysticks?
Much like how the virtual mech pilot you are looking through the eyes of is themselves doing?

Well I just spent all of my free time this last week absolutely banging my head against a wall trying to figure this out, and it was hard.
But I DID IT.
And it was so much work, I wanted to share it to wider audience so nobody else has suffer my pain.
(Full details: I am using Gentoo Linux with dual Virpil Constellation Alpha sticks on WarBRD bases, absolutely none of which Piranha games ever intended to support)

## The guide for what finally worked:
Step 0: Make sure both joysticks are visible to your linux operating system as dev/js0 and dev/js1, and that their input is registering correctly in whatever system settings you have going. If your HOTAS or HOSAS isn't working right naively in linux, it certainly won't work right in the game.

**Step 0.5**: Have Mechwarrior5 installed on Steam. You can probably use this guide for a non-Proton WINE install, but I can verify that the game (sans joystick support) works perfectly in Linux via Steam right out of the box, zero tinkering (if you have launch problems, you probably left some old <7 verison of Proton as your compatibility tool, just use anything 7+). Buy all the DLC. Load up on mods, I'm running 68 different ones, it all works great.

**Step 1**: Install AntiMicroX, a controller utility tool: https://github.com/AntiMicroX/antimicrox

**Step 2**: With your right joystick, setup AntiMicroX to push the mouse around as you push on the main X-Y axes of the stick.
Click the axis you want to map, select a preset such as "Mouse (Horizontal)".
Drop down that dead-zone to the smallest value you can get away with (I find the default quite high).
Click Mouse Settings, ramp up the "Speed" settings (I currently have 300 horizontal 150 vertical).
Choose an acceleration profile to your preference, I'm currently using quadratic.
Verify that, when AntiMicroX is running, your right joystick does in fact push the mouse around.

**Step 3**: With your left joystick, click "Controller Mapping" to create an SDL2 controller mapping string.
It will initially be blank.
Leave every field blank except for 'Left Stick X' and 'Left Stick Y'.
Go ahead and map these two to the X and Y joystick axes.
Copy the SDL 2 Game Controller Mapping String.
We'll use it later.

**Step 4**: Install Lutris if you don't it already, as we will use this to inject the SDL2 mapping string.
You will need to detect your Steam games, which requires changing a privacy setting in your Steam profile.
Open steam > Select your name at the top right > View My Profile > Edit Profile > Privacy Settings > Game Details > Select "Public" from the drop down.
Then tap the '+' on the top left of Lutirs, select 'Scan folder for games', select a folder that you installed Steam inside of and I think all your Steam games should populate.

**Step 5**: Add the _SDL2_ mapping string in _Lutris_.
Select _MechWarrior 5_ in _Lutris_, click the "^" arrow next to the Play button and select "Configure".
Go to "System Options".
Scroll to the bottom and you will see a field that says "SDL2 gamepad mapping".
Paste in the SDL2 string from Step 3.

**Step 6**: Open terminal and use the following commands to get inside the Proton environment for MW5:

Important! replace the folder destinations with the ones on your system if they are different!

export W="~/.steam/steam/steamapps/'Proton - Experimental'/dist"
export WINEVERPATH=$W
export WINESERVER=$W/bin/wineserver
export WINELOADER=$W/bin/wine
export WINEDLLPATH=$W/lib/wine/fakedlls
export LD_LIBRARY_PATH="$W/lib:$LD_LIBRARY_PATH"
export WINEPREFIX=~/.steam/steam/steamapps/compatdata/784080/pfx

**Step 7*w: Configure the WINE registry. After entering the Step 6 commands, open regedit using the following command:

```
wine regedit
```

Navigate to the following folder:
```
HKEY_LOCAL_MACHINE > System > CurrentControlSet > Services > winebus
```

Right-click on the field of registry entries and select "New" > "DWORD Value".

Add a "DWORD Value" and name it "DisableHidraw". Double click the "DisableHidraw" line and set the value data to "1".

Add the following additional entries:

"DisableInput" with value "1"

"Enable SDL" with value "1"

"Map Controllers" with a value "1"

And for good measure, we'll add the SDL mapping string here too, but it never seems to work for me (which is why I have to inject it with Lutirs).
Right-click > New > String Value. Name it "Map" or anything.
For Value Data, paste in the SDL string.

Not all of these may be necessary, but it's what I had these set to when I got things working.
You're safe to just close out of the registry window, it gets saved as you make entries.

For good measure, go ahead and run the following in the terminal window (you still have that open, right??):
```
wineboot
```

**Step 8**: Configure the controller input layers in WINE.
In the same terminal window as Step 6 and Step 7, go ahead and run the following command:
```
wine control
```

Open "Game Controllers".

Open the XInput tab and make sure that at least your left joystick is registering all inputs correctly.
If it's not, you've got bigger issues going on than the scope of this guide.

Back to the "Joysticks" tab.
You are going to split up your joysticks.
Your left joystick should remain in the "Connected (xinput device)" category.
For your right joystick, select it and click "Override" so that it is in the top "Connected" category, and not the center "Connected (xinput device)" category.

Click "Ok" to save your changes.

You can also close that terminal window now.

**Step 9**: Adjust the ingame joystick settings and validate that axial inputs are behaving as expected.
Make sure AntiMicroX is running with the setup from **Step 2**.
Run MW5 from Lutris, not the Steam application.

Go to Settings > Controls > Joystick.
This may take some experimentation, but go ahead and set all of the axis options to Throttle (or Joystick), whichever one is dead to the game with no registered input.
I personally set all axis settings to "Throttle Axis 1" as nothing appears to be inputting to this axis, but you may need to set it differently to avoid spurious input.

Now go ahead and get yourself into the cockpit of a mech.
The fastest way for me is Single Player > Instant Action.

Once you're in your mech, go ahead and verify that moving your right control stick around pushes around the targeting reticle.
If you need to invert an axis, go back to AntiMicroX, open the axis that needs inversion, go to cursor settings, and change your preset to "Mouse Horizontal (inverted)" or whatever.
It won't change any of the other sensitivity/deadzone settings, those will stay yay.

Then verify that the left stick has your forward-back thrust on the front-to-back axis, and turns your torso left and right on the side-to-side axis.

If any of these isn't working, make sure you did all of the steps correctly, otherwise just take the time to configure the sensitivity and inversion settings to your preference.

**Step 10**: Map your controller buttons to actions. 
Go back and forth between the game's Keyboard and Mouse settings under the Controls settings tab and AntiMicroX to individually map your joystick buttons in AntiMicroX to generate the keyboard keystroke for the action you want performed.
Fire weapons group, activate night vision, whatever commands are there - I haven't actually played this game myself yet.

You're done! You can use your two joysticks to pilot your mech on Linux!

## Why was all of this necessary?

_MechWarrior 5_ has very poor joystick support natively on Windows.
The joystick support is so painfully bad and just not broadly present in a general sense on Windows that trying make the generally poor joystick support in Linux gaming work with the poor MW5 Windows joystick support is like trying to thread a needle in a fucking hurricane while you're on fire.
I tried a lot of less absurd ways to do this, and was ultimately sabotaged by how under-developed the built-in joystick support is on MW5.
You can setup the "HOTASMappings.Remap" file to work in Linux by plugging your joysticks into a Windows computer, collecting the Vendor and Product ID strings from the Device Manager there, and then putting that into the HOTASMappings.Remap file on your linux machine and it will recognize the joysticks if they are in DInput layer.
You can even figure out the offset value that's necessary to get at the midpoint of the axis.
But the graduated axial input doesn't work, I just get full left authority when the joystick touches the left side of the axis, and then if I touch the right side of the axis I get full right authority until I go back to the left side.
I play tons of 'Windows' space games on Linux and never had this kind of issue.

I actually wrote a really impassioned email to Piranha's MW5 tech support email contact about the bad HOTAS support basically ruining my life and being absurd for a game where mechs are literally being piloted using HOTAS inside the game, back when I couldn't get dinput to work before I figured out my current solution.
GM Zen was very gracious with me, and said that "expanded HOTAS support is a frequently requested feature" and that he always forwards feedback about it to the overall MW5 team. 
Here's to hoping Piranha shows us some stepped up HOTAS support in future releases!
