# Validator

This is a request handler validation tool for RESTful API endpoints.

## How To Use

```python
from validrequest import validator, ValidationError
from typing import Dict, Any

def request_handler(request, response, next):
    # Note: type and state must be the first rules in that order
    # i.e. type (string,float,integer,number,dict)|state (required,sometimes)
    validation_rules: Dict[str, str] = {
        "q": "string|required|max:100",
        "timestamp": "float|sometimes|min:1|max:13",
        "comment": "string|required|min:10",
        "code": "integer|required",
        "version": "float|sometimes",
        "metadata": "dict|required",
        "timeSince": "number|required"
    }
    request_parameters: Dict[str, Any] = request.body
    try:
        validator(validation_rules, request_parameters)
        return response.status(200).send("Validation successful.")
    except ValidationError as e:
        return next({ "message": e })
    except Exception:
        return next({ "message": "Something else went wrong." })

    # You can also use a decorator (@validate) - see demo.py for an example
```
Refer to the ```demo.py``` file for further implementations.

## How to Install

```bash
python -m pip install validrequest
```

```bash
python -m pip install git+https://github.com/ableinc/validrequest.git
```

## Important - Migrating from v0.0.3 to v1.0.0

**Breaking Changes** in Version 1.0.0 - Please carefully review the changes below

- import library name has changed from "validator" to "validrequest":
    ```python
    from validrequest import validate, validator, ValidationError
    ```
- @validate decorator now accepts 'request' or 'req' as Request argument name
- parse_level was changed to payload_level for @validate decorator
- error callback must be named "next" for @validate decorator - it will not accept any other argument
- error callback will return a string error message with error type - it will no longer return JSON
- strict declaration has been removed - validation rules will always be checked against required and ignored for sometimes
- 'number' is now an acceptable type in validation rule - this can be used for fields that may be either integer or float
- Acceptable types in validation rules:
    - Integer: int | integer | number
    - Float: float | number
    - String: str | string
    - Dictionary: dict | dictionary | object
    - Boolean: bool | boolean
