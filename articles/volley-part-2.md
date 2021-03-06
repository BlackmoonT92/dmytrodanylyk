![Application Model](/assets/images/articles/volley-part-2.png)

### Volley - Android HTTP client
- [Part 1 - Quickstart](/articles/volley-part-1.md)
- [Part 2 - Application Model](/articles/volley-part-2.md)
- [Part 3 - Image Loader](/articles/volley-part-3.md)
- [Part 4 - Common Questions](/articles/volley-part-4.md)

### Part 2 - Application Model

> Do I need to instantiate request queue in each Service or Activity?

No

> Should I create singleton wrapper?

Yes

> Why ?

 - Request Queue creation is quite expensive
 - You will be able to access and cancel all your requests
 - You will be able to access and clear your cache


Let's create two class.

**Request Proxy**

- wrapper for request queue
- holds list of all application requests

**Request Manager**

- singleton which hold *Request Proxy*

Put those two classes in the same package.

```java
public class RequestProxy {

    private RequestQueue mRequestQueue;

    // package access constructor
    RequestProxy(Context context) {
    mRequestQueue = Volley.newRequestQueue(context.getApplicationContext());
    }
    public void login() {
        // login request
    }

    public void weather() {
        // weather request
    }
 }
```
```java
public class RequestManager {

    private static RequestManager instance;
    private RequestProxy mRequestProxy;

    private RequestManager(Context context) {
        mRequestProxy = new RequestProxy(context);
    }
    public RequestProxy doRequest() {
        return mRequestProxy;
    }

    // This method should be called first to do singleton initialization
    public static synchronized RequestManager getInstance(Context context) {
        if (instance == null) {
            instance = new RequestManager(context);
        }
        return instance;
    }

    public static synchronized RequestManager getInstance() {
        if (instance == null) {
            throw new IllegalStateException(RequestManager.class.getSimpleName() +
                    " is not initialized, call getInstance(..) method first.");
        }
        return instance;
    }
}
```

**Usage**

Initialize `RequestManager` in application class
```java
public class MyApplication extends Application {

    @Override
    public void onCreate() {
        super.onCreate();

        // RequestManager initialization
        RequestManager.getInstance(getApplicationContext());
    }
}
```

Now you can start you request as following.
```java
RequestManager.getInstance().doRequest().login(..);
RequestManager.getInstance().doRequest().weather(..);
```
