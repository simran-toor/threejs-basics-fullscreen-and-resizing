# 08 FULLSCREEN AND RESIZING
* canvas currently has fixed resolution of 800x600 
* usually better for vite to fill whole screen for a more immersive experience 
* we want: 
    - canvas to take all available space
    - make sure that it still fits if the user resizes their window
    - give the user a way to experiment with the experience in fullscreen

## FIT THE VIEWPORT
* instead of using fixed numbers in the `sizes` variable use `window.innerWidth` and `window.innerHeight`:
    ```
    // ...

    // Sizes
    const sizes = {
        width: window.innerWidth,
        height: window.innerHeight
    }

    // ...
    ```
* there is still a white margin and a scroll bar 
* browsers all have default stylings like more significant titles, underlined links, space between paragraphs, and paddings on the page 
* fix position of the canvas using CSS:
    - in style.css remove any type of `margin` or `padding` on all elements by using a wildcard `*`
    ```
    *
    {
        margin: 0;
        padding: 0;
    }
    ```
* fix canvas to top left using its webgl class to select it, also gets rid of scroll bar:
    ```
        .webgl
    {
        position: fixed;
        top: 0;
        left: 0;
    }
    ```
* if there is an outline when using drag and drop add `outline: none;` on the `.webgl`
* if you want to remove any type of scrolling even on touch screens you can add an `overflow: hideen` on both `html` and `body`
    ```
    html,
    body
    {
        overflow: hidden;
    }
    ```
## HANDLE RESIZE
* to resize canvas, first need to know when the window is being resized
* listen to the `resize` event on window
    * add `resize` listener right after the `sizes` variable:
        ```
        window.addEventListener('resize', () =>
        {
            console.log('window has been resized')
        })
        ```
    * update `sizes` variable:
    ```
    window.addEventListener('resize', () =>
    {
        // Update sizes
        sizes.width = window.innerWidth
        sizes.height = window.innerHeight
    })
    ```
    * update `camera` aspect ratio
    ```
    window.addEventListener('resize', () =>
    {
        // ...

        // Update camera
        camera.aspect = sizes.width / sizes.height
    })
    ```
    * b/c changed camera properties `aspect`, now need to update the projection matrix 
    ```
    window.addEventListener('resize', () =>
    {
        // ...

        camera.updateProjectionMatrix()
    })
    ```
    * update `renderer`
    ```
    window.addEventListener('resize', () =>
    {
        // ...

        // Update renderer
        renderer.setSize(sizes.width, sizes.height)
    })
    ```
    ## HANDLE PIXEL RATIO
    * some people will see a blurry render and stairs effect on the edges, this is b/c testing on a screen with a pixel ratio greater than `1`
    * pixel ratio corresponds to how many physical pixels you have on the screen for 1 pixel unit on the software part
    * to get screen pixel ratio use `window.devicePixelRatio` and to update pixel ratio of render call `renderer.setPixelRatio(...)
    * don't send the device pixel ratio to that methods, it will cause performance issues on high pixel ratio devices
    * having pixel ration greater than `2` is mostly marketing and you won't see a difference between `2` and `3` but it will create performance issues and drain battery faster
    * limit pixel ratio to `2` using `Math.min()`:
        ```
        renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2))
        ```
    * add above to the `resize` callback too b/c users with multiple screens with different pixel ratios usually resize their window when changing screens

    ## HANDLE FULLSCREEN
    * use a double click to toggle the fullscreen 
    * add double click event with the `dblclick` event:
        ```
        window.addEventListener('dblclick', () =>
    {
        console.log('double click')
    })
    ```
    * need 3 things:
        1. a way to know if already in fullscreen
        2. method to go into fullscreen
        3. a method to leave fullscreen
    * to know if alreay in fullscreen or not use `document.fullscreenElement`
        ```
        window.addEventListener('dblclick', () =>
    {
        if(!document.fullscreenElement)
        {
            console.log('go fullscreen')
        }
        else
        {
            console.log('leave fullscreen')
        }
    })
    ```
    * the method to request fullscreen is associated with the element b/c you can chose what will be in fullscreen. 
    * can be whole page, any DOM element or the `<canvas>`
    * method to leave fullscreen mode is available directly on the `document`
        ```
        window.addEventListener('dblclick', () =>
    {
        if(!document.fullscreenElement)
        {
            canvas.requestFullscreen()
        }
        else
        {
            document.exitFullscreen()
        }
    })
    ```

