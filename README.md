# getting-started-with-openai

### Important Links
* Text generation Models Api documentation: https://platform.openai.com/docs/guides/text-generation
* OpenAI API Parameters descriptions: https://platform.openai.com/docs/api-reference/chat/create
* API Documentation link on Fine-tuning: https://platform.openai.com/docs/guides/fine-tuning
* Deprecated models: https://platform.openai.com/docs/deprecations

### Model Comparison
Couple of models are tested to generate article title.

**Models tested**  
* gpt-3.5-turbo-1106 (chat model)
* gpt-3.5-turbo-instruct (completion model. Kinda like "ask a question, no chat")
* davinci-002 (completion model; also base model)

**Temperature**: decimal, 0.0 to 2.0. Low value means more consistent answer. Higher value means more diverse and creative answer.

```js
const OpenAI = require('openai')
const article = 'First, questions should be formulated; then answers should be searched; based on the answers, conclusion(s) should be made. Our life consists of thousands of big and small but important conclusions. But how many of them are the result of question-answer-conclusion process?'
const openai = new OpenAI({ apiKey: process.env.OPENAI__API_KEY })
openai.chat.completions.create({
  model: 'gpt-3.5-turbo-1106',
  messages: [
   { role: 'system', content: 'Please answer in JSON format.'},
   { role: 'user', content: `Please suggest a simple title for the following article. Here's the article:\n\n${article}` }
  ],
  response_format: { type: 'json_object' },
  temperature: 0.7
})
.then(console.log)
.catch(console.log)
```


API to request to completion models (`gpt-3.5-turbo-instruct`, `davinci-002`):
```js
function ask() {
   const article = 'First, questions should be formulated; then answers should be searched; based on the answers, conclusion(s) should be made. Our life consists of thousands of big and small but important conclusions. But how many of them are the result of question-answer-conclusion process?'
   
   const openai = new OpenAI({ apiKey: process.env.OPENAI__API_KEY })
   openai.completions.create({
      model: 'gpt-3.5-turbo-instruct', // davinci-002 / gpt-3.5-turbo-instruct / text-davinci-003 (deprecated)
      prompt: `Please suggest a simple title for the following article:\n\n${article}`,
      temperature: 1.0,
      max_tokens: 100
   })
      .then(console.log)
      .catch(console.log)
}
```

Here are the different answers from the different models with different temperature level.

#### Model: gpt-3.5-turbo-1106
**Temperature**: 0.2  
**Response**: `"The Importance of Question-Answer-Conclusion Process in Decision Making"`

Temperature: 0.7  
Response: `"The Importance of Question-Answer-Conclusion Process in Decision Making"`

**Temperature**: 1.5  
**Response**: `"The Art of Formulating Questions, Seeking Answers, and Drawing Conclusions"`

**Temperature**: 2.0  
**Response**: `"The Power of Questioning and Applying Conclusions in Daily Life"`

**Model: gpt-3.5-turbo-instruct**
**Temperature**: 0.7 
**Response**: `"The Importance of Formulating Questions before Reaching Conclusions"`

Here’s the funny one. Since `text-davinci-003` is deprecated, so the available base model `davinci-002` was used.

**Model: davinci-002**
**Temperature**: 02  
**Response**: `" We are so much in a hurry to reach the destination that we forget the journey. We are so much in a hurry to reach the destination that we forget the journey. We are so much in a hurry to reach the destination that we forget the journey. We are so much in a hurry to reach the destination that we forget the journey. We are so much in a hurry to reach the destination that we forget the journey. We are so much in a hurry to reach the destination that we forget the"`

**Temperature**: 1.0  
**Response**: `" Very few!"`

### Sample JSON Response

**Success response**
```json
{
   "id": "...",
   "object": "chat.completion",
   "created": 1706538723,
   "model": "gpt-3.5-turbo-1106",
   "choices": [
      {
         "index": 0,
         "message": {
            "role": "assistant",
            "content": "{\n  \"title\": \"The Art of Formulating Questions and Seeking Answers\"\n}"
         },
         "logprobs": null,
         "finish_reason": "stop"
      }
   ],
   "usage": {
      "prompt_tokens": 89,
      "completion_tokens": 65,
      "total_tokens": 154
   },
   "system_fingerprint": "..."
}
```


**Error Response**
```json
{
   "status": 404,
   "headers": {
      "alt-svc": "h3=\":443\"; ma=86400",
      "cf-cache-status": "DYNAMIC",
      "cf-ray": "...",
      "connection": "keep-alive",
      "content-encoding": "gzip",
      "content-type": "application/json; charset=utf-8",
      "date": "Mon, 29 Jan 2024 13:32:42 GMT",
      "server": "cloudflare",
      "set-cookie": "...",
      "strict-transport-security": "max-age=15724800; includeSubDomains",
      "transfer-encoding": "chunked",
      "vary": "Origin",
      "x-request-id": "..."
   },
   "error": {
      "message": "The model `text-davinci-003` has been deprecated, learn more here: https://platform.openai.com/docs/deprecations",
      "type": "invalid_request_error",
      "param": null,
      "code": "model_not_found"
   },
   "code": "model_not_found",
   "param": null,
   "type": "invalid_request_error"
}
```


### Notes
* To give you an idea of how `temperature` and `Top_p` parameters can be used in different scenarios, here’s a table with example values:

| Use Case |	Temperature |	Top_p |	Description
|---|--|--|--|
| Code Generation |	0.2 |	0.1 |	Generates code that adheres to established patterns and conventions. Output is more deterministic and focused. Useful for generating syntactically correct code.|
|Creative Writing |	0.7 |	0.8 |	Generates creative and diverse text for storytelling. Output is more exploratory and less constrained by patterns.|
|Chatbot Responses |	0.5 |	0.5 |	Generates conversational responses that balance coherence and diversity. Output is more natural and engaging.|
|Code Comment Generation |	0.3 |	0.2 |	Generates code comments that are more likely to be concise and relevant. Output is more deterministic and adheres to conventions.|
|Data Analysis Scripting |	0.2 |	0.1 |	Generates data analysis scripts that are more likely to be correct and efficient. Output is more deterministic and focused.|
|Exploratory Code Writing |	0.6 |	0.7 |	Generates code that explores alternative solutions and creative approaches. Output is less constrained by established patterns.|

* How to request for JSON response
  1. On initial system message, instruct to produce JSON. E.g. "Please wrap you asnwer in well formatted JSON.".
  2. Also use `response_format` parameter. `response_format: { type: 'json_object' }`.
