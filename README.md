# How to solve cloudflare | Using Puppeteer Node.JS

I feel that the more automated tasks that are available nowadays, the more they are valued, so the challenges become more apparent and difficult. For example, Cloudflare currently provides strong security measures to protect websites from all forms of automation tools.

But navigating these security features can be challenging for developers and organizations working on automation projects such as web scraping, data extraction, or testing. However, with the right tools and strategies, you can effectively address these challenges while remaining compliant with legal and ethical standards.

In this tutorial, then, I'll explore how to effectively solve Cloudflare like Turnstile by using Puppeteer and Node.js and the help from Captcha solver


### Bonus Code

Claim Your   <u>**Bonus Code**</u> for top captcha solutions; [CapSolver](https://www.capsolver.com/?utm_source=official&utm_medium=blog&utm_campaign=cloudflarenodejs): **WEBS**. After redeeming it, you will get an extra 5% bonus after each recharge, Unlimited
 ![](https://assets.capsolver.com/prod/images/post/2024-03-29/fbc29472-886c-45b2-9eb2-2b307f6d9700.png)


### Understanding Cloudflare’s Turnstile

[Cloudflare's Turnstile](https://www.cloudflare.com/products/turnstile/) is a sophisticated security feature designed to challenge users attempting to access protected sites. It employs a combination of techniques, including [JavaScript challenges](https://developers.cloudflare.com/turnstile/get-started/javascript/), CAPTCHAs, and behavior analysis, to assess whether the user is a legitimate human or an automated bot. For developers, this means that bypassing Turnstile requires more than just traditional web scraping techniques.



### Prerequisites

Before diving into the implementation, ensure you have the following tools and resources:

- **[Node.js](https://nodejs.org/)**: Install the latest version of Node.js from the official website.
- **[Puppeteer](https://pptr.dev/)**: A Node.js library that provides a high-level API to control headless Chrome or Chromium, useful for automation tasks.
- **[CapSolver API Key](https://www.capsolver.com/?utm_source=official&utm_medium=blog&utm_campaign=cloudflarenodejs)**: Sign up for CapSolver and obtain an API key for CAPTCHA-solving services.

### What is CapSolver?

[CapSolver](https://www.capsolver.com/?utm_source=official&utm_medium=blog&utm_campaign=cloudflarenodejs) is a powerful tool designed to assist with solving CAPTCHAs and other automated challenges. As websites increasingly deploy complex security mechanisms to deter bots and scrapers, CapSolver provides a reliable solution for overcoming these obstacles.

#### Key Features of CapSolver:

- **Comprehensive CAPTCHA Solutions**: CapSolver supports various types of CAPTCHAs, including reCAPTCHA, hCaptcha, Cloudflare, ImageToText and more, enabling seamless challenge resolution.
- **High Accuracy**: Leveraging advanced algorithms and machine learning models, CapSolver ensures high accuracy in solving CAPTCHAs, reducing failure rates and improving efficiency.
- **Easy Integration**: CapSolver offers a user-friendly API that integrates easily with your existing automation tools, including Puppeteer. This enables you to automate CAPTCHA solving as part of your web scraping or testing workflows.
- **24/7 Support**: CapSolver provides robust customer support to help resolve any issues or questions, ensuring smooth operation and timely assistance.

  Here’s a more detailed and enriched version of your explanation, along with a concluding section for the article:

---

### Detailed Implementation Steps

1. **Using Puppeteer and JavaScript to Call the CapSolver API and Access the Website**
   - Puppeteer is a powerful Node.js library that enables you to control headless Chrome or Chromium over the DevTools Protocol. In this context, it’s used to navigate web pages and handle complex interactions, such as dealing with CAPTCHAs.

2. **Retrieving the SiteKey for Turnstile**
   - The SiteKey is a crucial element that must be obtained to solve the Turnstile challenge. This key is unique to each website and is required to interact with the CAPTCHA-solving API. You can extract the SiteKey by analyzing the website’s HTML or by using tools like the [CapSolver Extension](https://www.capsolver.com/blog/Cloudflare/identify-cloudflare-turnstile-parameters) to streamline the process. For a detailed guide on how to identify the necessary parameters, refer to our dedicated blog post.

3. **Using JavaScript to Call the CapSolver API and Obtain a Token**
   - Once you have the SiteKey, you can make a request to the CapSolver API. This involves creating a task that CapSolver will process to solve the CAPTCHA. The API will return a token that you can use to bypass the Turnstile protection.
   - Here’s an example of how to interact with the CapSolver API using JavaScript:

```javascript
// npm install axios puppeteer-core
const axios = require('axios');
const puppeteer = require("puppeteer-core");

const api_key = "YOUR_API_KEY";
const site_key = "0xxxxxx";
const site_url = "https://xxx.xxx.xxx/xxx";
const proxy = "http://xxx:xxx@x.x.x.x:x"

async function capsolver() {
  const payload = {
    clientKey: api_key,
    task: {
      type: 'AntiTurnstileTaskProxyLess',
      websiteKey: site_key,
      websiteURL: site_url,
      metadata: {
          action: '',  // optional,
          type: "turnstile"
      },
      // proxy: proxy
    }
  };

  try {
    const res = await axios.post("https://api.capsolver.com/createTask", payload);
    const task_id = res.data.taskId;
    if (!task_id) {
      console.log("Failed to create task:", res.data);
      return;
    }
    console.log("Got taskId:", task_id);

    while (true) {
      await new Promise(resolve => setTimeout(resolve, 1000)); // Delay for 1 second

      const getResultPayload = {clientKey: api_key, taskId: task_id};
      const resp = await axios.post("https://api.capsolver.com/getTaskResult", getResultPayload);
      const status = resp.data.status;

      if (status === "ready") {
        return resp.data.solution.token;
      }
      if (status === "failed" || resp.data.errorId) {
        console.log("Solve failed! response:", resp.data);
        return;
      }
    }
  } catch (error) {
    console.error("Error:", error);
  }
}

const wait = (ms) => new Promise(resolve => setTimeout(resolve, ms));
async function reqSite(){
  let coockie;
  await capsolver().then(token => {
    console.log(token);
    coockie = token;
  });

  const browser = await puppeteer.launch({
    headless: false,
    executablePath: "Browser Path"
  });

  const page = await browser.newPage();
  await wait(500);
  await page.setCookie({
    name: "cf_clearance",
    value: coockie,
    domain: "xx.xx.xx"
  });
  await wait(500);
  await page.goto(site_url);
  // TODO
  await page.close();
  await browser.disconnect();
}

reqSite().then();
```

4. **Setting Cookies with Puppeteer and Accessing the Page Content**
   - With the token obtained from CapSolver, you can now set the appropriate cookies in Puppeteer to pass the Cloudflare challenge. This step is critical, as it allows you to access the protected content on the website.

### Conclusion

In this tutorial, we explored how to effectively navigate Cloudflare's Turnstile security using Puppeteer and the CapSolver API. By understanding the intricacies of Cloudflare's protective measures and leveraging powerful tools like CapSolver, developers can successfully automate tasks such as web scraping, data extraction, and testing without compromising compliance with legal standards.

Automation continues to grow in importance across various industries, and with it, the challenges posed by advanced security systems like Turnstile. By staying informed and utilizing the right strategies, you can ensure that your automation projects run smoothly and efficiently.

If you're working on similar projects, don’t hesitate to explore [CapSolver](https://www.capsolver.com/?utm_source=official&utm_medium=blog&utm_campaign=cloudflarenodejs) for robust CAPTCHA-solving solutions. For more insights, be sure to check out related topics such as [web scraping best practices](https://www.scrapingbee.com/blog/web-scraping-best-practices/) and [Puppeteer usage tips](https://pptr.dev/).

