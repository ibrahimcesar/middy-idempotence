<div align="center">
 
  <h1>🛵 📬  Idempotent Middleware for Middy</h1>
  <blockquote>An Idempotent Middy middleware for your AWS Lambdas</blockquote>

  [![TypeScript](https://badges.frapsoft.com/typescript/code/typescript.svg?v=101)](https://github.com/ellerbrock/typescript-badges/)

  [![Version](https://img.shields.io/npm/v/middy-idempotent?label=latest%20version)](https://www.npmjs.com/package/middy-idempotent
)&nbsp; &nbsp;[![License](https://badgen.net/github/license/ibrahimcesar/middy-idempotent)](./LICENSE)&nbsp; &nbsp;![GitHub issues by-label](https://img.shields.io/github/issues/ibrahimcesar/middy-idempotent/bug)
 

<p>Developed in 🇧🇷 <span role="img" aria-label="Flag for Brazil">Brazil</p>

</div>

## 🛵 What is does

[Middy](https://middy.js.org/) is a very simple middleware engine that allows you to simplify your AWS Lambda code when using Node.js. This middleware aims to simplify the implementations of a idempotent API.

Making an API idempotent is [not trivial as much people think](https://awslabs.github.io/aws-lambda-powertools-python/develop/utilities/idempotency/), and you could take a look at _Lambda Powertools for Python_ from AWS that does a great job to explain this use case and a concrete way to implement. There's also this great article from [Malcolm Featonby](https://twitter.com/mfeatonby), [Making retries safe with idempotent APIs](https://aws.amazon.com/builders-library/making-retries-safe-with-idempotent-APIs/) featured in the Amazon Builder's Library as [Architecture level 300](https://aws.amazon.com/blogs/architecture/category/learning-levels/advanced-300/) which falls ins the _advanced_ classification.

## 🚀 Install

Use your favorite package manager:

```bash
yarn add middy-idempotent
```

```bash
npm install middy-idempotent -S
```

## Usage

Besides `@middy/core`, you must also use `@middy/http-json-body-parser` since this middleware will read the request body and needed parsed as json. And right now I only tested twith the client provided by the `ioredis` lib as well, so you'll need to install it too. At tthe bottom there's a write-up where we'll find how to use a Serverless Database service called [Upstash](https://upstash.com/) for free that is currently (`0.0.20`) the only storage supported.

```ts
handler.use(jsonBodyParser()).use(
  idempotent({
    client: new Redis(process.env.UPSTASH_REDISS),
  })
);
```

Just place in your code, as soon as possible, passing the Redis client constructor with your `rediss://` url. See `demo` for an example of application with infrastructure provisioned in AWS CD
Besides the client, you have th possibility to choose your own idempotency key instead of the whole `event`:

| Prop   |      Type      |  Description |
|----------|:--------:|------------|
| **client** |  Instance of class Redis | instance of the class Redis provided by the lib [`ioredis`](https://github.com/luin/ioredis), the only supported at the moment |
| body |    boolean or string   | Optional. If `true` uses the body sent in the request. If no body is sent, this will yield an error.  |
| header | string | Optional. If you pass any value, will try to get this value as a key header in your request (i.e, `x-forwarded-for` and `x-idempotency-key`) |
| path | String. One of "rawPath" or "rawQueryString" | Optional. If you want to target a very specific (and possibly dynamic) path or raw query string in the request |
| ttl | number | Optional. Redis keys live forever by default, so being able to set a time to live is useful to avoid running out of memory in your Redis instance, as this will cause your lambdas to fail. Time unit: _seconds_.

Note that the optional targets are mutually excludents, they obey the hierarchy `body` > `header` > `path` meaning if you pass all of them, it will pick `body` and if that is not provided `header` is picked. The default behaviour is use all the event object as idempotency key.

### TODO
- [ ] Add more storages (DynamoDB, etc)

## 📚 Read more

- [Redis: Exploring Redis as Serverless Database to solve idempotence in APIs](https://dev.to/aws-builders/redis-exploring-redis-as-serverless-database-to-solve-idempotence-in-apis-2gma)

## See Also

[🛵 🔐  reCAPTCHA Middleware for Middy](https://github.com/ibrahimcesar/react-lite-youtube-embed/): reCAPTCHA validation Middy middleware for yours AWS Lambdas


## MIT License

Copyright (c) 2021 Ibrahim Cesar

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
SOFTWARE.
