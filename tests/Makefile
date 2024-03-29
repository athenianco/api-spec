LINT_OPENAPI_EXTRA_OPTS ?=
OPENAPI_GENERATOR_VERSION ?= v6.0.1
GITHUB_BASE_REF ?= master

.PHONY: all-checks
all-checks: spec lint-enforce lint-ibm yamllint-diff

../openapi_js.yaml: ../openapi.yaml
	sed -e 's/oneOf:/allOf:/g' -e 's/anyOf:/allOf:/g' ../openapi.yaml > ../openapi_js.yaml

.PHONY: spec
spec: ../openapi_js.yaml
	@mkdir -p build
	docker run --rm -v `pwd`/..:/local --user `id -u`:`id -g` \
	  openapitools/openapi-generator-cli:$(OPENAPI_GENERATOR_VERSION) \
	  generate --input-spec=/local/openapi_js.yaml --generator-name=javascript --output=/local/tests/build/js-client
	npm --prefix build/js-client install
	npm --prefix build/js-client test


build/openapi_rendered.yaml: ../openapi.yaml
	@mkdir -p build
	jinja2 ../openapi.yaml -o build/openapi_rendered.yaml

build/openapi_rendered_oas_validation.yaml: build/openapi_rendered.yaml
#	Following example is problematic due to it including an `id` propery
#	and multiple sub-schemas referencing it; remove it before calling openapi
	yq "del(.components.schemas.User.example)" \
		< build/openapi_rendered.yaml \
		> build/openapi_rendered_oas_validation.yaml


.PHONY: lint-enforce
lint-enforce: build/openapi_rendered.yaml
	npm install openapi-enforcer-cli
	npx openapi-enforcer validate build/openapi_rendered.yaml

.PHONY: lint-ibm
lint-ibm: build/openapi_rendered_oas_validation.yaml
	npm install ibm-openapi-validator
	npx lint-openapi -p -v $(LINT_OPENAPI_EXTRA_OPTS) build/openapi_rendered_oas_validation.yaml

.PHONY: yamllint-diff
yamllint-diff: yamllint.yml
	yamllint -c yamllint.yml ../openapi.yaml > yamllint.errors || true
	./git-diff-changed-lines.sh origin/$(GITHUB_BASE_REF)..HEAD ../openapi.yaml | sed -e 's/^/ /g' -e 's/$$/:/g' | (! grep -f - yamllint.errors)

.PHONY: yamllint
yamllint: yamllint.yml ../openapi.yaml
	yamllint -c yamllint.yml ../openapi.yaml
