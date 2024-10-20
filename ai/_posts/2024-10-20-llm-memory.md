---
layout: post
title: "Unlocking Long-Term Memory in LLMs: Strategies for Effective Memory Management"
sitemap: true
image: /assets/img/ai/llm_long_term_memory/thumbnail.png
hide_last_modified: true
---

- toc
{:toc}

## Introduction

LLMs are designed to retain only the immediate request context, requiring the entire conversation log to be included within a single request. While this capability is powerful, it is limited by token constraints. Therefore, it is essential to develop a new approach to enable long-term memory for LLMs.

---
## Memory Management Methodology

- Include conversation logs within the context
- Embed logs for efficient storage
- Categorize memory into periods (short-term, medium-term, long-term)

## Considerations

- Determine the optimal number of recent messages to include in the context
- Decide on the log embedding strategy (raw messages vs. summarized content)
- Establish criteria for executing the function to categorize memory
- Define standards for memory categorization
- Differentiate between memory types across periods

---

The concept is straightforward: I utilize the request context in two ways: Pure Recent Messages and Saved Messages.

### Request Context

It is crucial to determine how many recent messages to include. Given that modern LLMs provide ample token capacity, we have sufficient volume for messages. However, failing to consider the token count can lead to significantly higher costs, making cost-efficiency a priority. The exact number of messages to include may vary based on the specific use case, as the length of conversations often depends on the topic or context.

### Memory with Embedding

To effectively implement memory, we should leverage the **RAG technique** in conjunction with embedding vectors. Messages are stored in their embedded form, allowing for efficient retrieval. When messages are requested, the logic compares relevant messages based on their embeddings. For more information about embedding, refer to the ChatGPT API documentation: [OpenAI Embeddings Guide](https://platform.openai.com/docs/guides/embeddings).


However, embedding all messages poses a significant challenge: it can lead to inefficiencies as our model may retain information that is not pertinent. Therefore, we must **differentiate messages based on their importance and recency**. I discovered a paper that presents a formula for calculating message importance, which is quite insightful. I encourage you to take a look: [ACM Paper on Message Importance](https://dl.acm.org/doi/abs/10.1145/3586183.3606763)

> From this point forward, I propose a strategy. Similar to how humans gradually lose memory of older information, I envision summarizing memories using LLMs. For mid-term memory, we can assign an importance score with the LLM whenever we save data. As time progresses, older memories may become less relevant, prompting us to summarize this outdated data and store it in long-term memory.


---


## Conclusion

Effectively managing memory within LLMs is a multifaceted challenge that requires careful consideration of contextual limitations and the importance of message relevance. By leveraging techniques such as RAG and embedding vectors, we can enhance the efficiency and effectiveness of memory management. As we explore various strategies, including distinguishing between message types and implementing importance scoring, we pave the way for more sophisticated conversational agents.

Moving forward, I plan to publish articles detailing the actual development process, focusing on the challenges of contextual limitations and providing a comparative analysis of memory management techniques for LLMs. Through this journey, I aim to share insights gained along the way and contribute to the creation of more effective conversational agents.
