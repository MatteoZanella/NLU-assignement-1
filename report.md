# NLU assignment n.1 - Report
## Part A - spaCy
The helper function `doc_of(sentence)` is defined, because most of the task require getting the Doc of a sentence string.
### Task A1
From the documentation, the best way to match a relation is from below since a token has a single head and multiple children. 
Since the paths should be extracted from every token, the best strategy is exproring the tree with a BFS search from the root.

When a token is reached, its path (which is saved in a dictionary) is the concatenation of its head's path, already in the dictionary, with its dependency relation.
### Task A2
The function simpy exploits the `Token.subtree` iterator attribute to create the requested list, which is added to a dictionary
### Task A3
The objective is finding if a sequence of strings is a subtree of a string sentence. The main problems are:
1. The order of the sequence should be taken into account
2. there could be multiple or no occurences of the same word of the sequence in the sentence

To search rapidly if a sentence token text belongs to the sequence, a set with the sequence words is instantiated.
The first word of the sequence works as "anchor" to get the within-sequence head. If the sequence is a subtree, its root has to be reachable by the anchor.

For each occurrence of the anchor word in the sentence tokens, the within-sequence candidate root is obtained by climbing the tree until the head text is no more in the sequence set.
At this point, the candidate root subtree is compared with the input sequence and the function returns True if they match.
If they don't match, it's possible that the wrong occurrence of the anchor word was checked, so the algorithm continue scanning the tokens for the anchor.
