# NLU assignment n.1 - Report
## Part A - spaCy
The helper function `doc_of(sentence)` is defined because most of the task require getting the Doc of a sentence string.
### Task A1
From the documentation, the best way to match a relation is from below since a token has a single head and multiple children. 
But, since the paths should be extracted from every token, the best strategy is exploring the tree with a BFS search from the root.

When a token is reached, its path (which is then saved in a dictionary) is the concatenation of its head's path, already in the dictionary, with its dependency relation.
### Task A2
The function simpy exploits the `Token.subtree` iterator attribute to create the requested list, which is added to a dictionary
### Task A3
The objective is finding if a sequence of strings is a subtree of a string sentence. The main problems are:
1. The order of the sequence should be taken into account
2. there could be multiple or no occurences of the same word of the sequence in the sentence

To search rapidly if a sentence token text belongs to the sequence, a set with the sequence words is instantiated.
The first word of the sequence works as "anchor" to get the within-sequence head. If the input sequence is a subtree, its root has to be reachable by the anchor.

For each occurrence of the anchor word in the sentence tokens, the within-sequence candidate root is obtained by climbing the tree until its head text is no more in the sequence set.
At this point, the candidate root subtree is compared with the input sequence and the function returns True if they match.
If they don't match, it's possible that the wrong occurrence of the anchor word was checked, so the algorithm continue scanning the tokens for the next anchor occurrence.
### Task A4
The task is simply getting the head/root of a phrase: the Doc element is converted into a full-lenght Span with the slice operation `Doc[:]`, from which the root attribute is returned.
### Task A5
The function explores the sentence token by token, and if its `.dep_` attribute is one of interest, it adds performs the addition to the dictionary: first, it gets the subtree of the token, then it gets the Span of it by slicing the Doc with the indices of the first and the last element of the subtree `[subtree[0].i:subtree[-1].i+1]`.

As stated in the [official documentation](https://spacy.io/models/en), the indirect object relationship is not labelled with the `iobj` tag. Other [sources](https://downloads.cs.stanford.edu/nlp/software/dependencies_manual.pdf) says that the indirect object of a VP is the noun phrase which is the (dative) object of the verb. For this reason, the function search the `dative` tag for the indirect subject.

## Part B - NLTK Transition Parser
The primary obstacle of this task is the lack of NLTK documentation.
### Task B1-B3
Given the design choices of the library, it's easier to change features and model altogether. I kept only the eager algorithm, to simplify the code. I provided as parameter the desired classification model: Support Vector Machine or Multi-Layer Perceptron

The features considered in the original algorithm, each one checked to see if "it is informative", are:
- Stack
  - STK_0_FORM_\<word>
  - STK_0_LEMMA_\<lemma>
  - STK_0_POS_\<tag>
  - STK_0_FEATS_\<feats[0]> , ... , STK_0_FEATS_\<feats[N]>: Never appearing
  - STK_1_POS_\<tag>
  - STK_0_LDEP_\<leftmost dependency>: Never appearing
  - STK_0_RDEP_\<rightmost dependency>: Never appearing
- Buffer
  - BUF_0_FORM_\<word>
  - BUF_0_LEMMA_\<lemma>
  - BUF_0_POS_\<tag>
  - BUF_0_FEATS_\<feats[0]> , ... , BUF_0_FEATS_\<feats[N]>: Never appearing
  - BUF_1_FORM_\<word>
  - BUF_1_POS_\<tag>
  - BUF_2_POS_\<tag>
  - BUF_3_POS_\<tag>
  - BUF_0_LDEP_\<leftmost dependency>: Never appearing
  - BUF_0_RDEP_\<rightmost dependency>: Never appearing

Features are converted into binaries by transforming each string into an unique value followed by a default value of 1.0: "0:1.0 30:1.0 21:1.0 ..."

The function `_check_informative` has been changed to return None if not informative or the feature itself if informative.

I used the FastText libray to convert each word to a vector of length 6. I provided in the data folder the such model to convert the words, that is downloaded during the notebook execution. In the notebook it's also present the snippet I used on my local machine to shrink the 300-features model into a 6-features model.

POS tags have been included in the features by transforming them with a OneHotEncoding

The features I considered are:
- Stack
  - \<word> , ... , \<word> top 2 words of the stack
  - \<words> a discounted (0.6) sum of the rest of the stack
  - \<tag> , ... , \<tag> top 2 tags of the stack
- Buffer
  - \<word> , ... , \<word> first 3 words of the buffer
  - <words> a discounted (0.6) sum of the rest of the buffer
  - \<tag> , ... , \<tag> first 3 tags of the buffer

### Task B2
With a small number of training samples, the performance of the the new parser are well below the performances of the original one, but they are also much faster because the original one is not efficiently implemented.
But, when the MLP parser is trained on a larger amount of training samples (~10 minutes without GPU), it achieves way better performances. If the original parser would be better implemented, it could be trained on more samples and maybe achieve similar performances.

In conclusion, the new features do not seem to be better, but using a neural network classifier leads to a good improvement.

