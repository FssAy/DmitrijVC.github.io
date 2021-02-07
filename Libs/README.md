# UltraSC Libs
Here you can find list of all available libraries provided by Ultra

<br>

## sc.dll
Simple library for taking screenshots written in C++ <br>
Image is saved as a JPG file <br>
[Download v1.0.0](https://cdn.discordapp.com/attachments/763023419951939604/805115001504268328/SC.zip)

_<h3>exports</h3>_
- **Capture** - capture the screen and save it to a &lt;filename&gt; <br> 
Args: (const char* filename) <br>
Return: bool *[true if succeed, false if failed]*

## ultrasc-uploader.dll
Library for uploading images to UltraSC, written in C++ <br>
[GitHub](https://github.com/DmitrijVC/ultrasc-uploader),
[Download]()

_<h3>exports</h3>_
- **upload** - connects and uploads an image to UltraSC where &lt;base&gt; is an image content encoded in base64 <br> 
Args: (const char* title, const char* description, const char* base) <br>
Return: const char* *[UltraSC response]*

_<h3>additional responses</h3>_
"upload" function returns not only a UltraSC response, but also a couple more. <br>
Here is the list of them: <br>
 
