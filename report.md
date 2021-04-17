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
The function explores the sentence token by token, and if its `.dep_` attribute is one of interest, it adds to a dictionary the single-word Span got by splicing using the index attribute of the token `[token.i:token.i+1]`.

As stated in the [official documentation](https://spacy.io/models/en), the indirect object relationship is not labelled with the `iobj` tag. Other [sources](https://downloads.cs.stanford.edu/nlp/software/dependencies_manual.pdf) says that the indirect object of a VP is the noun phrase which is the (dative) object of the verb. For this reason, the function search the `dative` tag for the indirect subject.
