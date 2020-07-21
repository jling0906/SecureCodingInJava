---
layout: post
title:  "AJAX Security"
date:   2020-07-20 13:19:04 -0700
categories: jekyll update
---

### **Client Side (JavaScript)**

#### **1. Use .innerText instead of .innerHtml**
.innerText returns the text content inside the HTML element tag, while .innerHtml returns everything inside that element, including HTML formating, which allows potential XSS attacks. Hence, .innerText is preferred to prevent XSS.

#### **2. Don't perform encryption in client side code, use TLS/SSL and encrypt on the server**
First, it is insecure. The attackers can get the encrypted tokens and the responses from the server. With tons of token-response pairs, the attacker can perform any kind of attacks, such as fault attacks to guess the encryption algorithm.
 
Second, we can not expect all users to always turn on javascript on their browser.

#### **3. Avoid using eval()**
In frontend, eval enables the functionality of evaluate the JavaScript in the code, which may introduce injections.

#### **4. Do not write serialization code on your own.** 
Improper serialization code could cause security issues. Use framework instead of doing it on your own.

#### 5. **[Normalize](https://jling0906.github.io/SecureCodingInJava/jekyll/update/2020/05/20/normalization.html), [sanitize](https://jling0906.github.io/SecureCodingInJava/jekyll/update/2020/05/29/sanitization.html), [canonicalize](https://jling0906.github.io/SecureCodingInJava/jekyll/update/2020/05/23/canonicalization.html), and [validate](https://jling0906.github.io/SecureCodingInJava/jekyll/update/2020/06/01/validation.html) customer input to avoid injections**

#### 6. **Avoid building XML or JSON dynamically to prevent injections**
Any dynamically built code could introduce injections.

#### 7. **Never transmit the secrets to the clients**
Any secrets such as password, credit card number, social security number sent to the client can be attacked by a malicious user. So we must keep them on the server. If needed, we can encode it before sending it to the frontend. For example, on a retail website, you will never see you full credit card shown on your account, which is always partially masked with only the last few digits revealed.

References:
https://cheatsheetseries.owasp.org/cheatsheets/AJAX_Security_Cheat_Sheet.html