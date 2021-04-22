# UltraSC API
Here you can find all information needed to develop a UltraSC unofficial client.

<br>
<br>
<br>

# Uploading images
> Currently, there is no rate limit implemented, but I am working on it. <br>
> However, you can only upload images that are bellow 10 MB. <br>

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
`<status>:<message>` or `<status>:<message>:<details>`

Here is the list of all available responses:
- error:invalid data                        - *The received data could not be decoded*
- error:not an image                        - *The received data is not an image*
- error:image is too big                    - *Image is bigger than 10 MB*
- error:cannot save                         - *Image could not been saved to the database*
- error:above the limits                    - *Title or description length is above the limit*
- error:unsupported format                  - *The file format isn't supported*
- ok:&lt;token&gt;   - *Success*

**Ok response explanation:** <br>
&lt;token&gt; is your image token that you need in order to manage your images. <br>
Token consists of <ID>.<TimeStamp>.<Key>

<br>
<br>

# Getting Images
> To access a certain image you have to have its ID. <br>
> It's possible to get the image:
> -  directly
> -  ~~as a json response~~
> -  load it on the page

| Method          | Endpoint                          | Body               |
| :-------------: | :-------------------------------: | :----------------: |
| `GET`           | `/img?id=<ID>`                    |                    |
| `GET`           | `/images/<ID>.<FileExtention>`    |                    |

Request to /img will result in rendered page with image title, description, and preview, but
request to /images will result in a static file. <br>
**(WIP)** If you want to get a raw file from /img, just add `&raw=1` to the uri. 

<br>
<br>

# Removing Images
> To remove an Image you need the image's: id, stamp and key as a token.

| Method          | Endpoint           | Body               |
| :-------------: | :----------------: | :----------------: |
| `POST`          | `/api/img/delete`  | `token=<Token>`    |

_<h3>Responses</h3>_
- 200 - *Ok*
- 400 - *Missing token value (if not provided)*
- 404 - *Endpoint not found (if the uri doesn't start with `/api/img`)*
- 406 - *Invalid token value (if image not found or already removed)*

<br>
<br>

# Images Expiration
> Images automatically expire one month after an upload. <br>
> When image is expired it will be removed from the drive and database, <br>
> the only data that will remain would be an image key and id

| Method          | Endpoint        | Body                            |
| :-------------: | :-------------: | :-----------------------------: |
| `POST`          | `/api/img/exp`  | `token=<Token>&expire=<EXP>`    |

Amount of seconds in `<EXP>` can't be less than 1 minute and more than 10 years.

_<h3>Responses</h3>_
- 200 - *Ok*
- 400 - *Missing token, or exp values (if not provided)*
- 404 - *Endpoint not found (if the uri doesn't start with `/api/img`)*
- 406 - *Invalid token value (if image not found)*

<br>
<br>

# Legend
`<Token>` - value returned from the Ok response when uploading an image. <br>
`<EXP>` - integer representing after how many seconds the image will expire. <br>
`<ID>` - integer representing an image ID. <br>
`<FileExtention>` - Extension of the file (png, jpg, ico... etc)
