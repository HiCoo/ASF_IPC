# ASF_IPC

[![license](https://img.shields.io/github/license/deluxghost/ASF_IPC.svg?style=flat-square)](https://github.com/deluxghost/ASF_IPC/blob/master/LICENSE)
[![PyPI](https://img.shields.io/badge/Python-3-blue.svg?style=flat-square)](https://pypi.python.org/pypi/ASF-IPC)
[![PyPI](https://img.shields.io/pypi/v/ASF-IPC.svg?style=flat-square)](https://pypi.python.org/pypi/ASF-IPC)

A simple Python 3 library of [ArchiSteamFarm IPC API](https://github.com/JustArchi/ArchiSteamFarm/wiki/IPC)

## Examples

* [telegram-asf](https://github.com/deluxghost/telegram-asf)

## Installation

```shell
pip install ASF_IPC
```

## Usage

### Initialization

```python
import ASF_IPC as asf

api = asf.IPC(host='127.0.0.1', port=1242, password='blablabla')
```

### Fetch general data about ASF

```python
data = api.get_asf()
```

### Fetch status of bots

```python
data = api.get_bot('name of bot') # One bot
data = api.get_bot(['name', 'of', 'bots']) # Multiple bots
```

Note: method `get_bot()` returns `dict`.

### Erase bots

```python
api.delete_bot('name of bot') # One bot
api.delete_bot(['name', 'of', 'bots']) # Multiple bots
```

### Create/update config of a bot

```python
api.post_bot('name of bot', config={'BotName': 'foobar'}, keep_sensitive=True)
```

### Execute command

```python
message = api.command('version')
```

Note: method `command()` returns `str`.

### Get structure and type

```python
data = api.get_structure('ArchiSteamFarm.BotConfig')
data = api.get_type('ArchiSteamFarm.BotConfig')
```

Note: these methods return `dict`, check ASF Wiki for details.

## Don't forget to catch errors

ASF_IPC will raise every single error as an exception.
