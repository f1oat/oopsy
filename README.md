# Oopsy: Gen~ to Daisy

Exporting Max Gen patchers for the ElectroSmith Daisy hardware platforms

## Installing

Developing for an embedded platform like Daisy requires a few low-level tools and libraries to be installed first. First, follow the steps in the [Daisy wiki](https://github.com/electro-smith/DaisyWiki/wiki/1.-Setting-Up-Your-Development-Environment) to set up `ARM-Toolchain` and `dfu-util`. 

On OSX you can simply run:

```
brew install make armmbed/formulae/arm-none-eabi-gcc dfu-util
```

Second, install Oopsy and build the Daisy library dependencies:

```
# first open a console in your Documents/Max 8/Packages folder, then:
git clone https://github.com/grrrwaaa/oopsy.git
cd oopsy
git submodule update --init
git pull --recurse-submodules
cd source/DaisyExamples
./rebuild_libs.sh
cd ..
```

If you want to use Oopsy without having Max open, you'll also want to have [Node.js](https://nodejs.org/en/) installed. From within Max you'll need the Node4Max package instead.

## Using from the command line via Node.js

From the /source folder, run oopsy.js, telling it what hardware platform to build for, and pointing it to one or more cpp files that have been exported from gen~:

```
# for DaisyPatch:
node oopsy.js patch ../examples/simple.cpp
# for DaisyField:
node oopsy.js field ../examples/simple.cpp
# etc.
```

- If the Daisy is plugged in via USB and ready to accept firmware (the two tact switches on the Daisy Seed have been pressed) then the oopsy script will upload the binary to the hardware (otherwise you'll get the harmless "Error '74") 
- Up to eight cpp files can be mentioned in the arguments; they will all be loaded onto the Daisy, with a simple menu system to switch between them (on Patch/Petal: long encoder press then rotate to select, on Patch/Pod: long SW1 hold and SW2 to select)
- If the `watch` keyword is added to the oopsy.js arguments, it will re-run the process every time any of the cpp files change -- which is handy since gen~ will re-export on every edit.
- For a custom hardware configuration (other than Patch/Field/Petal/Pod) you can specify a JSON file in the arguments.

## Using from within Max

*Coming soon*

## How gen~ features map to Daisy

Mostly this works by adding an appropriate name to the `in`, `out`, and `param` objects.

- `in 1`, `in 2` (and `in 3`, `in 4` on the DaisyPatch) are the audio device inputs.
- `out 1`, `out 2` (and `out 3`, `out 4` on the DaisyPatch) are the audio device outputs.
- `param cv1`, `param gate2`, `param knob1`, `param key2`  etc. will give you the CV and gate inputs, hardware knobs, keys, etc. Use `@min` and `@max` to remap the normalized ranges. Note that on the Patch the CV and Knob inputs are the same. Use `@min` and `@max` to remap normalized ranges as desired.
- `out <n> cv1`, `out <n> gate2`, etc. will send the signal to a CV or Gate output (the timing resolution is limited by the block rate, 1ms by default). `<n>` just needs to be an output channel number that isn't being used for anything else.
- `in <n> midi` will give you a signal packed with any incoming MIDI bytes (see examples on parsing the signal). `<n>` just needs to be an output channel number that isn't being used for anything else.
- `out <n> midi` will let you fill a signal with MIDI data to send to the device MIDI output (see examples on formatting the signal). `<n>` just needs to be an output channel number that isn't being used for anything else.

