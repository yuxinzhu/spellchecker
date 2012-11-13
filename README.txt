#SpellChecker

### Run

SpellChecker is a simple spell checking program written in Python 2.7. To run, type:

    python2.7 spellchecker.py

SpellChecker supports two main functionalities. Typing a word will automatically return the first corrected suggestion found in `/usr/share/dict/words`:

	> sheeeeep
	sheep
	> yuxin
	NO SUGGESTION

Typing a correctly spelled word, followed by `--verify` will generate all possible mis-spellings of a word, and ensures that there are no occurences of "NO SUGGESTION" in the output.

	> awesome --verify
	awasama
	awasamaa
	awasamma
	awasammaa
	
	…
	
	uuwwuussuumuu
	uuwwuussuummu
	uuwwuussuummuu


	Success! All 80000 possible mis-spellings were successfuly spell checked.


Typing `--exit` at any time will quit the program.


###Implementation Details



_[Note to Reader] Below, the word, "permutation", is used interchangeably with cartesian product for ease of use, such that "permutation" on `{1,2}` is equivalent to `product('12', repeat=2) = [('1', '1'), ('1', '2'), ('2', '1'), ('2', '2')]`_

The program first loads a list of words of length, `n`, into a set whose initial overhead takes `O(n)` time, but whose amortized lookup time is `O(1)`. 

Suppose the user inputs a word of length, `m`, with `v` vowels. The program first generates a list of possible words permuted on the number duplicate characters, such that the word `"teepee"` results in `['tepe', 'tepee', 'teepe', 'teepee']`. Permutations of duplicates are generated using `itertools.product()`. The overall time complexity of this function is roughly `O(2^d * m)` since the dominant operation iterates through all elements in the cartesian product of `{1,2}`, repeated over `d`, where `d` is the number of duplicate instances (ie. teepee has two duplicate instances).

We then iterate through this list, and for each word, generate a list of additional words with each vowel permutated with all possible vowels. For instance, the word `"cat"`, would return `['cat', 'cit', 'cet', 'cot', 'cut']`. This is implemented similarly to the above function, and the overall time complexity of this function by analogy is `O(5^v * m)`, since the dominant operation iterates through all elements in the cartesian product of `{a, e, i, o, u}`, repeated over `v`, where `v` is the number of vowels. As above, the nested `m` value, accounts for the inner loop, where we generate variations of the word based on the cartesian product.

An alternative implementation uses tree recursion, and recursively generates a list of possible words by permutating through the first vowel, then branching out to permutate through the second vowel, and so on. I also implemented the algorithm this way, and found that their running times were comparable, but the iterative version worked slightly better with words with more vowels. Generating the list iteratively also allows us to more easily terminate the function immediately once a match is found in the word list.

The overall time complexity of this function can be calculated as a sum of the above two functions: O(m(2^d + 5^v)). Observe that the values of `d` and `v` are usually less than five.