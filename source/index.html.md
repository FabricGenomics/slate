---
title: API Reference

language_tabs:
  - python

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/tripit/slate'>Documentation Powered by Slate</a>

includes:
  - errors

search: true
---

# Introduction

Omicia provides an API based on the HTTPS protocol, using HTTP requests (GET,
POST, PUT and DELETE) to securely transmit variation
data, launch analyses and retrieve finished report data.

Most invalid requests (e.g. for invalid parameter values) return an HTTP status of
400, 404 or 422; authentication errors return an HTTP status of 401 or 403.

All dates and times are returned in PST.

# Authentication

> To authorize, use this code:

```python
import kittn

api = kittn.authorize('meowmeowmeow')
```

```shell
# With shell, you can just pass the correct header with each request
curl "api_endpoint_here"
  -H "Authorization: meowmeowmeow"
```

> Make sure to replace `meowmeowmeow` with your API key.

With the exception of the online documentation, every request must authenticate
against our database of API Keys, providing the login and password according
to the HTTP basic access authentication method (see `Wikipedia - basic access`_).
Please contact support_ to have them generate an API Key for your application.

An API Key is tied to a specific Opal user, referred to as
the API User hereon. Any email messages generated when using the Opal API are sent
to the API User.

Omicia stores passwords in a one-way encrypted hash according to industry
standard security procedures.  Passwords cannot be recovered,
but we can issue a new API key upon request.

The API uses SSL-encrypted HTTP for its protocol, via the normal port for
secure HTTP, 443. Attempts to query the server with non-encrypted HTTP
request on port 80 (ie. http://api.omicia.com instead of
https://api.omicia.com) will return a 302 Redirect HTTP response,
with no response body provided.


`Authorization: meowmeowmeow`

<aside class="notice">
You must replace <code>meowmeowmeow</code> with your personal API key.
</aside>

# Clinical Reports

## Get All Clinical Reports

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get()
```

> The above command returns JSON structured like this:

```json
[    {
        "objects": [
            {
                "id": 1202,
                "workspace_id": 71,
                "created_by": 110,
                "created_on": "2014-12-02T00:12:10",
                "panel_id": 1200,
                "genome_id": 119,
                "filter_id": 727,
                "status": "READY TO REVIEW",
                "test_type": "panel",
                "sample_collected_date": "2014-12-12T00:00:00",
                "sample_received_date": "2014-12-12T00:00:00",
                "accession_id": "AB12345",
                "report_approved_date": null,
                "variant_report_id": 186,
                "vaast_report_id": null,
                "include_cosmic": false,
                "version": 1,
                "variant_count": 13,
                "reviewed_variant_count": 0,
                "patient_dict": {
                    "first_name": "John",
                    "last_name": "Doe",
                    "dob_month": 7,
                    "sex": "Male",
                    "dob_year": 2014,
                    "indication_for_testing": "asthma",
                    "ordering_physician": "Jane Buck",
                    "specimen_type": "blood",
                    "ethnicity": "White",
                    "dob_day": 3
                }
            }
        ]
    }
```

List all Reports. This endpoint can also be used to find reports using
one of

- Accession ID: this is is the ID associated with the report at creation
- External ID: the external ID associated with a genome at upload
- Genome ID: the internal Opal ID given to the genome at upload

### HTTP Request

`GET https://api.omicia.com/reports`

### Query Parameters

Parameter | Required | Value | Limits
--------- | -------- | ----- | -----------
report_type     |  String      |      No    |     "Variant Report", "VAAST Trio Report"
report_status   |  String      |      No    |     "WAITING", "SUBMITTED", "RUNNING", "COMPLETE", "FAILED"
order_by        |  String      |      No    |     "run_date", "report_type", "report_status"
limit           |  Int         |      No    |     Positive
offset          |  Int         |      No    |     Positive
external_id     |  String      |      No    |     Alphanumeric string - substring match
accession_id    |  String      |      No    |     Alphanumeric string - substring match
genome_id       |  Int         |      No    |     A valid genome ID - exact match

<aside class="success">
Remember — a happy kitten is an authenticated kitten!
</aside>

## Get one Clinical Report

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

> The above command returns JSON structured like this:

```json
{
    "id": 1202,
    "workspace_id": 71,
    "type": "clinical_report",
    "created_by": 110,
    "created_on": "2014-12-02T00:12:10",
    "panel_id": 1200,
    "genome_id": 119,
    "filter_id": 727,
    "status": "READY TO REVIEW",
    "test_type": "panel",
    "sample_collected_date": "2014-12-12T00:00:00",
    "sample_received_date": "2014-12-12T00:00:00",
    "accession_id": "FLCMRCG479Y",
    "report_approved_date": null,
    "variant_report_id": 186,
    "vaast_report_id": null,
    "include_cosmic": false,
    "version": 1,
    "variant_count": 0,
    "reviewed_variant_count": 0,
    "filter_name": "None",
    "patient_dict": {
        "first_name": "John",
        "last_name": "Doe",
        "dob_month": 7,
        "sex": "Male",
        "dob_year": 2014,
        "indication_for_testing": "asthma",
        "ordering_physician": "Jane Buck",
        "specimen_type": "blood",
        "ethnicity": "White",
        "dob_day": 3
    }
}
```

This endpoint retrieves a specific clinical report.

In order to fetch an extended clinical report, which includes variant information and patient fields,
the parameter "extended" can be used with a value of "True."

<aside class="warning">Inside HTML code blocks like this one, you can't use Markdown, so use <code>&lt;code&gt;</code> blocks to denote code.</aside>

### HTTP Request

`GET https://api.omicia.com/reports/<id>`

### URL Parameters

Parameter | Value | Required | Limits
--------- | ----- | -------- | ------
extended  | String | No | "True"

## Get Final PDF Report

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

Fetch a PDF report for an approved clinical report.
Example usage: <a href="https://github.com/Omicia/omicia_api_examples/blob/master/python/ClinicalReportLaunchers/get_clinical_report_pdf.py">Get a clinical report final PDF</a>

<aside class="warning">This endpoint will return a response of status 422 if the requested report is not yet approved.</aside>

### HTTP Request

`GET https://api.omicia.com/reports/<id>/pdf_report`

## Get Preview PDF Report

```python
import kittn

api = kittn.authorize('meowmeowmeow')
api.kittens.get(2)
```

Fetch a preview PDF report for an unapproved clinical report.

Example usage: <a href="https://github.com/Omicia/omicia_api_examples/blob/master/python/ClinicalReportLaunchers/get_clinical_report_pdf.py">Get a clinical report final PDF</a>

<aside class="warning">This endpoint will return a response of status 422 if the requested report is already approved, or if it is still processing
or waiting for genomes.</aside>

### HTTP Request

`GET https://api.omicia.com/reports/<id>/pdf_preview`
