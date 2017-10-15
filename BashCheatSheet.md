# ENCODING
```bash
# get encoding of a text file in command line
file -bi filename


###### SORT non-ascii ######
# must use 'LC_COLLATE=C sort'
# if test contains non ascii symbols such as Chinese punctuations etc. 
cat test | LC_COLLATE=C sort | LC_COLLATE=C uniq
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

# ENCODING ISSUES
# converting from one encoding to another 
iconv -f GB18030 -t UTF8 text.txt
# see all encodings
iconv -l
# !! Determine encoding !!
chardet text.txt
# return: GB2312 with confidence 0.99
# but in fact this file is GB18030

# change file extensions of multiple files; does it work??
find . -name "*.csv" -exec sh -c 'mv "$1" "${1%.csv}.txt"' _ {} \;

# change file name prefix from bigred2 to karst
rename 's/^bigred2/karst/' bigred2_*

# find and replace string in multiple files: Occurrences of "foo" will be replaced with "bar".
sed -i 's/foo/bar/g' *

# How to print the number of characters in each line of a text file
awk '{ print length($0); }' abc.txt

# Extract only chinese characters
perl -CS -p -e 's/[^\s\p{Han}]//g'

# declare array:
declare -a Unix=('Debian' 'Red hat' 'Red hat' 'Suse' 'Fedora')

echo ${#Unix[@]} #Number of elements in the array 
echo ${#Unix}  #Number of characters in the first element of the array.i.e Debian
```
see [here](http://www.thegeekstuff.com/2010/06/bash-array-tutorial) for array declaration.

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

```

# vim
## substitution in visual block!
:%s/\%V{pattern}/{string}/g
# or
:'<,'>s/text1/text2/g
## redo
ctrl + R


