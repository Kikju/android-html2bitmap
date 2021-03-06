# Generate a bitmap from html on Android

Generates a bitmap of a defined width from html by rendering the content inside an off screen webview. 

Does not block main thread.

Uses the webView available on the system so it's possibilities and limitations will be mirrored by the library.

Height will be calculated using WebView.getContentHeight()

### Usage: 
```
dependencies {
    implementation 'com.izettle:html2bitmap:1.6'
}
```

```
Bitmap bitmap = new Html2Bitmap.Builder(context: Context, content: WebViewContent).build().getBitmap();
```

- **context** Used to create webview - application context is fine.
- **html** The html that should be rendered to a bitmap.
- **width** The requested width of the generated bitmap.
- **timeout** Seconds before the job will terminate - if timeout is reached will call stopLoading() on the webview and return null.

Calls are blocking and hence recommended to be moved into a background thread.

##### Under the hood
Will wait for remote and local resources to load before generating bitmap i.e. "http://www.sample.com/image.jpg"
will be downloaded before the screenshot is taken - _take this into account before defining a timeout for the whole process._

Can be used anywhere you have access to a context. The purpose of the library was to perform its 
job inside a background thread and not to be tied to an activity.

Calls are synchronous to simplify usage in a background thread.
```
new AsyncTask<Void, Void, Bitmap>() {
    @Override
    protected Bitmap doInBackground(Void... voids) {
        String html = "<html><body><p>Hello world!</p><br/>Html bitmap</body><html>";
        
        return new Html2Bitmap.Builder().setContext(context).setContent(WebViewContent.html(html)).build().getBitmap();   
    }

    @Override
    protected void onPostExecute(Bitmap bitmap) {
        if (bitmap != null) {
            imageView.setImageBitmap(bitmap);
        }
    }
}.execute();
```

Note: Some of the methods on the webview are required to be run on the mainThread 
of the application - so some operations will be performed on that thread.


![screenshot_1523608598](https://user-images.githubusercontent.com/373219/38725783-b3a9f364-3f08-11e8-8820-c8ecc7abe7ae.png)
