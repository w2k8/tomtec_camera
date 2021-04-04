# Project TomTec fotocamera

A few years ago, i have bought for my, then little childeren, a camera for there own.

![alt text][TomTec]

Now, more then 10 years later, i have found in a old box those camera's back. 

Now, noone want to use those camera's anymore. Even then it was not more then a toy.

So, now i have found the camera's back, lets crack them open en see if we can learn something from the hardware.


Lets first start the device in order to see if it still works

![TomTec-welcome]
![TomTec-goodbye]


## Opening the camera

![TomTec-open1]
After opening the camera we see directly a large IC hiding for a small part behind the LCD display.
After uncrewing and flipping over the screen we can see 1 more IC and another IC woth a large bulb of plastic on top of it.

When we take a close look at the 2 IC's, we can see an 1 MB SPI flash chip (Winbond 25D80VSIG) and another yet te determen IC. (AOTOM A641604L-6TE)

![TomTec-open2]

After we have de-solderd the SPI flash chip, we can dump the data from the chip and try to analize it.
![TomTec-open3]

![Programmer-2] 

![winbond]

After we dumped the firmware, lets use some standard tools to quick examine the data.

binwalk W25X80_20210403_170818.BIN 

|DECIMAL      | HEXADECIMAL    | DESCRIPTION|
|-------------|----------------|---------------------------------------------------|
|40416        | 0x9DE0         | JPEG image data, EXIF standard|
|40431        | 0x9DEF         | TIFF image data, big-endian, offset of first image directory: 136|
|105952       | 0x19DE0        | JPEG image data, EXIF standard|
|105967       | 0x19DEF        | TIFF image data, big-endian, offset of first image directory: 136|
|171488       | 0x29DE0        | JPEG image data, EXIF standard|
|171503       | 0x29DEF        | TIFF image data, big-endian, offset of first image directory: 136|
|237024       | 0x39DE0        | JPEG image data, EXIF standard|
|237039       | 0x39DEF        | TIFF image data, big-endian, offset of first image directory: 136|
|262656       | 0x40200        | JPEG image data, JFIF standard 1.01|
|302560       | 0x49DE0        | JPEG image data, EXIF standard|
|302575       | 0x49DEF        | TIFF image data, big-endian, offset of first image directory: 136|


Somehow, binwalk (-e) dont want to extract the images.
Lets try photorec to extract the images, if any. It can also be one or more false positive's

photorec have extracted 1 file:
```
  <fileobject>
    <filename>f0000513.jpg</filename>
    <filesize>10371</filesize>
    <byte_runs>
      <byte_run offset='0' img_offset='262656' len='32768'/>
    </byte_runs>
  </fileobject>
```

When we dump the strings from the dumpfile, we see come interesting strings.
For example SQBOOT: I have never hear from it before. But after a quick look at google, i cant find nothing interesting about SQBOOT except some boot's.

When we loop with xxd through the data we might se some interesting stuff.

And well... Wat do we see here. (after several loopt, i don't do a 64 colum view with xxd on a daily basis.)
![screenshot1]

Now i do want to change something in the firmware, and how nice would it be to drop my 
name in the shutdown screen.

```
fffff&"""ffffffffffffffffffffffffff""ff""fffffffffffffffffffffff
ffff&"""""fffffffffffffffffffffffff""ff""fffffffffffffffffffffff
ffff""""""bffffffffffffffffffffffff""ff""fffffffffffffffffffffff
fff&"""""""ffffffffffffffffffffffff""ff""fffffffffffffffffffffff
fff&""ff&""bfffffffffffffffffffffff""ff""fffffffffffffffffffffff
fff""bfff&"bfff&""ffffff""bfffff""f""ff""f""ff&"bfff"bff&""fffff
fff""ffff&bfff&""""ffff""""bfff""""""ff""""""ff"bff&"bf&"""bffff
ff&"bfffffffff"""""bff&"""""ff&""""""ff""""""bf"bff&"ff"""""ffff
ff&"bffffffff&""f&""ff""bf""bf&"bf&""ff""bf&"bf""ff&"ff""f&"bfff
ff&"bff&"""bf&"bff""ff""ff&"bf&"fff""ff""fff"bf&"ff""f&"bff"bfff
ff&"bff&"""bf""fff&"b&"bfff""f""fff""ff""fff""f&"ff"bf&"bff""fff
ff&"bff&"""bf""fff&"b&"bfff""f""fff""ff""fff""f&"bf"bf&""""""fff
fff"bff&"""bf""fff&"b&"bfff""f""fff""ff""fff""ff"b&"bf&""""""fff
fff""fffff"bf""fff&"b&"bfff""f""fff""ff""fff""ff"b&"ff&""""""fff
fff""bffff"bf""fff&"b&"bfff""f""fff""ff""fff""ff""&"ff&"bfffffff
fff&""fff""bf&"bff""ff""ff&"bf&"fff""ff""fff"bff&""bff&"bfffffff
fff&"""""""bf&""f&""ff""bf""bf&"bf&""ff""bf&"bff&""bfff""ff"ffff
ffff"""""""fff"""""bff&"""""fff""""""ff""""""fff&""bfff"""""bfff
ffff&"""""ffff&""""ffff""""bfff""""&"ff"b""""ffff""ffff&""""bfff
ffffff"""ffffff&""ffffff""bfffff""b&"ff"bf""fffff""fffff&""bffff
fffffffffffffffffffffffffffffffffffffffffffffffff""fffffffffffff
ffffffffffffffffffffffffffffffffffffffffffffffff&"bfffffffffffff
fffffffffffffffffffffffffffffffffffffffffffffff"""bfffffffffffff
fffffffffffffffffffffffffffffffffffffffffffffff"""ffffffffffffff
fffffffffffffffffffffffffffffffffffffffffffffff""bffffffffffffff
ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff
``` 

We can modiefy this to show it my name:

```
fffffffffffffffffffffffffffffffffffffffffffffffffffffffffff&"fff
ff""""""bffffffffffffffffffffffffffffffffffffffffffffffffff&"fff
ff"bfff""ffffffffffffffffffffffffffffffffffffffffffffffffff&"fff
ff"bffff"&f&"""""fff""fff""fff"bff"&""""bfff&"""""ffff&""""""fff
ff"bfff""f&"bfff""ff&"ff"""ffb"fff""fff""fff"bfff""ff&"bfff""fff
ff""""""bf""ffff&"fff"ff"f"bf""fff"ffff&"ff""ffff&"ff""ffff""fff
ff"bffffff""ffffb"fff"bf"f&"f"bfff"ffff&"ff""""""""ff""ffff""fff
ff"bffffff""ffff""fff&""ff"f""ffff"ffff&"ff""ffffffff""ffff""fff
ff"bfffffff""ff""bffff""fff"""ffff"ffff&"fff""bff&"fff""bf"""fff
ff"bffffffff&"""ffffff""fff&"bffff"ffff&"ffff&"""bfffff&""b&"fff
ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff
...
ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff
ff"""""""fffffffffffffffffffffffffffffffffffffffffffffffffffffff
ff"bfff&"bffffffffffffffffffffffffffffffffffffffffffffffffffffff
ff"bffff"bff"bffff&"ffffffffffffffffffffffffffffffffffffffffffff
ff""bff""fff&"ffff"bffffffffffffffffffffffffffffffffffffffffffff
ff""bb&""ffff"bff&"fffffffffffffffffffffffffffffffffffffffffffff
ff"bffff""fff&"ff"bfffffffffffffffffffffffffffffffffffffffffffff
ff"bffff""ffff"b&"ffffffffffffffffffffffffffffffffffffffffffffff
ff"bfff&""ffff&""bffffffffffffffffffffffffffffffffffffffffffffff
ff""""""bffffff""fffffffffffffffffffffffffffffffffffffffffffffff
fffffffffffffff"bfffffffffffffffffffffffffffffffffffffffffffffff
ffffffffffffff""ffffffffffffffffffffffffffffffffffffffffffffffff
ffffffffffff&""fffffffffffffffffffffffffffffffffffffffffffffffff
ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff
...
ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff
f"bffff""fffff"bf&"""""bffff"bffff""fff&""""bfffffffffffffffffff
f""ffff""bfff&"ffbbfff&"bfff"bffb"bfff""bff&"bffffffffffffffffff
f&"fff&"&"fff""ffffffff""fff"bf""fffff""ffff"bffffffffffffffffff
ff"bff"bf"fff"bffffffff"bfff"&""fffffff"bb&""fffffffffffffffffff
ff""ff"ff"bf&"ffffffff""ffff"""ffffffff"""""bfffffffffffffffffff
ff&"f&"ff&"f""fffffff"bfffff"b&"bfffff""ffff"bffffffffffffffffff
fff"&"bfff"f"bfffff&"fffffff"bfb""ffff"bffff""ffffffffffffffffff
fff"""ffff"&"fffff""ffffffff"bfff""fff""fffb"bffffffffffffffffff
fff&""ffff&""ffff&"""""""fff"bffff""fff"""""bfffffffffffffffffff
ffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffffff
```

In order to rebuild the firmware we need to break the firmware in pieces and rebuild it.

Lets break the firmware in rebuildable bloks
```
dd if=W25X80_20210403_170818.BIN of=part1.data.out skip=0 bs=1 count=262655
dd if=W25X80_20210403_170818.BIN of=part2.image.out skip=262656 bs=1 count=10371
dd if=W25X80_20210403_170818.BIN of=part3.data.out skip=273028 bs=1 count=10601
dd if=W25X80_20210403_170818.BIN of=part4.goodbye.out skip=283630 bs=1 count=8192
dd if=W25X80_20210403_170818.BIN of=part5.data.out skip=291823 bs=1 
```

```
cat part1.data.out part2.image.out part3.data.out part4.goodbye.in.powned_by_w2k8 part5.data.out > new_fw.data
```
Lets reflash the firmware to the little chip.

![Programmer]

And boot the camera. If everything went ok, it starts. 
And when we want to shut it down, it say: 
![TomTec-powned]



[TomTec]: ./images/bartsm01024n0001014.webp "TomTec"

[Programmer]: ./images/programmer.png "Programmer"

[Programmer-2]: ./images/20210404_130355.jpg "Programmer-2"

[TomTec-open1]: ./fotos/20210403_161520.jpg "TomTec-open1"

[TomTec-open2]: ./fotos/20210403_161508.jpg "TomTec-open2"

[TomTec-open3]: ./fotos/20210403_233212.jpg "TomTec-open3"

[TomTec-welcome]: ./fotos/20210403_232631.jpg "TomTec-welcome"

[TomTec-goodbye]: ./fotos/20210403_232625.jpg "TomTec-goodbye"

[TomTec-powned]: ./images/20210404_133703.jpg "TomTec-powned"

[winbond]: ./images/2021-04-04-104827.jpg "winbond"

[screenshot1]: ./images/Screenshot_2021-04-03_23-42-42.png "screenshot1"
