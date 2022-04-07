# Athenian API specification

[OpenAPI 3](https://swagger.io/specification/) YAML specification.
Athenian is developing [the product](https://www.athenian.co/) that provides insights into
the software development pipeline. The frontend and the backend speak the same language, "Athenian API".

To view the specification, paste the YAML in [editor.swagger.io](https://editor.swagger.io/) or go directly at [api.athenian.co/v1/ui](https://api.athenian.co/v1/ui/).
The API server runs at https://api.athenian.co/v1

### Details

There are two main blocks - the endpoints and the models. The endpoints are grouped by purpose
(metrics, filter, settings, etc.) and tagged.

The read-only endpoints support two authentication modes, [JWT](https://jwt.io/) and
[API Key](https://swagger.io/docs/specification/authentication/api-keys/).
The read-write endpoints work only with JWT.
You can generate an API Key with `/token/create` if your annual plan supports it.

The majority of the endpoints require setting the Athenian account ID. In other words, you must be
a registered client.

### Contributing

Sanity checks on the API specification can be executed with the included `Makefile`:

```
make -C tests
```

`jinja2` and `yq` are required to run tests.


Please:

- Suggest API changes.
- Fix typos and examples.
- Improve the documentation.

We accept contributions in pull requests and bug reports in GitHub issues. Everybody is welcome!

### License

[CC-BY-4.0](https://creativecommons.org/licenses/by/4.0/)
