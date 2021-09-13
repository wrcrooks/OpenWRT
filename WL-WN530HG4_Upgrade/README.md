# Wavlink WL-WN530HG4 Upgrade (Hardware + Software)

## Description
Resources for Unlocking the Full Potential of a $20 Wavlink WL-WN530HG4 (OpenWRT Install/Hardware Upgrades)

## Table of Contents
* [Materials](#materials)
* [Concepts](#concepts)
* [Questions](#questions)


## Materials
Tested on Two Arduino Nanos. Wiring Diagram:
| <u>Item</u>                                                | <u>QTY</u> | <u>Link</u> |
| ---------------------------------------------------------- | ---------- | ----------- |
| USB CH341A Programmer                                      | 1          | https://www.amazon.com/gp/product/B07YFPJQ5W |
| Winbond 128Mb (16MB) NOR Quad I/O SPI Flash (W25Q128JVSIM) | 1          | https://www.digikey.com/en/products/detail/winbond-electronics/W25Q128JVSIM/6819721 |


## Concepts
<!-- Some lessons learned during this project:
1. Only the Controller can initate communications, not Peripherals
2. SPI is "Bidirectional", meaning that <code>SPI.transfer()</code> returns a value from the <b><code>SPDR</code></b> pointer on the Peripheral
3. When <b><code>SPDR</code></b> is set during an interrupt, the set value is not sent to the Controller until the next time the Controller initiates at transfer
This means that the Controller and Peripheral will <i>always</i> be out of sync in that <code>indexController = 0</code> and <code>indexPeripheral = -1</code>
4. There is very little documentation on sending SPI messages larger than one Byte -->


## Questions

GitHub: [@wrcrooks](https://www.github.com/wrcrooks)\
Website: [willcrooks.net](http://www.willcrooks.net)