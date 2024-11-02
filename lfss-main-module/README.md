## Lexical Fuzzy Semantic Search 

The following novel implementation of Lexical Fuzzy Semantic Search to transform information retrieval with an algorithm that accomodates typographical errors as well as nuanced meanings. It initially finds the *m* typographical neighbours for a given input, followed by the *k* nearest semantic neighbours alongside the fuzzy search neighbours.

### Typographical Search Module

This Typographical Python module helps find words similar to a given input word. It works by comparing the input word with a list of words from a large corpus. It uses advanced language models, in this case, _bert-base-uncased_ for tokenisation and encoding of text and to identify words that have a resemblance to the input word. It then returns a list of similar words, sorted by how closely they match the input word.

#### What are we doing?

##### Input

input_string `(str)`: The input string for which typographical neighbors are to be found.\ metric `(str, optional)`: The metric used to calculate the distance between strings and it's set to _edit_distance_ by default.\ model_name `(str, optional)`: The name of the pre-trained language model to be used and it's set to _bert-base-uncased_ by default.

##### Output

typographical_neighbors_list `(List[str])`: A list of typographical neighbors of the input string, sorted by similarity.

Initializing the model: `self.model = AutoModel.from_pretrained(self.model_name).to(self.device)`

-----

### Semantic Search Module

- **SemanticSearch Class:**

  - Imagine being given an extensive book, and you want to find sentences in it similar to your search query.
  - This class performs that. It reads the book (your document) and finds sentences that are similar to your query.

- **find_semantic_neighbors:**

  - When passed a query, this function looks through the sentences in the book to find the ones that resemble the query.

- **_get_embedding:**

  - This function turns a sentence into a computer understandable format.

#### What are we doing?

##### Input

- The `SemanticSearch` class takes several inputs:

  - `model`: The name of the model to be loaded.
  - `tokenizer`: The name of the tokenizer to be loaded.
  - `document`: A list of strings representing the document.
  - `input_text`: A flag indicating whether the input is text or not.
  - `max_seq_length`: The maximum sequence length.

**Note**: The model being used in the SemanticSearch class is _togethercomputer/m2-bert-80M-32k-retrieval_. This model is loaded from the Hugging Face model hub using the AutoModelForSequenceClassification class made clear through thefollowing code snippet: `self.model = AutoModelForSequenceClassification.from_pretrained( model, trust_remote_code=True ).to(self.device)`

##### Output

- The `find_semantic_neighbors` method returns a list of tuples. Each tuple contains the text of a semantic neighbor and its similarity score.
- The `_get_embedding` method returns the embedding (a numerical representation) for the input text.

-----

### Fuzzy Search Module

- The `fuzzy_search.py` module was built to find the fuzzy/approximate words to a given string input. The module will be tied together with `typpgraphical.py` and `semantic_search.py` to find the nearest neighbors.
- The words that are used for finding the neighbors will be taken from the given document. The metric used to determine the closeness between two strings is **Levenshtein distance**.

- #### What are we doing?

  - **Inputs**: `a string / word` and `a document`.
  - **Outputs**: `A list of words that contain the nearest neighbors to the given word.`
  - **Requirements**: `None`

#### How to use this module

- To play around this module, run the following code: 
```python
query_substring = "apple" 
string = "ape apples banana ackle ssna pple" 
length_threshold = 3 
distance_threshold = 3 
n=4
nearest_neighbors_by_threshold = find_nearest_neighbors(query_substring, string, distance_threshold = distance_threshold, length_threshold = length_threshold)
nearest_neighbors_by_number = find_nearest_neighbors(query_substring, string, n = n, length_threshold = length_threshold)
print(f"The nearest neighbors for '{query_substring}' within an edit distance of the {distance_threshold} and a length threshold of {length_threshold} in the string.")
for neighbor, distance in nearest_neighbors_by_threshold: 
  print(f"Neighbor: '{neighbor}' (Edit Distance: {distance})")
  print(f"\n{n} nearest neighbors for '{query_substring}' with a length threshold of {length_threshold} in the string:")
  for neighbor, distance in nearest_neighbors_by_number: 
    print(f"Neighbor: '{neighbor}' (Edit Distance: {distance})") 
```

-----

### Attention Module

- This module's has been purposefully built to return the attention weights, which are a numerical representation of the given input. The module is created for integration with `\typographical_search.py`.
- The goal was to utilize a transformer's encoder block to obtain a word's **attention weights**, a key concept used by them to gain a deeper contextual understanding and understand the role that each word is playing in a sentence.

- #### What are we doing?

  ```
  - **Input type**: `a string of words/ a single word`
  ```

  - **Output type**: `A list of numbers (words)/ a single number, representing the attention weights.`
  - **Transformer used**: `bert-base-uncased`
  - **Requirements**: `nltk, torch, transformers(huggingface)`

#### How to use this module

- If you wish to play around with this module, then run the following code: `

  ```
  model = AttentionModel(input_string="A cat sat on the mat")
        top_tokens = model.get_n_words()
        print("Top tokens:", top_tokens)
  ```