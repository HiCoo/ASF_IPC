# ASF_IPC

[![license](https://img.shields.io/github/license/deluxghost/ASF_IPC.svg?style=flat-square)](https://github.com/deluxghost/ASF_IPC/blob/master/LICENSE)
[![PyPI](https://img.shields.io/badge/Python-3.6-blue.svg?style=flat-square)](https://pypi.python.org/pypi/ASF-IPC)
[![PyPI](https://img.shields.io/pypi/v/ASF-IPC.svg?style=flat-square)](https://pypi.python.org/pypi/ASF-IPC)
[![ASF](https://img.shields.io/badge/ASF-3.4.0.5%20supported-orange.svg?style=flat-square)](https://github.com/JustArchi/ArchiSteamFarm)

A simple asynchronous Python 3.6+ wrapper of [ArchiSteamFarm IPC API](https://github.com/JustArchi/ArchiSteamFarm/wiki/IPC)

**Powered by aiohttp, ASF_IPC is now fully asynchronous. Synchronous methods are no longer supported.** ASF_IPC resolves APIs automatically from the new swagger API of ASF, so ASF_IPC always has the latest APIs data as long as ASF is updated.

## Examples

* [simple-asf-bot](https://github.com/deluxghost/simple-asf-bot)

## Requirements

* Python 3.6+
* aiohttp

## Installation

```shell
pip3 install -U ASF_IPC
```

## Getting Started

This example shows how to send a command to ASF:

```python
import asyncio
from ASF import IPC

async def command(asf, cmd):
    return await asf.Api.Command['command'].post(command=cmd)

async def main():
    # The IPC initialization duration depends on the network
    async with IPC(ipc='http://127.0.0.1:1242', password='YOUR IPC PASSWORD') as asf:
        while True:
            cmd = input('Enter a command: ')
            resp = await command(asf, cmd)
            if resp.success:
                print(resp.result)
            else:
                print(f'Error: {resp.message}')

loop = asyncio.get_event_loop()
output = loop.run_until_complete(main())
loop.close()
```

## Find the endpoint

To get a list of all endpoints of ASF, open your web browser and visit the swagger page of your ASF instance (usually `http://127.0.0.1:1242/swagger`).

You can see many endpoints with their path, such as `/Api/Bot/{botNames}`, this endpoint in ASF_IPC is `asf.Api.Bot['botNames']`.

The replacing rule is simple: change `/{text}` to `['text']` and change `/` to `.`

Some more examples:

```python
asf.Api.ASF  # /Api/ASF
asf.Api.Command['command']  # /Api/Command/{command}
asf.Api.Bot['botNames'].Pause  # /Api/Bot/{botNames}/Pause
asf.Api.WWW.GitHub.Releases  # /Api/WWW/GitHub/Releases
asf.Api.WWW.GitHub.Releases['version']  # /Api/WWW/GitHub/Releases/{version}
```

## Send a request

After your endpoint found, you want to send some data to this endpoint, you can use `get()`, `post()`, `put()`, `delete()` methods, these methods have optional arguments:

* `body` (dict): the JSON request body.
* `params` (dict): the parameters in URL after a `?`, e.g. the `params` of `/Api/WWW/GitHub/Releases?count=10` is `{'count': 10}`

If you need to pass parameters in the path of the endpoint, e.g. `{botName}` in `/Api/Bot/{botName}/Redeem`, you can pass them as a keyword parameter of the method.

Some examples:

```python
# POST /Api/Command/status%20asf
await asf.Api.Command['command'].post(command='status asf')
# GET /Api/WWW/GitHub/Releases?count=10
await asf.Api.WWW.GitHub.Releases.get(params={'count': 10})
# POST /Api/Bot/robot with json body {'BotConfig': ...}
await asf.Api.Bot['botName'].post(body={'BotConfig': ...}, botName='robot')
```

## Get a response

After sending a request to the endpoint, we got a response object, which has 3 attributes:

* `Result` or `result` (str): some data returned by ASF.
* `Message` or `message` (str): describes what happened with the request.
* `Success` or `success` (bool): if the request has succeeded.

If ASF_IPC cannot give a value to some attributes, these attributes will be `None` or empty value.

## WebSocket endpoint

Example for `/Api/NLog`:

```python
import asyncio
from ASF import IPC

async def get_log(asf):
    async for resp in asf.Api.NLog.ws():  # use ws() instead of get(), post()...
        if resp.success:
            print(resp.result)

async def main():
    async with IPC(ipc='http://127.0.0.1:1242', password='YOUR IPC PASSWORD') as asf:
        while True:
            await get_log(asf)

loop = asyncio.get_event_loop()
output = loop.run_until_complete(main())
loop.close()

```
