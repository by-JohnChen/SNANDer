SNANDer
===

SNANDer - 串口 Nor/nAND/Eeprom 编程软件 (基于 CH341A)

![ch341a-nextProgrammer](https://raw.githubusercontent.com/Droid-MAX/SNANDer/master/photos/ch341a-nextProgrammer.jpg)

**依赖**

* gcc/mingw-w64, wget, make,和 libusb-1.0-dev (通过 apt 命令行工具)

**使用 Linux 编译**

* 在Debian/Ubuntu上安装依赖

```
sudo apt-get install gcc wget make libusb-1.0-dev -y
```

* 安装gcc和必要工具后，编译 `snander` 只需执行一条简单的命令：

```
make -C src/
```

编译成功后，可以在 `src`文件夹中找到生成的可执行文件。

* 或者，您也可以选择静态编译，静态编译 `snander` 只需执行一条简单的脚本命令:

```
./build-for-linux.sh
```

编译成功后，可以在 `build` 文件夹中找到生成的可执行文件。

**使用 Windows 编译**

* 使用 `PowerShell` 在 Windows上安装 `choco` 包管理工具

```
iwr https://chocolatey.org/install.ps1 -UseBasicParsing | iex
```

* 使用 choco 安装依赖

```
choco install mingw-w64 -y
```

* 安装 mingw-w64 和必要工具后, 编译 `snander` 只需执行一条简单的脚本命令:

```
./build-for-windows.sh
```

编译成功后，将在 `build` 文件夹中找到生成的可执行文件，包括x86和x64二进制文件。

**使用 MacOS 编译**

* 在 MacOS 上安装 `homebrew` 包管理工具

```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

* 使用 `homebrew` 安装依赖

```
brew install gcc wget make -y
```

* 安装必要工具后, 编译 `snander` 只需执行一条简单的脚本命令:

```
./build-for-darwin.sh
```

编译成功后，可以在 `build` 文件夹中找到生成的可执行文件。

**用法**

使用 `snander` 一目了然:

```
SNANDer - Serial Nor/nAND/Eeprom programmeR v.1.7.8 by McMCC <mcmcc@mail.ru>

  Usage:
 -h             display this message
 -d             disable internal ECC(use read and write page size + OOB size)
 -o <bytes>     manual set OOB size with disable internal ECC(default 0)
 -I             ECC ignore errors(for read test only)
 -k             Skip BAD pages, try to read or write in next page
 -L             print list support chips
 -i             read the chip ID info
 -E             select I2C EEPROM {24c01|24c02|24c04|24c08|24c16|24c32|24c64|24c128|24c256|24c512|24c1024}
                select Microwire EEPROM {93c06|93c16|93c46|93c56|93c66|93c76|93c86|93c96} (need SPI-to-MW adapter)
                select SPI EEPROM 25xxx {25010|25020|25040|25080|25160|25320|25640|25128|25256|25512|251024}
 -8             set organization 8-bit for Microwire EEPROM(default 16-bit) and set jumper on SPI-to-MW adapter
 -f <addr len>  set manual address size in bits for Microwire EEPROM(default auto)
 -s <bytes>     set page size from datasheet for fast write SPI EEPROM(default not use)
 -e             erase chip(full or use with -a [-l])
 -l <bytes>     manually set length
 -a <address>   manually set address
 -w <filename>  write chip with data from filename
 -r <filename>  read chip and save data to filename
 -v             verify after write on chip
```

范例:

1. 获取 Flash 信息.

```
$ ./SNANDer -i

SNANDer - Serial Nor/nAND/Eeprom programmeR v.1.7.8 by McMCC <mcmcc@mail.ru>

Found programmer device: WinChipHead (WCH) - CH341A
Device revision is 3.0.4
spi_nand_probe: mfr_id = 0xc2, dev_id = 0x22
Get Status Register 1: 0x38
Get Status Register 2: 0x10
Using Flash ECC.
Detected SPI NAND Flash: MXIC MX35LF2G, Flash Size: 256 MB
$
```

2. 完全擦除闪存，禁用内部ECC检查。无OOB，页面大小为2112字节

```
$ ./SNANDer -d -e

SNANDer - Serial Nor/nAND/Eeprom programmeR v.1.7.8 by McMCC <mcmcc@mail.ru>

Found programmer device: WinChipHead (WCH) - CH341A
Device revision is 3.0.4
spi_nand_probe: mfr_id = 0xc2, dev_id = 0x22
Get Status Register 1: 0x00
Get Status Register 2: 0x11
Disable Flash ECC.
Detected SPI NAND Flash: MXIC MX35LF2G, Flash Size: 256 MB
ERASE:
Set full erase chip!
Erase addr = 0x0000000000000000, len = 0x0000000010800000
Erase 100% [276824064] of [276824064] bytes
Elapsed time: 3 seconds
Status: OK
$
```

3. 使用禁用内部ECC检查的方式写入并验证闪存。无OOB，页面大小为2112字节。

```
$ ./SNANDer -d -v -w ecc_2Gb_2K_64_flashimage_rfb1_ac2600.bin

SNANDer - Serial Nor/nAND/Eeprom programmeR v.1.7.8 by McMCC <mcmcc@mail.ru>

Found programmer device: WinChipHead (WCH) - CH341A
Device revision is 3.0.4
spi_nand_probe: mfr_id = 0xc2, dev_id = 0x22
Get Status Register 1: 0x00
Get Status Register 2: 0x11
Disable Flash ECC.
Detected SPI NAND Flash: MXIC MX35LF2G, Flash Size: 256 MB
WRITE:
Write addr = 0x0000000000000000, len = 0x000000000E5A9D6F
Written 100% [240819567] of [240819567] bytes
Elapsed time: 4184 seconds
Status: OK
VERIFY:
Read addr = 0x0000000000000000, len = 0x000000000E5A9D6F
Read 100% [240819567] of [240819567] bytes
Elapsed time: 2047 seconds
Status: OK
$
```

4. 读取Microwire EEPROM Atmel 93C46并保存文件。

```
$ ./SNANDer -E 93c46 -r test.bin

SNANDer - Serial Nor/nAND/Eeprom programmeR v.1.7.8 by McMCC <mcmcc@mail.ru>

Found programmer device: WinChipHead (WCH) - CH341A
Device revision is 3.0.4
Microwire EEPROM chip: 93c46, Size: 64 bytes, Org: 16 bits, fix addr len: Auto
READ:
Read addr = 0x0000000000000000, len = 0x0000000000000080
Read_EEPROM_3wire: Set address len 6 bits
Read 100% [64] of [64] bytes
Read [128] bytes from [93c46] EEPROM address 0x00000000
Elapsed time: 0 seconds
Status: OK
```

5. 从文件中写入并验证Microwire EEPROM Atmel 93C46。

```
$ ./SNANDer -E 93c46 -w test.bin -v

SNANDer - Serial Nor/nAND/Eeprom programmeR v.1.7.8 by McMCC <mcmcc@mail.ru>

Found programmer device: WinChipHead (WCH) - CH341A
Device revision is 3.0.4
Microwire EEPROM chip: 93c46, Size: 64 bytes, Org: 16 bits, fix addr len: Auto
WRITE:
Write addr = 0x0000000000000000, len = 0x0000000000000080
Erase_EEPROM_3wire: Set address len 6 bits
Write_EEPROM_3wire: Set address len 6 bits
Written 100% [64] of [64] bytes
Wrote [128] bytes to [93c46] EEPROM address 0x00000000
Elapsed time: 1 seconds
Status: OK
VERIFY:
Read addr = 0x0000000000000000, len = 0x0000000000000080
Read_EEPROM_3wire: Set address len 6 bits
Read 100% [64] of [64] bytes
Read [128] bytes from [93c46] EEPROM address 0x00000000
Elapsed time: 1 seconds
Status: OK
```

6. 从文件中写入并验证SPI EEPROM Atmel AT25640B。

```
$ ./SNANDer -E 25640 -v -w test.bin

SNANDer - Serial Nor/nAND/Eeprom programmeR v.1.7.8 by McMCC <mcmcc_at_mail.ru>

Found programmer device: WinChipHead (WCH) - CH341A
Device revision is 3.0.4
SPI EEPROM chip: 25640, Size: 8192 bytes
WRITE:
Written addr = 0x0000000000000000, len = 0x0000000000002000
Wrote 100% [8192] bytes to [25640] EEPROM address 0x00000000
Elapsed time: 22 seconds
Status: OK
VERIFY:
Read addr = 0x0000000000000000, len = 0x0000000000002000
Read 100% [8192] bytes from [25640] EEPROM address 0x00000000
Elapsed time: 2 seconds
Status: OK
```

7. 使用页面大小快速写入并验证SPI EEPROM Atmel AT25640B文件。 （请从芯片数据手册中查找页面大小！！！）

```
$ ./SNANDer -E 25640 -v -w test.bin -s 32

SNANDer - Serial Nor/nAND/Eeprom programmeR v.1.7.8 by McMCC <mcmcc_at_mail.ru>

Found programmer device: WinChipHead (WCH) - CH341A
Device revision is 3.0.4
SPI EEPROM chip: 25640, Size: 8192 bytes
Setting page size 32B for write.
WRITE:
Write addr = 0x0000000000000000, len = 0x0000000000002000
Written 100% [8192] bytes to [25640] EEPROM address 0x00000000
Elapsed time: 1 seconds
Status: OK
VERIFY:
Read addr = 0x0000000000000000, len = 0x0000000000002000
Read 100% [8192] bytes from [25640] EEPROM address 0x00000000
Elapsed time: 2 seconds
Status: OK
```

**芯片支持列表**

```
SPI NAND Flash Support List:
001. GIGADEVICE GD5F1GQ4UA
002. GIGADEVICE GD5F1GQ4UB
003. GIGADEVICE GD5F1GQ4UC
004. GIGADEVICE GD5F1GQ4UE
005. GIGADEVICE GD5F1GQ5UE
006. GIGADEVICE GD5F1GQ5RE
007. GIGADEVICE GD5F2GQ4UB
008. GIGADEVICE GD5F2GQ4UE
009. GIGADEVICE GD5F2GQ4UC
010. GIGADEVICE GD5F4GQ4UB
011. GIGADEVICE GD5F4GQ4UC
012. ESMT F50L512
013. ESMT F50L1G
014. ESMT F50L1G41LB
015. ESMT F50L2G41LB
016. WINBOND W25N01G
017. WINBOND W25N02KV
018. WINBOND W25M02G
019. MXIC MX35LF1G
020. MXIC MX35LF2G
021. MXIC MX35LF2GE4AD
022. ZENTEL A5U12A21ASC
023. ZENTEL A5U1GA21BWS
024. ETRON EM73C044SNB
025. ETRON EM73C044SND
026. ETRON EM73C044SNF
027. ETRON EM73C044VCA
028. ETRON EM73C044VCD
029. ETRON EM73D044VCA
030. ETRON EM73D044VCB
031. ETRON EM73D044VCD
032. ETRON EM73D044VCG
033. ETRON EM73D044VCH
034. ETRON EM73D044SNA
035. ETRON EM73D044SNC
036. ETRON EM73D044SND
037. ETRON EM73D044SNF
038. ETRON EM73E044SNA
039. TOSHIBA TC58CVG0S3H
040. TOSHIBA TC58CVG1S3H
041. TOSHIBA TC58CVG2S0H
042. KIOXIA TC58CVG2S0HRAIJ
043. MICRON MT29F1G01
044. MICRON MT29F2G01
045. MICRON MT29F4G01
046. HEYANG HYF1GQ4UAACAE
047. HEYANG HYF2GQ4UAACAE
048. HEYANG HYF2GQ4UHCCAE
049. HEYANG HYF1GQ4UDACAE
050. HEYANG HYF2GQ4UDACAE
051. PN PN26G01A-X
052. PN PN26G02A-X
053. PN PN26Q01A-X
054. ATO ATO25D1GA
055. ATO ATO25D2GA
056. ATO ATO25D2GB
057. FM FM25S01
058. FM FM25S01A
059. FM FM25G01B
060. FM FM25G02B
061. FM FM25G02C
062. FM FM25G02
063. XTX XT26G02B
064. XTX XT26G01C
065. XTX XT26G01A
066. XTX XT26G02A
067. MIRA PSU1GS20BN
068. BIWIN BWJX08U
069. BIWIN BWET08U
070. FORESEE FS35ND01GD1F1
071. FORESEE FS35ND01GS1F1
072. FORESEE FS35ND02GS2F1
073. FORESEE FS35ND02GD1F1
074. FORESEE FS35ND02G-S3Y2
075. FORESEE FS35ND04G-S2Y2
076. DS DS35Q2GA
077. DS DS35Q1GA
078. FISON CS11G0T0A0AA
079. FISON CS11G1T0A0AA
080. FISON CS11G0G0A0AA
081. TYM TYM25D2GA01
082. TYM TYM25D2GA02
083. TYM TYM25D1GA03
084. XINCUN XCSP1AAWH-NT

SPI NOR Flash 支持列表:
001. AT25DF321
002. AT26DF161
003. F25L016
004. F25L16QA
005. F25L032
006. F25L32QA
007. F25L064
008. F25L64QA
009. GD25Q16
010. GD25Q32
011. GD25Q64CSIG
012. GD25Q128CSIG
013. YC25Q128
014. GD25Q256CSIG
015. MX25L8005M
016. MX25L1605D
017. MX25L3205D
018. MX25L6405D
019. MX25L12805D
020. MX25L25635E
021. MX25L51245G
022. FL016AIF
023. FL064AIF
024. S25FL016P
025. S25FL032P
026. S25FL064P
027. S25FL128P
028. S25FL129P
029. S25FL256S
030. S25FL512S
031. S25FL116K
032. S25FL132K
033. S25FL164K
034. EN25F16
035. EN25Q16
036. EN25QH16
037. EN25Q32B
038. EN25F32
039. EN25F64
040. EN25Q64
041. EN25QA64A
042. EN25QH64A
043. EN25Q128
044. EN25Q256
045. EN25QA128A
046. EN25QH128A
047. W25X05
048. W25X10
049. W25X20
050. W25X40
051. W25X80
052. W25X16
053. W25X32VS
054. W25X64
055. W25Q20CL
056. W25Q20BW
057. W25Q20EW
058. W25Q80
059. W25Q80BL
060. W25Q16JQ
061. W25Q16JM
062. W25Q32BV
063. W25Q32DW
064. W25Q64BV
065. W25Q64DW
066. W25Q128BV
067. W25Q128FW
068. W25Q256FV
069. W25Q256JW
070. W25Q256JWIM
071. W25Q512JV
072. W25Q512JVIM
073. W25Q512NW
074. W25Q512NWIM
075. M25P016
076. N25Q032A
077. N25Q064A
078. M25P128
079. N25Q128A
080. XM25QH32B
081. XM25QH32A
082. XM25QH64A
083. XM25QH128A
084. N25Q256A
085. MT25QL512AB
086. MD25D20
087. MD25D40
088. ZB25VQ16
089. ZB25VQ32
090. ZB25VQ64
091. ZB25VQ128
092. BY25D10
093. BY25D20
094. BY25D40
095. BY25D80
096. BY25Q16BS
097. BY25Q32BS
098. BY25Q64AS
099. BY25Q128AS
100. XT25F32B
101. XT25F32B
102. XT25F64B
103. XT25F128B
104. XT25Q128D
105. PM25LQ016
106. PM25LQ032
107. PM25LQ064
108. PM25LQ128
109. IC25LP016
110. IC25LP032
111. IC25LP064
112. IC25LP128
113. FS25Q016
114. FS25Q032
115. FS25Q064
116. FS25Q128
117. FM25W16
118. FM25W32
119. FM25W64
120. FM25W128
121. FM25Q16A
122. FM25Q32A
123. FM25Q64A
124. FM25Q128A
125. PN25F16
126. PN25F32
127. PN25F64
128. PN25F128
129. P25Q16H
130. P25Q32H
131. P25Q64H
132. P25Q128H

I2C EEPROM 支持列表:
001. 24c01
002. 24c02
003. 24c04
004. 24c08
005. 24c16
006. 24c32
007. 24c64
008. 24c128
009. 24c256
010. 24c512
011. 24c1024

Microwire EEPROM 支持列表:
001. 93c06
002. 93c16
003. 93c46
004. 93c56
005. 93c66
006. 93c76
007. 93c86
008. 93c96

SPI EEPROM 支持列表t:
001. 25010
002. 25020
003. 25040
004. 25080
005. 25160
006. 25320
007. 25640
008. 25128
009. 25256
010. 25512
011. 251024
```

**作者**

最初由 [McMCC](https://github.com/McMCCRU/SNANDer) 并根据GNU GPL 2.0或更高版本发布。
[Droid-MAX](https://github.com/Droid-MAX/) 的修改使其更容易在Windows上编译。
[JohnChen](https://github.com/by-JohnChen) 添加简体中文翻译。

**许可**

This is free software: you can redistribute it and/or modify it under the terms of
the latest GNU General Public License as published by the Free Software Foundation.

This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY;
without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
See the GNU General Public License for more details.

You should have received a copy of the GNU General Public License along with this program.
If not, see <http://www.gnu.org/licenses/>.
