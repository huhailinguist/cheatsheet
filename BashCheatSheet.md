# karst
-x to use GUI; try tregex. 
qsub -t 1-100 ...
PBS_ARRAYID

in the bash script:
python myscript.py PBS_ARRAYID

# mount unmount
```bash
sudo fdisk -l # see the mountable drives
sudo mount -t ntfs /dev/sda1 /media/hai/G/ # mount /dev/sda1 to /media/hai/G
sudo umoun /media/hai/G # to unmount a drive
```
# disk usage free space
```bash
df -h
```

# logical operators
```bash
&& # and
|| # or
if [ $counter -lt $x ] || [ $counter -gt $((x+100)) ]
  then
   continue
fi
```

# print certain line, or lines
```bash
# print line number 52 
sed -n '52p' # method 1 
sed '52!d' # method 2 
sed '52q;d' # method 3,  efficient on large files

sed -n '20,40p;41q' file_name
```

# string (not) contain substring
```bash
if [[ ! "abc" =~ "d" ]]; // if "abc" does NOT contain "d"
then
    do stuff
fi
```

# examine number of arguments from command line
```bash
if [ "$#" -ne 1 ]; then
   printf "\nUsage: ./tsg_hai.sh numIter\n\n"
   exit 1
fi
# refer to first argument: $1
```

# touch all files recursively
```bash
find -type f -exec touch {} +
```

# rsync
```bash
rsync -avztP # a=archive mode; v=verbose; z=compress; P=progress; t=keep time stamp
```

# remove all directories in current dir
```bash
rm -r */
```

# ENCODING
```bash
# get encoding of a text file in command line
file -bi filename

# reopen w/ encoding in vim
:e ++enc=gb2312

###### SORT non-ascii ######
# must use 'LC_COLLATE=C sort'
# if test contains non ascii symbols such as Chinese punctuations etc. 
cat test | LC_COLLATE=C sort | LC_COLLATE=C uniq

# converting from one encoding to another 
iconv -f GB18030 -t UTF8 text.txt
# see all encodings
iconv -l
# !! Determine encoding !!
chardet text.txt
# return: GB2312 with confidence 0.99
# but in fact this file is GB18030
```

# REGEX
```bash
grep -n # give line number, color the matches!! but with more/less no color
grep -c # count number of lines
grep -o # only the matches
grep -v # complement
egrep 'the .*? of' | less # egrep has greedy search
```

\b: word boundary

```bash
grep -Po '^\s+1\s\K\w+' test.txt
# \K means it will match what goes after ^\s+1\s, i.e. only print the \w+
#
# print lines whose 2nd column is empty. $'\t' says it's tab delimited
awk -F $'\t' '$2 == ""' file
#
# print lines whose 2nd column is not empty.
awk '$4' file
#
#Print only the lines that do not match a regular expression "/regex/" (emulates "grep -v").
awk '!/regex/' file
#
# print lines that is in file1 but not in file2 (both files have to be sorted)
diff --new-line-format="" --unchanged-line-format="" file1 file2
comm -23 file1 file2 
#
# Under unix, I want to copy all files with a certain extension (all mp3 files) from all subdirectories to another directory.
find dir_name '*.mp3' -exec cp -vuni '{}' "../dest_dir" ";"
#
# print the NUMth line of a file
sed "${NUM}q;d" file
tail -n+NUM file | head -n1
#
# show the last field using cut: first reverse the file, then cut the first column, and then reverse the first column.
cut filename | rev | cut -f 1 | rev

# count the number of a in each line:
tr -d -c 'a\n' < test.txt | awk '{ print length; }'
```

# read file
```bash
#!/bin/bash 
while IFS='' read -r line || [[ -n "$line" ]]; do    
echo "Text read from file: $line" 
done < "$filename"
```

# change file names
```bash
# change file extensions of multiple files; does it work??
find . -name "*.csv" -exec sh -c 'mv "$1" "${1%.csv}.txt"' _ {} \;

# change file name prefix from bigred2 to karst
rename 's/^bigred2/karst/' bigred2_*
```

# sed, awk
## sed must escape {}, as in [a-zA-Z]\{2,10\}
## for +, \s to work in sed, need to add -r!
```bash
# find and replace string in multiple files: Occurrences of "foo" will be replaced with "bar".
sed -i 's/foo/bar/g' *

# replace multiple strings at once
sed 's/ab/~~/g; s/bc/ab/g; s/~~/bc/g'

# How to print the number of characters in each line of a text file
awk '{ print length($0); }' abc.txt
# can count number of chinese characters in text file!

# sum the number of words each line in file
words=$(awk '{sum+=length($0)}END{print sum}' $file)

# Extract only chinese characters
perl -CS -p -e 's/[^\s\p{Han}]//g'

# delete empty lines
sed '/^$/d'

# sed delete
sed '/pattern to match/d' filename
```

# declare array:
```bash
Unix=('Debian' 'Red hat' 'Ubuntu' 'Suse' 'Fedora' 'UTS' 'OpenLinux')
# OR
declare -a Unix=('Debian' 'Red hat' 'Red hat' 'Suse' 'Fedora')

echo ${#Unix[@]} #Number of elements in the array 
echo ${#Unix}  #Number of characters in the first element of the array.i.e Debian
echo ${Unix[@]:3:2} # 2 elements starting from the position 3
```
### see [here](http://www.thegeekstuff.com/2010/06/bash-array-tutorial) for array declaration.

# variables
see [link](https://stackoverflow.com/questions/8748831/when-do-we-need-curly-braces-around-shell-variables).
```bash
var=10
# ALWAYS USE ${var}, not $var
"${foo}bar" # expand the variable foo
# Curly braces are also unconditionally required when:
${array[42]} # expanding array elements
${filename%.*} # using parameter expansion operations (remove extension)
```

# $(command) = \`command\`
Run the command and return its stdout. $() is better if you have nested command.
See [link](https://stackoverflow.com/questions/31255699/double-parenthesis-with-and-without-dollar)
```bash
echo "The current date is $(date)"
```

# for loops
```bash
# for loop, loop over every element
for i in "${myArray[@]}"
do
    echo $i
done

# or:
for year in {1991..2006..1}
do
    echo $year # 1991, 1992, 1993, ... , 2006
done

# or loop over files in a dir
for file in Data/*.txt 
do    
    for ((i = 0; i < 3; i++))    
    do        
        name=${file##*/}        
        base=${name%.txt}        
        ./MyProgram.exe "$file" Logs/"${base}_Log$i.txt"    
    done
done

# OR
for file in ../data/LCMC_seg/*
do
    # base=${name%.txt} # removes the trailing .txt
    echo ${file##*/} # removes the leading pathname up to the last /
    echo $(basename $file)
done
```

# get substring
```bash
a="thisisastring"
b=${a:0:4} # this
```

# if statement, incrementing variable, break
```bash
counter=0
for i in 1 2 3 4 5
do
    counter=$((counter+1))
    if [ $counter -eq 2 ]
       then
           break
     fi
     echo $i
done
```

# math calculation
see [here](https://www.shell-tips.com/2010/06/14/performing-math-calculation-in-bash/)
```bash
$[myvar+2]
# or
$((myvar+2))
# or
myvar=$(expr 1 + 1)
```
# string/int comparison
```bash
# int comparison
if [ "$a" -eq "$b" ] 
# -ne !=
# -gt >;    -ge >=
# -lt <;     -le <=
```

# double/single quotes
Single quotes do not interpret the things inside it (treat them as string). 
Double quotes evaluate the variables.
```bash
MYVAR=sometext 
echo "double quotes gives you $MYVAR" # quotes gives you sometext
echo 'single quotes gives you $MYVAR' # single quotes gives you $MYVAR
```

## see all mounted disks
```unix
# see all mounted disks
df -h
# see all disks, mounted and unmounted
lsblk
# mount a device on linux
sudo mount -t ntfs /dev/sda2 /media/hdd/
```
## restart ssh service
```unix
sudo service ssh restart
```
# IP 20171028
108.71.214.140

# vim
## substitution in visual block!
:%s/\%V{pattern}/{string}/g
# or
:'<,'>s/text1/text2/g
## redo
ctrl + R


