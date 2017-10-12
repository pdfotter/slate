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

## Create a PDF Template

```shell
curl https://www.pdfotter.com/api/v1/pdf_templates \
  -u test_apikey123: \
  -F file=@/path/to/template.pdf \
  -F name="Subscription Agreement"
```

> The command above returns JSON structured like this:

```json
{
  "id": "tem_1c3",
  "name": "Subscription Agreement",
  "processing_status": "pending",
  "processing_progress_percentage": 0,
  "fields": []
}
```

This endpoint creates a PDF Template from the file in the request. The PDF template needs to be processed before it is filled in or editted. The `processing_status` and `processing_progress_percentage` attributes in the response give you an idea of the progress. Processing times depend on the size of the PDF template's file, but it usually takes a few seconds. During processing, new fields may be detected and added to the template.

### HTTP Request

`POST https://www.pdfotter.com/api/v1/pdf_templates`

### Parameters

Parameter | Type |Description
--------- | ---- | -----------
file | file | The PDF file to create a template from (required)
name | string | The template's name (not required)


## Get a PDF Template

```shell
curl https://www.pdfotter.com/api/v1/pdf_templates/tem_1c3 \
  -u test_apikey123:
```

> The command above returns JSON structured like this:

```json
{
  "id": "tem_1c3",
  "name": "Subscription Agreement",
  "processing_status": "completed",
  "processing_progress_percentage": 100,
  "fields": [
    {"page_number": 1, "name": "user_name"},
    {"page_number": 2, "name": "user_signature"},
    {"page_number": 2, "name": "user_signed_at"}
  ]
}
```

This endpoint retrieves a PDF Template's details. For now, templates can only be created and configured through the [dashboard](https://www.pdfotter.com/pdf_templates). Template IDs can be found there as well.

### HTTP Request

`GET https://www.pdfotter.com/api/v1/pdf_templates/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | The ID of the template to retrieve (required)

## Fill In a PDF Template

```shell
curl -X POST https://www.pdfotter.com/api/v1/pdf_templates/tem_1c3/fill \
  -u test_apikey123: \
  -d data[user_name]="Mariusz Lapinski" \
  -d data[user_signature]="Mariusz Lapinski" \
  -d data[user_signed_at]="March 31, 2017" \
  > result.pdf
```

> The above command saves a PDF document to `result.pdf`.

This endpoint fills in a PDF Template with the data provided. It responds with the binary PDF file so you can store it on your end. PDF Otter won't store any of the data you pass it or the resulting PDF.

### HTTP Request

`POST https://www.pdfotter.com/api/v1/pdf_templates/<ID>/fill`

### Parameters

Parameter | Type |Description
--------- | ---- | -----------
data | object | Data to fill in the template with. If no data is provided, the generated PDF will be the template with empty fields.

## Update a PDF Template

```shell
curl -X PUT https://www.pdfotter.com/api/v1/pdf_templates/tem_1c3 \
  -u test_apikey123: \
  -d name="Old Subscription Agreement"
```

> The above command returns JSON structured like this:

```json
{
  "id": "tem_1c3",
  "name": "Old Subscription Agreement",
  "processing_status": "completed",
  "processing_progress_percentage": 100,
  "fields": [
    {"page_number": 1, "name": "user_name"},
    {"page_number": 2, "name": "user_signature"},
    {"page_number": 2, "name": "user_signed_at"}
  ]
}
```

This endpoint updates a PDF Template.

### HTTP Request

`PUT https://www.pdfotter.com/api/v1/pdf_files/<ID>`

### Parameters

Parameter | Type |Description
--------- | ---- | -----------
name | string | PDF Template's name

## Delete a PDF Template

```shell
curl -X DELETE https://www.pdfotter.com/api/v1/pdf_templates/tem_1c3 \
  -u test_apikey123:
```

> The command above returns no content

```json
```

This endpoint deletes the specified PDF template. If successful, a 204 (no content) response code is returned.

### HTTP Request

`DELETE https://www.pdfotter.com/api/v1/pdf_templates/<ID>`

### URL Parameters

Parameter | Description
--------- | -----------
ID | ID of the file to delete (required)

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

