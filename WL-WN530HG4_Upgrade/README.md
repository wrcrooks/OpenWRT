# Wavlink WL-WN530HG4 Upgrade (Hardware + Software)

## Description
Resources for Unlocking the Full Potential of a $20 Wavlink WL-WN530HG4 (OpenWRT Install/Hardware Upgrades)

## Table of Contents
* [Materials](#materials)
* [Procedure](#procedure)
* [Resources](#resources)
* [Questions](#questions)
<!-- * [Concepts](#concepts) -->


## Materials
| <u>Item</u>                                                | <u>QTY</u> | <u>Link</u> |
| ---------------------------------------------------------- | ---------- | ----------- |
| USB CH341A Programmer                                      | 1          | https://www.amazon.com/gp/product/B07YFPJQ5W |
| Winbond 128Mb (16MB) NOR Quad I/O SPI Flash (W25Q128JVSIM) | 1          | https://www.digikey.com/en/products/detail/winbond-electronics/W25Q128JVSIM/6819721 |



<!-- ## Concepts -->
<!-- Some lessons learned during this project:
1. Only the Controller can initate communications, not Peripherals
2. SPI is "Bidirectional", meaning that <code>SPI.transfer()</code> returns a value from the <b><code>SPDR</code></b> pointer on the Peripheral
3. When <b><code>SPDR</code></b> is set during an interrupt, the set value is not sent to the Controller until the next time the Controller initiates at transfer
This means that the Controller and Peripheral will <i>always</i> be out of sync in that <code>indexController = 0</code> and <code>indexPeripheral = -1</code>
4. There is very little documentation on sending SPI messages larger than one Byte -->

## Procedure
1. Disassemble router using a flat blade screwdriver and flat objects (such as plastic cards) to split two halves of the outer casing
    1. There were two plastic posts on the inside of my unit that had screws through them, accessible from under the label on the bottom
2. Using the USB CH341A Programmer and flashrom CLI utility (Linux) or AsProgrammer (Windows), make a backup of the stock Wavlink firmware (this will also be modified in the next step)
3. Remove the factory NOR SPI FLASH chip using a soldering iron and wick (preferred) or a heat gun
4. You can either program the replacement NOR QSPI FLASH chip before soldering (see Steps 6 & 7) or wait until after it is on the board (as long as you have the clips for the programmer)
5. Solder the replacement NOR QSPI FLASH chip onto the board in place of the now removed factory chip. *NOTE*: *Make sure pin one is in the same place as the factory chip (denoted by the dot on the IC)*
6. Expand the stock bin file with f pairs to make it 16MB in size
    1. Generate an empty bin file with the following command: <code>perl -e 'print "ff "x(16\*1024\*1024)'|xxd -r -p >16MB-EMPTY.bin</code> or download from [here](https://github.com/wrcrooks/OpenWRT/blob/main/WL-WN530HG4_Upgrade/bin/16MB-EMPTY.bin)
    2. Write the stock bin file to the front of the empty bin file with the following command: <code>dd if=\<ReadFactoryBin\> of=16MB-EMPTY.bin conv=notrunc</code> or download from [here](https://github.com/wrcrooks/OpenWRT/blob/main/WL-WN530HG4_Upgrade/bin/16MB-FACTORY-WN530HG4.bin) *NOTE*: *This factory firmware was shipped with my unit and may be outdated*
    3. Rename file something identifiable, EX: *16MB-FACTORY-WN530HG4.bin*
7. Flash the expanded stock bin file to the replacement NOR QSPI FLASH chip
8. Boot the device
9. Load initramfs OpenWRT build [link](http://downloads.openwrt.org/releases/21.02.0/targets/ramips/mt7620/openwrt-21.02.0-ramips-mt7620-wavlink_wl-wn530hg4-initramfs-kernel.bin) renamed to *WN530HG4-WAVLINK.bin*, also located [here](https://github.com/wrcrooks/OpenWRT/blob/main/WL-WN530HG4_Upgrade/bin/WN530HG4-WAVLINK.bin)
10. Upgrade OpenWRT to squashfs [provided](https://github.com/wrcrooks/OpenWRT/blob/main/WL-WN530HG4_Upgrade/bin/openwrt-ramips-mt7620-wavlink_wl-wn530hg4-squashfs-sysupgrade.bin) or custom compile your own with below modification
    1. Modify Line 83 of file <code>$BUILD_DIR/target/ramips/dts/mt7620a_wavlink_wl-wn530hg4.dts</code>
        1. Change <code>reg = <0x50000 0x7b0000></code> to <code>reg = <0x50000 0xfb0000></code>
        2. The reasoning here being that we want to add <code>0x800000 (DEC:8388608) 8MB</code> to <code>0x7b0000</code>
11. You should now see ~8.9MB (94%) available in the Software tab of OpenWRT
    1. <code>df -h</code> reveals that /rom is using up 5.0M and /overlay is using up 8.9M, so a total of 13.9/16.0MB is accounted for. Let's do the math using <code>mt7620a_wavlink_wl-wn530hg4.dts</code> or <code>cat /proc/mtd</code>
    2. Entire 16MB Flash: <code>0x1000000 (DEC:16777216) --> <u>16777216</u> / 1024 / 1024 = 16MB</code>
        1. Partition "u-boot": <code>0x0000000 (DEC:0) to 0x0030000 (DEC:196608) --> 196608 / 1024 = 192KB</code>
        2. Partition "config": <code>0x0030000 (DEC:196608) to 0x0040000 (DEC:262144) --> 65536 / 1024 = 64KB</code>
        3. Partition "factory": <code>0x0040000 (DEC:262144) to 0x0050000 (DEC:327680) --> 65536 / 1024 = 64KB</code>
        4. Partition "firmware": <code>0x0050000 (DEC:327680) to 0x1000000 (DEC:16777216) --> (<u>0x0FB0000</u>) 16449536 / 1024 / 1024 = 15.6875</code>
    3. Summary: <code>196608 + 65536 + 65536 + 16449536 = <u>16777216</u></code>
        1. The compiled squashfs image is 7.1MB in size so the remaining 8.9MB makes sense

## Resources
https://openwrt.org/toh/hwdata/wavlink/wavlink_wl-wn530hg4

## Questions
GitHub: [@wrcrooks](https://www.github.com/wrcrooks)\
Website: [willcrooks.net](http://www.willcrooks.net)