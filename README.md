# Improved Speedio Toolsetter Macros

Originally written by Alex Rosner of [Wisconsin CAM Solutions](https://www.instagram.com/wisconsin_cam), with minor modifications and documentation written by Justin Gray of Toolpath Labs

These macros have been written to assume that Tool number and Pot number match (i.e. Tool 1 is in pocket 1)

If you are using arbitrary tool numbers, then these will not work for you. 


## Installtion 

1) Copy these macros into your speedio control and run them. 
2) Remove any offset values 54.1P48. The macros assume this extended work offset matches the G53 (for stupid control reasons this works better than actually using G53)


## Tool Data Entry 
Before using these macros, you should edit programs `O8502.NC`, and `O8503.NC` 

All data units match your control setup. If you run in imperial, use inches. If you run in metric use mm.

### 8502 -- Settings File 

General machine specific settings. 

## #111, #112
You must change values for #111, #112 to match your machine units. 
	- 4000 mm/min and 500 mm/min are good for metric machines. 
	- 150 inch/min and 20 inch/min are good for imperial machines.

You must set #115 to match your gauge tool length, again respecting your machine units

## #113 - Back off distance for second touch
The faster you feed, the larger you need to back off for the second touch.
This one needs to be changed to match machine units.
Reasonable defaults are: 
 	- 2mm 
 	- 0.08inch

## #115 - Calibration Tool Gauge Length 
Set to match your tool, in your machine units

## #116 - Tool Breakage Saftey Distance Above Setter
Height above tool setter that you will rapid to before seeing for tool breakage detection

## #117 - Tool Offset Direction
set based on the location of your tool setter and the direction you want the tool to offset when a diameter is given. Most common choices are: 
	- Back Left (Tool shifts toward X+): 0
	- Back Right (Tool shifts toward X-): 180

But any angle is technically allowable. 

## 118 - Reference Tool Setter Seek Height 

This is a round numver large enough to seek the tool setter, but not too large to exceed z stroke. 

Change to match your units, and account for your column height. 

Typical machines have 300mm stroke, so 280 is fine. For imperial machines set to 11 inches. Some newer speedios have a large Z stroke of 380mm

## #119 Calibration Tool Number
 
Calibration Tool number to be used. Be sure to put your calibration tool in this pocket when doing calibration


```

### 8503 -- Tool Data File
Find the section matching your tool number and input the number of inserts, diameter, and orientation. 
If you are using a tool that you want to center touch, then leave them all zero. 

Example for T3, assuming a solid carbide endmill
```
N3
#106 = 0 (NUMBER OF INSERTS - IGNORED UNLESS DIAMETER IS NON ZERO)
#107 = 0 (DIAMETER TO USE FOR TOUCHOFF - USE 0 FOR CENTER TOUCHOFF)
#108 = 0 (DEGREES TO ORIENT SPINDLE FOR FIRST INSERT)
```


Example of T18 being a 2" diameter 4 insert face mill, but running on metric machine
```
N18
#106 = 4 (NUMBER OF INSERTS - IGNORED UNLESS DIAMETER IS NON ZERO)
#107 = 50.8 (DIAMETER TO USE FOR TOUCHOFF - USE 0 FOR CENTER TOUCHOFF)
#108 = 0 (DEGREES TO ORIENT SPINDLE FOR FIRST INSERT)
```


## Tool Setter Calibration - O8504

1) Position gauge tool at the XY location of the toolsetter 
2) Z height can be any level above the tool setter, as long as its not triggered 
3) call program `O8504.NC`


## Single Tool Setting - O8500

Run program `O8500.NC`, controled by going to the N# matching the tool number you want to touch off

## Multiple Tool Setting

1) Edit O8500.NC, lines 4 and 5 to set min and max tool number
2) Run program `O8500.NC`, cycle start from the top of the file




## Tool Breakage Detection - O8505

This is meant to be used a subprogram, called like `G65 P8505 E0.005`

The `E` argument sets the tolerance for the length check. Any discrepancy above the E value will throw an error. 


Note: this macro can not be used on tools that have a given diameter in the tool data file (`O8503`). It will safely error if called with those tools, but to avoid error do not enable break check on these tools. 



## TODO 

[ ] Explicit error check if G54.1 P48 is non-zero
[ ] Allow Breakcheck on tools with diameter and inserts
[ ] Improve settings file to pull default feed rates based on machine units
[ ] Account for primary offset angle (#117) in tool setting angle values to make tool data independent of tool setter location