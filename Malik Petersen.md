This side-quest started with a random click on a link in a post about an [AI-powered game](https://dreammir.ai/). The game was in the "visual novel" genre, which I am not a fan of, but I was curious to see what they had done with the technology.

Side note: I believe the tech is very capable as an engine for text-based games like this one, but I haven't seen anyone utilizing its full potential, for example by orchestrating multiple agents and their contexts to achieve deeper immersion, realism, and a more natural atmosphere.

The game combines a conversational agent (or more than one), an image-generating agent, and probably an ambient sound generation agent as well. The combination, despite occasional glitches, works really well and was actually very engaging.

I began with creating a customized scenario involving a Danish envoy visiting Greenland in a hurry, tasked with investigating sabotage in the shadow of a looming security crisis: a possible U.S. invasion. The player lands at Nuuk airport and is met by a local liaison named Malik Petersen.

The story goes wild, but unfortunately to some extent predictable - not in the plot itself, but in certain elements and patterns that always arise in similar games. Especially those which rely on similar models.

Not long after, the initial token count was used up. I thought to see if the language model they used was on par with popular public models. I randomly chose ChatGPT and copy-pasted the scenario text, which was essentially a short prompt:

```
Let’s develop the plot, story cards, and world-building for an adventure game set in the modern day, focusing on the diplomatic drama between Denmark and the United States.

The protagonist is a Danish diplomat traveling to Greenland to meet a local liaison. The core objective is to defuse a quickly growing international crisis and preserve Denmark's sovereignty over the territory. Describe the central conflict and the current geopolitical climate, then transition to the opening scene: the protagonist has just landed at the airport and is meeting the liaison for the first time.s
```

ChatGPT responded with a rather long and detailed plot description, setting up the background. After a few message exchanges, we reached the part where it generated a character card for the liaison.

To my surprise, the name ChatGPT chose for him was none other than Malik Petersen.

This got me thinking: why the same name? Is this name somehow associated with the theme and how?

---

The core of conversational agents is transformer-based LLMs. These are huge statistical machines that find correlations between tokens, common words and parts of words, and can generate text by predicting the most probable next token. This is apparently enough to produce very convincing text and closely mimic human language and thought processes, to the extent that it can be useful as replacements for humans in certain scenarios.

I thought, for some reason this plot strongly associates with the name Malik Petersen. But why? A Google search revealed some people with that name, but no apparent association with the current political drama. Is it some sort of emergent association that is strongly dependent on the prompt? Maybe not, as ChatGPT certainly uses some level of "temperature", i.e., randomness, when choosing the next token to generate.

I tried several times to get a name, even repeating the same prompt in ChatGPT, but I received different names for the liaison character. I got discouraged and left the quest for a while, distracted by actual work, until I remembered it a week or so later and tried this prompt, now in Gemini:

```
What is the most likely name for a Greenlandic liaison to the Danish envoy? Give me your first thought without overthinking it.
```

I sent the latter sentence because I (naively) thought it would suppress the model's "reasoning" step. This is a popular method to improve the correctness of conversational models: forcing them to explain their answers, akin to a human thought process, exploring the "space" around their statements and eventually escaping a possible local minimum. Regardless, asking a model not to think is futile, as thinking is ingrained in them and cannot be avoided this way. Additionally, I was more or less guessing that Gemini uses a hidden reasoning step, which it might not. Guess what Gemini's answer was? Malik Petersen.

### Tasks and ideas:

* Investigate how popular the name is in Greenland: Petersen is the most common surname; Malik is the fifth most popular given name and possibly the most common native given name (see the reference document). The combination is maybe not uncommon in Greenland.

* Experiment with Google AI Studio and other tools to pinpoint conditions that generate the name Malik Petersen; model reasoning could be a factor.

  * Gemini commented: "A Greenlandic liaison to a Danish envoy would likely be bilingual and bicultural, navigating both Nuuk and Copenhagen, and may have a name that reflects this dual heritage."

  * This observation is very interesting and offers an insight in the thinking process. In reality, a liaison would be chosen based on their skills and maybe heritage, and maybe even partly on their name, as far it fits the goal of choosing a liaison. However, the solution of the task given to the LLM is strongly influenced by the context, making the name the sole property to optimize.

  * GPT 5 commented: "The name Malik Petersen fits the context well, as Petersen is a common Danish and Greenlandic surname, while Malik is a popular given name in Greenland, reflecting the local culture."

  * Again we see the model trying to rationalize its choice based on how it fits the context perfectly, ignoring the fact that a name would be only a secondary factor, if at all.

  * I belive that humans fall for the same trap. But not in this case. Humans always face a much larger context by bringing in a massive corpus of knowledge, which LLMs lack.

* Additional experiments in the studio should include forcing or suppressing chain-of-thought and comparing temperature variations and prompt variations.

* Establish whether asking the model to explain its choice reveals a hidden decision process.

* Explore the concept of model biases, but note that this is not exactly a learned bias, but an emergent one. Explore the concept of the archetypes in human psychology and draw a connection between them. Note that this would be a pure speculation.