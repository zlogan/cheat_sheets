# ZSH
[http://reasoniamhere.com/2014/01/11/outrageously-useful-tips-to-master-your-z-shell/]

## File Picking
1. Globbing
  - ```ls zsh_demo```: list every file in the order
  - ```ls zsh_demo/*```: list every file in the suborders of zsh_demo
  - ```ls zsh_demo/*/*```: list every file in every folder two levels below the zsh_demo folder
  - ```ls zsh_demo/**/*```: list every file anywhere below the zsh_demo folder
  - ```ls zsh_demo/**/*.txt```: list every file that ends in .txt in every folder at any level below the zsh_demo folder

2. Glob operators
  - ```ls -l zsh_demo/**/*<1-10>.txt```: list text files that end in a number from 1 to 10
  - ```ls -l zsh_demo/**/[a]*.txt```: list text files that start with the letter a
  - ```ls -l zsh_demo/**/(ab|bc)*.txt```: list text files that start with either ab or bc
  - ```ls -l zsh_demo/**/[^cC]*.txt```: list text files that don't start with a lower or uppercase c

3. Glob qualifiers
  - ```print -l zsh_demo/**/*(/)```: show only directories
  - ```print -l zsh_demo/**/*(.)```: show only regular files
    - The . tells the glob to only show regular files (no directories, symbolic links, or other types of files).
  - ```ls -l zsh_demo/**/*(L0)```: show empty files
  - ```ls -l zsh_demo/**/*(Lk+3)```: show files greater than 3 KB
    - The Lm-2 tells the glob to show files smaller than 2 MB.
      - Use - for smaller, and + for greater; don’t use anything if you want to specify the exact size (Lm2).
      - Use m for megabytes, k for kilobytes, or nothing for just bytes (notice that these letters must appear before the sign).
  - ```print -l zsh_demo/**/*(mh-1)```: show files modified in the last hour
    - The mh-1 tells the glob to show files modified in the last hour
      - Use - if you want files modified within the last X units of time, and + for files modified more than X units of time ago.
      - Use M for Months, w for weeks, h for hours, m for minutes, and s for seconds (notice that these leters must appear before the sign).
  - ```ls -l zsh_demo/**/*(om[1,3])```: sort files from most to least recently modified and show the last 3
    - The om tells the glob to sort the remaining files by their modification date.
      - A lowercase o sorts by most recent first, to use the reverse order, make it uppercase O.
      - Use m to sort by modification date, and L to sort by size (oL).
    - The [1,3] tells the glob to show the first 3 files (since we just sorted the files, these will be the most recently modified ones).
    - You can also show a single file (for example, the second one [2])

  - extended:
    - ```print -l zsh_demo/*/*(e:'[[ ! -e $REPLY/malta ]]'```: show every continent that doesn't contain a country named malta
      - Let’s parse this magic:
        1. After the e, the string has to be delimited by a convenient character (in this case, a colon :), and the code must be surrounded by single quotes ', so the actual command is just [[ ! -e $REPLY/malta ]].
        2. The $REPLY variable contains every file name of the ones specified by the glob zsh_demo/*/* in turn, but only a single file at a time.
        3. [[ -e file ]] is a conditional expression that returns true if the file exists. We want it to return true when the file called malta doesn’t exist, so we reverse it with !.
        4. When the code is executed, the $REPLY variable takes the value of the next file and the code is executed again.

## Variable transformations
  - ```print -l zsh_demo/data/europe/poland/*.txt```: A plain old glob
  - ```print -l zsh_demo/data/europe/poland/*.txt(:t)```: Return the file name (t stands for tail)
  - ```print -l zsh_demo/data/europe/poland/*.txt(:t:r)```: Return the file name without the extension (r stands for remove_extension, I think)
  - ```print -l zsh_demo/data/europe/poland/*.txt(:e)```: Return the extension
  - ```print -l zsh_demo/data/europe/poland/*.txt(:h)```:Return the parent folder of the file (h stands for head)
  - ```print -l zsh_demo/data/europe/poland/*.txt(:h:h)```
  - ```print -l zsh_demo/data/europe/poland/*.txt([1]:h)```: Return the parent folder of the first file

  - ```my_file=(zsh_demo/data/europe/poland/*.txt([1]))```
    - If you want to store a glob in a variable, you must use parentheses

  - ```print -l $my_file```
  - ```print -l $my_file(:h)```: this is the syntax we saw before
  - ```print -l ${my_file:h}```: I find this syntax more convenient
  - ```print -l ${my_file(:h)}```: don't mix the two, or you'll get an error
  - ```print -l ${my_file:u}```: the :u modifier makes the text uppercase

## Magic tabbing
  - show the previous command
  - ```echo a b c```
  - ```!!```: instead of pressing <Enter>, press <Tab>, then press <Enter>
  - show two commands ago
    - ```echo d e f```
    - ```echo g h i```
    - ```!-2```  press <Tab>, then press <Enter>
  - add the last argument
    - ```ls zsh_demo/data/asia/laos/population.txt```
    - ```ls -l !!1```: press <Tab>, then press <Enter>
  - add all the previous arguments
    - ```echo a b c```
    - ```print -l !!*```: press <Tab>, then press <Enter>

  - **Specify which command you are interested in**
    1. The previous command ```!!``` is the one you’ll use most often.
    2. If you want to go back farther, use the minus sign - and a number: ```!-2```, ```!-3```.
    3. You can also use the current command ```!#```
    4. Pick what arguments you want to reuse
    5. To pick an argument from the previous command, just add a number ```!!1```, ```!!2```. Use ```!!$``` for the last argument.
    6. To pick an argument from two or more commands ago, add a colon : before the number ```!-2:1``` (because!-21 means something else).
    7. If you want to reference all the arguments, use an asterisk * ```!!*``` ```!-2:*```.
    8. If you want skip all the arguments except the first one or two, add a number before the asterisk ```!!2*```, ```!-2:2*```.


## Bonus tips