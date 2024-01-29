# getting-started-with-openai

### Knowing the Models
* `davinci`: `davinci-...` *fine-tuning* models are very useful to use as chat agent. When trained or fine-tuned with some plain instruction or data, it plays the role as instructed.

### Important Links
* Text generation Models Api documentation: https://platform.openai.com/docs/guides/text-generation
* OpenAI API Parameters descriptions: https://platform.openai.com/docs/api-reference/chat/create
* API Documentation link on Fine-tuning: https://platform.openai.com/docs/guides/fine-tuning
* Deprecated models: https://platform.openai.com/docs/deprecations

### Sample JSON Responses

#### Error Response
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

