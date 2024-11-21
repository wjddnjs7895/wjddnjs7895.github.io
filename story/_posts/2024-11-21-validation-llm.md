---
layout: post
title: "How I started Building My AI Product as a Solo Developer - Validation (with LLM)"
sitemap: true
image: /assets/img/story/validation-llm/thumbnail.png
hide_last_modified: true
---

- toc
{:toc}

## Before Development

Before diving into the full development process, I needed to validate whether the feature I envisioned was feasible. While I was confident that LLMs' capabilities would make it possible, I still had doubts due to my limited experience with AI development. To overcome this, I started by reading OpenAI’s documentation thoroughly.


---

## OpenAI

### OpenAI Developer Platform

![Full-width image](/assets/img/story/validation-llm/openai-developer-platform.png "OpenAI Developer Platform")
{:.lead width="100%" height="auto" loading="lazy"}
OpenAI Developer Platform
{:.figcaption}

OpenAI’s documentation exceeded my expectations—it was thorough, well-structured, and contained all the information I needed to get started. It gave me a clear roadmap for leveraging the OpenAI API effectively. I plan to share a follow-up post specifically about implementing OpenAI APIs in NestJS, but for now, here’s a summary of my initial exploration.


### Assistant API: Simplifying Conversational AI
> Build AI Assistants with essential tools and integrations.

![Full-width image](/assets/img/story/validation-llm/assistant-api.png "Assistant API")
{:.lead width="100%" height="auto" loading="lazy"}
Assistant API Logic
{:.figcaption}

The **Assistant API** stood out as a user-friendly approach to building conversational LLM applications. It offered memory optimization within token limits by automatically discarding unnecessary older data. Additionally, its straightforward thread-based operation made it easy to implement.

However, the API had a significant limitation for my project: it was still in beta and didn’t support audio input as of November 2024. This posed a dilemma—should I use the Assistant API to speed up development and forgo audio input, or stick with more basic API functions that offered flexibility at the cost of additional complexity? After careful consideration, I decided to use OpenAI’s basic API functions to future-proof my project while getting accustomed to core LLM capabilities.


### Managing LLM Costs

As highlighted in an earlier post, cost was a key factor in this project. Initially, I considered using OpenAI’s **Real-time API** for fully natural audio conversations. However, I quickly realized it would cost approximately **$0.50–$0.80 per minute**—far too expensive for sustained use.


I hypothesized that reducing audio input/output usage could cut costs. By allowing users to control their input time, I expected some savings. However, the results were disheartening. Although input costs decreased slightly, the main expense came from generating audio responses.

To address this, I switched to text-based responses and converted them to audio using OpenAI’s **Whisper-1** model. While this approach sacrificed the emotional nuance of direct audio responses, it significantly reduced costs. OpenAI’s TTS (Text-to-Speech) models were far cheaper but required additional processing time due to the need for sequential requests (text generation first, followed by TTS).

![Full-width image](/assets/img/story/validation-llm/whisper-1.png "Whisper-1")
{:.lead width="100%" height="auto" loading="lazy"}
Whisper-1
{:.figcaption}


### Writing Effective Prompts and Restricting Response Types

Once I finalized the core API functions, the next step was designing the AI prompt. Writing prompts was relatively straightforward—clear and concise instructions were all I needed to guide the model.

>**Key Features:**
> 1. **Japanese Conversation AI:** I aimed to build an AI capable of natural conversations in Japanese.
> 2. **Scenario Generation:** The AI also needed to create situations and missions for role-playing exercises.

While building the conversational AI was simple, restricting the response type posed a challenge. Here, OpenAI’s **Function Calling** feature proved invaluable.

![Full-width image](/assets/img/story/validation-llm/function-calling.png "Function Calling")
{:.lead width="100%" height="auto" loading="lazy"}
Function Calling Logic
{:.figcaption}

With Function Calling, I defined specific response formats by restricting the output to an object type. This approach allowed me to extract only the data I needed without additional processing.


![Full-width image](/assets/img/story/validation-llm/api-response.png "API Response")
{:.lead width="100%" height="auto" loading="lazy"}
Actual API Response
{:.figcaption}

---

## Final Thoughts

While the journey was full of challenges, including cost optimization and overcoming limitations of beta features, I made significant progress in understanding LLM capabilities. These insights laid the groundwork for my next steps in development. I’ll share detailed code examples and implementation strategies in future posts.