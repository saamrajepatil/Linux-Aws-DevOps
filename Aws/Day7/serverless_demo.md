
## 📁 Part 1: Create S3 Static Website

### 1. Create or use an existing S3 bucket

* Uncheck “Block all public access”

### 2. Enable **Static Website Hosting**

* Go to **Properties > Static website hosting**
* Enable and set:

  * Index document: `index.html`

### 3. Add Bucket Policy to allow public access:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "PublicRead",
      "Effect": "Allow",
      "Principal": "*",
      "Action": "s3:GetObject",
      "Resource": "arn:aws:s3:::your-bucket-name/*"
    }
  ]
}
```

---

## 📝 Part 2: Upload Frontend Files

### 🔹 `index.html`

```html
<!DOCTYPE html>
<html>
<head>
    <title>Serverless Frontend</title>
</head>
<body>
    <h2>Serverless API Response:</h2>
    <div id="output">Loading...</div>
    <script src="main.js"></script>
</body>
</html>
```

### 🔹 `main.js`

```js
// Replace with your actual API Gateway endpoint
const apiUrl = "https://<your-api-id>.execute-api.<region>.amazonaws.com/dev/welcome";

fetch(apiUrl)
    .then(response => response.text())
    .then(data => {
        document.getElementById("output").innerText = data;
    })
    .catch(error => {
        document.getElementById("output").innerText = "Error: " + error;
    });
```

Upload both files to your S3 bucket.

---

## 🧠 Part 3: Lambda Function

If using **Lambda Proxy Integration**, return CORS headers like this:

```js
exports.handler = async (event) => {
    return {
        statusCode: 200,
        headers: {
            "Access-Control-Allow-Origin": "*", // CORS fix
            "Content-Type": "text/plain"
        },
        body: "Hello from Lambda!"
    };
};
```

> If you return JSON, change `Content-Type` and parse `response.json()` in your JS.

---

## 🌐 Part 4: API Gateway Configuration

### ✅ A. Check CORS is enabled

#### If using **REST API**:

1. Go to your `/welcome` resource → `GET` method
2. Click **Actions > Enable CORS**
3. Confirm headers:

   * `Access-Control-Allow-Origin`: `*`
   * `Access-Control-Allow-Methods`: `GET,OPTIONS`
   * `Access-Control-Allow-Headers`: `Content-Type`

✅ **Deploy your API** again after changes.

#### ✅ B. Add an `OPTIONS` method manually (important for REST API)

1. On the same resource (`/welcome`), click **Actions > Create Method > OPTIONS**
2. Integration type: **Mock**
3. Add **Integration Response headers**:

   * `Access-Control-Allow-Origin`: `*`
   * `Access-Control-Allow-Methods`: `GET,OPTIONS`
   * `Access-Control-Allow-Headers`: `Content-Type`

Then deploy again.

---

## 🔁 Final Checklist

✅ Lambda returns proper headers
✅ CORS is enabled on API Gateway (for both `GET` and `OPTIONS`)
✅ API is deployed
✅ S3 bucket has public read access
✅ JS `fetch()` uses correct API URL
✅ You uploaded the latest `main.js`

---

## 🧪 Final Test

1. Open the S3 static website URL
2. Open Developer Tools → Network tab
3. Reload the page
4. Look for the API call result

---

## 🧰 Optional Enhancements

* Add **CloudFront** for custom domain
* Use **HTTPS** for S3 via CloudFront
* Add **error messages** in `main.js` for better user feedback

---
