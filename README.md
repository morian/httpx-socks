# httpx-socks

[![Build Status](https://api.travis-ci.com/romis2012/httpx-socks.svg?branch=master)](https://travis-ci.com/github/romis2012/httpx-socks)
[![Coverage Status](https://coveralls.io/repos/github/romis2012/httpx-socks/badge.svg?branch=master&_=q)](https://coveralls.io/github/romis2012/httpx-socks?branch=master)
[![PyPI version](https://badge.fury.io/py/httpx-socks.svg)](https://badge.fury.io/py/httpx-socks)
<!--
[![Downloads](https://pepy.tech/badge/httpx-socks/month)](https://pepy.tech/project/httpx-socks)
-->

The `httpx-socks` package provides proxy transports for [httpx](https://github.com/encode/httpx) client. 
SOCKS4(a), SOCKS5, HTTP (tunneling) proxy supported.
It uses [python-socks](https://github.com/romis2012/python-socks) for core proxy functionality.


## Requirements
- Python >= 3.6
- httpx==0.21.*
- python-socks>=2.0.0
- async-timeout>=3.0.1 (optional)
- trio>=0.16.0 (optional)


## Installation

only sync proxy support:
```
pip install httpx-socks
```

to include optional asyncio support (it requires async-timeout):
```
pip install httpx-socks[asyncio]
```

to include optional trio support:
```
pip install httpx-socks[trio]
```


## Usage

#### sync transport
```python
import httpx
from httpx_socks import SyncProxyTransport

def fetch(url):
    transport = SyncProxyTransport.from_url('socks5://user:password@127.0.0.1:1080')
    with httpx.Client(transport=transport) as client:
        res = client.get(url)
        return res.text
```

#### async transport (asyncio, trio)
```python
import httpx
from httpx_socks import AsyncProxyTransport

async def fetch(url):
    transport = AsyncProxyTransport.from_url('socks5://user:password@127.0.0.1:1080')
    async with httpx.AsyncClient(transport=transport) as client:
        res = await client.get(url)
        return res.text
```

#### secure proxy connections (aka "HTTPS proxies", experimental feature, both sync and async support)
```python
import ssl
import httpx
from httpx_socks import AsyncProxyTransport

async def fetch(url):
    proxy_ssl = ssl.SSLContext(ssl.PROTOCOL_TLS)
    proxy_ssl.verify_mode = ssl.CERT_REQUIRED
    proxy_ssl.load_verify_locations(...)
    
    transport = AsyncProxyTransport.from_url('http://user:password@127.0.0.1:8080', proxy_ssl=proxy_ssl)
    async with httpx.AsyncClient(transport=transport) as client:
        res = await client.get(url)
        return res.text
```
