# Shops API

This documentation describes the snabble API endpoints related to the
management and access of shops. These endpoints are available on the
`api` subdomain. See [General API access](api_general.md) for general
information about api access.

### Endpoints

* [Get all shops](#get-get-shops): `GET /{project}/shops}`
* [Get shop](#get-get-shop): `GET /{project}/shops/{slug}`
* [Get shop by `externalId`](#get-shop-by-externalid): `GET /{project}/shops/search?externalId={slug}`
* [Create shop](#create-shop): `POST /{project}/shops`
* [Update shop](#update-shop): `PUT /{project}/shops/{slug}`
* [Delete shop](#delete-shop): `DELETE /{project}/shops/{slug}`

## Model

### Shop

| Path                        | Type     | Description                                                        |
|-----------------------------|----------|--------------------------------------------------------------------|
| `id`                        | `string` | The id of the Shop                                                 |
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
| `external`                  | `Object` | Custom object containing additional data                           |

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
      "href" : "http://localhost:8080/demo/shops/1"
    }
  }
}
```

### Customizing a Shop

Since there is no model that fits all use-cases, it is possible to
associate a custom object with each shop through the `external`
field. This field can be used to associate additional links or other
kind of informations with the shop.

Keep in mind, that the SDK initially downloads the list of shops
containing the whole `external` object.

## Get all shops
`GET /{project}/shops`

Get the list of shops of the project.

**Required permissions** : shopsRead for the project

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : List of [Shops](#shop)

## Get shop
`GET /{project}/shops/{id}`

Get the shop.

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [Shop](#shop)

## Get shop by `externalId`
`GET /{project}/shops/search/?externalId={externalId}`

Search the shop by its `externalId`.

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
`PUT /{project}/shops/{id}`

Update the shop.

### Request

**Content-Type** : application/json

**Data** : [Shop](#shop)

### Success Response `200 OK`

**Content-Type** : application/json

**Data** : [Shop](#shop)


## Delete shop
`DELETE /{project}/shops/{id}`

Delete the shop.

### Success Response `200 OK`
