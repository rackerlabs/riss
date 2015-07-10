# JSON Representations

## Guidelines

1. JSON representations should avoid needless boilerplate and ceremony.
2. JSON representations should be documented as [JSON Schema](http://json-schema.org/).
3. Avoid a top level JSON object that simply names the "type" of the representation. Instead start with the value of the object. For example, prefer:

    *Good example: single object*

    ``` json
    {
        "firstName": "Joe",
        "lastName": "Customer"
    }
    ```

    *Good example: list*

    ``` json
    [
        {
            "firstName": "Charlie",
            "lastName": "Customer"
        }
    ]
    ```

    over

    *Bad Example: single object*

    ``` json
    {
        "customer": {
            "firstName": "Joe",
            "lastName": "Customer"
        }
    }
    ```

    *Bad example: list*

    ``` json
    {
        "customers": [
            {
                "firstName": "Charlie",
                "lastName": "Customer"
            }
        ]
    }
    ```

## Standards

1. JSON representations *MUST* be valid JSON as defined in the [ECMA Standard 404: The JSON Data Interchange Format](http://www.ecma-international.org/publications/files/ECMA-ST/ECMA-404.pdf).
    * See also:
        - [IETF RFC 7158](http://tools.ietf.org/html/rfc7158) for JavaScript Object Notation (JSON) Data Interchange Format standard
        - [www.json.org](http://www.json.org/)
2.  Service JSON representations *MUST* pass a JSON validator such as [JSONLint.com](http://jsonlint.com/)
3. Naming. JSON object names *MUST* conform to JavaScript naming conventions also known as "Camel Case".
    * Reference: "Names" in [Code Conventions for the JavaScript Programming Language](http://javascript.crockford.com/code.html)
    * Good Examples:
        -  `targetNode`
        -  `primaryLoadBalancer`
    * Bad Examples:
        - `Big Boy` - reason: leading capital and space
        - `Bar-top` - reason: punctuation in the name
4.  Paginated Responses must:

    1.  Wrap response with a pluralized entity name
    2.  Provide an attribute total with the number of results found by the operation
    3.  Example:

        ``` json
        {
          "total": 12000,
          "customers": [{
            "firstName": "Joe",
            "lastName": "Customer"
          }]
        }
        ```

5.  Numeric values should be of "number" type:
    1.  Example:

        Good

        ``` json
        {
            "currentBalance": 1510.75,
            "pastDue": 10.5,
            "amountDue": 1500.25,
            "unbilledCharges": 692.5,
            "currency": "USD"
        }
        ```

        Bad

        ``` json
        {
            "currentBalance": "1510.75",
            "pastDue": "10.5",
            "amountDue": "1500.25",
            "unbilledCharges": "692.5",
            "currency": "USD"
        }
        ```

## Request Guidelines

1. Request Representations should prefer links to resources over resource IDs/magic numbers/names.
2. Do not duplicate URL parameters in the Request Representation

## Response Standards

1. Include a "self" link to the resource
2. Include links to other resources related to this resource such as:
    * "child" resources
    * "parent" resources
    * other relationships
3. Include links that indicate actions that are available for this resource
    * DISCUSSION: need solution for authorization of actions

## Response Proposed Standards

This section needs to be proven out.

1. Use [Hydra vocabulary](http://www.hydra-cg.com/spec/latest/core/) for related resources and actions in JSON representations.
