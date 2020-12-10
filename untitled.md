---
description: >-
  This is a direct "import" of the Slate SDK Docs. No additional formatting has
  been done except seeing first code examples to correct language and in tabs.
  Subpages would be better.
---

# Skynet Docs Currently

## Introduction <a id="introduction"></a>

Welcome to the Skynet SDK docs!

We have SDK support for several languages:

* Shell \(using curl\). See the [Using The Shell]() section.
* [Browser JS](https://github.com/NebulousLabs/skynet-js)
* [NodeJS](https://github.com/NebulousLabs/nodejs-skynet)
* [Python](https://github.com/NebulousLabs/python-skynet)
* [Go](https://github.com/NebulousLabs/go-skynet)

as well as the [Skynet CLI](https://github.com/NebulousLabs/skynet-cli) \(written in Go\).

These SDKs are wrappers around HTTP calls to the corresponding Skynet endpoints. Full documentation for the Skynet HTTP API can be found in the [Sia API Documentation](https://sia.tech/docs/#skynet).

### Tutorial <a id="tutorial"></a>

We have a detailed tutorial for [Creating Your First Webapp On Skynet](https://blog.sia.tech/creating-your-first-web-app-on-skynet-ec6f4fff405f) on our tech blog. Check it out if you are just starting out!

### A Note About Language Differences <a id="a-note-about-language-differences"></a>

Though we tried to keep the SDKs as similar to each other as possible in usage, differences between the languages -- the idiosyncracies and best practices of each -- resulted in differences between the SDKs. We've noted them where necessary throughout the documentation.

#### Function Parameters <a id="function-parameters"></a>

In most of the SDKs the additional options may be left out of the function calls, in which case the default options are used. In **Go** the options must always be passed in, as the language does not support optional function parameters.

#### Case <a id="case"></a>

Note that the casing of functions and their parameters differs between the languages:

| Language | Case |
| :--- | :--- |
| Javascript | [`camelCase`](https://en.wikipedia.org/wiki/Camel_case) |
| Python | [`snake_case`](https://en.wikipedia.org/wiki/Snake_case) |
| Go | [`PascalCase`](https://en.wikipedia.org/wiki/PascalCase) |

For consistency throughout this document, functions and their parameters are documented using `camelCase`.

#### Standard Responses <a id="standard-responses"></a>

Functions will respond with the desired content on success and with errors or exceptions on failure. The error messages may contain HTTP status codes \(see [the Sia Docs](https://sia.tech/docs/#standard-responses)\).

Functions will fail differently in each SDK depending on the language:

| Language | Failure Mode |
| :--- | :--- |
| Javascript | Exceptions are raised and must be caught with a `try-catch` block. |
| Python | Same as above. |
| Go | Errors are returned and must be checked for explicitly. |

### Using The Shell <a id="using-the-shell"></a>

```bash
function skynet() {
  curl -X POST "https://siasky.net/skynet/skyfile" -F "file=@$1" \
    | jq ".skylink" | xargs -I _ echo "https://siasky.net/_"
}
```

Our shell examples present only simple curl calls. You may find it helpful to write wrappers around these in Bash, or the shell language of your choice, to make them more reusable. To the right we have provided a simple example which uses [`jq`](https://stedolan.github.io/jq/).

## Getting Started <a id="getting-started"></a>

### Making Your First API Call <a id="making-your-first-api-call"></a>

{% tabs %}
{% tab title="curl" %}
```bash
curl -L -X POST "https://siasky.net/skynet/skyfile/" -F 'file=@image.jpg'
```
{% endtab %}

{% tab title="CLI" %}
```bash
skynet upload "./image.jpg"
```
{% endtab %}

{% tab title="Browser JS" %}
```javascript
import { SkynetClient } from "skynet-js";

const client = new SkynetClient();

// Assume we have a file from an input form.

async function uploadExample() {
  try {
    const skylink = await client.uploadFile(file);
    console.log(`Upload successful, skylink: ${skylink}`);
  } catch (error) {
    console.log(error)
  }
}
```
{% endtab %}

{% tab title="NodeJS" %}
```javascript
const { SkynetClient } = require('@nebulous/skynet');

const client = new SkynetClient();

(async () => {
    const skylink = await client.uploadFile("./image.jpg");
    console.log(`Upload successful, skylink: ${skylink}`);
})();
```
{% endtab %}

{% tab title="Python" %}
```python
import siaskynet as skynet

client = skynet.SkynetClient()

skylink = client.upload_file("image.jpg")
print("Upload successful, skylink: " + skylink)
```
{% endtab %}

{% tab title="Go" %}
```go
package main

import (
    "fmt"
    skynet "github.com/NebulousLabs/go-skynet/v2"
)

var client = skynet.New()

func main() {
    skylink, err := client.UploadFile("./image.jpg", skynet.DefaultUploadOptions)
    if err != nil {
        panic("Unable to upload: " + err.Error())
    }
    fmt.Printf("Upload successful, skylink: %v\n", skylink)
}
```
{% endtab %}
{% endtabs %}

The SDKs are set up to be as simple as possible. Despite the many options for configuration, most users will be able to get started with a single API call. In the example on the right, we upload the file `image.jpg` to the default Skynet portal \(which in Browser JS is the portal running the code and in other SDKs is `https://siasky.net`\).

**Note:** If you are testing or running a Browser JS app locally, you will either have to run a Skynet portal locally or initialize the Skynet Client to connect to an existing portal such as `https://siasky.net`. See [Portal Selection]() below.

### Skynet Client <a id="skynet-client"></a>

The above examples also demonstrate the use of the Skynet client. This client is required to make any API calls. It sets the portal that will be used for all requests. It can also be initialized with custom connection options that will be applied in all calls, unless calling an API method with the same options, which then take precedent and override what was set in client initialization.

Please see [Using The Skynet Client]() for more information.

### Portal Selection <a id="portal-selection"></a>

In order to use a specific portal, you will need to use it as the first argument in client creation. Please see [Basic Portal Selection]() for more information.

### Setting Additional Options <a id="setting-additional-options"></a>

Each SDK function also accepts additional options. These vary depending on the endpoint and are documented alongside each function.

#### Common Options <a id="common-options"></a>

Every function accepts the following common options:

| Option | Description | Default |
| :--- | :--- | :--- |
| `APIKey` | The API password used for [authentication](). | `""` |
| `customUserAgent` | Allows changing the User Agent, as some portals may reject user agents that are not `Sia-Agent` for security reasons. | `""` |
| `onUploadProgress` | Optional callback to track upload progress. | undefined |

### Useful Constants <a id="useful-constants"></a>

Here are some constants exported by the SDKs which may be of use in applications.

| Constant | Description | Default |
| :--- | :--- | :--- |
| `defaultSkynetPortalUrl` | The default Skynet portal to use, if one is not provided. | `"https://siasky.net"` |
| `uriSkynetPrefix` | The Skynet URI prefix. | `"sia://"` |

## Using The Skynet Client <a id="using-the-skynet-client"></a>

The Skynet Client is required to make any API calls. It sets the portal that will be used for all requests. It can also be initialized with custom connection options that will be applied in all calls, unless calling an API method with the same options \(which then take precedent and override what was set in client initialization\).

### Basic Portal Selection <a id="basic-portal-selection"></a>

```text
import { SkynetClient } from "skynet-js";

// Or SkynetClient() without arguments to use the default portal.
const client = new SkynetClient("https://some-other-portal.xyz");
```

```text
const { SkynetClient } = require('@nebulous/skynet');

// Or SkynetClient() without arguments to use the default portal.
const client = new SkynetClient("https://some-other-portal.xyz");
```

```text
import siaskynet as skynet

# Or SkynetClient() without arguments to use the default portal.
client = skynet.SkynetClient("https://some-other-portal.xyz")
```

```text
package main

import (
    "fmt"
    skynet "github.com/NebulousLabs/go-skynet/v2"
)

var client = skynet.NewCustom("https://some-other-portal.xyz", skynet.Options{})
```

To make any API calls you will need to first create a new client with the desired portal. The client can be initialized without arguments to let the client choose it for you \(see below\) or the portal can be specified. See the code example on the right.

### Default Portal Selection <a id="default-portal-selection"></a>

The default portal used is `https://siasky.net` for all SDKs _except Browser JS_, which tries to use the portal which is running the sky app. The default portal is accessible through the exported function, `defaultPortalUrl`, and no configuration is required to use it. Having a reasonable choice already selected keeps friction for new developers low.

In the future the default selection will be smarter and there will be more options for default portal selection, such as configuration files. Please see [this issue](https://github.com/NebulousLabs/skynet-docs/issues/21).

### Setting Connection Options <a id="setting-connection-options"></a>

```text
import { SkynetClient } from "skynet-js";

const client = new SkynetClient("", { APIKey: "foobar" });
```

```text
const { SkynetClient } = require('@nebulous/skynet');

const client = new SkynetClient("", { APIKey: "foobar" });
```

```text
import siaskynet as skynet

# Or SkynetClient() without arguments to use the default portal.
client = skynet.SkynetClient("", {"api_key": "foobar"})
```

```text
package main

import (
    "fmt"
    skynet "github.com/NebulousLabs/go-skynet/v2"
)

var client = skynet.NewCustom("", skynet.Options{APIKey: "foobar"})
```

Apart from setting a persistent portal, client initialization also allows you to choose persistent connection settings. These will be applied on every subsequent API call using the client, unless the setting is overridden in a particular call.

Please see [Setting Additional Options]().

## Uploading To Skynet <a id="uploading-to-skynet"></a>

### Uploading A File <a id="uploading-a-file"></a>

```text
curl -L -X POST "https://siasky.net/skynet/skyfile/" -F 'file=@image.jpg'
```

```text
skynet upload "./image.jpg"
```

```text
import { SkynetClient } from "skynet-js";

const client = new SkynetClient();

// NOTE: This example is different from the other SDKs because we cannot just
// take a path to a local file.

async function uploadExample() {
  try {
    const skylink = await client.uploadFile(file);
  } catch (error) {
    console.log(error)
  }
}
```

```text
const { SkynetClient } = require('@nebulous/skynet');

const client = new SkynetClient();

(async () => {
    const skylink = await client.uploadFile("./image.jpg");
    console.log(`Upload successful, skylink: ${skylink}`);
})();
```

```text
import siaskynet as skynet

client = skynet.SkynetClient()

skylink = client.upload_file("image.jpg")
print("Upload successful, skylink: " + skylink)
```

```text
package main

import (
    "fmt"
    skynet "github.com/NebulousLabs/go-skynet/v2"
)

var client = skynet.New()

func main() {
    skylink, err := client.UploadFile("./image.jpg", skynet.DefaultUploadOptions)
    if err != nil {
        panic("Unable to upload: " + err.Error())
    }
    fmt.Printf("Upload successful, skylink: %v\n", skylink)
}
```

Uploading a file to Skynet can be done through a Skynet portal or your local `siad` instance.

#### Parameters <a id="parameters"></a>

| Field | Description |
| :--- | :--- |
| `path` | The local path where the file to upload may be found. |

_Browser JS:_

| Field | Description |
| :--- | :--- |
| `file` | The `File` object returned from an input form. |

#### Additional Options <a id="additional-options"></a>

| Field | Description | Default |
| :--- | :--- | :--- |
| `portalFileFieldName` | The field name for files on the portal. Usually should not need to be changed. | `"file"` |
| `portalDirectoryFileFieldName` | The field name for directories on the portal. Usually should not need to be changed. | `"files[]"` |
| `customFilename` | Custom filename. This is the filename that will be returned when downloading the file in a browser. | `""` |
| `customDirname` | Custom dirname. If this is empty, the base name of the directory being uploaded will be used by default. | `""` |
| `skykeyName` | The name of the skykey on the portal used to encrypt the upload. | `""` |
| `skykeyID` | The ID of the skykey on the portal used to encrypt the upload. | `""` |
| `timeout_seconds` | The timeout in seconds. | `""` |

#### Response <a id="response"></a>

```text
{
  "skylink": "CABAB_1Dt0FJsxqsu_J4TodNCbCGvtFf1Uys_3EgzOlTcg",
  "merkleroot": "QAf9Q7dBSbMarLvyeE6HTQmwhr7RX9VMrP9xIMzpU3I",
  "bitfield": 2048
}
```

```text
Successfully uploaded file! Skylink: sia://CABAB_1Dt0FJsxqsu_J4TodNCbCGvtFf1Uys_3EgzOlTcg
```

```text
"sia://CABAB_1Dt0FJsxqsu_J4TodNCbCGvtFf1Uys_3EgzOlTcg"
```

```text
"sia://CABAB_1Dt0FJsxqsu_J4TodNCbCGvtFf1Uys_3EgzOlTcg"
```

```text
"sia://CABAB_1Dt0FJsxqsu_J4TodNCbCGvtFf1Uys_3EgzOlTcg"
```

```text
"sia://CABAB_1Dt0FJsxqsu_J4TodNCbCGvtFf1Uys_3EgzOlTcg"
```

| Field | Description |
| :--- | :--- |
| `skylink` | This is the skylink that can be used when downloading to retrieve the file that has been uploaded. It is a 46-character base64 encoded string that consists of the merkle root, offset, fetch size, and Skylink version which can be used to access the content. |
| `merkleroot` | \(`curl` only\) This is the hash that is encoded into the skylink. |
| `bitfield` | \(`curl` only\) This is the bitfield that gets encoded into the skylink. The bitfield contains a version, an offset and a length in a heavily compressed and optimized format. |

### Uploading A Directory <a id="uploading-a-directory"></a>

```text
curl -L -X POST "https://siasky.net/skynet/?filename=images" -F 'files[]=@./images/image1.png' -F 'files[]=@./images/image2.png'
```

```text
import { getRelativeFilePath, getRootDirectory, SkynetClient } from "skynet-js";

const client = new SkynetClient();

// Assume we have a list of files from an input form.

async function uploadDirectoryExample() {
  try {
    // Get the directory name from the list of files.
    // Can also be named manually, i.e. if you build the files yourself
    // instead of getting them from an input form.
    const filename = getRootDirectory(files[0]);

    // Use reduce to build the map of files indexed by filepaths
    // (relative from the directory).
    const directory = files.reduce((accumulator, file) => {
      const path = getRelativeFilePath(file);

      return { ...accumulator, [path]: file };
    }, {});

    const skylink = await client.uploadDirectory(directory, filename);
  } catch (error) {
    console.log(error);
  }
}
```

```text
const { SkynetClient } = require('@nebulous/skynet');

const client = new SkynetClient();

(async () => {
    const url = await client.uploadDirectory("./images");
    console.log(`Upload successful, url: ${url}`);
})();
```

```text
import siaskynet as skynet

client = skynet.SkynetClient()

url = client.upload_directory("./images")
print("Upload successful, url: " + url)
```

```text
package main

import (
    "fmt"
    skynet "github.com/NebulousLabs/go-skynet/v2"
)

var client = skynet.New()

func main() {
    url, err := client.UploadDirectory("./images", skynet.DefaultUploadOptions)
    if err != nil {
        panic("Unable to upload: " + err.Error())
    }
    fmt.Printf("Upload successful, url: %v\n", url)
}
```

It is possible to upload a directory as a single piece of content. Doing this will allow you to address your content under one skylink, and access the files by their path. This is especially useful for webapps.

For example, let's say you upload a web app with the following simple structure:

```text
src
|-- favicon.ico
|-- index.html
|-- css
    |-- main.css
|-- js
    |-- app.js
```

The four files can be accessed as follows:

```text
[portal url]/[skylink]/favicon.ico
[portal url]/[skylink]/index.html
[portal url]/[skylink]/css/main.css
[portal url]/[skylink]/js/app.js
```

#### Parameters <a id="parameters-2"></a>

| Field | Description |
| :--- | :--- |
| `path` | The local path where the directory to upload may be found. |

_Browser JS:_

| Field | Description |
| :--- | :--- |
| `directory` | Object containing `File`s from an input form to upload, indexed by their path strings. |
| `filename` | The name of the directory. |

#### Additional Options <a id="additional-options-2"></a>

See [Uploading A File]().

#### Response <a id="response-2"></a>

```text
{
  "skylink": "EAAV-eT8wBIF1EPgT6WQkWWsb3mYyEO1xz9iFueK5zCtqg",
  "merkleroot": "QAf9Q7dBSbMarLvyeE6HTQmwhr7RX9VMrP9xIMzpU3I",
  "bitfield": 2048
}
```

```text
Successfully uploaded directory! Skylink: sia://EAAV-eT8wBIF1EPgT6WQkWWsb3mYyEO1xz9iFueK5zCtqg
```

```text
"sia://EAAV-eT8wBIF1EPgT6WQkWWsb3mYyEO1xz9iFueK5zCtqg"
```

```text
"sia://EAAV-eT8wBIF1EPgT6WQkWWsb3mYyEO1xz9iFueK5zCtqg"
```

```text
"sia://EAAV-eT8wBIF1EPgT6WQkWWsb3mYyEO1xz9iFueK5zCtqg"
```

```text
"sia://EAAV-eT8wBIF1EPgT6WQkWWsb3mYyEO1xz9iFueK5zCtqg"
```

See [Uploading A File]().

### Uploading With Encryption <a id="uploading-with-encryption"></a>

```text
skynet upload "./image.jpg" --skykey-name "my-skykey"
```

```text
// NOTE: this feature has not yet been implemented for this SDK.

import { SkynetClient } from "skynet-js";

const client = new SkynetClient();

// Assume we have a file from an input form.

async uploadEncryptionExample() {
  try {
    const skylink = await client.uploadFile(file, { skykeyName: "my-skykey" });
  } catch (error) {
    console.log(error)
  }
}
```

```text
// NOTE: this feature has not yet been implemented for this SDK.

const { SkynetClient } = require('@nebulous/skynet');

const client = new SkynetClient();

(async () => {
    const skylink = await client.uploadFile(
        "./image.jpg",
        { skykeyName: "my-skykey" }
    );
    console.log(`Upload successful, skylink: ${skylink}');
})();
```

```text
# NOTE: this feature has not yet been implemented for this SDK.

import siaskynet as skynet

client = skynet.SkynetClient()

skylink = client.upload_file("image.jpg", { skykeyName: "my-skykey" })
print("Upload successful, skylink: " + skylink)
```

```text
package main

import (
    "fmt"
    skynet "github.com/NebulousLabs/go-skynet/v2"
)

var client = skynet.New()

func main() {
    opts := skynet.DefaultUploadOptions
    opts.SkykeyName = "my-skykey"
    skylink, err := client.UploadFile("./image.jpg", opts)
    if err != nil {
        panic("Unable to upload: " + err.Error())
    }
    fmt.Printf("Upload successful, skylink: %v\n", skylink)
}
```

If you have a skykey on the portal you can ask the portal to encrypt the uploaded content for you. Simply pass the skykey name or ID in the custom options when uploading a file or directory.

See the additional options in [Uploading A File]().

Also see [Encryption]().

## Downloading From Skynet <a id="downloading-from-skynet"></a>

### Downloading A File <a id="downloading-a-file"></a>

```text
curl -A "Sia-Agent" "https://siasky.net/CABAB_1Dt0FJsxqsu_J4TodNCbCGvtFf1Uys_3EgzOlTcg" -o dst.jpg
```

```text
skynet download "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg" "./dst.jpg"
```

```text
import { SkynetClient } from "skynet-js";

const client = new SkynetClient();
const skylink = "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg";

try {
  client.downloadFile(skylink);
  // Or client.openFile(skylink) to open it in a new browser tab.
} catch (error) {
  console.log(error);
}
```

```text
const { SkynetClient } = require('@nebulous/skynet');

const client = new SkynetClient();
const skylink = "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg";

(async () => {
    await client.downloadFile("./dst.jpg", skylink);
    console.log('Download successful');
})();
```

```text
import siaskynet as skynet

client = skynet.SkynetClient()
skylink = "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg"

client.download_file("./dst.jpg", skylink)
print("Download successful")
```

```text
package main

import (
    "fmt"
    skynet "github.com/NebulousLabs/go-skynet/v2"
)

const skylink = "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg"
var client = skynet.New()

func main() {
    err := client.DownloadFile("./dst.go", skylink, skynet.DefaultDownloadOptions)
    if err != nil {
        panic("Something went wrong, please try again.\nError: " + err.Error())
    }
    fmt.Println("Download successful")
}
```

This function downloads a skylink using HTTP streaming. The call blocks until the data is received. There is a 30s default timeout applied to downloading a skylink. If the data can not be found within this 30s time constraint, a `404` error will be returned. This timeout is configurable.

#### Parameters <a id="parameters"></a>

| Field | Description |
| :--- | :--- |
| `path` | The local path where the file should be downloaded to. |
| `skylink` | The skylink that should be downloaded. The skylink can contain an optional path. |

_Browser JS:_

| Field | Description |
| :--- | :--- |
| `skylink` | The skylink that should be downloaded. The skylink can contain an optional path. |

#### Additional Options <a id="additional-options"></a>

| Field | Description | Default |
| :--- | :--- | :--- |
| `path` | The path to use after the skylink. See the next section. | `""` |
| `skykeyName` | The name of the skykey on the portal used to decrypt the download. | `""` |
| `skykeyID` | The ID of the skykey on the portal used to decrypt the download. | `""` |
| `timeout_seconds` | The timeout in seconds. | `""` |

#### Response <a id="response"></a>

Empty on success.

### Downloading A File From An Uploaded Directory <a id="downloading-a-file-from-an-uploaded-directory"></a>

```text
curl -A "Sia-Agent" "https://siasky.net/XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg/dir2/file3" -o dst.jpg
```

```text
skynet download "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg/dir2/file3" "./dst.jpg"
```

```text
import { SkynetClient } from "skynet-js";

const client = new SkynetClient();

// Using the skylink.
try {
  const skylink = "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg/dir2/file3";
  client.downloadFile(skylink);
} catch (error) {
  console.log(error);
}

// Using the path option.
try {
  const skylink = "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg";
  client.downloadFile(skylink, { path: "dir2/file3" });
} catch (error) {
  console.log(error);
}
```

```text
const { SkynetClient } = require('@nebulous/skynet');

const client = new SkynetClient();

// Using the skylink.
(async () => {
  const skylink = "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg/dir2/file3";
    await client.downloadFile("./dst.jpg", skylink);
    console.log('Download successful');
})();

// Using the path option.
(async () => {
  const skylink = "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg";
    await client.downloadFile("./dst.jpg", skylink, { path: "dir2/file3" });
    console.log('Download successful');
})()
```

```text
import siaskynet as skynet

client = skynet.SkynetClient()

# Using the skylink.
skylink = "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg/dir2/file3"
client.download_file("./dst.jpg", skylink)
print("Download successful")

# Using the path option.
skylink = "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg"
client.download_file("./dst.jpg", skylink, { "path": "dir2/file3" })
print("Download successful")
```

```text
package main

import (
    "fmt"
    skynet "github.com/NebulousLabs/go-skynet/v2"
)

var client = skynet.New()

func main() {
    // Using the skylink.
    skylink := "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg/dir2/file3"
    opts := skynet.DefaultDownloadOptions
    err := client.DownloadFile("./dst.go", skylink, opts)
    if err != nil {
        panic("Something went wrong, please try again.\nError: " + err.Error())
    }
    fmt.Println("Download successful")

    // Using the path option.
    skylink = "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg"
    opts.Path = "dir2/file3";
    err := client.DownloadFile("./dst.go", skylink, opts)
    if err != nil {
        panic("Something went wrong, please try again.\nError: " + err.Error())
    }
    fmt.Println("Download successful")
}
```

It is possible to download files from uploaded directories if their paths relative to the uploaded directory are known. There are two ways to do this.

#### Including The Path In The Skylink <a id="including-the-path-in-the-skylink"></a>

The skylink being passed in can contain an optional path. This path can specify a directory or a particular file. If specified, only that file or directory will be returned. The examples here use the directory structure from [Uploading A Directory]() to illustrate this.

#### The Path Additional Parameter <a id="the-path-additional-parameter"></a>

There is a caveat to the above approach: the skylink is used as-is and any special characters in the appended path are not encoded. We recommend using the additional option `path` to let the SDK properly encode it for you.

The `path` option also is easier to use if you would otherwise have to manually append the path to the skylink.

```text
curl -I -A "Sia-Agent" "https://siasky.net/CABAB_1Dt0FJsxqsu_J4TodNCbCGvtFf1Uys_3EgzOlTcg"
```

```text
# NOTE: this function has not yet been implemented for this SDK.

skynet metadata "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg"
```

```text
// NOTE: this function has not yet been implemented for this SDK.

import { SkynetClient } from "skynet-js";

const client = new SkynetClient();
const skylink = "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg";

async metadataExample() {
  try {
    const md = await client.getMetadata(skylink);
  } catch (error) {
    console.log(error);
  }
}
```

```text
// NOTE: this function has not yet been implemented for this SDK.

const { SkynetClient } = require('@nebulous/skynet');

const client = new SkynetClient();
const skylink = "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg";

(async () => {
    const md = await client.getMetadata(skylink);
    console.log(Get metadata successful');
})();
```

```text
import siaskynet as skynet

client = skynet.SkynetClient()
skylink = "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg"

md = client.get_metadata(skylink)
```

```text
// NOTE: this function has not yet been implemented for this SDK.

package main

import (
    "fmt"
    skynet "github.com/NebulousLabs/go-skynet/v2"
)

const skylink = "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg"
var client = skynet.New()

func main() {
    md, err := client.Metadata(skylink, skynet.DefaultMetadataOptions)
    if err != nil {
        panic("Something went wrong, please try again.\nError: " + err.Error())
    }
    fmt.Printf("Get metadata successful, metadata: %+v\n", md)
}
```

It is possible to get metadata about a file or directory without fetching the entire content. These API calls will perform a HEAD request that fetches the headers for the given skylink. These headers are identical to the ones that would be returned if the request had been a GET request.

#### Parameters <a id="parameters-2"></a>

| Field | Description |
| :--- | :--- |
| `skylink` | The skylink that should be downloaded. The skylink can contain an optional path. This path can specify a directory or a particular file. If specified, only that file or directory will be returned. |

#### Additional Options <a id="additional-options-2"></a>

See [Downloading A File]().

#### Response <a id="response-2"></a>

```text
{
  "mode":     640,
  "filename": "folder",
  "subfiles": {
    "folder/file1.txt": {
      "mode":         640,
      "filename":     "folder/file1.txt",
      "contenttype":  "text/plain",
      "offset":       0,
      "len":          6
    }
  }
}
```

```text
{
  "mode":     640,
  "filename": "folder",
  "subfiles": {
    "folder/file1.txt": {
      "mode":         640,
      "filename":     "folder/file1.txt",
      "contenttype":  "text/plain",
      "offset":       0,
      "len":          6
    }
  }
}
```

```text
{
  "mode":     640,
  "filename": "folder",
  "subfiles": {
    "folder/file1.txt": {
      "mode":         640,
      "filename":     "folder/file1.txt",
      "contenttype":  "text/plain",
      "offset":       0,
      "len":          6
    }
  }
}
```

```text
{
  "mode":     640,
  "filename": "folder",
  "subfiles": {
    "folder/file1.txt": {
      "mode":         640,
      "filename":     "folder/file1.txt",
      "contenttype":  "text/plain",
      "offset":       0,
      "len":          6
    }
  }
}
```

```text
{
  "mode":     640,
  "filename": "folder",
  "subfiles": {
    "folder/file1.txt": {
      "mode":         640,
      "filename":     "folder/file1.txt",
      "contenttype":  "text/plain",
      "offset":       0,
      "len":          6
    }
  }
}
```

```text
{
  "mode":     640,
  "filename": "folder",
  "subfiles": {
    "folder/file1.txt": {
      "mode":         640,
      "filename":     "folder/file1.txt",
      "contenttype":  "text/plain",
      "offset":       0,
      "len":          6
    }
  }
}
```

Coming Soon

### Downloading With Decryption <a id="downloading-with-decryption"></a>

```text
skynet download [skylink] [destination] --skykey-name "my-skykey"
```

```text
// NOTE: this feature has not yet been implemented for this SDK.

import { SkynetClient } from "skynet-js";

const client = new SkynetClient();
const skylink = "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg";

try {
  client.download(skylink, { skykeyName: "my-skykey" });
} catch (error) {
  console.log(error);
}
```

```text
// NOTE: this feature has not yet been implemented for this SDK.

const { SkynetClient } = require('@nebulous/skynet');

const client = new SkynetClient();
const skylink = "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg";

(async () => {
    await client.downloadFile(
        "./dst.jpg",
        skylink,
        { skykeyName: "my-skykey" }
    );
    console.log('Download successful');
})();
```

```text
# NOTE: this feature has not yet been implemented for this SDK.

import siaskynet as skynet

client = skynet.SkynetClient()
skylink = "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg"

client.download_file("./dst.jpg", skylink, { skykeyName: "my-skykey" })
print("Download successful")
```

```text
package main

import (
    "fmt"
    skynet "github.com/NebulousLabs/go-skynet/v2"
)

const skylink = "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg"
var client = skynet.New()

func main() {
    // Must have a 'skylink' from an earlier upload.

    opts := skynet.DefaultDownloadOptions
    opts.SkykeyName = "my-skykey"
    err := client.DownloadFile("./dst.go", skylink, opts)
    if err != nil {
        panic("Something went wrong, please try again.\nError: " + err.Error())
    }
    fmt.Println("Download successful")
}
```

If you have a skykey on the portal you can ask the portal to decrypt the downloaded content for you. Simply pass the skykey name or ID in the custom options when downloading.

See the additional options in [Downloading A File]().

Also see [Encryption]().

## SkyDB <a id="skydb"></a>

SkyDB is a framework that allows Skynet users to create decentralized accounts and store mutable files in those accounts. It is built on top of the [Registry](), where every SkyDB entry in the Registry is a skylink that contains the full data.

### Getting Data From SkyDB <a id="getting-data-from-skydb"></a>

```text
# There are no API endpoints for SkyDB -- you must use the registry directly.
```

```text
import { SkynetClient, genKeyPairFromSeed } from "skynet-js";

const client = new SkynetClient();
const { publicKey } = genKeyPairFromSeed("this seed should be fairly long for security");

const dataKey = "myApp";

async function getJSONExample() {
  try {
    const { data, revision } = await client.db.getJSON(publicKey, dataKey);
  } catch (error) {
    console.log(error);
  }
}
```

#### Function <a id="function"></a>

`getJSON`

#### Parameters <a id="parameters"></a>

| Field | Type | Description |
| :--- | :--- | :--- |
| `publicKey` | `string` | User's public key as a hex-encoded string. Can be generated with the `genKeyPairFromSeed` function. |
| `dataKey` | `string` | The key of the data to fetch for the given user. |

#### Response <a id="response"></a>

```text
{
  data: {
    example: "This is some example JSON data."
  },
  revision: 11
}
```

### Setting Data On SkyDB <a id="setting-data-on-skydb"></a>

```text
# There are no API endpoints for SkyDB -- you must use the registry directly.
```

```text
import { SkynetClient, genKeyPairFromSeed } from "skynet-js";

const client = new SkynetClient();
const { privateKey } = genKeyPairFromSeed("this seed should be fairly long for security");

const dataKey = "myApp";
const json = { example: "This is some example JSON data." };

async function setJSONExample() {
  try {
    await client.db.setJSON(privateKey, dataKey, json);
  } catch (error) {
    console.log(error);
  }
}
```

#### Function <a id="function-2"></a>

`setJSON`

#### Parameters <a id="parameters-2"></a>

| Field | Type | Description |
| :--- | :--- | :--- |
| `privateKey` | `string` | User's private key as a hex-encoded string. Can be generated with the `genKeyPairFromSeed` function. |
| `dataKey` | `string` | The key of the data to fetch for the given user. |
| `json` | `object` | The JSON object to set for the given private key and data key. |

#### Response <a id="response-2"></a>

Empty on success.

### Getting The Entry URL <a id="getting-the-entry-url"></a>

```text
import { SkynetClient, genKeyPairFromSeed } from "skynet-js";

const client = new SkynetClient();
const { publicKey } = genKeyPairFromSeed("this seed should be fairly long for security");

const dataKey = "foo";

function getEntryExample() {
  try {
    const url = client.registry.getEntryUrl(publicKey, dataKey);
  } catch (error) {
    console.log(error);
  }
}
```

#### Function <a id="function-3"></a>

`getEntryUrl`

#### Parameters <a id="parameters-3"></a>

See [Getting Data From The Registry]().

#### Response <a id="response-3"></a>

```text
"https://siasky.net/skynet/registry?publickey=ed25519%3Ac1197e1275fbf570d21dde01a00af83ed4a743d1884e4a09cebce0dd21ae254c&datakey=7c96a0537ab2aaac9cfe0eca217732f4e10791625b4ab4c17e4d91c8078713b9"
```

## Registry <a id="registry"></a>

The registry allows getting and setting registry entries with a user's key and a data key \(e.g. the name of an app\).

Registry entries contain data \(currently capped at 113 bytes\), the data key, and a revision number. The revision number is incremented by 1 every time the registry entry is changed. The latest revision number, plus one, is required when running `setEntry`. Because data at a given revision number cannot be modified, the registry effectively provides versioned, immutable data.

### Getting Data From The Registry <a id="getting-data-from-the-registry"></a>

```text
curl -X GET -G -A "Sia-Agent" -u "": -d "publickey=ed25519%3Ab4f9e43178222cf33bd4432dc1eca49499397ecd1f7de23b568f3fa1e72e5c7c" -d "datakey=79c05b4b67764ad99a7976a7d2fb1cfce4f196ea217ef0356af042cb5492bd5d" "https://siasky.net/skynet/registry"
```

```text
import { SkynetClient, genKeyPairFromSeed } from "skynet-js";

const client = new SkynetClient();
const { publicKey } = genKeyPairFromSeed("this seed should be fairly long for security");

const dataKey = "foo";

async function getEntryExample() {
  try {
    const { entry, signature } = await client.registry.getEntry(publicKey, dataKey);
  } catch (error) {
    console.log(error);
  }
}
```

#### Function <a id="function"></a>

`getEntry`

#### Parameters <a id="parameters"></a>

| Field | Type | Description |
| :--- | :--- | :--- |
| `publicKey` | `string` | User's public key as a hex-encoded string. Can be generated with the `genKeyPairFromSeed` function. |
| `dataKey` | `string` | The key of the data to fetch for the given user. |

#### Response <a id="response"></a>

```text
{
  entry: {
    datakey: "foo",
    data: "bar",
    revision: 0
  },
  signature: "788dddf5232807611557a3dc0fa5f34012c2650526ba91d55411a2b04ba56164"
}
```

### Setting Data On The Registry <a id="setting-data-on-the-registry"></a>

```text
curl -L -X POST -d '{"publickey":{"algorithm":"ed25519","key":[180,249,228,49,120,34,44,243,59,212,67,45,193,236,164,148,153,57,126,205,31,125,226,59,86,143,63,161,231,46,92,124]},"datakey":"79c05b4b67764ad99a7976a7d2fb1cfce4f196ea217ef0356af042cb5492bd5d","revision":7,"data":[65,65,65,118,74,68,105,95,101,102,75,76,101,99,83,65,112,78,56,110,89,113,84,110,122,83,106,76,116,102,66,55,80,99,69,110,81,112,75,75,101,79,88,100,54,119],"signature":[2,105,4,22,162,190,49,191,180,70,89,91,4,177,81,87,79,175,225,40,224,69,173,193,113,227,225,106,48,121,221,1,119,92,253,115,198,90,142,167,2,108,245,249,217,99,112,174,87,117,213,5,105,162,191,242,129,103,244,126,136,68,33,11]}' "https://siasky.net/skynet/registry"
```

```text
import { SkynetClient, genKeyPairFromSeed } from "skynet-js";

const client = new SkynetClient();
const { privateKey } = genKeyPairFromSeed("this seed should be fairly long for security");

const dataKey = "foo";
const data = "bar";
const revision = 0;
const entry = { datakey, data, revision };

async function setEntryExample() {
  try {
    await client.registry.setEntry(privateKey, dataKey, entry);
  } catch (error) {
    console.log(error);
  }
}
```

#### Function <a id="function-2"></a>

`setEntry`

#### Parameters <a id="parameters-2"></a>

| Field | Type | Description |
| :--- | :--- | :--- |
| `privateKey` | `string` | User's private key as a hex-encoded string. Can be generated with the `genKeyPairFromSeed` function or with PKI in the node-forge library on NPM. Should be kept secret. |
| `entry` | `RegistryEntry` | The registry entry to set. See below. |

#### `RegistryEntry` <a id="registryentry"></a>

This object corresponds to a versioned entry in the registry.

| Field | Type | Description |
| :--- | :--- | :--- |
| `datakey` | `string` | The key of the data for the given entry. |
| `data` | `string` | The data for this entry. Capped at 113 bytes, but can be a skylink or an HNS domain. |
| `revision` | `number` | The revision number of this entry. It must be 1 more than the latest revision number, or 0 if the entry doesn't exist. |

#### Response <a id="response-2"></a>

Empty on success.

## Crypto Utilities <a id="crypto-utilities"></a>

This page documents cryptographic utilities for generating public and private keys as well as secure seeds. Private keys should, as the name implies, be kept private. The seeds should also be handled carefully as they can be used to derive public/private key pairs.

### Generating Key Pairs And Seeds <a id="generating-key-pairs-and-seeds"></a>

```text
import { genKeyPairAndSeed } from "skynet-js";

const { publicKey, privateKey, seed } = genKeyPairAndSeed();
```

If you do not yet have a seed, you can generate one with this function and it will also generate an associated key pair. The seed is generated using strong, cryptographic-grade randomness. It is recommended that you only store the seed for later use and re-derive the key pair with `genKeyPairFromSeed`, and that you store it securely.

#### Function <a id="function"></a>

`genKeyPairAndSeed`

#### Parameters <a id="parameters"></a>

None

#### Response <a id="response"></a>

```text
{
  publicKey: "f8a7da8324fabb9d57bb32c59c48d4ba304d08ee5f1297a46836cf841da71c80",
  privateKey: "c404ff07fba961000dfb25ece7477f45b109b50a5169a45f3fb239343002c1cff8a7da8324fabb9d57bb32c59c48d4ba304d08ee5f1297a46836cf841da71c80",
  seed: "c1197e1275fbf570d21dde01a00af83ed4a743d1884e4a09cebce0dd21ae254c"
}
```

### Generating Key Pairs From Seeds <a id="generating-key-pairs-from-seeds"></a>

```text
import { genKeyPairFromSeed } from "skynet-js";

const { publicKey, privateKey } = genKeyPairFromSeed("this seed should be fairly long for security");
```

If you already have a seed \(e.g. from a previous call to `genKeyPairAndSeed`\) you can deterministically derive the same keypair.

#### Function <a id="function-2"></a>

`genKeyPairFromSeed`

#### Parameters <a id="parameters-2"></a>

| Field | Type | Description |
| :--- | :--- | :--- |
| `seed` | `string` | The seed that should be used to generate a deterministic keypair. Can be a long and secure passphrase. |

#### Response <a id="response-2"></a>

```text
{
  publicKey: "f8a7da8324fabb9d57bb32c59c48d4ba304d08ee5f1297a46836cf841da71c80",
  privateKey: "c404ff07fba961000dfb25ece7477f45b109b50a5169a45f3fb239343002c1cff8a7da8324fabb9d57bb32c59c48d4ba304d08ee5f1297a46836cf841da71c80",
}
```

### Deriving Child Seeds <a id="deriving-child-seeds"></a>

```text
import { deriveChildSeed, genKeyPairAndSeed } from "skynet-js";

const { publicKey, privateKey, masterSeed } = genKeyPairAndSeed();
const childSeed = deriveChildSeed(masterSeed, "foo");
```

This function can be used to derive a child seed from a given master seed and subseed. For example, the master seed can be a long and secure passphrase while the subseed can be the name of an application.

#### Function <a id="function-3"></a>

`deriveChildSeed`

#### Parameters <a id="parameters-3"></a>

| Field | Type | Description |
| :--- | :--- | :--- |
| `masterSeed` | `string` | The master seed that should be used to generate a deterministic keypair. Can be a long and secure passphrase. |
| `seed` | `string` | The subseed that, in combination with the master seed, results in a deterministic keypair. |

#### Response <a id="response-3"></a>

```text
"f79cd29b92124c80a662f3085bba98955f7defbaed6e58ea891b901dc99aafc0"
```

## Encryption <a id="encryption"></a>

Encryption and decryption in Skynet are performed using _skykeys_. Skykeys can be created and queried using the functions in this section. The name or ID of a skykey must be provided when uploading or downloading with encryption.

See also:

* [Uploading With Encryption]()
* [Downloading With Decryption]()

### Creating A Skykey <a id="creating-a-skykey"></a>

```text
curl -A "Sia-Agent"  -u "": -d "name=key_to_the_castle" -d "type=private-id" "localhost:9980/skynet/createskykey"
```

```text
skynet skykey create "testcreateskykey"
```

```text
// NOTE: this function has not yet been implemented for this SDK.

import { SkynetClient } from "skynet-js";

const client = new SkynetClient();
const name = "testcreateskykey";

async function createSkykeyExample() {
  try {
    const skykey = await client.createSkykey(name, "private-id");
  } catch (error) {
    console.log(error)
  }
}
```

```text
// NOTE: this function has not yet been implemented for this SDK.

const { SkynetClient } = require('@nebulous/skynet');

const client = new SkynetClient();
const name = "testcreateskykey";

(async () => {
    const skykey = await client.createSkykey(name, "private-id");
})();
```

```text
# NOTE: this function has not yet been implemented for this SDK.

import siaskynet as skynet

client = skynet.SkynetClient()
name = "testcreateskykey"

skykey = client.create_skykey(name, "private-id")
print("Create skykey successful, skykey: " + skykey)
```

```text
package main

import (
    "fmt"
    skynet "github.com/NebulousLabs/go-skynet/v2"
)

const name = "testcreateskykey"
var client = skynet.New()

func main() {
    fmt.Printf("Creating skykey with name %v...\n", name)
    skykey, err := client.CreateSkykey(name, "private-id", skynet.DefaultCreateSkykeyOptions)
    if err != nil {
        panic("Unable to create skykey: " + err.Error())
    }
    fmt.Printf("Created skykey %v\n", skykey)
```

This function creates a skykey stored under the given name.

#### Parameters <a id="parameters"></a>

| Parameter | Description |
| :--- | :--- |
| `name` | Desired name of the skykey. |
| `type` | Desired type of the skykey. The two supported types are `"public-id"` and `"private-id"`. Users should use `"private-id"` skykeys unless they have a specific reason to use `"public-id"` skykeys which reveal skykey IDs and show which skyfiles are encrypted with the same skykey. |

#### Response <a id="response"></a>

```text
{
  "skykey": "skykey:AUI0eAOXWXHwW6KOLyI5O1OYduVvHxAA8qUR_fJ8Kluasb-ykPlHBEjDczrL21hmjhH0zAoQ3-Qq?name=testskykey1",
  "name": "key_to_the_castle",
  "id": "ai5z8cf5NWbcvPBaBn0DFQ==",
  "type": "private-id"
}
```

```text
{
  "skykey": "skykey:AUI0eAOXWXHwW6KOLyI5O1OYduVvHxAA8qUR_fJ8Kluasb-ykPlHBEjDczrL21hmjhH0zAoQ3-Qq?name=testskykey1",
  "name": "key_to_the_castle",
  "id": "ai5z8cf5NWbcvPBaBn0DFQ==",
  "type": "private-id"
}
```

```text
{
  "skykey": "skykey:AUI0eAOXWXHwW6KOLyI5O1OYduVvHxAA8qUR_fJ8Kluasb-ykPlHBEjDczrL21hmjhH0zAoQ3-Qq?name=testskykey1",
  "name": "key_to_the_castle",
  "id": "ai5z8cf5NWbcvPBaBn0DFQ==",
  "type": "private-id"
}
```

```text
{
  "skykey": "skykey:AUI0eAOXWXHwW6KOLyI5O1OYduVvHxAA8qUR_fJ8Kluasb-ykPlHBEjDczrL21hmjhH0zAoQ3-Qq?name=testskykey1",
  "name": "key_to_the_castle",
  "id": "ai5z8cf5NWbcvPBaBn0DFQ==",
  "type": "private-id"
}
```

```text
{
  "skykey": "skykey:AUI0eAOXWXHwW6KOLyI5O1OYduVvHxAA8qUR_fJ8Kluasb-ykPlHBEjDczrL21hmjhH0zAoQ3-Qq?name=testskykey1",
  "name": "key_to_the_castle",
  "id": "ai5z8cf5NWbcvPBaBn0DFQ==",
  "type": "private-id"
}
```

| Field | Type | Description |
| :--- | :--- | :--- |
| `skykey` | string | Base-64 encoded Skykey |
| `name` | string | Name of the Skykey |
| `id` | string | ID of the Skykey |
| `type` | string | Desired type of the skykey. See above for more information. |

### Adding A Skykey <a id="adding-a-skykey"></a>

```text
curl -A "Sia-Agent" -u "": -d "skykey=BAAAAAAAAABrZXkxAAAAAAAAAAQgAAAAAAAAADiObVg49-0juJ8udAx4qMW-TEHgDxfjA0fjJSNBuJ4a" "localhost:9980/skynet/addskykey"
```

```text
skynet skykey add "skykey:AUI0eAOXWXHwW6KOLyI5O1OYduVvHxAA8qUR_fJ8Kluasb-ykPlHBEjDczrL21hmjhH0zAoQ3-Qq"
```

```text
// NOTE: this function has not yet been implemented for this SDK.

import { SkynetClient } from "skynet-js";

const client = new SkynetClient();
const skykey = "skykey:AUI0eAOXWXHwW6KOLyI5O1OYduVvHxAA8qUR_fJ8Kluasb-ykPlHBEjDczrL21hmjhH0zAoQ3-Qq";

async function addSkykeyExample() {
  try {
    await client.addSkykey(skykey);
  } catch (error) {
    console.log(error)
  }
}
```

```text
// NOTE: this function has not yet been implemented for this SDK.

const { SkynetClient } = require('@nebulous/skynet');

const client = new SkynetClient();
const skykey = "skykey:AUI0eAOXWXHwW6KOLyI5O1OYduVvHxAA8qUR_fJ8Kluasb-ykPlHBEjDczrL21hmjhH0zAoQ3-Qq";

(async () => {
    await client.addSkykey(skykey);
})();
```

```text
# NOTE: this function has not yet been implemented for this SDK.

import siaskynet as skynet

client = skynet.SkynetClient()
skykey = "skykey:AUI0eAOXWXHwW6KOLyI5O1OYduVvHxAA8qUR_fJ8Kluasb-ykPlHBEjDczrL21hmjhH0zAoQ3-Qq"

client.add_skykey(skykey)
print("Add skykey successful")
```

```text
package main

import (
    "fmt"
    skynet "github.com/NebulousLabs/go-skynet/v2"
)

const skykey = "skykey:AUI0eAOXWXHwW6KOLyI5O1OYduVvHxAA8qUR_fJ8Kluasb-ykPlHBEjDczrL21hmjhH0zAoQ3-Qq"
var client = skynet.New()

func main() {
    fmt.Printf("Adding skykey %v...\n", skykey)
    err := client.AddSkykey(skykey, skynet.DefaultAddSkykeyOptions)
    if err != nil {
        panic("Unable to add skykey: " + err.Error())
    }
```

This function stores the given skykey with the renter's skykey manager.

#### Parameters <a id="parameters-2"></a>

| Parameter | Description |
| :--- | :--- |
| `skykey` | Base-64 encoded skykey |

#### Response <a id="response-2"></a>

Error or exception on failure.

### Getting A Skykey By Name <a id="getting-a-skykey-by-name"></a>

```text
# -G option is required to send --data (-d) with a GET.
curl -X GET -G -A "Sia-Agent" -u "": -d "name=key_to_the_castle" "localhost:9980/skynet/skykey"
```

```text
skynet skykey get name "testcreateskykey"
```

```text
// NOTE: this function has not yet been implemented for this SDK.

import { SkynetClient } from "skynet-js";

const client = new SkynetClient();
const name = "testcreateskykey";

async function getSkykeyByNameExample() {
  try {
    const skykey = await client.getSkykeyByName(name);
  } catch (error) {
    console.log(error)
  }
}
```

```text
// NOTE: this function has not yet been implemented for this SDK.

const { SkynetClient } = require('@nebulous/skynet');

const client = new SkynetClient();
const name = "testcreateskykey";

(async () => {
    const skykey = await client.getSkykeyByName(name);
})();
```

```text
# NOTE: this function has not yet been implemented for this SDK.

import siaskynet as skynet

client = skynet.SkynetClient()
name = "testcreateskykey"

skykey = client.get_skykey_by_name(name)
print("Get skykey successful, skykey: " + skykey)
```

```text
package main

import (
    "fmt"
    skynet "github.com/NebulousLabs/go-skynet/v2"
)

const name = "testcreateskykey"
var client = skynet.New()

func main() {
    fmt.Printf("Getting skykey with name %v...\n", name)
    skykey, err := client.GetSkykeyByName(name, skynet.DefaultGetSkykeyOptions)
    if err != nil {
        panic("Unable to get skykey: " + err.Error())
    }
    fmt.Printf("Skykey: %#v\n", skykey)
```

This function returns the base-64 encoded skykey stored under that name.

#### Parameters <a id="parameters-3"></a>

| Parameter | Description |
| :--- | :--- |
| `name` | Name of the skykey being queried. |

#### Response <a id="response-3"></a>

See [Creating A Skykey]().

#### Getting A Skykey By ID <a id="getting-a-skykey-by-id"></a>

```text
# -G option is required to send --data (-d) with a GET.
curl -X GET -G -A "Sia-Agent" -u "": -d "id=qwxONTt4agqbEmzPlSywaQ==" "localhost:9980/skynet/skykey"
```

```text
skynet skykey get id "pJAPPfWkWXpss3BvMDCJCw=="
```

```text
// NOTE: this function has not yet been implemented for this SDK.

import { SkynetClient } from "skynet-js";

const client = new SkynetClient();
const id = "pJAPPfWkWXpss3BvMDCJCw==";

async function getSkykeyByIdExample() {
  try {
    const skykey = await client.getSkykeyById(id);
  } catch (error) {
    console.log(error)
  }
}
```

```text
// NOTE: this function has not yet been implemented for this SDK.

const { SkynetClient } = require('@nebulous/skynet');

const client = new SkynetClient();
const id = "pJAPPfWkWXpss3BvMDCJCw==";

(async () => {
    const skykey = await client.getSkykeyById(id);
})();
```

```text
# NOTE: this function has not yet been implemented for this SDK.

import siaskynet as skynet

client = skynet.SkynetClient()
id = "pJAPPfWkWXpss3BvMDCJCw=="

skykey = client.get_skykey_by_id(id)
print("Get skykey successful, skykey: " + skykey)
```

```text
package main

import skynet "github.com/NebulousLabs/go-skynet/v2"

const id = "pJAPPfWkWXpss3BvMDCJCw=="
var client = skynet.New()

func main() {
    fmt.Printf("Getting skykey with id %v...\n", id)
    skykey, err := client.GetSkykeyByID(id, skynet.DefaultGetSkykeyOptions)
    if err != nil {
        panic("Unable to get skykey: " + err.Error())
    }
    fmt.Printf("Skykey: %#v\n", skykey)
}
```

This function returns the base-64 encoded skykey stored under that ID.

#### Parameters <a id="parameters-4"></a>

| Parameter | Description |
| :--- | :--- |
| `id` | ID of the skykey being queried. |

#### Response <a id="response-4"></a>

See [Creating A Skykey]().

### Listing Skykeys <a id="listing-skykeys"></a>

```text
curl -A "Sia-Agent" -u "": "localhost:9980/skynet/skykeys"
```

```text
// NOTE: this function has not yet been implemented for this SDK.

import { SkynetClient } from "skynet-js";

const client = new SkynetClient();

async function getSkykeysExample() {
  try {
    const skykeys = await client.getSkykeys();
  } catch (error) {
    console.log(error)
  }
}
```

```text
// NOTE: this function has not yet been implemented for this SDK.

const { SkynetClient } = require('@nebulous/skynet');

const client = new SkynetClient();

(async () => {
    const skykeys = await client.getSkykeys();
})();
```

```text
# NOTE: this function has not yet been implemented for this SDK.

import siaskynet as skynet

client = skynet.SkynetClient()

skykeys = client.get_skykeys()
print("Get skykeys successful, skykeys: " + skykeys)
```

```text
package main

import (
    "fmt"
    skynet "github.com/NebulousLabs/go-skynet/v2"
)

var client = skynet.New()

func main() {
    fmt.Println("Listing skykeys...")
    skykeys, err := client.ListSkykeys(skynet.DefaultListSkykeysOptions)
    if err != nil {
        panic("Unable to get skykeys: " + err.Error())
    }
    fmt.Printf("Skykeys: %v\n", skykeys)
}
```

This function lists all skykeys on the given portal.

#### Parameters <a id="parameters-5"></a>

None

#### Response <a id="response-5"></a>

List of Skykeys \(see [Creating A Skykey]()\).

## Handshake <a id="handshake"></a>

[Handshake](https://handshake.org/) is a protocol which allows the creation of update-able content with persistent links, backed by the Skynet infrastructure. For more information on using Handshake with Skynet, please see [this blog post](https://blog.sia.tech/skynet-handshake-d5d16e6b632f).

The SDKs contain support for downloading from Handshake domains as well as for resolving Handshake domains to retrieve the underlying skylinks.

### Downloading Handshake Files <a id="downloading-handshake-files"></a>

```text
curl -A "Sia-Agent" "https://siasky.net/hns/doesn"
```

```text
# NOTE: this function has not yet been implemented for this SDK.

skynet hns download "doesn" "./dst.html"
```

```text
import { SkynetClient } from "skynet-js";

const client = new SkynetClient();
const domain = "doesn";

try {
  client.downloadFileHns(domain);
  // Or client.openFileHns(domain) to open it in a new browser tab.
} catch (error) {
  console.log(error);
}
```

```text
# NOTE: this function has not yet been implemented for this SDK.

const { SkynetClient } = require('@nebulous/skynet');

const client = new SkynetClient();
const domain = "doesn";

(async () => {
    await client.downloadFileHns("./dst.html", domain);
    console.log('Handshake download successful');
})();
```

```text
# NOTE: this function has not yet been implemented for this SDK.

import siaskynet as skynet

client = skynet.SkynetClient()
domain = "doesn"

client.download_file_hns("./dst.html", domain)
print("Handshake download successful")
```

```text
// NOTE: this function has not yet been implemented for this SDK.

package main

import (
    "fmt"
    skynet "github.com/NebulousLabs/go-skynet/v2"
)

var client = skynet.New()

func main() {
  const domain = "doesn"

    err := client.DownloadFileHns("./dst.html", domain, skynet.DefaultDownloadHnsOptions)
    if err != nil {
        panic("Something went wrong, please try again.\nError: " + err.Error())
    }
    fmt.Println("Handshake download successful")
}
```

This function downloads a file from a given Handshake domain on the portal's `/hns` endpoint. To give an example, the full URL of the Sia-controlled Handshake domain `doesn` is `https://siasky.net/hns/doesn`.

#### Parameters <a id="parameters"></a>

| Field | Description |
| :--- | :--- |
| `path` | The local path where the file should be downloaded to. |
| `domain` | The Handshake domain that should be downloaded. |

_Browser JS:_

| Field | Description |
| :--- | :--- |
| `domain` | The Handshake domain that should be downloaded. |

#### Response <a id="response"></a>

Empty on success.

### Resolving Handshake Domains <a id="resolving-handshake-domains"></a>

```text
curl -A "Sia-Agent" "https://siasky.net/hnsres/doesn"
```

```text
# NOTE: this function has not yet been implemented for this SDK.

skynet hns resolve "doesn"
```

```text
import { SkynetClient } from "skynet-js";

const client = new SkynetClient();
const domain = "doesn";

try {
  const data = client.resolveHns(domain);
} catch (error) {
  console.log(error);
}
```

```text
# NOTE: this function has not yet been implemented for this SDK.

const { SkynetClient } = require('@nebulous/skynet');

const client = new SkynetClient();
const domain = "doesn";

(async () => {
    const data = await client.resolveHns(domain);
    console.log('Handshake resolve successful');
})();
```

```text
# NOTE: this function has not yet been implemented for this SDK.

import siaskynet as skynet

client = skynet.SkynetClient()
domain = "doesn"

data = client.resolve_hns(domain)
print("Handshake resolve successful")
```

```text
// NOTE: this function has not yet been implemented for this SDK.

package main

import (
    "fmt"
    skynet "github.com/NebulousLabs/go-skynet/v2"
)

const domain = "doesn"
var client = skynet.New()

func main() {
    err := client.ResolveHns(domain, skynet.DefaultResolveHnsOptions)
    if err != nil {
        panic("Something went wrong, please try again.\nError: " + err.Error())
    }
    fmt.Println("Handshake resolve successful")
}
```

This function resolves a given Handshake domain and returns its TXT record. In the context of Skynet, this should contain a `skylink` field. For example, a request to `https://siasky.net/hnsres/doesn` returns only the data `{"skylink":"sia://IAC6CkhNYuWZqMVr1gob1B6tPg4MrBGRzTaDvAIAeu9A9w"}`.

#### Parameters <a id="parameters-2"></a>

| Field | Description |
| :--- | :--- |
| `domain` | The Handshake domain that should be resolved. |

#### Response <a id="response-2"></a>

```text
{"skylink":"sia://IAC6CkhNYuWZqMVr1gob1B6tPg4MrBGRzTaDvAIAeu9A9w"}
```

```text
{"skylink":"sia://IAC6CkhNYuWZqMVr1gob1B6tPg4MrBGRzTaDvAIAeu9A9w"}
```

```text
{"skylink":"sia://IAC6CkhNYuWZqMVr1gob1B6tPg4MrBGRzTaDvAIAeu9A9w"}
```

```text
{"skylink":"sia://IAC6CkhNYuWZqMVr1gob1B6tPg4MrBGRzTaDvAIAeu9A9w"}
```

```text
{"skylink":"sia://IAC6CkhNYuWZqMVr1gob1B6tPg4MrBGRzTaDvAIAeu9A9w"}
```

The full TXT record containing the skylink is returned.

## Browser JS API <a id="browser-js-api"></a>

The following are some methods and utilities that only make sense in the browser, and thus are only provided by the Browser JS SDK.

### Opening A File <a id="opening-a-file"></a>

```text
import { SkynetClient } from "skynet-js";

const client = new SkynetClient();
const skylink = "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg";

try {
  client.openFile(skylink);
} catch (error) {
  console.log(error);
}
```

Use the client to open a skylink in a new browser tab. Browsers support opening natively only limited file extensions like `.html` or `.jpg` and will fallback to downloading the file.

#### Parameters <a id="parameters"></a>

See [Downloading A File]().

#### Response <a id="response"></a>

Empty on success.

### Getting The Download URL <a id="getting-the-download-url"></a>

```text
import { SkynetClient } from "skynet-js";

const client = new SkynetClient();
const skylink = "XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg";

try {
  const url = client.getSkylinkUrl(skylink);
} catch (error) {
  console.log(error);
}
```

Use the client to generate a direct `skylink` url.

#### Parameters <a id="parameters-2"></a>

See [Downloading A File]().

#### Additional Options <a id="additional-options"></a>

| Field | Description | Default |
| :--- | :--- | :--- |
| `download` | Option to include download directive in the url that will force a download when used. | `false` |

#### Response <a id="response-2"></a>

```text
"https://siasky.net/XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg"
```

| Field | Description |
| :--- | :--- |
| `url` | The URL for the given skylink on the client portal. |

### Getting A Handshake URL <a id="getting-a-handshake-url"></a>

```text
import { SkynetClient } from "skynet-js";

const client = new SkynetClient();
const domain = "doesn";

try {
  const url = client.getHnsUrl(domain);
} catch (error) {
  console.log(error);
}
```

Use the client to generate a direct Handshake url from a Handshake domain.

#### Parameters <a id="parameters-3"></a>

See [Downloading Handshake Files]().

#### Additional Options <a id="additional-options-2"></a>

| Field | Description | Default |
| :--- | :--- | :--- |
| `download` | Option to include download directive in the url that will force a download when used. | `false` |

#### Response <a id="response-3"></a>

```text
"https://siasky.net/hns/doesn"
```

| Field | Description |
| :--- | :--- |
| `url` | The URL for the given Handshake domain on the client portal. |

### Getting A Handshake Resolver URL <a id="getting-a-handshake-resolver-url"></a>

```text
import { SkynetClient } from "skynet-js";

const client = new SkynetClient();
const domain = "doesn";

try {
  const url = client.getHnsresUrl(domain);
} catch (error) {
  console.log(error);
}
```

Use the client to generate a direct Handshake Resolver url from a Handshake domain.

#### Parameters <a id="parameters-4"></a>

See [Resolving Handshake Domains]().

#### Additional Options <a id="additional-options-3"></a>

| Field | Description | Default |
| :--- | :--- | :--- |
| `download` | Option to include download directive in the url that will force a download when used. | `false` |

#### Response <a id="response-4"></a>

```text
"https://siasky.net/hnsres/doesn"
```

| Field | Description |
| :--- | :--- |
| `url` | The URL for the given Handshake Resolver domain on the client portal. |

### Parsing Skylinks <a id="parsing-skylinks"></a>

```text
import { parseSkylink } from "skynet-js";

const client = new SkynetClient();
const uri = "sia://XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg";

try {
  const skylink = parseSkylink(uri);
} catch (error) {
  console.log(error);
}
```

Extract a skylink from a string.

#### Parameters <a id="parameters-5"></a>

| Field | Description |
| :--- | :--- |
| `string` | The string to extract a skylink from. |

Currently supported string types are:

* direct skylink string, for example `"XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg"`
* `sia:` prefixed string, for example `"sia:XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg"`
* `sia://` prefixed string, for example `"sia://XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg"`
* skylink from url, for example `"https://siasky.net/XABvi7JtJbQSMAcDwnUnmp2FKDPjg8_tTTFP4BwMSxVdEg"`

#### Response <a id="response-5"></a>

```text
"CABAB_1Dt0FJsxqsu_J4TodNCbCGvtFf1Uys_3EgzOlTcg"
```

| Field | Description |
| :--- | :--- |
| `skylink` | See [Uploading A File](). |

## API Authentication <a id="api-authentication"></a>

```text
curl -X POST -A "Sia-Agent" --user "":"foobar" \
  "https://siasky.net/skynet/skyfile" -F 'file=@image.jpg'
```

```text
skynet upload "./image.jpg" --api-key "foobar" --custom-user-agent "Sia-Agent"
```

```text
import { SkynetClient } from "skynet-js";

const client = new SkynetClient();

async function authenticationExample() {
  try {
    const skylink = await client.uploadFile(
      file,
      { APIKey: "foobar", customUserAgent: "Sia-Agent" }
    );
  } catch (error) {
    console.log(error);
  }
}
```

```text
const { SkynetClient } = require('@nebulous/skynet');

const client = new SkynetClient();

(async () => {
    const skylink = await client.uploadFile(
    "./image.jpg",
    { APIKey: "foobar", customUserAgent: "Sia-Agent" }
  );
    console.log(`Upload successful, skylink: ${skylink}`);
})();
```

```text
import siaskynet as skynet

client = skynet.SkynetClient();

skylink = client.upload_file(
  "image.jpg",
  {"api_key": "foobar", "custom_user_agent": "Sia-Agent"}
)
print("Upload successful, skylink: " + skylink)
```

```text
package main

import (
    "fmt"
    skynet "github.com/NebulousLabs/go-skynet/v2"
)

var client = skynet.New()

func main() {
    opts := skynet.DefaultUploadOptions
    opts.APIKey = "foobar"
  opts.CustomUserAgent = "Sia-Agent"
    skylink, err := client.UploadFile("./image.jpg", opts)
    if err != nil {
        panic("Unable to upload: " + err.Error())
    }
    fmt.Printf("Upload successful, skylink: %v\n", skylink)
}
```

Portals will likely require authentication on several of their endpoints. This is to prevent unwanted modifications to the blocklist, portal list, etc.

If you are authorized to use password-protected endpoints on a portal, you may authenticate yourself by setting the `APIKey` custom option when calling a function.

### Setting The User Agent <a id="setting-the-user-agent"></a>

The portal may also require that certain sensitive requests contain a custom user agent header, usually `Sia-Agent`. This is for security purposes, as otherwise a malicious website could make requests to your local portal on your behalf and steal coins.

We want this to be an opt-in for now, so `Sia-Agent` is not currently the default. You may change the user agent header by setting the `customUserAgent` custom option. See [Setting Additional Options]().

### More Information <a id="more-information"></a>

For more information about authentication on portals and local `siad` instances please see [the Sia Docs](https://sia.tech/docs/#authentication).

## Updating From v1 <a id="updating-from-v1"></a>

Users wishing to update their SDK from `v1` to `v2` should note the following:

1. All SDKs have been updated to match Browser JS and require a client. You will first need to create a client and then make all API calls from this client.
2. The `defaultPortalUrl` string has been renamed to `defaultSkynetPortalUrl` and `defaultPortalUrl` is not a function.
3. Browser JS has had the following updates:
   1. `download` and `open` were renamed to `downloadFile` and `openFile`.
   2. `upload` was renamed to `uploadFile` and the response was changed to only include a skylink. To obtain the full response as in the old `upload`, use the new `uploadFileRequest`.
   3. `uploadFile` and `uploadDirectory` now return skylinks prefixed by `sia:`.
   4. `getDownloadUrl` has been renamed to `getSkylinkUrl`.

