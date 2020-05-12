# Azure API Management Monetization

One of the advantages of exposing APIs through an API Management solution is the ability to monetize the API consumption. This is the case for Azure's API Management. There's no built-in solution to monetize, the process is described in the following document utilizing a billing platform such as stripe:

- https://azure.microsoft.com/en-us/blog/how-to-monetize-apis-with-azure-api-management/

> Note: the article includes sample code that interface with a stripe to issue invoices and process payments. The code offers great ideas as to how to develop your own invoicing solution.

## Objective

This document describes how to monetize the APIs using the API Management where you may want to develop your own invoicing system.

## Important Concepts

### Developer Subscription Key

This is a code issued to a developer that will allow API Managment to no only grant access to the API but to track the number of times the API has been called by that developer. This is the key to monetization.

### Metered, Consumption, and Combination billing

- Metered billing: charge per call. Example, charge $100 for 1000 calls for a standard plan, $200 for 10,000 for the premium plan, and $500 for unlimited.
  - > Note: it may be a good idea to set a rate limit
- Consumption: charge for a total number of call per a given period. For example, charge $.01 for eavery call.
- Combination: charge for a total number of calls per period and then charge per call. For example, charge $100 for 1000 calls and $.01 for any call after that amount.

### Products

In Azure API Management, product is defined as a collection of APIs. Products have Access Groups defined as administrators, developers and guests. Users are assigned to Access Groups. Those users who are assigned to Access Groups will have access to those products that have the Access Group defined. Products need to be published.

### Product Policies

Product can include policies that can limit the numer of request per minute and the total of call per month. For example, the policies below limit the number of calls to 5 per minute and a total of 100 per month.

```xml
<policies>
    <inbound>
        <rate-limit calls="5" renewal-period="60" />
        <quota calls="100" renewal-period="604800" />
        <base />
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

### Groups

A group (or Access Group) is a collection of users.

### Users

A user may be assigned a group and products. For each product, the user will receive a primary and secondary subscription keys.

## Calling an API Management exposed API with a developer subscription key

- Set a header with the following key value:
  - ```Ocp-Apim-Subscription-key:key```

## Consumption Reporting

- Set a header for the API shared access key or get a bearer token
  - ```Authorization:key```

## API Management Rest API

API Magements comes with a complete set of REST APIs for management and configuration

### Reports APIs

The report APIs allow extraction of statistics by for example API and subscription among others.

> Note: More documentation can be found here https://docs.microsoft.com/en-us/rest/api/apimanagement/2019-12-01/reports

### Calls by subscription

Call:

```curl
https://apim.management.azure-api.net/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/Microsoft.ApiManagement/service/{serviceName}/reports/bySubscription?$filter=timestamp ge datetime'2020-05-01T00:00:00' and timestamp le datetime'2020-06-01T00:00:00'&api-version=2019-12-01
```

With the following header key value:

```Authorization:SAS TOKEN```

Result:

```json
{
    "value": [
        {
            "name": "",
            "userId": "/users/1",
            "productId": "/products/starter",
            "subscriptionId": "/subscriptions/5eb848bb082b16004d070001",
            "callCountSuccess": 0,
            "callCountBlocked": 0,
            "callCountFailed": 0,
            "callCountOther": 0,
            "callCountTotal": 0,
            "bandwidth": 0,
            "cacheHitCount": 0,
            "cacheMissCount": 0,
            "apiTimeAvg": 0.0,
            "apiTimeMin": 0.0,
            "apiTimeMax": 0.0,
            "serviceTimeAvg": 0.0,
            "serviceTimeMin": 0.0,
            "serviceTimeMax": 0.0
        }]
}
```

## Questions

### Does APIM come with an out of the box invoicing solution.

No. This functionality has to be impleted.

### Does APIM maintain the list of prices

No. This has to be defined outside the application.

