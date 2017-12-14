# Android-Hacks
Here are hacks and tricks useful in android developing

### WebView YouTube player is not autoplay on prelollipop devices
This is a known problem for which you have two possible solutions:
1) If you can target APi >= 17 you can rely on the new WebView and the new WebSettings api method setMediaPlaybackRequiresUserGesture()
```
WebSettings settings = webview.getSettings();
settings.setMediaPlaybackRequiresUserGesture(false);
```
2) If your target api is < 17 than you have to simulate an user's tap on the WebView at the right time (like after the page is loaded and before sending the play command):
```
private void emulateClick(final WebView webview) {
    long delta = 100;
    long downTime = SystemClock.uptimeMillis();
    float x = webview.getLeft() + webview.getWidth()/2; //in the middle of the webview
    float y = webview.getTop() + webview.getHeight()/2;

    final MotionEvent motionEvent = MotionEvent.obtain( downTime, downTime + delta, MotionEvent.ACTION_DOWN, x, y, 0 );
    final MotionEvent motionEvent2 = MotionEvent.obtain( downTime + delta + 1, downTime + delta * 2, MotionEvent.ACTION_UP, x, y, 0 );

    Runnable tapdown = new Runnable() {
        @Override
        public void run() {
            if (webview != null) {
                webview.dispatchTouchEvent(motionEvent);
            }
        }
    };

    Runnable tapup = new Runnable() {
        @Override
        public void run() {
            if (webview != null) {
                webview.dispatchTouchEvent(motionEvent2);
            }
        }
    };

    int toWait = 0;
    int delay = 100;
    webview.postDelayed(tapdown, delay);
    delay += 100;
    webview.postDelayed(tapup, delay);
}
```
