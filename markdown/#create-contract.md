# Create a contract

Creating a contract is the first step to hiring an independent contractor through Deel. There are many contract types available:

- [Fixed rate](#create-a-fixed-rate-contract)
- Hourly rate
- Fixed price

> 👍 
>
> All the contracts are created using the [Create contract](https://developer.deel.com/reference/createcontract) endpoint. The difference between them is in the parameters that you will pass in the request payload.

## Before you begin

There's a few pieces of information that will be needed as you progress through this guide. Retrieving them beforehand will help you save when you need to prepare the payload.

Before preparing the payload, make sure to retrieve:

- A valid [token to authenticate your requests](https://developer.deel.com/docs/api-tokens-1)
- The Legal entity ID from the [Get list of legal entities](https://developer.deel.com/reference/getlegalentitylist) endpoint.
- The group ID from the [Get team list](https://developer.deel.com/reference/getgrouplist).

> 👍 Other endpoints will be used to retrieve additional information needed to create a contract
> 
> We recommend becoming familiar with them:
> - [GET job title list](https://developer.deel.com/reference/getjobtitlelist)
> - [GET seniority levels](https://developer.deel.com/reference/getsenioritylist)

### Request payload structure

Contracts are created by making a request to the [POST Create contract](https://developer.deel.com/reference/createcontract) endpoint.

The request payload will include the contract details in the `data` object. We'll look at each object of the payload in the following sections of the article, and look at a full sample request towards the end.

```curl Request payload
curl --request POST \
     --url 'https://api.letsdeel.com/rest/v2/contracts' \
     --header 'authorization: Bearer TOKEN' \
     --header 'content-type: application/json' \
     --data-raw '
{
  "data": {
    …
  }
}'
```

Where:

- `TOKEN` is the token to authorize the API call

## Create a fixed-rate contract

The "Fixed rate" contract type users see in the UI corresponds to the `ongoing_time_based` type in the API.

### 1. Fill in the generic details

Start filling the `data` object with the contract details located the top level of the payload.

```json
{
  "data": {
    "country_code": "US",
    "external_id": "11001100110",
    "notice_period": 10,
    "scope_of_work": "Lorem ipsum dolor sit amet.",
    "special_clause": "Lorem ipsum dolor sit amet.",
    "start_date": "2024-08-01",
    "state_code": "IL",
    "termination_date": "2024-08-31",
    "title": "Contract",
    "type": "ongoing_time_based",
    "who_reports": "client"
    …
  }
}
```

Where:

| Name | Required | Type | Format | Description | Example |
|-|-|-|-|-|-|
| `start_date` | true | string | date | The start date of the contract. Use the ISO-8601 short date format YYYY-MM-DD. | `2024-08-01` |
| `title` | true | string | - | The name of the contract. | `Contract` |
| `type` | true | string | enum | Determines the fixed rate contract type. For a fixed rate contract, the type must be set to `ongoing_time_based`. | `ongoing_time_based` |
| `country_code` | false | string | - | The code of the country of the contractor's tax residence. Leave it blank to use the country indicated by the contractor when they create their profile. Use the ISO 3166-1 alpha-2 code in capital letters for the residence of the contractor. A list of the available country codes can be found on our [Help Center](https://help.letsdeel.com/hc/en-gb/articles/15021126310161-How-To-Troubleshoot-Issues-When-Mass-Importing-Employees#h_01GZJS969FZY3K2K4V8YGRNEGJ). | `US` |
| `external_id` | false | string | - | An external ID associated with the contract. | `11001100110` |
| `notice_period` | false | number | - | The number of days of notice required to terminate the contract. | `10` |
| `scope_of_work` | false | string | - | Fill it with a job description or a summary of the roles and responsibilities of the worker. | `Lorem ipsum dolor sit amet.` |
| `special_clause` | false | string | - | Fill it with any special clauses to be included in the contract. | `Lorem ipsum dolor sit amet.` |
| `state_code` | false | string | - | If the `country_code` parameter is set to `US`, indicates the state code of the contract. | `IL` |
| `termination_date` | false | string | date | The termination date of the contract. Use the ISO-8601 short date format YYYY-MM-DD. | `2024-08-31` |
| `who_reports` | false | string | enum | Specifies whether the client, the worker, or both will be providing regular reports during the working relationship. | `client` |

### 2. Fill in the client information

Each contract must be associated with a legal entity and a group, that are client-specific information. The `client` object allows to associate the contract being created to existing legal entities and groups using their IDs. You can retrieve the IDs from the following endpoints:

- For the legal entity ID, use the [Get list of legal entities](https://developer.deel.com/reference/getlegalentitylist) endpoint
- For the group ID, use the [Get team list](https://developer.deel.com/reference/getgrouplist) endpoint

> 👍
> 
> New legal entities and groups can be created using the [POST create legal entity](https://developer.deel.com/reference/createlegalentity) and the [POST create group](https://developer.deel.com/reference/creategroup) endpoints respectively.

```json
{
  "data": {
    …,
    "client": {
      "legal_entity": {
        "id": "00000000-0000-0000-0000-000000000000"
      },
      "team": {
        "id": "00000000-0000-0000-0000-000000000000"
      }
    }
  }
}
```

Where:

| Name | Required | Type | Format | Description | Example |
|-|-|-|-|-|-|
| `client.legal_entity.id` | true | string | UUID | The ID of the legal entity. | `d3m0d3m0-d3m0-d3m0-d3m0-d3m0d3m0d3m0` |
| `client.team.id` | true | string | UUID | The ID of the group. | `d3m0d3m0-d3m0-d3m0-d3m0-d3m0d3m0d3m0` |


### 3. Fill in the worker's personal information

The `worker` object contains the worker's personal information. The email must be unique in your organization. If the email already exists, the contract will be connected to the worker's existing profile.

> 👍 Use the worker's personal email
> 
> This way they can access their account and contract even after the contract has ended.

```json
{
  "data": {
    …,
    "worker": {
      "expected_email": "demo@email.com",
      "first_name": "John",
      "last_name": "Doe"
    }
  }
}
```

Where:

| Name | Required | Type | Format | Description | Example |
|-|-|-|-|-|-|
| `worker.expected_email` | true | string | email | The email of the contractor | `demo@email.com` |
| `worker.first_name` | true | string | - | The first name of the contractor | `John` |
| `worker.last_name` | true | string | - | The last name of the contractor | `Doe` |

### 4. Fill in the job title and seniority

Optionally, a job title and a seniority level can be associated to the contract using the `job_title` and `seniority` objects. You can retrieve the job title and seniority level IDs from the following endpoints:

- For the job title ID, use the [GET job title list](https://developer.deel.com/reference/getjobtitlelist) endpoint
- For the seniority ID, use the [GET seniority levels](https://developer.deel.com/reference/getsenioritylistt) endpoint

> 👍 Creating new job titles
> 
> New job titles can be created by replacing the `job_title.id` with `job_title.name`, containing the name of the new job title. For example:
> 
> ```
>     "job_title": {
>       "name": "Director"
>     },
> ```
> 
>

```json
{
  "data": {
    …,
    "job_title": {
      "id": "12345"
    },
    "seniority": {
      "id": "12345"
    }
  }
}
```

Where:

| Name | Required | Type | Format | Description | Example |
|-|-|-|-|-|-|
| `job_title.id` | true | number | ID | The ID of the job title | `12345` |
| `seniority.id` | false | number | ID | The ID of the seniority level | `12345` |

### 5. Fill in the meta information

The `meta` object allows to adjust additional settings of the contract, such as requiring the contractor to provide additional compliance documents as per their country's local laws.

> 👍
> 
> We recommend requesting these documents to meet compliance obligations.

```json
{
  "data": {
    …,
    "meta": {
      "documents_required": true
    }
  }
}
```

Where:

| Name | Required | Type | Format | Description | Example |
|-|-|-|-|
| `documents_required` | true | boolean | - | Determines whether contractors must provide [additional compliance documents](https://help.letsdeel.com/hc/en-gb/articles/4407745411217-What-compliance-documents-will-contractors-need-to-upload). We recommend requesting these documents to meet compliance obligations. | true |

### 6. Fill in the compensation details

How much the contractor will be compensated and when can be defined in the `compensation_details` object.

```json
{
  "data": {
    …,
    "compensation_details": {
      "amount": 5000,
      "currency_code": "USD",
      "cycle_end": 30,
      "cycle_end_type": "DAY_OF_MONTH",
      "first_payment": 1500,
      "first_payment_date": "2024-09-05",
      "frequency": "weekly",
      "notice_period": 0,
      "pay_before_weekends": true,
      "payment_due_days": 5,
      "payment_due_type": "REGULAR",
      "scale": "monthly"
    }
  }
}
```

Where:

| Name | Required | Type | Format | Description | Example |
|-|-|-|-|-|-|
| `compensation_details.amount` | true | number | - | The amount of the compensation to be paid to the contractor. How frequently the compensation is determined by the `frequency` parameter. | 5000 |
| `compensation_details.currency_code` | true | string | - | Indicates the currency code of the compensation following the 3-digit ISO 4217 format. Visit the [Help center](https://help.letsdeel.com/hc/en-gb/articles/4407737591953-In-What-Currencies-Can-I-Create-A-Contractor-Contract) for a list of supported currencies. | `USD` |
| `compensation_details.cycle_end` | true | number | - | Indicates the day when the invoicing cycle ends, based on what's set in the `cycle_end_type` parameter. For example, use 7 for a Sunday or use 30 for the 30th of the month. If the month is shorter than the number indicated, the cycle ends on the last day of the month. | 30 |
| `compensation_details.cycle_end_type` | true | string | enum | Together with the `cycle_end` parameter, determines when the compensation cycle ends. Available values are:<br/>- `DAY_OF_MONTH` for the calendar day of the month<br/>- `DAY_OF_WEEK` for the week day in weekly payments<br/>- `DAY_OF_LAST_WEEK` for the last week day of the month | `DAY_OF_MONTH` |
| `compensation_details.frequency` | true | string | enum | Determines how often the compensation is paid to the contractor. The available values are `monthly`, `weekly`, `biweekly`, `semimonthly`, and `calendar_month`. | `weekly` |
| `compensation_details.first_payment` | false | number | - | Determines the amount of the first payment, based on the invoicing cycle. | `1000` |
| `compensation_details.first_payment_date` | false | string | date | Can be used to force the date of the first payment when the date falls outside of the invoicing cycle. Use the ISO-8601 short date format YYYY-MM-DD. | `2024-09-05` |
| `compensation_details.notice_period` | false | number | - | Determines the days of notice that each party must give to end the contract. | `15` |
| `compensation_details.pay_before_weekends` | false | boolean | - | When set to `true`, if the pay day falls on a weekend, the compensation payment will be anticipated to the last working day of the week. | `true` |
| `compensation_details.payment_due_days` | false | number | - | When the `payment_due_type` parameter is set to `REGULAR`, this parameter determines the offset of days for the payment to be due. For example, set it to 5 if you want the payment to be due 5 days after the last day of the invoicing cycle. | `0` |
| `compensation_details.payment_due_type` | false | string | enum | Determines when the payment is due. If you select `REGULAR`, the payment is determined by the `payment_due_days` parameter. If you select `WITHIN_MONTH` the payment is due on the last day of the invoicing cycle. | `WITHIN_MONTH` |
| `compensation_details.scale` | false | string | enum | Defines the amount scale. For example, 'monthly' means amount per month. For fixed rate contract types, the scale can only be `monthly`. | `monthly` |

### 7. Make the API request

Now that all the contract information is set, it's time to make the API request. Here's how the request should look like.

A successful response will return a `200` status code and include the ID of the created contract and all of its information in the payload. Note down the id parameter at the top of the response, it will be needed for the next step: signing the contract.

```curl Request payload
curl --request POST \
     --url 'https://api.letsdeel.com/rest/v2/contracts' \
     --header 'authorization: Bearer TOKEN' \
     --header 'content-type: application/json' \
     --data-raw '
{
  "data": {
    "country_code": "US",
    "external_id": "11001100110",
    "notice_period": 10,
    "scope_of_work": "Lorem ipsum dolor sit amet.",
    "special_clause": "Lorem ipsum dolor sit amet.",
    "start_date": "2024-08-01",
    "state_code": "IL",
    "termination_date": "2024-08-31",
    "title": "Contract",
    "type": "ongoing_time_based",
    "who_reports": "client",
    "client": {
      "legal_entity": {
        "id": "00000000-0000-0000-0000-000000000000"
      },
      "team": {
        "id": "00000000-0000-0000-0000-000000000000"
      }
    },
    "worker": {
      "expected_email": "demo@email.com",
      "first_name": "John",
      "last_name": "Doe"
    },
    "job_title": {
      "id": "12345"
    },
    "seniority": {
      "id": "12345"
    },
    "meta": {
      "documents_required": true
    },
    "compensation_details": {
      "amount": 5000,
      "currency_code": "USD",
      "cycle_end": 30,
      "cycle_end_type": "DAY_OF_MONTH",
      "first_payment": 1500,
      "first_payment_date": "2024-09-05",
      "frequency": "weekly",
      "notice_period": 0,
      "pay_before_weekends": true,
      "payment_due_days": 5,
      "payment_due_type": "REGULAR",
      "scale": "hourly"
    }    
  }
}'
```
```json Response payload
{
  "data": {
      "id": "dd333m00",
      "title": "Contract",
      "type": "ongoing_time_based",
      "status": "waiting_for_client_sign",
      "created_at": "2024-07-30T10:39:30.241Z",
      "client": {
          "team": {
              "id": 684012,
              "public_id": "a3722444-c0c7-468a-9635-04971195d8ed",
              "name": "Wayne Enterprise Global"
          },
          "legal_entity": {
              "id": 551835,
              "name": "Wayne Enterprise Global",
              "email": "demo@company.com",
              "type": "company",
              "subtype": "general-partnership",
              "registration_number": "987654321",
              "vat_number": "123456789"
          }
      },
      "worker": {
          "expected_email": "demo@email.com",
          "first_name": "John",
          "last_name": "Doe"
      },
      "invitations": {
          "client_email": "",
          "worker_email": ""
      },
      "signatures": {
          "client_signature": "",
          "client_signed_at": "",
          "worker_signature": "",
          "worker_signed_at": "",
          "signed_at": ""
      },
      "compensation_details": {
          "currency_code": "USD",
          "amount": "5000",
          "scale": "monthly",
          "frequency": "monthly",
          "first_payment": "",
          "first_payment_date": "2024-09-04T18:29:59.999Z",
          "gross_annual_salary": "",
          "gross_signing_bonus": "",
          "gross_variable_bonus": ""
      },
      "contract_template": null,
      "employment_details": {
          "type": "ongoing_time_based",
          "days_per_week": 0,
          "hours_per_day": 0,
          "probation_period": 0,
          "paid_vacation_days": 0
      },
      "job_title": "Academic Advisor",
      "scope_of_work": "Lorem ipsum dolor sit amet, consectetur adipiscing elit.Lorem ipsum dolor sit amet, consectetur adipiscing elit.",
      "seniority": {
          "id": 3,
          "name": "Senior (Individual Contributor Level 3)",
          "level": 3
      },
      "special_clause": "Lorem ipsum dolor sit amet, consectetur adipiscing elit.Lorem ipsum dolor sit amet, consectetur adipiscing elit.",
      "start_date": "2024-07-31T18:30:00.000Z",
      "termination_date": "2024-08-31T18:29:59.999Z",
      "is_archived": false,
      "notice_period": 15,
      "custom_fields": [],
      "external_id": null
  }
}
```

<!-- ## What's next

Now that the contract has been created, it's time to sign it. To learn how, check the [Signing a contract](https://developer.deel.com/docs/signing-a-contract) guide. -->
