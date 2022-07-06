# What is Zero?
Zero is a secrets manager specifically designed for managing APIs credentials. It makes work with most of the popular APIs seamless and intuitive. Its goal is simplicity and performance. The core engine is written in Rust and serves a modern GraphQL API.

# How does it work?
Zero provides multi-platform SDKs (TypeScript/Go/Python), CI/CD, and cloud tooling for requesting and using secrets in app runtime and OS environment. Users are allowed to define one or more Zero tokens, assign secrets to each one of them in web UI, and then request it in their apps using SDK or other relevant instruments provided by Zero.

# Installation and usage
## App runtime

First you’ll need to install the SDK. Follow the example for your platform:
```sh
npm install @zerosecrets/zero
```

```sh
go get github.com/zerosecrets/go-sdk
```

```sh
poetry install zero-sdk
```

Once the SDK is available in your app you can use it to fetch the secrets. Please don’t forget that you’ll need your Zero-token to do this. You can create one in the web UI. Don’t share it publicly! Examples below imply that you store a Zero-token in the OS environment.

__TypeScript__

```typescript
import {zero} from '@zerosecrets/zero'

// You can pick the secrets you’d like to fetch
const secrets = await zero({token: process.env.ZERO_TOKEN, apis: ["aws"]}).fetch()
console.log(secrets) // {secret: "value", secret2: "value2"}
```

__Go__

```go
package main

import (
	"log"
	"os"

	zero "github.com/zerosecrets/go-sdk"
)

func main() {
	// You can pick the secrets you’d like to fetch
	api, err := zero.Zero(os.Getenv("ZERO_TOKEN"), []string{"aws"})
	secrets, err := api.Fetch()
	log.Println(secrets) // map[aws:map[secret:value secret2:value2]]
}
```

__Python__

```python
import os
from zero_sdk import zero

ZERO_TOKEN = os.getenv("ZERO_TOKEN")
# You can pick the secrets you’d like to fetch
secrets = zero(token=ZERO_TOKEN, apis=["aws"]).fetch()
print(secrets) # {'aws': {'secret': 'value', 'secret2': 'value2'}}
```
## GitHub action
For the case when you need secrets to be applied in your CI/CD we built GtiHub action. It takes Zero-token as an input, downloads the secrets and sets environment variables. Please store Zero-token in GitHub secrets, it’s a very sensitive data, and should never be exposed publicly:

```yml
steps:
  - uses: zerosecrets/github-actions/token-to-secrets@main
    id: zero
    with:
      zero-token: ${{ secrets.ZERO_TOKEN }}
      apis: aws,digitalOcean,googleServices,...
  - name: echo secrets
    run: |
      echo "${{ steps.zero.outputs.zero-secrets }}"
      env | grep ZERO_SECRET
```
## GraphQL API
Finally, for those who need direct access to API we provide GraphQL API endpoint. This is how you can query your secrets. Notice that as it’s GraphQL API you can query as many Zero-tokens per single request as you need:

```graphql
# POST https://core.tryzero.com/v1/graphql

query ZeroSecrets($token: String!, $slugs: [String!]) {
  secrets(zeroToken: $token, pick: $slugs) {
    __typename
    id
    name

    fields {
      __typename
      name
      value
    }
  }
}
```
