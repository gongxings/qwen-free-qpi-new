# Qwen AI Free Service

<span>[ <a href="README.md">中文</a> | English ]</span>

This project is modified from [https://github.com/LLM-Red-Team/qwen-free-api](https://github.com/LLM-Red-Team/qwen-free-api), thanks for the contribution!
**Important: The original project contained malicious code due to supply chain attacks. It is strongly recommended to no longer use the original project.**

Modification reasons:
1. The official API changes in the original project caused answer out-of-order, making the API basically unusable
2. The original project author rarely updates anymore
3. The malicious code in the original project has been removed. Welcome to review this project's source code

[![](https://img.shields.io/github/license/llm-red-team/qwen-free-api.svg)](LICENSE)
![](https://img.shields.io/github/stars/llm-red-team/qwen-free-api.svg)
![](https://img.shields.io/github/forks/llm-red-team/qwen-free-api.svg)
![](https://img.shields.io/docker/pulls/akashrajpuroh1t/qwen-free-api-fix.svg)

Supports high-speed streaming output, multi-turn dialogues, internet search, long document reading, image analysis, zero-configuration deployment, multi-token support, and automatic session trace cleanup.

Fully compatible with the ChatGPT interface.

Also, the following free APIs are available for your attention:

Moonshot AI (Kimi.ai) API to API [kimi-free-api](https://github.com/LLM-Red-Team/kimi-free-api/tree/master)

StepFun (StepChat) API to API [step-free-api](https://github.com/LLM-Red-Team/step-free-api)

ZhipuAI (ChatGLM) API to API [glm-free-api](https://github.com/LLM-Red-Team/glm-free-api)

Meta Sota (metaso) API to API [metaso-free-api](https://github.com/LLM-Red-Team/metaso-free-api)

Iflytek Spark (Spark) API to API [spark-free-api](https://github.com/LLM-Red-Team/spark-free-api)

Lingxin Intelligence (Emohaa) API to API [emohaa-free-api](https://github.com/LLM-Red-Team/emohaa-free-api) (OUT OF ORDER)

## Update Notes

1. Modified chat.ts to modify the non-streaming output interface method, referencing the original project issue [Returned messages are garbled](https://github.com/LLM-Red-Team/qwen-free-api/issues/72)

2. Updated models.ts model list to support qwen3-235b-a22b, qwen3-coder-plus, qwen-plus-latest and other latest models

3. Repackaged the docker image for the new version, `akashrajpuroh1t/qwen-free-api-fix:latest`

4. Fixed the malicious code issue in the source code and repackaged. Verification as follows:

- The obfuscated code is at the end of the `src/api/chat.js` file, and after building the container, it is at line 1871 or so in the `/app/dist/index.js` file, as shown in the red box below:
![index.js](./doc/index.js.png)

- After fixing, removed the obfuscated code. After building the container, it is at line 1951 or so in the `/app/dist/index.js` file, as shown below:
![index-fix.js](./doc/index-fix.js.png)

Models have been tested as follows:

![](https://cdn.jsdelivr.net/gh/xiaoY233/PicList@main/public/assets/qwen-free.png)

### Changelog

- v1.0.0-fix (2025-11-23)
    - Fixed malicious code issue in source code, resolved build errors

- v0.0.23.2 (2025-11-09)
    - Fixed response duplication issue: optimized streaming response handling logic, only extract incremental content when text length increases, avoiding duplicate output

- v0.0.23.1 (2025-08-12)
    - Fixed error: STRING_IS_BLANK - sessionId cannot be empty

- v0.0.23 (2025-08-11)
    - After testing, the original interface actually specified model ID does not take effect, modified chat.ts related logic to support specified model ID.
    - Updated models.ts list according to official latest Chat-API interface [https://chat.qwen.ai/api/models](https://chat.qwen.ai/api/models)

## Disclaimer

**Reverse APIs are unstable, and it is recommended to go to the official Alibaba Cloud https://dashscope.console.aliyun.com/ to use the paid API to avoid the risk of being banned.**

**This organization and individual do not accept any financial donations and transactions. This project is purely for research, exchange and learning purposes!**

**For personal use only, it is prohibited to provide services to others or for commercial use, so as not to put pressure on the official service, otherwise the risk is borne by yourself!**

**For personal use only, it is prohibited to provide services to others or for commercial use, so as not to put pressure on the official service, otherwise the risk is borne by yourself!**

**For personal use only, it is prohibited to provide services to others or for commercial use, so as not to put pressure on the official service, otherwise the risk is borne by yourself!**

  
## Effect Examples

### Identity Verification

![Identity Verification](./doc/example-1.png)

### Multi-turn Dialogue

![Multi-turn Dialogue](./doc/example-2.png)

### AI Drawing

![AI Drawing](./doc/example-3.png)

### Long Document Reading

![Long Document Reading](./doc/example-5.png)

### Image Analysis

![Image Analysis](./doc/example-6.png)

### 10-Thread Concurrency Test

![10-Thread Concurrency Test](./doc/example-4.png)

## Access Preparation

### Method1

Log in to [Tongyi Qianwen](https://tongyi.aliyun.com/qianwen)

Enter Tongyi Qianwen and start a random conversation, then press F12 to open the developer tools. Find the value of `tongyi_sso_ticket` in Application > Cookies, which will be used as the Bearer Token value for Authorization: `Authorization: Bearer TOKEN`

![Get tongyi_sso_ticket](./doc/example-0.png)

### Method2

Log in to [Alibaba Cloud](https://www.aliyun.com/) (not recommended if the account has important assets such as servers). If the account has not previously entered [Tongyi Qianwen](https://tongyi.aliyun.com/qianwen), you need to first agree to the terms, otherwise it will not take effect.

Then press F12 to open the developer tools. Find the value of `login_aliyunid_ticket` in Application > Cookies, which will be used as the Bearer Token value for Authorization: `Authorization: Bearer TOKEN`
![Get login_aliyunid_ticket](./doc/example-7.png)

### Multi-Account Access

You can provide multiple account `tongyi_sso_ticket` or `login_aliyunid_ticket` and use `,` to join them:

`Authorization: Bearer TOKEN1,TOKEN2,TOKEN3`

The service will pick one each time a request is made.

## Docker Deployment

Please prepare a server with a public IP and open port 8000.

Pull the image and start the service

```shell
docker run -it -d --init --name qwen-free-api-fix -p 8000:8000 -e TZ=Asia/Shanghai akashrajpuroh1t/qwen-free-api-fix:latest
```

check real-time service logs

```shell
docker logs -f qwen-free-api-fix
```

Restart service

```shell
docker restart qwen-free-api-fix
```

Shut down service

```shell
docker stop qwen-free-api-fix
```

### Docker-compose Deployment

```yaml
version: '3'

services:
  qwen-free-api:
    container_name: qwen-free-api-fix
    image: akashrajpuroh1t/qwen-free-api-fix:latest
    restart: always
    ports:
      - "8000:8000"
    environment:
      - TZ=Asia/Shanghai
```

### Other Deployments

Other deployment methods are not recommended as they have not been tested. It is recommended to use Docker deployment.

## Interface List

Currently, the `/v1/chat/completions` interface compatible with openai is supported. You can use the client access interface compatible with openai or other clients, or use online services such as [dify](https://dify.ai/) Access and use.

### Conversation completion

Conversation completion interface, compatible with openai's [chat-completions-api](https://platform.openai.com/docs/guides/text-generation/chat-completions-api).

**POST /v1/chat/completions**

The header needs to set the Authorization header:

```
Authorization: Bearer [tongyi_sso_ticket/login_aliyunid_ticket]
```

Request data:
```json
{
    // Fill in the model name as you like.
    "model": "qwen",
    // Currently, multi-round conversations are realized based on message merging, which in some scenarios may lead to capacity degradation and is limited by the maximum number of tokens in a single round.
    // If you want a native multi-round dialog experience, you can pass in the ids obtained from the last round of messages to pick up the context
    // "conversation_id": "bc9ef150d0e44794ab624df958292300-40811965812e4782bb87f1a9e4e2b2cd",
    "messages": [
        {
            "role": "user",
            "content": "who RU？"
        }
    ],
    // If using SSE stream, please set it to true, the default is false
    "stream": false
}
```
            
Response data:
```json
{
    // For a native multi-round conversation experience, this id, you can pass in the conversation_id for the next round of conversation to pick up the context
    "id": "bc9ef150d0e44794ab624df958292300-40811965812e4782bb87f1a9e4e2b2cd",
    "model": "qwen",
    "object": "chat.completion",
    "choices": [
        {
            "index": 0,
            "message": {
                "role": "assistant",
                "content": "I'm Qwen."
            },
            "finish_reason": "stop"
        }
    ],
    "usage": {
        "prompt_tokens": 1,
        "completion_tokens": 1,
        "total_tokens": 2
    },
    "created": 1710152062
}
```

