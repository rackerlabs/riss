# JSON Representations

# JSON Standards

1.  Service JSON representations must follow the [IETF RFC 7158](http://tools.ietf.org/html/rfc7158) for JavaScript Object Notation (JSON) Data Interchange Format standard.
2.  Service JSON representations must pass a JSON validator such as the [JSONLint.com](http://jsonlint.com/) linter.
3.  Services should provide a JSON should provide a [JSON Schema](http://json-schema.org/) document to document Service JSON Representations.
4.  Object names in Service JSON representations must be named Camel Case JSON Body. Examples:

    1.  `targetNode`
    2.  `primaryLoadBalancer`

5.  Responses must specify a top level object name for the response. Examples:
    1.  Single object example:

        ```
        {
          "customer": {
            "firstName": "Joe",
            "lastName": "Customer"
          }
        }
        ```

    2.  List of objects example:

        ```
        {
          "customers": [{
              "firstName": "Joe",
              "lastName": "Customer"
            }]
        }
        ```

6.  Paginated Responses must:

    1.  Wrap response with a pluralized entity name
    2.  Provide an attribute total with the number of results found by the operation
    3.  Example:

        ```
        {
          "total": 12000,
          "customers": [{
            "firstName": "Joe",
            "lastName": "Customer"
          }]
        }
        ```

7.  Numeric values should be of "number" type:
    1.  Example:

        Good

        ```
        {
          "balance": {
            "currentBalance": 1510.75,
            "pastDue": 10.5,
            "amountDue": 1500.25,
            "unbilledCharges": 692.5,
            "currency": "USD"
          }
        }
        ```

        Bad

        ```
        {
          "balance": {
            "currentBalance": "1510.75",
            "pastDue": "10.5",
            "amountDue": "1500.25",
            "unbilledCharges": "692.5",
            "currency": "USD"
          }
        }
        ```










