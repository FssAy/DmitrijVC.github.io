# UltraSC

Ultra is a simple image hosting platform available for everyone. <br>
Uploading an image is 100% anonymous, without logging any data about the user. <br>
For now the official client is not yet available. <br>
Bellow you can find info about how to create an unofficial client.


# Uploading images
> Currently, there is no rate limit implemented, but I am working on it. <br>
> However, you can only upload images that are bellow 10 MB. <br>
> Also every file is saved as a PNG for now. <br>

_<h3>WebSocket</h3>_
To upload an image, you have to connect to the Ultra web socket first. <br>
**ws host:** `135.125.132.235:3000` <br> 
**req from:** `https://pastebin.com/raw/8S11wyQQ` 

Message should contain the title, description and the whole image encoded in base64, all separated by the null terminator. <br>
**example message**: `<title>\0<description>\0<imgInBase64>`

_<h3>Limitations</h3>_

**Length Limits:** 
- title: 25  
- description: 200 
- base64: 9999775 

_<h3>Example</h3>_

Whole upload process in Python3:
```python
import asyncio
import websockets
import base64


async def upload(host: str, data: str) -> str:
    async with websockets.connect(host) as websocket:
        await websocket.send(data)
        print(f"UltraSC - result: [{await websocket.recv()}]")

f = open("screenshoot.png", "rb")
img_data = b"Test Title\0Test Description that can be much longer\0" + base64.b64encode(f.read())
f.close()

asyncio.get_event_loop().run_until_complete(
    upload("ws://135.125.132.235:3000", img_data)
)
```

_<h3>Responses</h3>_
If everything went correctly, and your data was sent, you should receive the response. <br>
`<status>:<message>`

Here is the list of all available responses:
- error:invalid data        - *The received data could not be decoded*
- error:not an image        - *The received data is not an image*
- error:image is too big    - *Image is bigger than 10 MB*
- error:can not save        - *Image could not been saved to the database*
- error:above the limits    - *Title or description length is above the limit*
- ok:&lt;id&gt;             - *Success, where &lt;id&gt; is your image ID*


# Getting Images
> To access a certain image you have to have its ID. <br>
> It's possible to get the image:
> -  directly
> -  ~~as a json response~~
> -  and load it on the page

_<h3>Endpoint</h3>_
The endpoint for accessing an image is: `/img?id=<ID>` <br>
If the image with a certain ID doesn't exist, you will be welcomed with a "404 not found" image. <br>
Otherwise, it will load a page with desired image. <br>
If you add `&api=1` you will get a raw image. <br>

Additionally, you can access static images like `/images/<ID>.png` <br>

# Removing Images
> Ultra database will be wiped every month

# Disclaimer
> Please keep in mind that UltraSC is still in the development! <br>
