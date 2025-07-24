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
