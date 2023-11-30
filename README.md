# CORS-PoC

steps and what to look for:

### 1. Send Requests with Modified Origin Header:

- BurpSuite or Curl to modify request
- Modify the Origin header to something other than the target application's domain. This can be the URL of your GitHub Pages site where the PoC will be hosted, or it can be set to null.
- The purpose is to simulate a cross-origin request which is what CORS policies are meant to control.

### 2. Analyze the Response Headers:

- `Access-Control-Allow-Origin`: If this header is present in the response and its value is `*`, `null`, or *exactly matches* the Origin header value you sent, it might indicate a misconfiguration.
- `Access-Control-Allow-Credentials`: If this is set to `true` while the Access-Control-Allow-Origin header is not restricting origins properly, it definitely indicates a misconfiguration.
- `Vary: Sometimes`, you may also see a `Vary: Origin` response header. This means that the server is varying the response based on the value of the Origin header. If you see this, but the Access-Control-Allow-Origin header is not set correctly, it still indicates a misconfiguration.

### 3. Crafting and Using the PoC:

- The PoC HTML page with the JavaScript code will try to make an XHR/fetch request to the target endpoint when you click the button.
- If the CORS policy is misconfigured, the browser will allow this cross-origin request to succeed, and your JavaScript code will be able to read the response.
- If the request succeeds and you can read the data, this is proof that the CORS policy is not correctly implemented because under proper CORS policy, the browser should not allow reading of the response data from a different origin.

### 4. What to Expect After Clicking the PoC Button:

- Success Case (CORS Misconfigured): If the CORS is misconfigured, the request initiated by your PoC page will succeed, and the JavaScript code on your page will be able to access the response, typically showing it in an alert box or console logging it.
- Failure Case (CORS Configured Correctly): If the CORS policy is properly configured, the browser will block the request, and your JavaScript’s error handling code should catch this. The XMLHttpRequest’s status will be 0, and depending on your error handling, you might see an alert or a console message indicating the failure.

### 5. What You Are Hoping For:

- If you are testing this as a security researcher or bug bounty hunter, you are hoping to demonstrate that the CORS policy allows for arbitrary origins to make requests with credentials and receive responses that should only be allowed from specific origins.
- A successful PoC would be able to read and display data that should be restricted, proving the vulnerability.
