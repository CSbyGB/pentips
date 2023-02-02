# Flipper Zero

![image](https://user-images.githubusercontent.com/96747355/202935703-176c3882-dd10-47a7-be15-1c673237716f.png)

## Out of the box

Once you've received your Flipper Zero, you will need to set it up.

### SD Card

You will need an SD card in order to play with your flipper zero, it is pretty much useless without it.  
You can not buy any SD card, you will need a specific one, see [here](https://docs.flipperzero.one/basics/sd-card) the requirements.  
You can also check the same link to format the SD Card through the flipper. It is pretty straightforward.
They mention in the documentation that it is not necessary to buy one that is more than 32 GB even though it will still work with more.
[Here](https://a.co/d/2WEUbgd) is the one I got from Amazon Canada.

### Update firmware

Once the SD card is inserted and formatted, you will be able to update the firmware. Here is how to do this:

1. Get the app on your computer from this [link](https://docs.flipperzero.one/qflipper)
2. Install it
3. Plug your flipper to your computer and update the firmware, really straightforward as well.

> For more information, you have the full chapter about the firmware update in the documentation [here](https://docs.flipperzero.one/basics/firmware-update).

## Infrared

### Clone a remote

Once again something really simple you can clone a remote. Here is how to do it:

1. In the menu go to Infrared
2. Click on learn new remote
3. Point the remote at the IR port and push the button you wish to clone (every button will be saved separately)

Now you can use your flipper as a remote. Pretty cool.

## Music Player

- You have a music player in the flipper zero. 
- I changed the path but in mine it is here  
![music app](https://user-images.githubusercontent.com/96747355/211212560-9755c484-69dc-449f-86eb-65faacc5a16c.png)  
- You will have to put the music files in this folder. They can be txt file or fmf (flipper music file)  
![path](https://user-images.githubusercontent.com/96747355/211212604-4df8ec0d-1850-4335-923a-039f65bc5e32.png)  

### Make your own music

- You want to make your own music. It is possible. Use [this RTTTL Online tool](https://adamonsoon.github.io/rtttl-play/) to hear what it is going to look like.
- And then you can use [this documentation by neverfa11ing](https://github.com/neverfa11ing/FlipperMusicRTTTL) to create the file. I really like the image he shared. It is really comprehensive in one look  
![howstuffworks](https://user-images.githubusercontent.com/6899421/171048290-1e95c9ba-5c26-4e6b-a969-ecd6003c6423.gif)  
- [This documentation by tonsil](https://github.com/Tonsil/flipper-music-files) is also really good 

### Convert Solfege notation to Letter notation

- To convert notes to letter notation. If you are like me and never remember how to convert solfege notation to letter, here is a matrice

|Solfege notation|Letter notation|
|--|--|
|DO|C |
|RE|D |
|MI|E |
|FA|F|
|SOL|G|
|LA|A|
|SI|B|  

### Where is flat?

- If you want a flat (bemol) you will need to use you musician skills. I could not find a way to make a bemol except by using its sharp equivalent so for example if you want mib you will need to use re#.
- Here are a few possibilities for this scenario
  - reb is do# = C#
  - mib is re# = D#
  - solb is fa# = F#
  - lab is sol# = G#
  - sib is la# = A#
  - fab is mi = E
  - dob is si = B

### Tempo

- To calculate the tempo we use a metronome one click of a metronome is a beat.   

|Italian | English | BPM |
|--------|--------|------|
|Largo |	Large |	40 - 60 |
|Lento	|Slow	|52 - 68|
|Adagio|	Adagio|	60 - 80|
|Andante|	Andante|	76 - 100|
|Moderato	|Moderate|	88 - 112|
|Allegretto| Allegretto|	100 - 128|
|Allegro	|Allegro|	112 - 160|
|Vivace|	Lively|	120 - 140|
|Presto|	Fast|140 - 200|
|Prestissimo|	Very fast|140 - 200|

- Depending on the piece the tempo will be different. You will have to calculate according to this. These images from flowkey can help us. We can assume (even though it is not always true) that for example if it says on a music sheet allegro moderato 120 the quarter note will be 120 bpm and we will calculate the other according to this.

![image](https://user-images.githubusercontent.com/96747355/211213661-dbc5a217-5686-4200-b01d-a6fb83c53d47.png)  
![image](https://user-images.githubusercontent.com/96747355/211213689-f3bbcaab-8fb8-4f37-bbca-0e0fb2103c08.png)  

### My music files

- [Here](https://github.com/CSbyGB/pentips/tree/main/flipper-zero-music-file) is a link to the folder where I will put the music files I will make using this methodology.

### Resources for music with flipper Zero

- [flipper music files - Tonsil](https://github.com/Tonsil/flipper-music-files)
- [An RTTTL Parser Class by marco c on Arduino++](https://arduinoplusplus.wordpress.com/2019/12/23/an-rtttl-parser-class/)
- [Apprendre le solfege](https://www.apprendrelesolfege.com/)
- [Flowkey](https://www.flowkey.com/en/piano-guide/reading-notes)

## Using a Flipper Zero to access PI source code on IoT devices

- See Dana Epp's blog article [here](https://danaepp.com/using-a-flipper-zero-to-access-api-source-code-on-iot-devices) for a detailed explaination on how to do this

## Resources

{% embed url="https://docs.flipperzero.one/" %} Official Documentation {% endembed %}  
{% embed url="https://flipperzero.one/" %} Official Website {% endembed %}  
{% embed url="https://discord.com/invite/y5E5m8jbgb" %} Official Discord Server {% endembed %}  
{% embed url="https://twitter.com/flipper_zero" %} Official Twitter {% endembed %}  
