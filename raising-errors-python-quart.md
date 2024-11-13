## Error types and what they do

    assert False, clinicians

    if False:
      raise AssertionError(clinicians)

Both of the above will raise the same error (this is a case when more than one
clinician is returned in clinicians). Clinicians will be stringified as an error 
here, but this isn't a good way to do this.

Instead use:

    raise RuntimeError("Too many clinicians")

This will be picked up by Sentry.

--

    raise APIError(404, "NOT_FOUND")

Ships the rest of the code

    return {"code": "CLINICIAN_NOT_FOUND", 404}

Returns the message, but says that the operation was successful.

--

Quart error

    abort(404) 

Will return html errors, but we want JSON.

--

    throw new Error
    or
    raise Error 

These won't generate a JSON error. They will just throw an HTML error
and Quart will display a generic 500.


### Asserts

We often use asserts to satisfy the type checker (myPy) when we know it's not None,
so we'd use:

    assert X is not None
    *below this line X won't be typed as None*


If you run python like this, it'll get rid of 'asserts' and 'debug' stuff to speed up 
the code

    python -0


