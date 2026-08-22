<div align="center">

<!-- BANNER -->
<img src="https://raw.githubusercontent.com/serverless/serverless/main/assets/images/serverless_framework_v1_dark.gif" alt="Serverless Framework" width="550"/>

<br/>
<br/>

# ⚡ AWS Serverless HTTP API — Node.js

### Production-grade · Scalable · Zero-Infrastructure · Lightning Fast

<br/>

[![Serverless](https://img.shields.io/badge/serverless-v3-FD5750?style=for-the-badge&logo=serverless&logoColor=white)](https://www.serverless.com/)
[![Node.js](https://img.shields.io/badge/node.js-18.x-339933?style=for-the-badge&logo=node.js&logoColor=white)](https://nodejs.org/)
[![AWS Lambda](https://img.shields.io/badge/AWS-Lambda-FF9900?style=for-the-badge&logo=amazon-aws&logoColor=white)](https://aws.amazon.com/lambda/)
[![API Gateway](https://img.shields.io/badge/API-Gateway-FF4F8B?style=for-the-badge&logo=amazon-aws&logoColor=white)](https://aws.amazon.com/api-gateway/)
[![License: MIT](https://img.shields.io/badge/License-MIT-22C55E?style=for-the-badge)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-Welcome-6366F1?style=for-the-badge)](CONTRIBUTING.md)

<br/>

> **A battle-hardened, minimal-footprint HTTP API built on AWS Lambda + API Gateway via the Serverless Framework.**
> Deploy in seconds. Scale to millions. Pay for nothing when idle.

<br/>

[🚀 Quick Start](#-quick-start) · [📖 Docs](#-api-reference) · [🛠 Local Dev](#-local-development) · [🤝 Contributing](#-contributing) · [⭐ Examples](#-advanced-examples)

---

</div>

<br/>

## 📌 Table of Contents

- [✨ Why This Template?](#-why-this-template)
- [🏗 Architecture](#-architecture)
- [🧰 Prerequisites](#-prerequisites)
- [🚀 Quick Start](#-quick-start)
- [⚙️ Configuration](#️-configuration)
- [📁 Project Structure](#-project-structure)
- [📡 API Reference](#-api-reference)
- [🛠 Local Development](#-local-development)
- [🧪 Testing](#-testing)
- [🌍 Environment Variables](#-environment-variables)
- [🔐 Security](#-security)
- [📈 Performance & Scaling](#-performance--scaling)
- [⚡ Advanced Examples](#-advanced-examples)
- [🤝 Contributing](#-contributing)
- [📄 License](#-license)

---

<br/>

## ✨ Why This Template?

| Feature | Details |
|---|---|
| 🟢 **Zero cold-start overhead** | Lightweight Node.js handler with minimal dependencies |
| 💸 **True pay-per-use** | No charges when idle — AWS Free Tier friendly |
| 🔁 **Auto-scaling** | From 0 to thousands of concurrent requests automatically |
| 🌐 **Global edge** | Deploy to any AWS region with a single flag |
| 🔒 **Secure by default** | IAM-scoped permissions, no over-privileged roles |
| ⚙️ **IaC-first** | Entire infra defined in `serverless.yml` — no click-ops |
| 🛠 **Dev-friendly** | Local emulation with `serverless-offline`, no cloud needed |

---

<br/>

## 🏗 Architecture

```
                         ┌─────────────────────────────────────────────┐
                         │                  AWS Cloud                   │
                         │                                             │
  Client (curl / app)    │    ┌──────────────┐     ┌──────────────┐   │
       │                 │    │              │     │              │   │
       │  HTTP Request   │    │  API Gateway │     │   Lambda     │   │
       │─────────────────┼───▶│  (HTTP API)  │────▶│  Function    │   │
       │                 │    │              │     │  (Node.js)   │   │
       │◀────────────────┼────│  Stage: dev  │◀────│              │   │
       │  JSON Response  │    │              │     │              │   │
                         │    └──────────────┘     └──────────────┘   │
                         │                               │             │
                         │                    ┌──────────▼──────────┐ │
                         │                    │   CloudWatch Logs   │ │
                         │                    └─────────────────────┘ │
                         └─────────────────────────────────────────────┘
```

**Request Flow:**
1. Client sends HTTP `GET` request to API Gateway endpoint
2. API Gateway routes it to the mapped Lambda function
3. Lambda executes the Node.js handler and returns a response
4. API Gateway forwards the JSON response back to the client
5. All invocation logs are streamed to **CloudWatch**

---

<br/>

## 🧰 Prerequisites

Before you begin, ensure you have the following installed and configured:

| Tool | Version | Install |
|---|---|---|
| **Node.js** | `>= 18.x` | [nodejs.org](https://nodejs.org/) |
| **npm** | `>= 9.x` | Bundled with Node.js |
| **Serverless CLI** | `v3` | `npm i -g serverless` |
| **AWS CLI** | `>= 2.x` | [aws.amazon.com/cli](https://aws.amazon.com/cli/) |
| **AWS Account** | Active | [aws.amazon.com](https://aws.amazon.com/) |

### Configure AWS Credentials

```bash
aws configure
# AWS Access Key ID:     <your-access-key>
# AWS Secret Access Key: <your-secret-key>
# Default region:        us-east-1
# Default output format: json
```

> 💡 **Tip:** Use AWS IAM roles with least-privilege access. Never commit credentials to version control.

---

<br/>

## 🚀 Quick Start

Get your API live in under 2 minutes:

```bash
# 1. Clone the repository
git clone https://github.com/your-org/aws-node-http-api.git
cd aws-node-http-api

# 2. Install dependencies
npm install

# 3. Deploy to AWS (dev stage, us-east-1)
serverless deploy

# 4. Call your live API 🎉
curl https://xxxxxxxxxx.execute-api.us-east-1.amazonaws.com/
```

**Expected Output:**

```json
{
  "message": "Go Serverless v3.0! Your function executed successfully!",
  "input": {
    "version": "2.0",
    "routeKey": "GET /",
    "rawPath": "/",
    "requestContext": { ... }
  }
}
```

---

<br/>

## ⚙️ Configuration

All infrastructure configuration lives in `serverless.yml`:

```yaml
service: aws-node-http-api-project

provider:
  name: aws
  runtime: nodejs18.x
  region: us-east-1        # 🌍 Change your target region here
  stage: ${opt:stage, 'dev'}

functions:
  hello:
    handler: handler.hello
    events:
      - httpApi:
          path: /
          method: GET
```

### Deploy to Different Stages

```bash
# Deploy to production
serverless deploy --stage prod

# Deploy to a specific region
serverless deploy --region eu-west-1

# Deploy with a custom profile
serverless deploy --aws-profile myprofile
```

---

<br/>

## 📁 Project Structure

```
aws-node-http-api/
├── 📄 handler.js          # Lambda function handler
├── 📄 serverless.yml      # Infrastructure as Code (IaC) config
├── 📄 package.json        # Node.js dependencies & scripts
├── 📄 .gitignore          # Ignored files (node_modules, .serverless)
└── 📄 README.md           # You are here
```

### `handler.js` — The Core

```javascript
'use strict';

module.exports.hello = async (event) => {
  return {
    statusCode: 200,
    body: JSON.stringify(
      {
        message: 'Go Serverless v3.0! Your function executed successfully!',
        input: event,
      },
      null,
      2
    ),
  };
};
```

---

<br/>

## 📡 API Reference

### `GET /`

Returns a success message with the full Lambda event context.

**Request**

```http
GET https://<api-id>.execute-api.<region>.amazonaws.com/
```

**Response `200 OK`**

```json
{
  "message": "Go Serverless v3.0! Your function executed successfully!",
  "input": {
    "version": "2.0",
    "routeKey": "GET /",
    "rawPath": "/",
    "rawQueryString": "",
    "headers": { ... },
    "requestContext": { ... },
    "isBase64Encoded": false
  }
}
```

| Field | Type | Description |
|---|---|---|
| `message` | `string` | Static success message |
| `input` | `object` | Full API Gateway event payload |

---

<br/>

## 🛠 Local Development

### Option 1 — Direct Lambda Invocation

Run the function locally without any HTTP layer:

```bash
serverless invoke local --function hello
```

```json
{
  "statusCode": 200,
  "body": "{\n  \"message\": \"Go Serverless v3.0! ...\"\n}"
}
```

### Option 2 — Full API Gateway Emulation (Recommended)

Install and run `serverless-offline` for a full local HTTP server:

```bash
# Install the plugin (one-time)
serverless plugin install -n serverless-offline

# Start the local server
serverless offline
```

Your API is now available at `http://localhost:3000`:

```bash
curl http://localhost:3000/
```

> 🔁 The server **hot-reloads** on file changes — no restart needed during development.

### Option 3 — Emulate with Docker (Advanced)

```bash
# Use AWS SAM for Docker-based local Lambda emulation
npm install -g aws-sam-cli
sam local start-api
```

---

<br/>

## 🧪 Testing

```bash
# Run unit tests
npm test

# Run with coverage report
npm run test:coverage

# Run integration tests against deployed API
API_URL=https://xxx.execute-api.us-east-1.amazonaws.com npm run test:integration
```

**Recommended Testing Stack:**

| Layer | Tool |
|---|---|
| Unit | [Jest](https://jestjs.io/) |
| Integration | [Supertest](https://github.com/ladjs/supertest) |
| E2E | [Artillery](https://www.artillery.io/) |
| Load | [k6](https://k6.io/) |

---

<br/>

## 🌍 Environment Variables

Define environment variables in `serverless.yml`:

```yaml
provider:
  environment:
    NODE_ENV: ${opt:stage, 'dev'}
    MY_SECRET: ${ssm:/my-app/my-secret}   # From AWS SSM Parameter Store
    API_KEY: ${env:API_KEY}               # From local shell environment
```

> 🔐 **Never hardcode secrets.** Use [AWS SSM Parameter Store](https://docs.aws.amazon.com/systems-manager/latest/userguide/systems-manager-parameter-store.html) or [AWS Secrets Manager](https://aws.amazon.com/secrets-manager/).

---

<br/>

## 🔐 Security

> ⚠️ By default, the deployed API is **publicly accessible**. For production, implement one of the following:

### Option A — Lambda Authorizer (JWT/OAuth)

```yaml
functions:
  hello:
    handler: handler.hello
    events:
      - httpApi:
          path: /
          method: GET
          authorizer:
            name: myAuthorizer
```

### Option B — API Key Authentication

```yaml
provider:
  apiGateway:
    apiKeys:
      - myApiKey
```

### Option C — AWS Cognito User Pools

```yaml
httpApi:
  authorizers:
    cognitoAuthorizer:
      identitySource: $request.header.Authorization
      issuerUrl: https://cognito-idp.{region}.amazonaws.com/{userPoolId}
      audience:
        - !Ref UserPoolClient
```

📖 Full docs: [Serverless Auth Docs](https://www.serverless.com/framework/docs/providers/aws/events/http-api/#jwt-authorizers)

---

<br/>

## 📈 Performance & Scaling

| Metric | Value |
|---|---|
| **Cold Start** | ~200–400ms (Node.js 18.x) |
| **Warm Invocation** | < 10ms |
| **Concurrency** | 1,000 (default, adjustable) |
| **Timeout** | 6s (default), max 15min |
| **Max Payload** | 6MB (sync), 256KB (async) |

### Tips to Minimize Cold Starts

- ✅ Keep `node_modules` lean — use `bundling` with esbuild
- ✅ Use Lambda **Provisioned Concurrency** for latency-critical paths
- ✅ Enable **Lambda SnapStart** (Java only, but useful to know)
- ✅ Deploy in **arm64** (Graviton2) for 20% better price/perf ratio

```yaml
provider:
  architecture: arm64   # 🦾 Graviton2 — faster & cheaper
  memorySize: 256       # 💾 Right-size your function
  timeout: 10           # ⏱ Don't leave it at 6s default
```

---

<br/>

## ⚡ Advanced Examples

| Example | Description | Link |
|---|---|---|
| 🗄 **DynamoDB CRUD** | Full REST API with persistence | [View](https://github.com/serverless/examples/tree/master/aws-node-rest-api-with-dynamodb) |
| 🔑 **JWT Auth** | Protected routes with Cognito | [View](https://github.com/serverless/examples) |
| 📦 **TypeScript** | Fully typed Lambda handlers | [View](https://github.com/serverless/examples/tree/master/aws-node-typescript-rest-api-with-dynamodb) |
| 📬 **SQS Queue** | Async messaging with SQS | [View](https://github.com/serverless/examples) |
| 📸 **S3 Trigger** | Process uploads automatically | [View](https://github.com/serverless/examples) |
| 🌐 **GraphQL** | Apollo Server on Lambda | [View](https://github.com/serverless/examples) |

---

<br/>

## 🤝 Contributing

Contributions, issues, and feature requests are welcome!

```bash
# Fork & clone
git clone https://github.com/your-username/aws-node-http-api.git

# Create your feature branch
git checkout -b feature/amazing-feature

# Commit your changes
git commit -m 'feat: add amazing feature'

# Push and open a Pull Request
git push origin feature/amazing-feature
```

Please follow [Conventional Commits](https://www.conventionalcommits.org/) and ensure all tests pass before submitting a PR.

---

<br/>

## 📄 License

Distributed under the **MIT License**. See [`LICENSE`](LICENSE) for more information.

---

<br/>

<div align="center">

**Built with ❤️ using the [Serverless Framework](https://www.serverless.com/)**

⭐ Star this repo if it saved you time!

<br/>

[![Serverless](https://img.shields.io/badge/Powered%20by-Serverless-FD5750?style=flat-square&logo=serverless)](https://www.serverless.com/)
[![AWS](https://img.shields.io/badge/Deployed%20on-AWS-FF9900?style=flat-square&logo=amazon-aws)](https://aws.amazon.com/)
[![Node.js](https://img.shields.io/badge/Runtime-Node.js-339933?style=flat-square&logo=node.js)](https://nodejs.org/)

</div>

