This reprex starts with the `urlInput` Shiny [custom input binding](https://shiny.rstudio.com/gallery/custom-input-bindings.html).

The following are the only changes from the Shiny example above and are located in the file `url-input-binding.js`:

 1. This `console.log` is at the top to track when the binding is created:

    ```
    console.log("Creating input binding for urlInput");
    ```

 2. The `find` function in the `urlInputBinding` object includes an additional line that uses the JavaScript spread operation:
 
    ```
    find: function(scope) {
      Math.max(...[0, 1, 2]); // This is the new line
      return $(scope).find('input[type="url"]');
    }
    ```

To run the test:
```
library(shinytest)
app <- ShinyDriver$new()
app$getDebugLog("browser")
app$stop()
```

You should see, besides a different timestamp, the following (I do not on my machine):
```
B/I 13:29:06.10 Creating input binding for urlInput (:)
```

To fix it, just comment out the `Math.max` line and rerun.

It is possible that this is related to [this PhantomJS ticket](https://github.com/ariya/phantomjs/issues/15187), which basically makes it sound like modern JavaScript can cause problems. This is also hinted at in [this shinytest support ticket](https://github.com/rstudio/shinytest/issues/317). **The main issue I have is the silent failure to load the javascript file.** I have no idea which part of the JavaScript is causing the problem!

This issue might stop being an issue if, as is suggested [in this ticket](https://github.com/rstudio/chromote), `shinytest` moves away from PhantomJS to [headless Chrome](https://github.com/rstudio/chromote).
