# What is Zero?
Zero is a secrets manager specifically designed for managing API credentials. It makes working with most popular APIs seamless and intuitive. Its goal is simplicity and high performance. The core engine is written in Rust and serves a modern GraphQL API.

# How does it work?
Zero provides multi-platform SDKs (TypeScript/Go/Python), CI/CD, and cloud tooling for requesting and using secrets in application at runtime and OS environment. Users can define one or more Zero tokens and assign secrets to each of them in a web UI. Then, the secrets can be requested by the application or script using one of Zero’s SDKs.

# Installation and usage
## App runtime

First you’ll need to install the SDK for your platform.

**Node JS**:
```sh
npm install @zerosecrets/zero
```

**Go**:
```sh
go get github.com/zerosecrets/go-sdk
```

**Python**:
```sh
poetry install zero-sdk
```
Once the SDK is installed, you can use it to fetch the secrets. Please don’t forget that you’ll need a Zero token to do this. You can create one in the web UI.

> ⚠️ Don’t share it publicly! In the examples below, we assume the Zero token is available as an environment variable.

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
When you need secrets in your CI/CD pipeline, you can use our  GitHub action. It takes a Zero token as input, downloads the secrets, and sets environment variables. The Zero token should be stored as a GitHub secret since it is sensitive data that **should never be exposed publicly**:,

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
Finally, for those who need more direct access to Zero, we provide a GraphQL API. The code below shows how to query your secrets. Since it is a GraphQL API, you can request many  Zero tokens in a single API call.

```graphql
# POST https://core.tryzero.com/v1/graphql

query {
  secrets(zeroToken: "YOUR_ZERO_TOKEN", pick: ["pick-some", "of-your-apis"]) {
    name

    fields {
      name
      value
    }
  }
}
```
