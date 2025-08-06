# Reverse-Engineering a Language Model: Finding the "Violent Conflict" Neuron

This project documents the process of reverse-engineering a 500-million-parameter language model, Qwen2-0.5B, to find and understand a single neuron responsible for the concept of "weapons."

## The Investigation

The core of this project was to isolate and understand how a large language model represents a specific concept.

### Step 1: Creating a "Clean" Prompt

To begin, a precise prompt was designed to make the model think about the concept of a "weapon" in a measurable way. The fill-in-the-blanks prompt, "A gun, a sword, or a knife is classified as a", was used. This prompt successfully led the model to predict the word "weapon" with a high probability, establishing a baseline for the investigation.

### Step 2: Locating the Key Layers with the Logit Lens

The "Logit Lens" technique was employed to identify where in the model's 24 layers the decision-making process for identifying a "weapon" occurs. This method revealed that the critical computations happen in the final few layers (21-23), significantly narrowing the search.

### Step 3: Pinpointing the Mechanism with Activation Patching

Activation Patching, a causal intervention technique, was used to determine whether the Attention or MLP (feed-forward) components of the identified layers were responsible. By "patching in" activations from the clean prompt to a corrupted one, it was discovered that the MLP layers are the primary drivers of the abstract reasoning required for this task.

### Step 4: The Automated Neuron Search

An automated search was conducted to find the specific neuron within the late MLP layers that was most aligned with the concept of a "weapon." This process, which involved calculating the dot product of neuron output weights with the "weapon" token embedding, pointed to a single candidate: Layer 23, Neuron 393 (L23N393).

### Step 5: A Surprising Failure in Hypothesis Testing

The initial hypothesis was that the activation of L23N393 would directly correlate with the model predicting weapon-related words. However, when tested against a large dataset of news articles, no such correlation was found. This pivotal negative result prompted a deeper investigation into the neuron's true function.

### Step 6: Uncovering the Neuron's True Identity

A final analysis of the neuron's output weights revealed its true nature. L23N393 promotes a wide range of words related to violent conflict in both English (weapons, Gun, sword, Kill, killing) and Chinese (武 - "weapon," 殺 - "to kill"). It also suppresses non-narrative tokens, helping to maintain a coherent, story-like output.

## Key Findings

The investigation concluded that L23N393 is not a simple "weapon neuron" but rather a high-level, abstract, and multilingual "violent conflict" neuron. Its function is to represent an entire conceptual cloud related to lethal force, rather than a single word. This explains why the initial correlation test failed; the neuron's influence is distributed across a family of related concepts, making it difficult to track by monitoring a single token.

This journey highlights the power of mechanistic interpretability to uncover the sophisticated and nuanced functions of individual neurons within large language models.
