# UltraSC

Ultra is a simple image hosting platform available for everyone. <br>
Uploading an image is 100% anonymous, without logging any data about the user. <br>
For now the official client is not yet available. <br>
Bellow you can find info about how to create an unofficial client.


# Uploading images
> Currently, there is no rate limit implemented, but I am working on it. <br>
> However, you can only upload images that are bellow 10 MB. <br>
> Also every file is saved as a PNG for now.

_<h3>WebSocket</h3>_
To upload an image, you have to connect to the Ultra web socket first. <br>
**ws port:** `3000`

Then read the whole file into bytes, and encode it to base64. Example:
```rust
use base64::encode;
use std::fs::File;

fn read() -> String {
    let mut f = File::open("image.png").unwrap();
    let mut buffer = Vec::new();
    f.read_to_end(&mut buffer).unwrap();
    encode(buffer)
}
```
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
file_content = base64.b64encode(f.read())
f.close()

asyncio.get_event_loop().run_until_complete(
    upload("ws://135.125.132.235:3000", file_content)
)
```

_<h3>Responses</h3>_
If everything went correctly and your data was sent, you should receive the response. <br>
`<status>:<message>`

Here is the list of all available responses:
- error:invalid data        - *The received data could not be decoded*
- error:not an image        - *The received data is not an image*
- error:image is too big    - *Image is bigger than 10 MB*
- error:can not save        - *Image could not been saved to the database*
- ok:&lt;id&gt;             - *Success, where &lt;id&gt; is your image ID*


# Getting Images
> To access a certain image you have to have its ID. <br>
> It's possible to get the image:
> -  directly
> -  ~~as a json response~~
> -  and load it on the page

_<h3>Endpoint</h3>_
The endpoint for accessing an image is: `/img?id=<ID>` <br>
If the image with a certain ID doesn't exist, you will be welcomed with a 404 page and "NOT_FOUND" status code. <br>
Otherwise, it will load a page with this image. <br>
If you add `&api=1` you will get a raw image. <br>

Additionaly you can access static images like `/images/<ID>.png` <br>


# Disclaimer
> Please keep in mind that UltraSC is still in the development! <br>
