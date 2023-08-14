# Q2: Implement the search method for `StreamingService` 

## Background

for the method `get_show()` in `StreamingService`, we require users to provide the exact title of the show in order to be able to get the corresponding show / show details. In reality, however, we do not search in this way. Think about how you search on Google or Netflix:
  * (i) The case of the words should not matter
  * (ii) Often we do not use the whole name to search for a show
    * For example, for the show `"The Queen's Gambit"`, users may just type the relatively distinctive word `"gambit"`
  * (iii) We may even search a show just based on some features
    * For example, user may just type `"chess"` (what the show is about) or `"Anya"` (the main actress) when searching the show
  * (iv) Users may not even spell the words correctly (!), or the words entered are only partially matched (e.g. `queen` instead of `queen's`)
  * (v) As the search is no longer exact, it is likely that the search engine will return multiple possible results (rather than just one) and often the result is sorted by based on some criteria (e.g. by relevance, popularity of the show or the watch history)

While (i) is easy to address (we have done something similar in the workshops), (ii) and (iii) require some more work. Essentially, it is not enough to just perform a simple string comparison or use the title of the show as a key (if `dict` is used) to retrieve the corresponding record. A possible (but naive) way to address (ii) and (iii) is to look at the details of each show and see if there is any match. The fourth point is more troublesome and we are not going to handle it in this project.

In this part of the project, we are going to build a "smarter" search engine for `StreamingService` with the use of:
1. _Inverted index_
2. Search with multiple keywords

Below we provide further details of each of the elements. Note that while below shows you how to implement the search engine in 2 steps, you can do it in one go if you want to.

---

### Step 1. build an inverted index

[Inverted index](https://en.wikipedia.org/wiki/Inverted_index) (or "index" for short) maps word/term to its location. An index for a search engine is similar to the index of words in the back of a book like the following:

<center><img src="figs/index.png" width="600"/></center>

For example, the index above tells you that you can find the content related to/mentioning the term "class variable" on p.119 of the book. For book indexing, the words are also sorted in alphabetical order so we can search where the word of interest is in the index easily.

Below we show you an _overly simplified_ example of an index, with the use of the Netflix show data for illustrative purpose. For simplicity, we assume we only have 2 shows: `"The Queen's Gambit"` and `"Magnus"`. We further assume we only use the "title" and the "description" of the shows to build the indexing.

The data we consider in this _overly simplified_ example is:

|        | show 1              | show 2  |
|--------|---------------------|---------|
| Title  | The Queen's Gambit  | Magnus  |
| Description | In a 1950s orphanage, a young girl reveals an astonishing talent for chess and begins an unlikely journey to stardom while grappling with addiction | This dramatic documentary explores the life and mind of Magnus Carlsen, the charismatic Norwegian prodigy who became world chess champion |

Now we build the index mapping the terms to the location of the show. For simplicity we assume the location for the show `The Queen's Gambit` is 1 and 2 for `Magnus`. With the given data, the index should look like the following:

| terms    | location |
|----------|----------|
| the      | 1        |
| queen's  | 1        |
| gambit   | 1        |
| in       | 1        |
| a        | 1        |
| 1950s    | 1        |
| ...      | ...      |
| chess    | 1, 2     |
| and      | 1, 2     |
| begins   | 1        |
| ...      | ...      |
| magnus   | 2        |
| this     | 2        |
| dramatic | 2        |
| ...      | ...      |
| champion | 2        |

Note:
* Everything has converted into lowercase with punctuation at the front or back of the words removed for more convenient comparison later
* You do NOT need to have the "location" to be some numerical number - you have the freedom to choose how to represent the location of the show

After such index is built, we can use it to provide the search results quickly. For example, if the user searches "Gambit", the search engine then just looks up the term "gambit" (as case of the term does not matter) from the table and finds the location of the show is 1. It therefore returns the show corresponding to such location, which is a show with the title "The Queen's Gambit". If the user searches "Norwegian", then it returns the show with the title "Magnus". If the user searches "chess", then it should return both the show with the title "The Queen's Gambit" and and the show with the title "Magnus".

As the first step, modify the class definition for the given `StreamingService` in [`src/streaming_service.py`](src/streaming_service.py) by the following:

1. Write the function definition for the method `_build_index()` in `StreamingService`. When `_build_index()` is called, it builds an index based on the shows stored in `self._shows` using the idea illustrated above (i.e. map the terms available in each show to the location of the corresponding show), and stored the built index as an instance variable `self._index`. Apart from `self`, the method does not take any argument. The method does not have a return value - it only creates `self._index` when called. 

    Note:
    * You should choose an appropriate container to represent the index
    * Please handle the case and the punctuations, as demonstrated in the example above
    * While for the simplified example above we build the index by only considering the title and the description of the shows, the index should at least make use of the information from "title", "description" and "cast" of each show. Feel free to add any information you think is useful to help users to find the right show

2. Write the function definition for the method `search()` in `StreamingService`, for which it makes use of the `self._index` (created after `_build_index()` is called) for searching. The method takes 1 argument apart from `self`, which represents a term used for searching (type: `str`, length > 0). it returns a collection of shows (e.g. a `list` of `Show`, or a `set` of `Show`) which represents _all_ the shows that contain the given string. For simplicity, for now we assume user will only use one term to search (e.g. `"Gambit"` but not `"Queen's Gambit"`). Case and punctuation at the front or back of the term should not affect the search result

    Think carefully when the indexing should be done (i.e. when `_build_index()` should be called). Should it be done at the beginning when we create an instance of `StreamingService`? Or should it be done when searching? Does it need to be called every time `search()` is called?

Example use:

```
>>> # suppose example_show_data below is a pandas DataFrame with the information of the two shows above
>>> example_platform = StreamingService('example', example_show_data)
>>> search_1_result = example_platform.search('chess')
>>> for one_show in search_1_result:
        print(one_show.get_title())
The Queen's Gambit
Magnus
>>> search_2_result = example_platform.search('gambit')
>>> for one_show in search_2_result:
        print(one_show.get_title())
The Queen's Gambit
```

---

## Step 2.

Now our search engine is "smarter", but it is still quite different from the behaviour of search engines that we use. In reality, users usually provide more than one term to search. For example, if a user want to search `"The Queen's Gambit"`, s/he is likely to provide something like `"chess Anya"` instead of just one term like `"chess"`. Therefore, we will now update our search engine so that users can use multiple terms to search.

Do the following:

1. Rewrite the `search()` method in `StreamingService` created in step (1) so that now it takes a `string` that can have multiple terms in it (separated by spaces). For example, it takes `"chess Anya"` as an argument and breaks it down into two terms `"chess"` and `"Anya"`. It will then return all the shows for which _all_ the terms are matched. 

    For example, for `"chess Anya"`, the `search()` method should return only one show `"The Queen's Gambit"`. This is because for the term `"chess"` we get (suppose the whole set of given netflix data in the `data` folder is used):
    ```
    'Magnus',
    'Searching for Bobby Fischer',
    'The Coldest Game',
    "The Queen's Gambit"
    ```

    and for `"anya"`, we have:
    ```
    "Angela's Christmas",
    "Angela's Christmas Wish",
    'Barry',
    'Creeped Out',
    'Making The Witcher',
    'Raja Rasoi Aur Anya Kahaniyan',
    'The Dark Crystal: Age of Resistance',
    "The Queen's Gambit",
    'The Witch',
    'The Witcher',
    'The Witcher: A Look Inside the Episodes'
    ```

    The only show that appears in both search results is `"The Queen's Gambit"`.

    Note:
    * For readability, the search results shown here only have the title name, but your method should return a collection of `Show`, not `string`.

2.  create an instance `netflix` from `StreamingService` using data from `netflix_titles.csv` in the `data` folder. Do some searches via it to demonstrate that `search()` method works. Please make sure you use `streaming_service.py` as a module

---



---

## Hints

* You may want to store the locations of the show corresponding to a given term using [`set`](https://docs.python.org/3/library/stdtypes.html#set), as the order of the locations (e.g. locations `1`, `2` or `2`, `1` for `"chess"`) does not matter and using `set` will make step (2) easier to implement. You may find `set.intersection` useful

---

## Note

* You can make use of the solution from PS10 if you want to
* You can use `pandas` to load in the data `netflix_titles.csv`
