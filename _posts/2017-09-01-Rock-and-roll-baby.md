---
layout: post
title: Rock and Roll Baby
categories: [Python, Asyncio]
tags: [code, python, async]
comments: true
description: Discover asyncio with drum sheet music !
---

Hello lovely reader :heart:

It's been a while. I got not time to write, I changed job, moved out to a new place and I could go on with lame excuses all night long. But I'm back, super back.

![I'm back](/assets/images/imback.jpg)

Sooooo... I needed a subject to get back on track. I'm a drummer and yesterday, I wanted to get some drum sheet music and guess what, there are emoji for that : :drum: :shit: :musical_note: so here is my new blog post !

I found a [quite old french website](http://www.drumsandco.com/partitions.php) with :drum: :musical_note: sheets on it. Only problem, I had not enough motivation to download all the files one by one.

So I looked at the source code, copy/pasted some lines, invoked a minor demon :smiling_imp:, launched sublime text and after a few nervous keystrokes, I got something similar to the following content :

```
AC%20DC%20-%20Back%20In%20Black.pdf;AC_DC : Back In Black
AC%20DC%20-%20Hell's%20Bells.pdf;AC_DC : Hell's Bells
AC%20DC%20-%20Highway%20To%20Hell.pdf;AC_DC : Highway To Hell
AC%20DC%20-%20Let's%20Get%20It%20Up.pdf;AC_DC : Let's Get It up
touch_too_much.pdf;AC_DC : touch_too_much
...
```

So  metal right ? :metal:

Each line is `part_of_a_url`;`title`.

I wrote a tiny script to download all the music sheets from this file:

```python
"""Script to download drums parts"""

import request


def get_list_of_urls_and_filename():
    urls_and_filenames = []

    with open('./list_parts.txt', 'r') as parts:
        for line in parts.readlines():
            (url, name) = (*line.split(';'), )
            name = name.replace('\n', '').replace(' ', '_')
            urls_and_filenames.append((f'{base}{url}', f'{name}.pdf'))

    return urls_and_filenames


def main():
    base = 'http://www.drumsandco.com/Partitions/'
    urls_and_filenames = get_list_of_urls_and_filename()
    len_dls = len(urls_and_filenames)

    for (counter, (url, name)) in enumerate(urls_and_filenames):
        print(f'* [{counter + 1}/{len_dls}] Downloading "{url}" to "{name}"')
        download_file(name, url)


def download_file(path, url):
    """Download drums pdf file"""
    resp = request.get(url)

    if resp.status_code != 200:
        return print(f'{url} failed')

    with open(path, 'wb') as output:
        output.write(resp.content)


if __name__ == '__main__':
    main()
```

This is fine and I have now more than 200 music sheets thanks to these peeps. Hooray ! :tada:

But guess what, it took so long I almost died of boredom.

YOU CAN'T SEE ME, THE TIME IS NOW, HERE IIIIIIIIIS **asyncio** (not John Cena, sorry)

This is a perfect example to try asyncio. We have a bunch of files to download and instead of downloading them one after another, let's download them all at once !

```python
import aiohttp
import asyncio
...


async def main():
    global session
    session = aiohttp.ClientSession()
    tasks = []
    base = 'http://www.drumsandco.com/Partitions/'
    urls = get_list_of_urls_and_filename()
    len_dls = len(urls_and_filenames)

    for (counter, (url, name)) in enumerate(urls_and_filenames):
        print(f'* [{counter + 1}/{len_dls}] Downloading "{url}" to "{name}"')
        tasks.append(download_file(name, url))

    await asyncio.wait(tasks)
    session.close()


async def download_file(path, url):
    """Download drums pdf file"""
    async with session.get(url) as resp:
        if resp.status != 200:
            return print(f'{url} failed')

        with open(path, 'wb') as output:
            while True:
                chunk = await resp.content.read(1024)
                if not chunk:
                    break
                output.write(chunk)


if __name__ == '__main__':
    loop = asyncio.get_event_loop()
    loop.run_until_complete(main())

```

Annnnnd.... It's a bit longer (10 more tiny tiny lines) but this script runs in like no time ! Where's the magic ? Well `async` and `await` makes your code.... wait... ASYNCHRONOUS.

Now, you don't have to wait for the first file to be downloaded to get the second one. Might seem obvious but I tend to use the first solution too often. It's important to find real case scenario where you can use asyncio.

Some say it's the future. I don't know. I need to go practice my drum skills first.

See you later alligator :crocodile:

Cheers :beers:
