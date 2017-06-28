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
  -H "Authorization: Bearer test_yd61q"
```

Authentication to the API is performed via Bearer Auth. Include your API key in the `Authorization` header value. You can find your API Keys on your [account page](https://www.pdfotter.com/account#api-keys).

# PDF Templates

## Get a PDF Template

```shell
curl https://www.pdfotter.com/api/v1/pdf_templates/tem_1c3 \
  -H "Authorization: Bearer test_yd61q"
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
  -H "Authorization: Bearer test_yd61q"
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

## Create a PDF File

```shell
curl https://www.pdfotter.com/api/v1/pdf_files \
  -H "Authorization: Bearer test_yd61q" \
  -d pdf_template=tem_1c3 \
  -d name="Mariusz Lapinski's Subscription Agreement - Pending" \
  -d data[user_name]="Mariusz Lapinski" \
  -d data[user_signature]="Mariusz Lapinski" \
  -d data[user_signed_at]="March 31, 2017"
```

> The above command returns JSON structured like this:

```json
{
  "id": "fil_b2c",
  "name": "Mariusz Lapinski's Subscription Agreement - Pending",
  "status": "pending",
  "version": 1,
  "urls": {
    "download": null,
    "share":    "https://www.pdfotter.com/fil_b2c/share"
  },
}
```

This endpoint creates a PDF File - fills in a PDF Template - with the data provided.

### HTTP Request

`POST https://www.pdfotter.com/api/v1/pdf_files`

### Parameters

Parameter | Description
--------- | -----------
pdf_template | A template ID (required)
name | The file's name (recommended)
data | Data to fill in the template with. If no data is provided, the generated PDF will just be the template.

<aside class="notice">
  A download url won't be available immediately after creating or updating a file. This is because the file's PDF is generated in a background job. A share URL will always be provided. The share URL displays the loading status or the PDF as soon as it has been generated. Share this URL with your users or embed it in an iframe.
</aside>

## Get a PDF File

```shell
curl https://www.pdfotter.com/api/v1/pdf_files/fil_b2c \
  -H "Authorization: Bearer test_yd61q"
```

> The above command returns JSON structured like this:

```json
{
  "id": "fil_b2c",
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
  The download URL is only available if the file's status is <code>completed</code>. Possible statuses are <code>pending</code>, <code>processing</code>, <code>completed</code>, and <code>failed</code>.
</aside>

## Update a PDF File

```shell
curl -X PUT https://www.pdfotter.com/api/v1/pdf_files/fil_b2c \
  -H "Authorization: Bearer test_yd61q" \
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

Parameter | Description
--------- | -----------
ID | A file ID (required)
name | Updates the name given to this file (optional)
data | Data to fill in your template with. If no data is provided, an empty template will be created.

<aside class="notice">
  Updating a file increments the version number.
</aside>

## Get All PDF Files

```shell
curl -G https://www.pdfotter.com/api/v1/pdf_files \
  -H "Authorization: Bearer test_yd61q" \
  -d pdf_template=tem_1c3
```

> The command above returns JSON structured like this:

```json
[
  {
    "id": "fil_1c3",
    "name": "Jane Doe's Subscription Agreement",
    "status": "completed",
    "version": 1,
    "urls": {
      "download": "https://www.pdfotter.com/fil_1c3/download?v=1",
      "share": "https://www.pdfotter.com/fil_1c3/share"
    }
  },
  {
    "id": "fil_a4c",
    "name": "John Doe's Subscription Agreement",
    "status": "processing",
    "version": 2,
    "urls": {
      "download": null,
      "share": "https://www.pdfotter.com/fil_a4c?v=2"
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
