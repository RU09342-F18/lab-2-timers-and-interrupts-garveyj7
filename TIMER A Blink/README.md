# Timer A Blink on the MSP430G2553 and MSP430F5529
This code blinks the LED on the MSP430s and increases the blink speed as the user presses the button.  It uses interrupts to work, as opposed the previous lab where delay cycles were used.  Interrupts are events (ex. a button press) that cause the cpu to stop the normal program and perform operations (will be covered later).

# How does this code work?
Firstly, the LED and button are defined as output and input.  This is done by setting PxDIR and PxOUT.  A pull up resistor is defined on the button to make it work properly (PxREN).  Next, the interrupt is set up.

## Interrupts
This code enables an interrupt on the button and capture control register (CCRn).  This makes it so that when either of them are triggered, an operation is performed.  Interrupts are established on the button by PxIE, PxIES.  Next, CCRn is defined.  In order to put the MSP430 in CCRn mode, CCTLx is set to CCIE (Capture/compare interrupt enable).  

There are a variety of clock select options on the board, the point of this lab was to use ACLK.  In order to set the clock the TASSELx function was used.
* 00 - TACLK
* 01 - ACLK
* 10 - SMCLK
* 11 - INCLK

This was set through TACTL = TASSEL_1.  However, these clocks move fast, so their value are divided with an internal divider (ID_x).

* 00 - /1
* 01 - /2
* 10 - /4
* 11 - /8

An initial integer is set for the CCRn.  The clock counts up and down with integers, when the integer reaches CCRn, the LED is triggered.

Next, the interrupts are enabled with enable_interrupt() and the code uses a while loop to loop infinitely.

## The timer interrupt
The timer interrupt occurs when the clock reaches the value stored in CCRn.  In this event, the LED is toggled on/off.

## The button interrupt
The button interrupt occirs when the button is pressed.  The button press decrements CCRn by a value, effectively making the next timer interrupt occur sooner.  This results in the LED delay shortening.  Additionally, the flag set on the button is cleared.

## MSP430G2553 Specific changes
In order to get the clock to run properly, this must be inserted into main.
```c
BCSCTL3 = LFXT1S_2;
```
This enables the internal oscillator for the clock on board.  If this is omitted, the G2 will look for an external clock to use.

## MSP430F5529 Specific changes
When converting the code to work with the F5529, the output and input pins had to be changed.  Additionally, all instances of the control register must include 'TAx' when compared to the G2.
For example:
CCTLx (On the G2) becomes TA0CCTLx (On the F5529)

<!---# TIMER A Blink
The TIMER peripherals can be used in many situations thanks to it flexibility in features. For this lab, you will be only scratching the surface as to what this peripheral can do. 

## Up, Down, Continuous 
There are a few different ways that the timer module can count. For starters, one of the easiest to initialize is Continuous counting where in the TIMER module will alert you when its own counting register overflows. Up mode allows you to utilize a Capture/Compare register to have the counter stop at a particular count and then start back over again. You can also set the TIMER to Up/Down mode where upon hitting a counter or the overflow, instead of setting the counter back to zero, it will count back down to zero. 

## Task
Using the TIMER module instead of a software loop, control the speed of two LEDS blinking on your development boards. Experiment with the different counting modes available as well as the effect of the pre-dividers. Why would you ever want to use a pre-divider? What about the Capture and Compare registers? Your code should include a function (if you want, place it in its own .c and .h files) which can convert a desired Hz into the proper values required to operate the TIMER modules.

### Extra Work
#### Thinking with HALs
So maybe up to this point you have noticed that your software is looking pretty damn similar to each other for each one of these boards. What if there was a way to abstract away all of the particulars for a processor and use the same functional C code for each board? Just for this simple problem, why don't you try and build a "config.h" file which using IFDEF statements can check to see what processor is on board and initialize particular registers based on that.

#### Low Power Timers
Since you should have already done a little with interrupts, why not build this system up using interrupts and when the processor is basically doing nothing other than burning clock cycles, drop it into a Low Power mode. Do a little research and figure out what some of these low power modes actually do to the processor, then try and use them in your code. If you really want to put your code to the test, using the MSP430FR5994 and the built in super cap, try and get your code to run for the longest amount of time only using that capacitor as your power source. --->
