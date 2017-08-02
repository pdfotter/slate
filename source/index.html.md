---
title: PDF Otter API Reference

language_tabs:
  - shell

toc_footers:
  - <a href='https://www.pdfotter.com/account#api-keys'>Get an API Key</a>

includes:

search: true
---

# Introduction

Welcome to the PDF Otter API. You can use this API to fill in templates created in your [Dashboard](https://www.pdfotter.com/pdf_templates).

We are working on ways to make working with PDF Otter faster and simpler. Feel free to reach out to us if you have any feedback or suggestions: [hello@pdfotter.com](mailto:hello@pdfotter.com).

# Authentication

> Example request:

```shell
curl https://www.pdfotter.com/api/v1/pdf_templates \
  -u test_apikey123:
```

Authentication to the API is performed using Basic Auth. Include your API key as the username. The password can be left blank. You can find your API Keys on your [account page](https://www.pdfotter.com/account#api-keys).

# PDF Templates

## Get a PDF Template

```shell
curl https://www.pdfotter.com/api/v1/pdf_templates/tem_1c3 \
  -u test_apikey123:
```

> The command above returns JSON structured like this:

```json
{
  "id": "tem_1c3",
  "name": "Subscription Agreement"
}
```

This endpoint retrieves a single PDF Template's details. For now, templates can only be created and configured through the [dashboard](https://www.pdfotter.com/pdf_templates). Template IDs can be found there as well.

### HTTP Request

`GET https://www.pdfotter.com/api/v1/pdf_templates/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the template to retrieve (required)


## Get All PDF Templates

```shell
curl -G https://www.pdfotter.com/api/v1/pdf_templates \
  -u test_apikey123:
```

> The command above returns JSON structured like this:

```json
[
  {
    "id": "tem_1c3",
    "name": "Subscription Agreement"
  },
  {
    "id": "tem_1b3",
    "name": "Very Important Contract"
  }
]
```

This endpoint retrieves all PDF Templates associated with the account.

### HTTP Request

`GET https://www.pdfotter.com/api/v1/pdf_templates`


# PDF Files

## Create PDF File

```shell
curl -X POST https://www.pdfotter.com/api/v1/pdf_files.pdf \
  -u test_apikey123: \
  -d pdf_template=tem_1c3 \
  -d name="Mariusz Lapinski's Subscription Agreement - Pending" \
  -d save_result=false \
  -d data[user_name]="Mariusz Lapinski" \
  -d data[user_signature]="Mariusz Lapinski" \
  -d data[user_signed_at]="March 31, 2017" \
  > tem_1c3_filled_in.pdf
```

> The above command saves a PDF document to `tem_1c3_filled_in.pdf`. Because `save_result=false`, it won't be saved on PDF Otter for later use.

This endpoint creates a PDF File - fills in a PDF Template - with the data provided. It responds with the binary PDF file so you can store it on your end. PDF Otter won't store the file if you set the `save_result` flag to `false`. If you choose to save the result, it will be available in your dashboard and can be made public to share with your users.

### HTTP Request

`POST https://www.pdfotter.com/api/v1/pdf_files.pdf`

### Parameters

Parameter | Type |Description
--------- | ---- | -----------
pdf_template | string | A template ID (required)
name | string | The file's name (recommended)
save_result | boolean | Tells PDF Otter if it should save the file on its service (default: `true`)
is_public | boolean | Whether the file can be viewed using a share URL. This parameter is ignored if save_result is false. (default: `true`)
data | object | Data to fill in the template with. If no data is provided, the generated PDF will just be the template.

## Create PDF File (asynchronously)

```shell
curl -X POST https://www.pdfotter.com/api/v1/pdf_files.json \
  -u test_apikey123: \
  -d pdf_template=tem_1c3 \
  -d data[user_name]="Mariusz Lapinski" \
  -d data[user_signature]="Mariusz Lapinski" \
  -d data[user_signed_at]="March 31, 2017"
```

> The above command returns JSON structured like this:

```json
{
  "id": "fil_b2c",
  "pdf_template": "tem_1c3",
  "is_public": true,
  "name": "Mariusz Lapinski's Subscription Agreement - Pending",
  "status": "pending",
  "version": 1,
  "urls": {
    "download": null,
    "share":    "https://www.pdfotter.com/fil_b2c/share"
  },
}
```

Filling in and receiving a PDF binary may take a few hundred milliseconds. If you want a faster response from the PDF Otter API, you can fill in a PDF asynchronously. Your PDF template is filled in the same way, except the processing is performed in the background and the result is saved by default. You'll get back a JSON representation of your PDF File.

### HTTP Request

`POST https://www.pdfotter.com/api/v1/pdf_files`

### Parameters

Parameter | Type |Description
--------- | ---- | -----------
pdf_template | string | A template ID (required)
name | string | The file's name (recommended)
is_public | boolean | Whether the file can be viewed using a share URL (default: `true`)
data | object | Data to fill in the template with. If no data is provided, the generated PDF will just be the template.

<aside class="notice">
  Despite being created asynchronously, you can still share these PDF files with your users as long as they remain public. This is the purpose of the share URL, which displays the loading status or the PDF once it has been generated. A download url won't be available immediately after creating or updating a file because the file won't be ready for download. The share and download URLs won't be provided if is_public is false.
</aside>

## Get PDF File

```shell
curl https://www.pdfotter.com/api/v1/pdf_files/fil_b2c \
  -u test_apikey123:
```

> The above command returns JSON structured like this:

```json
{
  "id": "fil_b2c",
  "pdf_template": "tem_1c3",
  "is_public": true,
  "name": "Mariusz Lapinski's Subscription Agreement - Pending",
  "status": "completed",
  "version": 1,
  "urls": {
    "download": "https://www.pdfotter.com/fil_b2c/download?v=1",
    "share":    "https://www.pdfotter.com/fil_b2c/share"
  },
}
```

This endpoint retrieves a single PDF File’s details.

### HTTP Request

`GET https://www.pdfotter.com/api/v1/pdf_files/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the file to retrieve (required)

<aside class="notice">
  The download URL is only available if the file is public and its status is <code>completed</code>. Possible statuses are <code>pending</code>, <code>processing</code>, <code>completed</code>, and <code>failed</code>.
</aside>

## Download PDF File

```shell
curl -L https://www.pdfotter.com/api/v1/pdf_files/fil_b2c/download \
  -u test_apikey123: \
  > fil_b2c_filled_in.pdf
```

> The above command saves the returned binary PDF as `fil_b2c_filled_in.pdf`


This endpoint is for downloading the filled in PDF file. If the file has not finished processing, a 400 error will be returned.

### HTTP Request

`GET https://www.pdfotter.com/api/v1/pdf_files/<ID>/download`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the file to download (required)

<aside class="notice">
Make sure that the HTTP client you use to make this request follows redirects (like the -L flag in curl). This endpoint redirects the request to a signed S3 URL, which expires after 60 seconds.
</aside>

## Update PDF File

```shell
curl -X PUT https://www.pdfotter.com/api/v1/pdf_files/fil_b2c \
  -u test_apikey123: \
  -d name="Mariusz Lapinski's Subscription Agreement - Complete" \
  -d data[user_name]="Mariusz Lapinski" \
  -d data[user_signature]="Mariusz Lapinski" \
  -d data[user_signed_at]="March 31, 2017" \
  -d data[admin_name]="Harry Otter" \
  -d data[admin_signature]="Harry Otter" \
  -d data[admin_signed_at]="April 1, 2017"
```

> The above command returns JSON structured like this:

```json
{
  "id": "fil_b2c",
  "pdf_template": "tem_1c3",
  "is_public": true,
  "name": "Mariusz Lapinski's Subscription Agreement - Complete",
  "status": "pending",
  "version": 2,
  "urls": {
    "download": null,
    "share":    "https://www.pdfotter.com/fil_b2c/share"
  },
}
```

This endpoint updates a PDF File and generates a new PDF with the data provided. Data from the previous version won't be carried over to the new one, so you need to provide data for every field even if the field values haven't changed.

### HTTP Request

`PUT https://www.pdfotter.com/api/v1/pdf_files/<ID>`

### Parameters

Parameter | Type |Description
--------- | ---- | -----------
ID | string | A file ID (required)
name | string | Updates the name given to this file (optional)
is_public | boolean | Whether the file can be viewed using a share URL
data | object | Data to fill in your template with. If no data is provided, an empty template will be created.

<aside class="notice">
  Updating a file increments the version number.
</aside>

## Delete PDF File

```shell
curl -X DELETE https://www.pdfotter.com/api/v1/pdf_files/fil_1c3 \
  -u test_apikey123:
```

> The command above returns no content

```json
```

This endpoint deletes the specified file. If successful, a 204 (no content) response code is returned.

### HTTP Request

`DELETE https://www.pdfotter.com/api/v1/pdf_files/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | ID of the file to delete (required)

## Get All PDF Files

```shell
curl -G https://www.pdfotter.com/api/v1/pdf_files \
  -u test_apikey123: \
  -d pdf_template=tem_1c3
```

> The command above returns JSON structured like this:

```json
[
  {
    "id": "fil_1c3",
    "pdf_template": "tem_1c3",
    "is_public": true,
    "name": "Jane Doe's Subscription Agreement",
    "status": "completed",
    "version": 1,
    "urls": {
      "download": "https://www.pdfotter.com/fil_1c3/download?v=1",
      "share": "https://www.pdfotter.com/fil_1c3/share"
    }
  },
  {
    "id": "fil_3f6",
    "pdf_template": "tem_1c3",
    "is_public": false,
    "name": "Secret User's Subscription Agreement",
    "status": "completed",
    "version": 1,
    "urls": {
      "download": null,
      "share": null
    }
  },
  {
    "id": "fil_a4c",
    "pdf_template": "tem_1c3",
    "is_public": true,
    "name": "John Doe's Subscription Agreement",
    "status": "processing",
    "version": 2,
    "urls": {
      "download": null,
      "share": "https://www.pdfotter.com/fil_a4c/share"
    }
  }
]
```

This endpoint retrieves all files for a given template (represented as JSON, not the PDF documents). If no template ID is provided, an error will be returned.

### HTTP Request

`GET https://www.pdfotter.com/api/v1/pdf_files`

### Query Parameters

Parameter | Description
--------- | -----------
pdf_template | A template ID (required)
