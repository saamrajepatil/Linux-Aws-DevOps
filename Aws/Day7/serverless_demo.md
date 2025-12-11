simple, practical, step-by-step demo** to create a **serverless architecture** using **API Gateway + AWS Lambda** and test it — *using only the AWS Console* (since you prefer console-based steps).

This demo will create a **REST API** endpoint that triggers a Lambda function.
It returns:
`{"message": "Hello from Lambda"}`

---

# ✅ **DEMO: Create & Test API Gateway + Lambda (Serverless Architecture)**

**Total time:** 10–12 minutes
**Tools:** AWS Console only

---

# **STEP 1 — Create a Lambda Function**

1. Go to **AWS Console → Lambda**
2. Click **Create function**
3. Choose:

   * **Author from scratch**
   * Function name: `helloDemoFunction`
   * Runtime: **Python 3.12** (or Node.js, your choice)
   * Architecture: x86_64
4. Click **Create function**

---

## **STEP 2 — Add Code in Lambda**

### If Python:

Paste this code:

```python
def lambda_handler(event, context):
    return {
        "statusCode": 200,
        "body": "Hello from Lambda!"
    }
```

Click **Deploy**

---

## **STEP 3 — Test Lambda (Before API Gateway)**

1. Click **Test**
2. Create a test event → select **Create new event**
3. Event name: `test1`
4. Leave JSON as default
5. Save and click **Test**

You should see a response:

```
Hello from Lambda!
```

If this works → continue.

---

# **STEP 4 — Create API Gateway REST API**

1. Go to **API Gateway → APIs**
2. Click **Create API**
3. Choose **REST API** (not HTTP API for now — REST is easier to demo)
4. Click **Build**

In “Create New API”:

* API Name: `LambdaDemoAPI`
* Endpoint type: **Regional**
* Click **Create API**

---

# **STEP 5 — Create a Resource**

1. In left menu → **Resources**
2. Click **Actions → Create Resource**
3. Resource name: `hello`
4. Resource path will be: `/hello`
5. Click **Create Resource**

---

# **STEP 6 — Create GET Method**

1. Select `/hello`
2. Click **Actions → Create Method**
3. Choose **GET**
4. Click the tick ✔️

Configure GET:

* Integration type: **Lambda Function**
* Region: Select your Lambda region
* Lambda Function: **helloDemoFunction**

Click **Save** → Allow permissions.

---

# **STEP 7 — Deploy the API**

1. Click **Actions → Deploy API**
2. Deployment stage: **New stage**
3. Stage name: `dev`
4. Click **Deploy**

You will now see an **Invoke URL**, example:

```
https://abcd1234.execute-api.ap-south-1.amazonaws.com/dev
```

Your GET URL will be:

```
https://abcd1234.execute-api.ap-south-1.amazonaws.com/dev/hello
```

---

# **STEP 8 — Test From Browser or Postman**

Open the full URL in your browser:

```
https://xxxxxx.execute-api.ap-south-1.amazonaws.com/dev/hello
```

You should see:

```
Hello from Lambda!
```

Congratulations — your **serverless API is working** 🎉

---

# 🧪 **STEP 9 — Add JSON Response (Optional Demo Upgrade)**

Update Lambda code to return JSON:

```python
import json

def lambda_handler(event, context):
    return {
        "statusCode": 200,
        "headers": {"Content-Type": "application/json"},
        "body": json.dumps({"message": "Hello from Lambda!"})
    }
```

Deploy it and test again.

---

# 🚀 **STEP 10 — End-to-End Flow**

**Client → API Gateway → Lambda → Response**

This demo covers:

* Lambda creation
* Testing Lambda
* Creating API Gateway REST API
* Mapping `/hello` GET to Lambda
* Deploying API
* Testing the endpoint

---





_______________________________________________________________________________________________________________________
second demo:
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
