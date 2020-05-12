# Azure API Management Monetization

One of the advantages of exposing APIs through an API Management solution is the ability to monetize the API consumption. This is the case for Azure's API Management. There's no built-in solution to monetize, the process is described in the following document utilizing a billing platform such as stripe:

- https://azure.microsoft.com/es-mx/blog/how-to-monetize-apis-with-azure-api-management/

> Note: the article includes sample code

This document describes how to monetize the APIs using the API Management restful APIs to access the execution statistics.

## Basics Concepts

### Subscription

It is an code issued to a developer that will allow API Managment to no only grant access to the API but to track the number of times the API has been called by that developer. This is the key to monetization.

### Metered, Consumption, Combination billing

- Metered billing: charge per call. Example, charge $100 for 1000 calls for a standard plan, $200 for 10,000 for the premium plan, and $500 for unlimited.
  - > Note: it may be a good idea to set a rate limit
- Consumption: charge for a total number of call per a given period
- Combination: charge for a total number of calls per period and then charge per call.

### Products

In Azure API Management, product is defined as a collection of APIs. Products have Access Groups defined as administrators, developers and guests. Users are assigned to Access Groups. Those users who are assigned to Access Groups will have access to those products that have the Access Group defined.

### Product Policies

Product can include policies that can limit the numer of request per minute and the total of call per month, for example.

Sample policy:

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

A group is a collection of users.

### Users

A user may be assigned a group and products. For each product, the user will receive a primary and secondary subscription keys.

## Calling the API with the subscription key

- Set a header with the following key value:
  - ```Ocp-Apim-Subscription-key:key```

## Consumption Reporting

