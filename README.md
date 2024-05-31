# Table of contents
- [Linux Laptop](#linux-laptop)
    - [Disable Closing Lid Sleep Mode](#disable-closing-lid-sleep-mode)
    - [Turn screen off/on](#turn-screen-offon)
- [SSH](#ssh)
    - [New SSH Key](#new-ssh-key)
- [Windows](#windows)
    - [Delete All Files Which Name Includes](#delete-all-files-which-name-includes)
- [Webcomponents](#webcomponents)
    - [List multi tag elements on website](#list-multi-tag-elements-on-website)
- [Whatsapp Download All Images](#whatsapp-download-all-images)

## Linux Laptop
### Disable Closing Lid Sleep Mode
```bash
sudo vim /etc/systemd/logind.conf
# change these
#HandleLidSwitch=suspend
HandleLidSwitch=ignore

#LidSwitchIgnoreInhibited=yes
LidSwitchIgnoreInhibited=no

# restart service
sudo service systemd-logind restart
```

### Turn screen off/on
```bash
# off
setterm --blank force

# on
setterm --blank poke
```

## SSH
### New SSH Key
```bash
ssh-keygen -t ed25519
cat ~/.ssh/id_ed25519.pub
```

SSH Key Settings:

- [GitHub](https://github.com/settings/keys)


## Windows 
### Delete All Files Which Name Includes
1. Go to your Folder inside explorer
2. `Shift` + `Right click`
3. `Open powershell window here`
```powershell
# replace FILE_NAME_INCLUDE with the characters your file should include
get-childitem | where-object {$_.Name -like "*FILE_NAME_INCLUDE*"} | foreach ($_) {remove-item $_.fullname}
```

## Webcomponents
### List multi tag elements on website
Get all multi tag elements on a website
```js
Array.from(document.querySelectorAll('*')).filter(el => el.tagName.includes('-'))
```

Get all unique multi tags on a website
```js
[...new Set(Array.from(document.querySelectorAll('*')).filter(el => el.tagName.includes('-')).map(el => el.tagName))]
```

### Whatsapp Download All Images
1. Go to the media page of a whatsapp group
    - Go to Group
    - Click on the 3 dot menu
    - Click on Group Info
    - Click on media... Should open all images inside the group in the sidebar
2. Load all relevant messages/media from your phone (scrolling up and clicking download more from phone)
3. Open Developer tools 
4. Wait until all images are loaded, can check the count of images loaded via:
```js
// (paste code and execute inside the developer console)
Array.from(document.querySelectorAll('div[role="listitem"]')).length
```
5. preload all images
```js
// this can also take a while
Array.from(document.querySelectorAll('div[role="listitem"] [data-icon="media-download"]')).forEach(el => el.click())

// can check the count of all downloadable
Array.from(document.querySelectorAll('div[role="listitem"] div[style]')).filter(el => getComputedStyle(el).backgroundImage.includes('url(')).map(el => getComputedStyle(el).backgroundImage.replace('url("', '').replace('")', '')).length
```
6. download all
```js
// (paste code and execute inside the developer console)
async function downloadAll() {
    const blobUrls = Array.from(document.querySelectorAll('div[role="listitem"] div[style]')).filter(el => getComputedStyle(el).backgroundImage.includes('url(')).map(el => getComputedStyle(el).backgroundImage.replace('url("', '').replace('")', ''))

    for (let i = 0; i < blobUrls.length; i++) {
        // otherwise browser blocks or something idk
        await new Promise(resolve => setTimeout(resolve, 100))
        const link = document.createElement("a")
        link.href = blobUrls[i]
        link.download = String(i)

        document.body.appendChild(link)
        link.dispatchEvent(
            new MouseEvent('click', {
                bubbles: true,
                cancelable: true,
                view: window
            })
        )
        document.body.removeChild(link)
    }
}

downloadAll()
```
