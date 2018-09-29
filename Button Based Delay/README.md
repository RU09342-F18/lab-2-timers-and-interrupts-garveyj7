# Button Based Delay
The button-based delay was implemented on the MSP430G2553 and MSP430F5529.  This code uses a button on the MSP430s to set the blinking frequency of the LED on board.  For example, if the user holds in the button for 1 second, there will be a 1 second delay on the LED.  This code utilizes the onboard clocks and the interrupt feature.

## Looking into the code
Firstly, the pins are assigned to the LED and button.  The pull up resistor is enabled on the button.  In order to utilize the interrupt, it is assigned to the button.

CCR0 is the key variable in this program, this is the value that the internal clock counts up to before toggling the LED.  This value can be set as an initial condition, and is altered later on by the button.

## The timer interrupt
The timer interrupt occurs when the internal clock counts up to the value of CCR0.  When this condition is met, the LED toggles on or off.
## The button interrupt
The button interrupt occurs when the user presses the button.  It has two instances, when the button is pressed, and when the button is released.  When the button is pressed, Timer A is cleared, the interrupt is disabled, and a timer counts the length of time the button is held in.  When the button is released, CCR0 is assigned the amount of time that the button was held in.  Additionally, the interrupt is enabled again and ready for the button to be pressed once more.

## MSP430G2553 Specific Changes
The initial code was created using the MSP430G2. 
The G2 code uses the ACLK (TASSEL_1), this clock is one of four available on the board.
It is important to note that this line of code must be implemented if using the G2:
BCSCTL3 = LFXT1S_2;
This line of code allows for the clock to use internal oscillator.  Without this, the code will not run properly because the MSP430 assumes it can use an external oscillator.

## MSP430F5529 Specific Changes
The F5529 uses the TACLK (TASSEL_0), it was found that when trying to use the ACLK on this board, it was easy to overflow CCR0.  This would force the LED to be either on or off and not blink.  This was remedied by choosing TACLK and reducing the internal divider to 2.

When using the F5529, there is no need to tell it to use the internal oscillator.

Additionally, a couple more things change when compared to the G2:
Timer names: For example, all instances of CTL (on the G2) become TA0CTL.
Button pin assignment: P1.3 on the G2 becomes P1.1
<!--- ************************************************************************************************************************
# Button Based Delay
Now that you have begun to familiarize yourself with the TIMER modules, why don't we make an interesting change to our code from the last lab.
## Task
Setup your microcontroller to initially blink and LED at a rate of 10Hz upon restarting or powering up. Then utilizing one of the buttons on board, a user should be able to set the delay or blinking rate of the LED by holding down a button. The duration in which the button is depressed should then become the new rate at which the LED blinks. As previously stated, you most likely will want to take advantage of the fact that TIMER modules exist and see if you can let them do a bulk of the work for you.
### Extra Work
## Reset Button
What is a piece of electronics without a reset button? Instead of relying on resetting your processor using the built in reset circuitry, why not instead use another button to reset the rate back to 10Hz.
## Button Based Hertz
Most likely using two buttons, what if instead of making a delay loop based on the time, the user could instead enter a mode where the number of times they pressed the button would become the number in Hz of the blinking rate? How do you think you would implement that with just one button?--->
