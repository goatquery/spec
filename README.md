# Goat Query Specification

## Overview

Goat Query is a set of libraries designed to provide a standardized way of paging, ordering, selecting, searching, counting, and filtering data. These libraries facilitate efficient query processing and simplify the implementation of these common operations in your API. The SDKs that are currently available are:

- `.NET`
- `Go`

This document serves as a specification for acceptable query parameters, their types, their meanings, default values, and the expected response structure.

---

## Query Parameters

The following query parameters are supported by Goat Query:

| Name    | Type    | Required | Default | Description                                                                                |
| ------- | ------- | -------- | ------- | ------------------------------------------------------------------------------------------ |
| Top     | Integer | No       | 0       | Specifies the maximum number of records to retrieve.                                       |
| Skip    | Integer | No       | 0       | Specifies the number of records to skip from the beginning.                                |
| Count   | Boolean | No       | False   | Indicates whether to include the total count of records in the response body.              |
| OrderBy | String  | No       | ""      | Specifies the field to order the records by.                                               |
| Select  | String  | No       | ""      | Defines the fields to include in the response. Multiple fields can be separated by commas. |
| Search  | String  | No       | ""      | Performs a text-based search on the data.                                                  |
| Filter  | String  | No       | ""      | Filters the data based on specified conditions.                                            |

### Top

If the default value is provided, then do not perform the `Top` action and instead respond with the entire dataset. The server is responsible for applying limitations on the value of this query parameter to prevent exhaustive queries.

Example: `/api/users?top=10`

### Skip

`Skip` will only occur if the query parameter value is greater than 0.

Example: `/api/users?skip=10`

### Count

If the value of `Count` is `true`, then the total count of records, after `Search` and `Filter` have been applied, will be returned response body under the property `count`.

Example: `/api/users?count=true`

### Order By

The `OrderBy` query parameter follows the format of `<property> <direction>`, which can be chained for multiple properties by using commas as delimiters. The order supplied will be the order in which the ordering occurs.

The valid directions are:

- `asc`
- `desc`

Example: `/api/users?orderby=firstname asc, lastName desc`

### Select

The `Select` query parameter accepts a comma-separated value of properties that are expected to be returned. The JSON structure should only include the specified properties.

Example: `/api/users?select=firstname, lastName`

### Search

The `Search` query parameter is a free-text search field. It is up to the server to provide the implementation for this, including the fields and operations to query this value on. The same search functionality should be applied consistently across the specific resource. For example, every time you query the users table, the search functionality tied to users should be applied to maintain consistency and expected results.

Example: `/api/users?search=John Doe`

### Filter

The `Filter` query parameter is a string that defines properties, operations, and values to perform advanced filtering on a dataset. This query parameter follows the format of `<property> <operand> '<value>'` and can be chained together with conjunctions to result in `<property> <operand> '<value>' <conjunction> <property> <operand> '<value>'`.

The valid conjunctions are:

- `or`
- `and`

The valid operands are:

- `eq`
- `ne`
- `contains`

#### Conjunctions

##### And

This will perform an AND operation between the two queries.

##### Or

This will perform an OR operation between the two queries.

#### Operands

##### eq

This will filter where the value is an exact match.

##### ne

This will filter where the value is not an exact match.

##### contains

This will filter where the value is contained within the properties value.

Examples:

- `/api/users?filter=firstname eq 'John' and lastname eq 'Doe'`
- `/api/users?filter=firstname ne 'John' or lastname contains 'Doe'`

---

## Responses

There are two different JSON structures for responses defined in this specification: successful responses and error responses.

### Successful

#### Body

If the query is valid and results in the correct dataset being returned, the dataset should always be an array. When returned from the server, it will exist within the `value` property.

The response should follow the JSON structure:

```json
{
  "value": [
    {
      "id": "00000000-0000-0000-0000-000000000000",
      "email": "john.doe@github.com",
      "firstname": "John",
      "lastname": "Doe"
    }
  ]
}
```

If `count` is set to `true`

```json
{
  "count": 1,
  "value": [
    {
      "id": "00000000-0000-0000-0000-000000000000",
      "email": "john.doe@github.com",
      "firstname": "John",
      "lastname": "Doe"
    }
  ]
}
```

### Error

#### Body

If there has been an error during the operation, the response body should include the properties, `status` and `message`.

The response should follow the JSON structure:

```json
{
  "status": 400,
  "message": "The query parameter 'Top' could not be parsed to an integer"
}
```
