# OpenAPI Validation

## npm run docs:lint
```
> api@1.0.0 docs:lint
> openapi-cli lint openapi/openapi.yaml

No configurations were provided -- using built in recommended configuration by default.

validating openapi/openapi.yaml...
openapi/openapi.yaml: validated in 42ms

Woohoo! Your API description is valid. 🎉
```

## npm run docs:bundle
```
> api@1.0.0 docs:bundle
> openapi-cli bundle openapi/openapi.yaml -o openapi/bundle.yaml

bundling openapi/openapi.yaml...
📦 Created a bundle for openapi/openapi.yaml at openapi/bundle.yaml 35ms.
```

## npm run docs:site
```
> api@1.0.0 docs:site
> redocly build-docs openapi/bundle.yaml -o openapi/index.html

Prerendering docs

🎉 bundled successfully in: openapi/index.html (126 KiB) [⏱ 3ms].
```
