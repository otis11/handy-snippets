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
