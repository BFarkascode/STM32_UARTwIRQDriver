# STM32_UARTwIRQDriver
Bare metal non blocking (!) master driver for UART in STM32_L0x3.

## General description

Similar to what has been done with the I2C driver, the need arose within a project of mine to make the UART driver non-blocking as well. If one thinks about it, this isn't particularly surprisingly: having a passive "sniffer" on the UART bus while our program is minding it's own business means that we may interrupt the execution of the program only when the command to do so comes over on the bus. This increases efficiency, not to mention, our program won't just sit idly around for the UART message to come around (as it currently does with the "STM32_Bootloader").

Of note, we will only make the UART non-blocking on the Rx side since that is the bigger issue in case of command-and-control systems from the slave's perspective. I currently don't see any need to extend it to Tx, nor how it could be showcased in a useful manner.

### Problems…problems…
What are the problems that are coming right at us?

1)	We still don't know the size of the message the is coming over the bus: We need to keep the OG driver's capacity to detect the end of a message.

2)	We already have an IRQ active: In the OG version of the UART driver, we were using the IRQ only when we wanted to detect idle lines (i.e. a clear point when there is no communication on the bus). This IRQ is only enabled when we have noticed the bus being activated with the right sequence - that is, a "proper" message is being sent over, on details what that means, check the OG driver. This capability must not be compromised when we extend the IRQ handle to deal with the "sniffing" or we lose all utility of our OG driver. Thus, we must slightly rework, how the idle detection is activated.

+1) DMA and TIM: Again, we could just rely on DMA, but in a command-and-control setup the number of bytes flying on the bus is maybe a few bytes every second...almost negligible. Allocating a powerful resource as DMA to it would be a huge overkill and prevent us from using it for something more complicated.

### What do we want then?
To summarize, what we want is an active "sniffing" on the UART line without blocking the execution of the code. We want the UART to be on "standby" until a message comes through and interrupt code execution only when the message has been received. This message then can be used to change the execution of the code.

## Previous relevant projects
The following projects should be checked:
- STM32_UARTDriver (we will be modifying this original code)
- STM32_I2CwIRQDriver (we will be following the same philosophy for the modification as presented here)

## To read
No additional reading material necessary.

## Particularities
None. This is a rather straightforward code optimization project.

## User guide
Same as for the OG driver.

