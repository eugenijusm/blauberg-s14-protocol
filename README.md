# blauberg-s14-protocol

> [!Important]
> Disclaimer: Use this information at your own risk. Author is not responsible in any way for any damage.

Below is reverse engineered protocol which is used for S14 recuperator control panels by Recom and Blauberg. Note there is also S22 panel, which externally looks the same, but uses completely different protocol. These are not interchangeable; it's also possible to use other panel models with wifi or lcd instead of S22, but not instead of S14. So check your unit documentation. I didn't have hands-on S22 panel but according to documentation it's possible to have multiple panels, so it has address switches inside. Circuitry inside S14 is quitte simple.

This was analyzed on Recom 3 SE-EPP recuperator.

Communication is uart  
Baudrate: 600  
Logic levels: 3.3V (!)  
Rx/Tx marking on panel is from panels perspective; i.e. tx is panel transmission to main unit  

Whole protocol consists of single byte sent by control panel and single byte response from main unit.  
Panel sends data even when unit is turned off.  
Interval between panel bytes is 125ms

In case panel doesn't receive any responses from main unit, exclamation pictogram starts to blink (if it constantly lit it means main unit reports fault, see below).

Panel data
| Bit | Meaning |
| - | - |
| 0 | Speed |
| 1 | Speed |
| 2 | Filter button is touched |
| 3 | Bypass active |
| 4 | Not used |
| 5 | Not used |
| 6 | Not used |
| 7 | Not used |

Speed is expressed as mode in binary form (LSB first): 0 = 00, 1 = 10, 2 = 01, 3 = 11  
Bypass is a flag  
Filter button is used to reset filter counter, counter is handled by main unit (see response below) and to reset it button needs to be held for a few seconds. That seems to be detected and handled by main unit and all panel does is just provide information if it's pressed or not.  

So:  
0x0E = Speed 2, Bypass active, Filter button touched;  
0x0D =  Speed 3, Bypass active

Main unit data
| Bit | Meaning |
| - | - |
| 0 | Filter timer expired |
| 1 | Fault |
| 2 | Not used |
| 3 | Not used |
| 4 | Not used |
| 5 | Not used |
| 6 | Not used |
| 7 | Not used |

In normal circumstances main unit replies with 0x00
