# What is Zero?
Zero is a secrets manager specifically designed for managing API credentials. It makes working with most popular APIs seamless and intuitive. Its goal is simplicity and high performance. The core engine is written in Rust and serves a modern GraphQL API.

# How does it work?
Zero provides **multi-platform SDKs** and cloud tooling for requesting and using secrets in application at runtime and OS environment:
* [TypeScript](https://github.com/zerosecrets/typescript-sdk)
* [Go](https://github.com/zerosecrets/go-sdk)
* [Rust](https://github.com/zerosecrets/rust-sdk)
* [Python](https://github.com/zerosecrets/python-sdk)
* CI/CD ([GitHub action](https://github.com/zerosecrets/github-actions))

Users can define one or more Zero tokens and assign secrets to each of them in a web UI. Then, the secrets can be requested by the application or script using one of Zero’s SDKs.

# Installation and usage
## SDK
Instructions on how to use SDK can be found in their respective repositories.

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
