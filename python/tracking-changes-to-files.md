---
title: Tracking changes to files
url: /python/tracking-file-changes
tags: [python]
---

When you run a `watch` in [Forklift][forklift], it waits for files to be changed and runs tests on each change. This took a little bit of work to get right. 

Actually watching the files themselves is fairly easy, especially with the [Watchdog][watchdog] library in Python. Unfortunately, it seems to work a little oddly on MacOS X - it kept raising the same file modification event multiple times. This might be because it's still using `kqueue` instead of `FSEvent`, or because of Lion doing its intelligent autosave… I haven't really checked. 

It was also slightly irritating to realize that the events were being raised every time I hit save, because the tests would start and fill the screen with Growl notification. Action doesn't need to be taken on every file modification event - only when the file is actually changed. 

One easy way to handle this is to simply hash the contents of the file, and store the hash with the file name as the key. Here's how the hashing method works:

```python
def file_digest(file):
    digest = hashlib.md5()
    with open(file) as f:
        [digest.update(line) for line in f.readlines()]
    return digest.hexdigest()
```

Calling this method with a file path will open the file, update the hashing object with each line, and give you a readable digest. We can then store the digest map on a module level dictionary, and check for matches. 
    
```python
_file_signatures = {}
def file_changed(file):
    digest = file_digest(file)
    has_changed = file not in _file_signatures or \
                        _file_signatures[file] != digest
    _file_signatures[file] = digest
    return has_changed
```

This works pretty well - one minor issue is that it doesn't really take a snapshot of all the files that are being watched to start with, so the first time a file is saved it doesn't know if it was actually changed or not. Don't see a problem with that, though. 


[forklift]: http://www.runway7.net/forklift
[watchdog]: http://packages.python.org/watchdog/

