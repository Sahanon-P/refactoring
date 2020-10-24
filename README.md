# Refactoring
## Readability 
From Sorawichaya Readability code : https://github.com/b6210545602/PA4
### Attribute type 
In the src/readability/strategy/ReadStrategy.java class
https://github.com/b6210545602/PA4/blob/master/src/readability/strategy/ReadStrategy.java
``` java
public class ReadStrategy {
    /** number of word */
    private int wordNumbers;
    /** number of lines */
    private int lineNumbers;
    /** number of sentences */
    private int sentenceNumbers;
    /** number of syllables */
    private int syllableNumbers;
    /** name of output statement, so tell text come from file or url */
    private String name;
 
    /**
     * get number of words
     * @return total amount of words 
     */
    public double getWords() {
        return (double) wordNumbers;
    }

    /**
     * get number of lines
     * @return total amount of lines
     */
    public double getLines() {
        return (double) lineNumbers;
    }

    /**
     * get number of sentences
     * @return total amount of sentences
     */
    public double getSentences() {
        return (double) sentenceNumbers;
    }

    /**
     * get numbers of syllables
     * @return total amount of syllables
     */
    public double getSyllables() {
        return (double) syllableNumbers;
    }
}
```
- Refactoring Sign
  - Unnessescary forced type.
  - Naming for attribute.
 
- Refactoring : Change the type of the attribute and remove forced type in get method.
 ``` java
 public class ReadStrategy {
    // This is just an example.
    /** number of word */
    private double wordNumbers;
    
    /**
     * get number of words
     * @return total amount of words 
     */
    public double getWords() {
        return wordNumbers;
    }
}
```

- Refactoring : Change attribute name to be more meaningful.
  - The attribute "name" represented the name of the file and url path so actually the name must be considered as "pathName".
``` java
    /** name of output statement, so tell text come from file or url */
    private String pathName;
```

### countSentence(String line) method
In the src/readability/strategy/Counter.java class
https://github.com/b6210545602/PA4/blob/master/src/readability/strategy/Counter.java
``` java
 public int countSentence(String line) {
        int sentencegroups = 0;
        String[] lines = line.split("\\s+");
        for(String word: lines) {
            // the word end with ".", ";", "!", "?"
            if (word.endsWith(".") || word.endsWith(";") || word.endsWith("!") || word.endsWith("?")) {
                String subword = word.substring(0, word.length() - 1);
                if (isNumeric(subword)) sentencegroups--;
                sentencegroups++;
            }
        } // end of the line
        // more code
 ```
 - Refactoring sign
    - if statement is too complex
    - The suffix check in the statement can be replace with the constant.
 
 - Refactoring : Improve the if statement condition and create the constant for suffix.
 ``` java
 public int countSentence(String line) {
        int sentenceGroups = 0;
        String[] lines = line.split("\\s+");
        final String SUFFIX = ".;!?";
        for(String word: lines) {
            if (SUFFIX.contains(word.substring(word.length()-1))){
                String subword = word.substring(0, word.length() - 1);
                if (isNumeric(subword)) sentenceGroups--;
                sentenceGroups++;
 ```
### Extract method and move method
In the src/readability/strategy/ReadStrategy.java class
https://github.com/b6210545602/PA4/blob/master/src/readability/strategy/ReadStrategy.java
```java
    /**
     * conut number of words, lines, sentences and syllables
     * @param line get line from scanner
     */
    public void countAll(String line) {
        //count line
        lineNumbers++;
        
        Counter counter = new Counter();
        if (!line.equals("")) {
            String[] wordList = line.split("[.,()_/:!?;]|\\s|\"");
            // count syllable
            for(String word: wordList) {
                int n = counter.countSyllable(word.toLowerCase());
                syllableNumbers += n;
                if (n > 0) {
                    // it is a word
                    wordNumbers++;
                }
            }
        }
        int i = counter.countSentence(line);
        sentenceNumbers += i;
    }
```

- Refactoring sign
  - if statement can be improved.
  - The calculation inside method should be extract to be another method.
  - The extracted method should considered as one of the method in Counter (as you can see that inside this method has initialize the Counter to call the countSentence method).

- Refactoring : Extract the method inside the countAll and move to Counter class.
``` java
public class Counter{

    public int countLines(){
        return 1;
    }

    public int getSyllable(String[] wordList){
        int syllableNumbers = 0;
        for(String word: wordList) {
            int n = this.countSyllable(word.toLowerCase());
            syllableNumbers += n;
        }
        return syllableNumbers;

    }

    public int countWords(String[] wordList){
        int wordNumbers = 0;
        for(String word: wordList) {
            int n = this.countSyllable(word.toLowerCase());
            if (n > 0){
                wordNumbers++;
            }
        }
        return wordNumbers;
    }
}

```
and inside the ReadStrategy class.

``` java
public class ReadStrategy{
    /**
     * conut number of words, lines, sentences and syllables
     * 
     * @param line get line from scanner
     */
    public void countAll(String line) {
        Counter counter = new Counter();
        this.lineNumbers += counter.countLines();
        if (!line.isBlank()) {
            String[] wordList = line.split("[.,()_/:!?;]|\\s|\"");
            this.syllableNumbers += counter.getSyllable(wordList);
            this.wordNumbers += counter.countWords(wordList);
        }
        int i = counter.countSentence(line);
        sentenceNumbers += i;
    }
}
```
As you can see the if statement from `if (!line.equals(""))` to be `if (!line.isBlank())` which is more funtional.





  
 
 
 
 
        
 
