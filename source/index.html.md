---
title: AirMason v1.0.0
language_tabs:
    - shell: Shell
    - javascript: NodeJS
includes: []
search: true
highlight_theme: darkula
---

# API Reference

Welcome to AirMason's API! You can use this documentation to access all our third party API endpoints, such as importing employees.

The API is organized around REST. All requests should be made over SSL. All request and response bodies, including errors, are encoded in JSON.

We also have some specific language bindings to make integration easier. You can switch the programming language of the examples with the tabs in the top right.

Currently we support the following official client bindings:

-   Shell
-   NodeJS

<!-- To play around with a few examples, we recommend a REST client called Postman. Simply tap the button below to import a pre-made collection of examples. -->

# Authentication

> Example request.

```shell
  curl --location --request PUT 'https://dev-api.airmason.com/v1/employees/import' \
    --header 'Authorization: Apikey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
```

```javascript
  const request = require("request");
  <!-- prettier-ignore -->
  request({
    method: "PUT",
    url: "https://dev-api.airmason.com/v1/employees/import",
    headers: {
      Authorization: "Apikey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    }
  }, (error, response) => {});
```

Authentication is done via the API key over HTTPS. Unauthenticated requests will return an HTTP 401 response.

[Reach out to us](https://www.airmason.com/contact) for obtaining a API key

# API Response

Our API returns standard HTTP success or error status codes. For errors, we will also include extra information about what went wrong encoded in the response as JSON. The various HTTP status codes we might return are listed below.

| Code | Title                 | Description                             |
| ---- | --------------------- | --------------------------------------- |
| 200  | OK                    | The request was successful              |
| 400  | Bad request           | Bad request                             |
| 401  | Unauthorized          | Your API key is invalid or not provided |
| 404  | Not found             | The resource does not exist             |
| 422  | Validation error      | A validation error occurred             |
| 50X  | Internal Server Error | An error occurred with our API.         |

## Error Response

> Example error response.

```shell
  {
    "errors": [
      {
        "msg": "Invalid value",
        "param": "isPatch",
        "location": "body"
      },
      {
        "msg": "Invalid value",
        "param": "organizationId",
        "location": "body"
      }
    ]
  }
```

```javascript
  {
    "errors": [
      {
        "msg": "Invalid value",
        "param": "isPatch",
        "location": "body"
      },
      {
        "msg": "Invalid value",
        "param": "organizationId",
        "location": "body"
      }
    ]
  }
```

In case API encounter an error, response JSON will return with `errors` key (of type `array`). Various error params that API might return are mentioned below:

| Parameter | Required | Description                      |
| --------- | -------- | -------------------------------- |
| msg       | true     | Type of error                    |
| param     | false    | Parameter name                   |
| location  | false    | body \| query \| param \| header |

## Success Response

> Example error response.

```shell
  {
    "data": {
      ...
    }
  }
```

```javascript
  {
    "data": {
      ...
    }
  }
```

In case of success, response JSON will return with `data` key (of type `object`).

# Import Employees API

This API lets you import employees into your organization.

## Request

Import employees in organization

### URL

`PUT https://dev-api.airmason.com/v1/employees/import`

### Params

```shell
  curl --location --request PUT 'https://dev-api.airmason.com/v1/employees/import' \
    --header 'Authorization: Apikey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' \
    --form 'file=@/filepath/filename.csv' \
    --form 'isPatch=0'
```

```javascript
  const request = require('request');
  const fs = require('fs');
  <!-- prettier-ignore -->
  request({
    'method': 'PUT',
    'url': 'https://dev-api.airmason.com/v1/employees/import',
    'headers': {
      'Authorization': 'Apikey xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'
    },
    formData: {
      'file': {
        'value': fs.createReadStream('filepath/filename.csv'),
        'options': {
          'filename': 'filename.csv',
          'contentType': null
        }
      },
      'isPatch': '0'
    }
  }, (error, response) => {});
```

| Parameter     | Type   | In     | Required | Description                                                                                                                      |
| ------------- | ------ | ------ | -------- | -------------------------------------------------------------------------------------------------------------------------------- |
| Authorization | string | header | true     | Generated api key                                                                                                                |
| file          | file   | body   | true     | CSV of employee list <br/>[Download sample csv](/csv/employee-import-sample-csv.csv) <br/> CSV file params are described below   |
| isPatch       | number | body   | true     | **0** or **1**. <br/> **0** - Consider it as complete list of employees. <br/> **1** - Add these employees to existing employees |

### CSV file params

| Parameter   | Type       | Required | Description             |
| ----------- | ---------- | -------- | ----------------------- |
| employee_id | string     | true     | Employee id             |
| email       | string     | false    | Employee email address  |
| first_name  | string     | true     | Employee first name     |
| last_name   | string     | false    | Employee last name      |
| dob         | dd/mm/yyyy | false    | Employee date of birth  |
| team_name   | string     | false    | Employee team name      |
| location    | string     | false    | Employee location       |
| state       | string     | false    | Employee location state |
| source_id   | string     | false    | Employee source id      |

## Response

> Example response.

```shell
  {
    "data": {
      "total": 5,
      "imported": 3,
      "errorInRows": [
        {
          "rowNumber": 3,
          "fieldName": "first_name"
        },
        {
          "rowNumber": 6,
          "fieldName": "employee_id"
        }
      ]
    }
  }
```

```javascript
  {
    "data": {
      "total": 5,
      "imported": 3,
      "errorInRows": [
        {
          "rowNumber": 3,
          "fieldName": "first_name"
        },
        {
          "rowNumber": 6,
          "fieldName": "employee_id"
        }
      ]
    }
  }
```

### Response params

| Parameter             | Required | Type   | Description                                    |
| --------------------- | -------- | ------ | ---------------------------------------------- |
| total                 | true     | number | Total number of employees processed            |
| imported              | true     | number | Number of employees imported (created/updated) |
| errorInRows           | true     | array  | List of validation issues                      |
| errorInRows.rowNumber | true     | number | CSV row number that has validation issue       |
| errorInRows.fieldName | true     | string | CSV field name that has validation issue       |
