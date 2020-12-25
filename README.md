# french-words
This is a list of French words with partial POS-tagging and relative frequencies.

## Sources
The list is based on several sources, including most notably:

- the Debian package [wfrench](https://packages.debian.org/fr/sid/wfrench): a high quality plain list of words
- [Lexique 3.83](http://www.lexique.org/): a high quality list of words with POS-tagging and relative frequencies of word ; all the words have been included, some minor corrections were made
- [DELA dictionary](https://infolingu.univ-mlv.fr/DonneesLinguistiques/Dictionnaires/telechargement.html) from the University of Marne-la-Vallée: a high quality list of words with POS-tagging, more comprehensive than the previous one especially for conjugated forms of verbs ; POS tags were merged with the previous list according to the following algorithm:
	* map DELA tags to Lexique.org tags
	* if only one of the two lists had a POS-tag for a word, use this POS-tag for the merged list
	* if both lists had a POS-tag for a word, manually select the appropriate POS-tag
- [this word list on Github](https://github.com/lorenbrichter/Words): a good quality plain list of words, a bit more comprehensive than the one from the Debian package
- [this other word list on Github](https://github.com/hbenbel/French-Dictionary): various-quality lists of words with POS-tagging ; only words not in the previous lists were added ; one file has a medium-quality plain list of words which was manually merged with the global list
- [Google Ngrams](http://storage.googleapis.com/books/ngrams/books/20200217/fre/fre-1-ngrams_exports.html): a poor-quality list of words with automatic POS-tagging and relative frequencies of word ; this list has been made up with automatic OCR and POS-tagging ; it is really comprehensive but the quality was so bad that only the relative frequencies were used for words already in the merged list, no new words were used from this list

Some manual work was carried out to clean the resulting lists.

## Structure of the file
The list of words is in a tab-separated file with the following fields:

- word
- POS-tagging, using the Lexique.org nomenclature
- associated lemma
- frequency according to Lexique.org (measured in number of occurrences per million of words)
- frequency according to Google Ngrams (measured in number of occurrences per million of words)


## POS tags
The following tags are used:

| Tag     | Signification                    | Number of entries | Most common entries                        |
|---------|----------------------------------|-------------------|--------------------------------------------|
|         | Unknown                          | 727               |                                            |
| ADJ     | Adjective                        | 77660             | même, autre, petit                         |
| ADJ:dem | Demonstrative adjective          | 4                 | cette, ces                                 |
| ADJ:ind | Undefined adjective (determiner) | 37                | quelques, quelque, chaque, tout, plusieurs |
| ADJ:int | Interrogative adjective          | 4                 | quelle                                     |
| ADJ:num | Numeral adjective                | 139               | deux, trois, quatre, cinq, dix             |
| ADJ:pos | Possessive adjective             | 32                | son, sa, ses, mon, ma                      |
| ADV     | Adverb                           | 3752              | pas, ne, n', plus, bien                    |
| ART     | Indeterminate article            | 1                 |                                            |
| ART:def | Definite article                 | 9                 | la, le, les, l', du                        |
| ART:ind | Indefinite article               | 4                 | un                                         |
| AUX     | Auxiliary                        | 1                 | n'                                         |
| CON     | Indeterminate conjunction        | 7                 | puis, comment, soit, néanmoins, cependant  |
| CON:c   | Coordinating conjunction         | 10                | et, mais, ou, ni, car                      |
| CON:s   | Subordinating conjunction        | 26                | comme, quand, si, s', parce que            |
| NOM     | Noun                             | 164538            | temps, fois, peu, yeux, tête               |
| ONO     | Onomatopoeia or interjection     | 302               | ah, oh, eh, bon, hein                      |
| PRE     | Preposition                      | 100               | de, à, d', en, dans                        |
| PRO     | Indeterminate pronoun            | 31                |                                            |
| PRO:dem | Demonstrative pronoun            | 17                | ce                                         |
| PRO:ind | Undefined pronoun                | 43                | rien, tout                                 |
| PRO:int | Interrogative pronoun            | 6                 | duquel, auxquels, desquels, desquelles     |
| PRO:per | Personal pronoun                 | 50                | il, je, elle                               |
| PRO:pos | Possessive pronoun               | 23                | mienne, sienne, mien, sien, siens          |
| PRO:rel | Relative pronoun                 | 17                | qui, que, où, dont, quoi                   |
| VER     | Verb                             | 485076            | est, était, dit, faire, avait              |

## Statistics

- **732.616 entries**
- **691.969 words** (some may have more than one associated POS-tag hence the multiple entries)
- **619.310** words have a lemma associated to them
- **225.403** words have at least one data about their relative frequency (either from Lexique.org or Google Ngrams)

## Fun facts (and some playing with shell scripting)
This is what we can do with some regular expressions and shell scripting!

- The longest word is « parabutoxyphénylacéthydroxamiques » with 33 letters, which is an adjective by the way :-). The longest noun is « œsogastroduodénofibroscopies » with 28 letters. The longest verb is « déconstitutionnaliseraient » with 26 letters.
- If we exclude technical (chemical or medical) words, the longest words are « interdépartementalisations », « déconstitutionnaliseraient », « déconstitutionnalisassions », « déconstitutionnalisassions », « désinstitutionnaliseraient » and « désinstitutionnalisassions » with 26 letters.

```bash
awk  $'BEGIN{FS="\t";OFS="\t"};{print $1,length($1)}' french.txt | sort -t $'\t' -k2 -n -r | head -n 1 # Longest word
grep -v -E "'|-" french.txt | awk  $'BEGIN{FS="\t";OFS="\t"};{if ($2=="NOM") print $1,length($1)}' | sort -t $'\t' -k2 -n -r | head -n 30 # Longest noun
grep -v -E "'|-" french.txt | awk  $'BEGIN{FS="\t";OFS="\t"};{if ($2=="VER") print $1,length($1)}' | sort -t $'\t' -k2 -n -r | head -n 30 # Longest verb
```

- The former is also the word with the most vowels and the most consonants, with 15 vowels and 18 consonants.

```bash
cut -f 1 french.txt | sed -e "s/A\|E\|I\|O\|U\|Y\|a\|e\|i\|o\|u\|y\|é\|ê\|è\|ë\|â\|à\|ä\|û\|ü\|î\|ï//g" | awk $'BEGIN{FS="\t";OFS="\t"};{print $1,length($1)}' | paste - french.txt | sort -t $'\t' -k2 -n -r | head -n 1 # Most consonants
cut -f 1 french.txt | sed -e "s/B\|C\|D\|F\|G\|H\|J\|K\|L\|M\|N\|P\|Q\|R\|S\|T\|V\|W\|X\|Z\|b\|c\|ç\|d\|f\|g\|h\|j\|k\|l\|m\|n\|p\|q\|r\|s\|t\|v\|w\|x\|y\|z//g" | awk $'BEGIN{FS="\t";OFS="\t"};{print $1,length($1)}' | paste - french.txt | sort -t $'\t' -k2 -n -r | head -n 1 # Most vowels
```

- The former is also the word with the greatest number of different letters. It has 20 different letters (or 19 if we consider 'é' and 'e' to be the same letter).


```bash
cut -f 1 french.txt | sed -n -e ':a;s/\(.\)\(.*\)\1/\1\2/g;t a;p' french.txt | awk $'BEGIN{OFS="\t"};{print $1,length($1)}' | paste - french.txt | cut -f 2,3 | sort -t $'\t' -k1 -n -r | head -n 1
```

- 2033 words contain each of the vowels a, e, i, o, u and y. One of the shortest ones is « rougeoyai ».

```bash
cut -f 1 french.txt | grep -E 'a|â|à' | grep -E 'e|é|è|ê|ë' | grep -E 'i|î|ï' | grep -E 'o|ô|ö' | grep -E 'u|û|ü|ù' | grep 'y' | wc -l # Count the number of words
cut -f 1 french.txt | grep -E 'a|â|à' | grep -E 'e|é|è|ê|ë' | grep -E 'i|î|ï' | grep -E 'o|ô|ö' | grep -E 'u|û|ü|ù' | grep 'y' | awk $'BEGIN{FS="\t";OFS="\t"};{print $1,length($1)}' | sort -t $'\t' -k2 -n | head -n 20 # List shortest words
```

- There are 35 strict palindroms (with respect to the accents) or 52 loose palindroms (accented versions of characters are considered the same as the original character) with 5 letters or more, the longest of which are « essayasse » and « ressasser ».

```bash
awk -F $'\t' '{x="";for (i=length($1);i!=0;i--) x=(x substr($1,i,1));if (length($1)>=5 && x==$1) print $1}' french.txt | sort | uniq | wc -l # Strict version
cut -f 1 french.txt | sort | uniq | iconv -f utf8 -t ascii//TRANSLIT | awk -F $'\t' '{x="";for (i=length($1);i!=0;i--) x=(x substr($1,i,1));if (length($1)>=5 && x==$1) print $1}' | wc -l # Loose version
awk -F $'\t' 'BEGIN{OFS="\t"};{x="";for (i=length($1);i!=0;i--) x=(x substr($1,i,1));if (length($1)>=5 && x==$1) print $1,length($1)}' french.txt | sort -t $'\t' -k2 -n -r | head -n 3
```

- The word « hérédodégénérescence » is the one with one letter repeated most often. There are 8 e in this word. If we take the accents into account, the record is held by several words with 7 s, like « assassinasses », « ressassasses », « saussuritisasses », « désassaisonnasses »...

```bash
awk -F $'\t' 'BEGIN{OFS="\t"};{delete a;for (i=1;i<=length($1);i++) a[substr($1,i,1)]++;asort(a,b,"@val_num_desc");print $1,b[1]}' french.txt | sort -t $'\t' -k2 -n -r | head # Strict version (accents taken into account)
cut -f 1 french.txt | iconv -f utf8 -t ascii//TRANSLIT | awk -F $'\t' 'BEGIN{OFS="\t"};{delete a;for (i=1;i<=length($1);i++) a[substr($1,i,1)]++;asort(a,b,"@val_num_desc");print $1,b[1]}' | sort -t $'\t' -k2 -n -r | head # Loose version (accented characters are the same as corresponding non-accented characters)
```

- 7 words of 14 letters are made of only different letters : « ancylotheriums », « cryptogamiques », « cylindromateux », « gymnoblastique », « hydnocarpiques », « stylographique », and « xylographiques ». If we consider that an accented version of the letter is a different letter than the original version, the word « hydromagnétiques » has 16 different letters.

```bash
cut -f 1 french.txt | grep -v -E '(.).*\1'  | awk 'BEGIN{OFS="\t"};{print $1,length($1)}' | sort -t $'\t' -k2 -n -r | head # Strict version
cut -f 1 french.txt | iconv -f utf8 -t ascii//TRANSLIT | grep -v -E '(.).*\1' | awk 'BEGIN{OFS="\t"};{print $1,length($1)}' | sort -t $'\t' -k2 -n -r | head # Loose version
```

- The most frequently-used words are in this order: « de », « la », « et », « à », « le », « il », « les », « un », « l' », « d' ».

```bash
sort -t $'\t' -k4 -n -r french.txt | head
```

- The most frequently-used nouns are in this order: « temps », « fois », « yeux », « tête », « homme », « vie », « jour », « main », « mère », « monde », « père », « chose », « femme ».

```bash
grep NOM french.txt | sort -t $'\t' -k4 -n -r | head
```

- The most frequently-used adjectives are in this order: « même », « autre », « petit », « grand », « petite », « seul », « jeune », « bon », « sûr », « seule », « vrai », « première », « bonne », « vieux », « beau ».

```bash
grep ADJ french.txt | sort -t $'\t' -k4 -n -r | head
```

- If we exclude technical (chemical or medical) words, the word which is worth the greatest amount of points at Scrabble (by summing up the values of its letters, and checking if no letter is used more than the number of its tokens in the game) is « déshypothéqueriez » with 50 points. If we limit ourselves to words with 7 letters or less, the record is held by « whiskys » with 37 points. The words with the greatest ratio of points per number of letters are « yak », « wok », « wax » and « kyu ».

```python
import fileinput
import unidecode

scr={ 'a':[1,9], 'b':[3,2], 'c':[3,2], 'd':[2,3], 'e':[1,15], 'f':[4,2], 'g':[2,2], 'h':[4,2], 'i':[1,8], 'j':[8,1], 'k':[10,1], 'l':[1,5], 'm':[2,3], 'n':[1,6], 'o':[1,6], 'p':[3,2], 'q':[8,1], 'r':[1,6], 's':[1,6], 't':[1,6], 'u':[1,6], 'v':[4,2], 'w':[10,1], 'x':[10,1], 'y':[10,1], 'z':[10,1] }
dic=[]
for line in fileinput.input():
    word=line.split('\t')[0]
    word=unidecode.unidecode(word)
    letters=set(word)
    ok=True
    for c in letters:
        if c not in scr or len(list(filter(lambda it:it==c,word)))>scr[c][1]:
            ok=False
            break
    if ok:
        dic.append([word,sum(map(lambda it:scr.get(it,[0])[0],word))])
dic.sort(key=lambda it:it[1],reverse=True)
for l in dic:
    print(l)
```
