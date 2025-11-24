# TextRankSummaries

## Overview 
This project implements the TextRank algorithm using MapReduce to automatically summarize fictional works by H.P.Lovecraft. 

We then evaluated our generated summaries against human-written summaries using ROUGE metrics and demonstrate that extractive summarization performs poorly on fiction due to limited vocabulary overlap between sentences. 

### Graph Creation 
We want to create a graph-based representation of the text. 
The representation will be like this: 
   - Vertices become sentences.
   - Edges are similarity between sentence pairs.
   - Edge weights are the cosine similarity of TF-IDF embeddings.

First, to get cosine similarity, we need text embeddings. We created a NLP pipeline to do so via Spark Mllib. 
The pipeline follows these steps: 
   - Tokenization -> splits each sentence into a bag of words representation
   - HashingTF -> converts tokens into raw frequency vectors
   - IDF -> scales each vector by inverse document frequency at sentence level
   In this, each sentence is treated as its own document.
A cross-join is performed over all sentence IDs and their TF-IDF vectors.
A UDF computes pairwise cosine similarity for each sentence pair.
The final dataframe forms an adjacency list representation of the text graph.

### Distributed TextRank Model via MapReduce
Input: An RDD containing the adjacency list of text graph 

Parameters: 
  - dampening factor (d)
  - tolerance (for convergence)
  - maximum iterations

Function:
Iterate until the L₁ norm of the difference between score vectors falls below the tolerance, or the iteration limit is reached.

Output: 
Top 5 sentence IDs with the highest TextRank scores. These sentences are the final summary. 

### Evaluation via ROUGE 
ROUGE outputs several metrics: 
  - ROUGE-1 -> unigram overlap
  - ROUGE-2 -> bigram overlap
  - ROUGE-L -> longest common subsequence (LCS)
  - ROUGE-W -> weighted LCS

## Conclusion
Because extractive summarization can only reuse exact words and sentences from the source text, the vocabulary mismatch in fiction leads to poor ROUGE performance, which highlights the  limitations of TextRank for narrative prose.


Refer to the file `CS_6307_Project_Final_TextRankSummaries.pdf` for full paper. 


  




   
