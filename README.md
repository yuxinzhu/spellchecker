#SpellChecker

`python2.7 spellchecker.py`

__Note__: This is not meant to be a generic spell-checker, but one that meets the case requirements of the problem below.

###Problem

Write a program that reads a large list of English words (e.g. from /usr/share/dict/words on a unix system) into memory, and then reads words from stdin, and prints either the best spelling suggestion, or "NO SUGGESTION" if no suggestion can be found. The program should print ">" as a prompt before reading each word, and should loop until killed.

Your solution should be faster than O(n) per word checked, where n is the length of the dictionary. That is to say, you can't scan the dictionary every time you want to spellcheck a word.

For example:

	> sheeeeep
	sheep
	> peepple
	people
	> sheeple
	NO SUGGESTION
	The class of spelling mistakes to be corrected is as follows:

Case (upper/lower) errors: "inSIDE" => "inside"
Repeated letters: "jjoobbb" => "job"
Incorrect vowels: "weke" => "wake"
Any combination of the above types of error in a single word should be corrected (e.g. "CUNsperrICY" => "conspiracy").

If there are many possible corrections of an input word, your program can choose one in any way you like. It just has to be an English word that is a spelling correction of the input by the above rules.

Final step: Write a second program that *generates* words with spelling mistakes of the above form, starting with correctly spelled English words. Pipe its output into the first program and verify that there are no occurrences of "NO SUGGESTION" in the output.

### Run

SpellChecker is a simple spell checking program written in Python 2.7. To run, type:

    python2.7 spellchecker.py

SpellChecker supports two main functionalities. Typing a word will automatically return the first corrected suggestion found in `/usr/share/dict/words`:

	> sheeeeep
	sheep
	> acceptible
	acceptable
	> ignorence
	ignorance
	> yuxin
	NO SUGGESTION

Typing a correctly spelled word, followed by `--verify` will generate all possible mis-spellings of a word, and spell-check each one.

	> awesome --verify
	awasama
	awasamaa
	awasamma
	awasammaa

	…

	uuwwuussuumuu
	uuwwuussuummu
	uuwwuussuummuu


	Success! All 80,000 possible mis-spellings were successfully generated and spell-checked.


Typing `--exit` at any time will quit the program.


###Implementation Details



__[Note to Reader]__ Below, the word, "permutation", is used interchangeably with cartesian product for ease of use, such that "permutation" on `{1,2}` is equivalent to `product('12', repeat=2) = [('1', '1'), ('1', '2'), ('2', '1'), ('2', '2')]`

The program first loads a list of words of length, `n`, into a set whose initial overhead takes `O(n)` time, but whose amortized lookup time is `O(1)`.

Suppose the user inputs a word of length, `m`, with `v` vowels. The program first generates a list of possible words permuted on the number duplicate characters, such that the word `"teepee"` results in `['tepe', 'tepee', 'teepe', 'teepee']`. Permutations of duplicates are generated using `itertools.product()`. The overall time complexity of this function is roughly `O(2^d + m)` since the dominant operation iterates through all elements in the cartesian product of length `{1,2}`, repeated `d` times, where `d` is the number of duplicate instances (ie. teepee has two duplicate instances [2^2]).

We then iterate through this list, and for each word, generate a list of additional words with each vowel permutated with all possible vowels. For instance, the word `"cat"`, would return `['cat', 'cit', 'cet', 'cot', 'cut']`. This is implemented similarly to the above function, and the overall time complexity of this function by analogy is `O(5^v + m)`, since the operation iterates through all elements in the cartesian product of `{a, e, i, o, u}`, repeated over `v`, where `v` is the number of vowels.

An alternative implementation uses tree recursion, and recursively generates a list of possible words by permutating through the first vowel, then branching out to permutate through the second vowel, and so on. I also implemented the algorithm this way, and found that their running times were comparable, but the iterative version worked slightly better with words with more vowels. Generating the list iteratively also allows us to more easily terminate the function immediately once a match is found in the word list.

The overall time complexity of this function can be calculated as the product of the above two functions: O(2^d * 5^v). Observe that the values of `d` and `v` are usually less than five.
