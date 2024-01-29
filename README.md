# getting-started-with-openai

### Knowing the Models
* `davinci`: `davinci-...` *fine-tuning* models are very useful to use as chat agent. When trained or fine-tuned with some plain instruction or data, it plays the role as instructed.

### Important Links
* Text generation Models Api documentation: https://platform.openai.com/docs/guides/text-generation
* OpenAI API Parameters descriptions: https://platform.openai.com/docs/api-reference/chat/create
* API Documentation link on Fine-tuning: https://platform.openai.com/docs/guides/fine-tuning
* Deprecated models: https://platform.openai.com/docs/deprecations

### Model Comparison
Models tested:
* gpt-3.5-turbo-1106 (chat model)
* gpt-3.5-turbo-instruct (completion model. Kinda like "ask a question, no chat")
* davinci-002 (completion model; also base model)

Temperature: decimal, 0.0 to 2.0. Low value means more consistent answer. Higher value means more diverse and creative answer.

```js
const OpenAI = require('openai')
const article = 'First, questions should be formulated; then answers should be searched; based on the answers, conclusion(s) should be made. Our life consists of thousands of big and small but important conclusions. But how many of them are the result of question-answer-conclusion process?'
const openai = new OpenAI({ apiKey: process.env.OPENAI__API_KEY })
openai.chat.completions.create({
  model: 'gpt-3.5-turbo-1106',
  messages: [
   { role: 'system', content: 'Please answer in JSON format.'},
   { role: 'user', content: `Please suggest a simple title for the following article. Also please suggest types, categories of this article and extract keywords. Here's the article:\n\n${article}` }
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
      model: 'davinci-002', // davinci-002 / gpt-3.5-turbo-instruct / text-davinci-003 (deprecated)
      prompt: `Please suggest a simple title, types and categories for the following article. Respond in json. Here's the article:\n\n${article}`,
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
**Response**  
`"{\n  \"title\": \"The Importance of Question-Answer-Conclusion Process in Decision Making\",\n  \"types\": [\"opinion\", \"reflection\"],\n  \"categories\": [\"decision making\", \"critical thinking\"],\n  \"keywords\": [\"question\", \"answer\", \"conclusion\", \"decision making\", \"critical thinking\", \"process\"]\n}"`

Temperature: 0.7
Response: 
```
"{\n  \"title\": \"The Importance of Question-Answer-Conclusion Process in Decision Making\",\n  \"types\": [\"Opinion\", \"Reflection\"],\n  \"categories\": [\"Decision Making\", \"Critical Thinking\"],\n  \"keywords\": [\"question\", \"answer\", \"conclusion\", \"decision making\", \"critical thinking\", \"process\"]\n}"
```

Temperature: 1.5
Response: 
```
"{\n  \"suggested_title\": \"The Art of Formulating Questions, Seeking Answers, and Drawing Conclusions\",\n  \"types\": [\"Opinion\", \"Philosophical\", \"Self-Reflection\"],\n  \"categories\": [\"Philosophy\", \"Personal Development\", \"Decision-Making\"],\n  \"extracted_keywords\": [\"questioning\", \"searching for answers\", \"concluding\", \"life decisions\"]\n}"
```

Temperature: 2.0
Response: 
```
"{\n  \"title\": \"The Power of Questioning and Applying Conclusions in Daily Life\",\n  \"types\": [\"philosophical\", \"relatable\"],\n  \"categories\": [\"personal growth\", \"self-reflection\", \"mindfulness\"],\n  \"keywords\": [\"questioning\", \"answers\", \"conclusions\", \"daily life\", \"self-improvement\"]\n}"
```

Model: gpt-3.5-turbo-instruct
Temperature: 0.7
Response: 
```
"\n\n{\n  \"title\": \"The Importance of Formulating Questions before Reaching Conclusions\",\n  \"types\": [\"Opinion\", \"Advice\"],\n  \"categories\": [\"Personal Development\", \"Critical Thinking\", \"Decision Making\"]\n}"
```

Here’s the funny one. I read about efficient results of “text-davinci-003” as fine-tune model. So I had an admiration for “davinci” model. Since text-davinci-003 is deprecated so I used the available base model davinci-002. Take a look its responses.

Model: davinci-002
Temperature: 02
Response:
```
" We are so much in a hurry to reach the destination that we forget the journey. We are so much in a hurry to reach the destination that we forget the journey. We are so much in a hurry to reach the destination that we forget the journey. We are so much in a hurry to reach the destination that we forget the journey. We are so much in a hurry to reach the destination that we forget the journey. We are so much in a hurry to reach the destination that we forget the"
```

Temperature: 1.0
```json
" Very few!\n\nCategories:\n\nhttps://www.geeksforgeeks.org/what-is-your-strength/\n\nTitle: What strengths do you bring to this job?\n\nQuestion: Tell me about a time when you were a member of an effective team at work. What role did you play?\n\nQuestion: Describe your leadership style. When have you been the most effective as a leader? When have you been the least effective?\n\nQuestion: Please describe a recent difficult team project and how you addressed it.\n\nQuestion: Answer"
```


### Sample JSON Responses

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
            "content": "{\n  \"title\": \"The Art of Formulating Questions and Seeking Answers\",\n  \"types\": [\"opinion\", \"self-improvement\"],\n  \"categories\": [\"productivity\", \"personal development\"],\n  \"keywords\": [\"questions\", \"answers\", \"conclusions\", \"formulation\", \"decision-making\"]\n}"
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
