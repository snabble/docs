# Shops API

This documentation describes the snabble API endpoints related to the
management and access of shops. These endpoints are available on the
`api` subdomain. See [General API access](api_general.md) for general
information about api access.

### Endpoints

* [Get all shops](#get-get-shops): `GET /{project}/shops}`
* [Get shop](#get-get-shop): `GET /{project}/shops/{slug}`
* [Create shop](#create-shop): `POST /{project}/shops`
* [Update shop](#update-shop): `PUT /{project}/shops/{slug}`
* [Delete shop](#delete-shop): `DELETE /{project}/shops/{slug}`

## Model

### Shop

| Path                        | Type     | Description                                                        |
|-----------------------------|----------|--------------------------------------------------------------------|
| `project`                   | `string` | The id of the Project                                              |
| `externalId`                | `string` | An identifier provided by external sources                         |
| `name`                      | `string` | The name of the shop                                               |
| `email`                     | `string` | Email address                                                      |
| `phone`                     | `string` | Phone number                                                       |
| `street`                    | `string` | Street and number                                                  |
| `zip`                       | `string` | Postal code                                                        |
| `city`                      | `string` | City                                                               |
| `state`                     | `string` | State                                                              |
| `country`                   | `string` | Country                                                            |
| `lat`                       | `number` | Latitude                                                           |
| `lon`                       | `number` | Longitude                                                          |
| `openingHoursSpecification` |          | Description of the opening hours of the store. Each item contains the `dayOfWeek` (`MONDAY`, `TUESDAY`, `WEDNESDAY`...), the time when it `opens` and the time when it `closes`. It is possible to add multiple items per day. |
| `services[]`                | `Array`  | List of services provided by the shop                              |

Example:

```
{
  "project" : "some-project",
  "externalId" : "some-id",
  "name" : "name",
  "lat" : 1.0,
  "lon" : 1.0,
  "openingHoursSpecification" : [
    {
      "opens" : "08:00",
      "closes" : "12:00",
      "dayOfWeek" : "MONDAY"
    },
    {
    "opens" : "14:00",
    "closes" : "20:00",
    "dayOfWeek" : "MONDAY"
    },
    ...
  ],
  "phone" : "111",
  "email" : "email1",
  "services" : [ "backery" ],
  "state" : "region",
  "street" : "street 1",
  "city" : "city",
  "zip" : "postalCode",
  "country" : "country",
  "links" : {
    "self" : {
      "href" : "http://localhost:8080/api/1/shops/city-street-1-epvxvKgNP8"
    }
  }
}
```

## Get all shops
`GET /{project}/shops`

Get the list of shops of the project.

**Required permissions** : shopsRead for the project

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : List of [Shops](#shop)

## Get shop
`GET /{project}/shops/{slug}`

Get the shop.

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [Shop](#shop)

## Create shop
`POST /{project}/shops`

Create a shop.

### Request

**Content-Type** : application/json

**Data** : [Shop](#shop)

### Success Response `201 Created`

**Location** : [Get Shop](#get-shop)

**Content-Type** : application/json

**Data** : [Shop](#shop)


## Update shop
`PUT /{project}/shops/{slug}`

Update the shop.

### Request

**Content-Type** : application/json

**Data** : [Shop](#shop)

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [Shop](#shop)


## Delete shop
`DELETE /{project}/shops/{slug}`

Delete the shop.

### Success Response `200 OK`
