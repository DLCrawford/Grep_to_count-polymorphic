Grep_to_count-polymorphic
=========================

grep and AWK to count number of variable and invariable Tags with a minimum n=20 and 100 reads 
There are several steps that could be more efficient (e.g., a while do for cat in all the locuc log files in different folders)
But this works
-------
# cat tara locus log: must be done in many step
# in the directory with raw locus logs 
cat yourfile_Gtypes_ctg1*.LocusLog.txt >TZB_chr1x_txt
cat yourfile_Gtypes_ctg2*.LocusLog.txt >TZB_chr2x_txt
cat yourfile_Gtypes_ctg3*.LocusLog.txt >TZB_chr3x_txt
cat yourfile_Gtypes_ctg4*.LocusLog.txt >TZB_chr4x_txt
cat yourfile_Gtypes_ctg5*.LocusLog.txt >TZB_chr5x_txt
cat yourfile_Gtypes_ctg6*.LocusLog.txt >TZB_chr6x_txt
cat yourfile_Gtypes_ctg7*.LocusLog.txt >TZB_chr7x_txt
cat yourfile_Gtypes_ctg8*.LocusLog.txt >TZB_chr8x_txt
cat yourfile_Gtypes_ctg9*.LocusLog.txt >TZB_chr9x_txt

# now cat the cats
cat TZB_chr*_txt >TZB_all_LocusLog.txt

# how big is that file?
# all possible lines
grep -cE '($)' TZB_all_LocusLog.txt
346241
#all lines with data from locus log (not headers, # of TAGS)
grep -cE "1" TZB_all_LocusLog.txt
#336060
# all headers
grep -c "chr" TZB_all_LocusLog.txt
#10181  Notice this = the number of "chromosomes" (scaffolds)

# there are 10,181 headers.  Let us get rid all but the first of these
# I am using grep c to test before I write a file
#  I am going to cheat,  I cannot seem to grep and line return "new line" with "chr"
#none of the below work   SUGGESTION? 
#grep -cE "$chr" TZB_all_LocusLog.txt
#grep -c '$^chr'  TZB_all_LocusLog.txt
#grep -c '\nchr'  TZB_all_LocusLog.txt
---  
#       So I change the first header to "_chr"
#Now I can see if grep see all 10180 using "^" beginning of line
grep -c '^chr'  TZB_all_LocusLog1.txt
10180
# "everything but = -v,  keeping all lines except thos beginning with  "newline & chr  "^chr'"
grep -cv '^chr'  TZB_all_LocusLog1.txt
# 336061  
# notice 336,061 is 10,180 less than the total number of lines, SO, let us write this file
grep -v '^chr'  TZB_all_LocusLog1.txt  >TZB_allLLog.txt
#How many lines? 
grep -cE '($)' TZB_allLLog.txt
#336061
------
#moved TZB_allLLog.txt up one directory to hapmap
#how many TAGS are polymorphic
grep -c "polymorphic" TZB_allLLog.txt
62311
#how many are "invariant"
grep -c "invariant" TZB_allLLog.txt
# 206119
#  62,311 are polymorphis and 206,119 are invariant.  ?  this is not 336,061
grep -c "tooFew" TZB_allLLog.txt
# 67630  67630_ 206,119 + 62,311 = 336,060-- the number of tags (w/o the single header). 
# how many have a minimum of 20 taxa?   which column has nTax, the 8th column has "nTaxaCovered"
#So one way to solve the problem (there may be other more clever ones I'm not aware of), is to use the "awk" command instead of the "grep" command.

awk '{if ($8>19) print $0;}' TZB_allLLog.txt > TZB_20taxa_LLog.txt

# how many tags with 20 taxa
grep -c "1" TZB_20taxa_LLog.txt
#110487

#how many have a minimum of 100 reads?   6th column is "nReads"
awk '{if ($8>99) print $0;}' TZB_20taxa_LLog.txt > TZB_20taxa100reads_LLog.txt
grep -c "1" TZB_20taxa100reads_LLog.txt
#15,332
______
Now the heart of the matter.  For tags with minimum of 20 taxa and 100 reads, how many are polymorphic and how many SNP per tag
#how many TAGS are polymorphic
grep -c "polymorphic" TZB_20taxa100reads_LLog.txt
#14721
#how many are "invariant"
grep -c "invariant" TZB_20taxa100reads_LLog.txt
# 611    96% are polymorphic.   
# column 13 is "nVarSitesKept"

 awk '{ sum+=$13} END {print sum}' TZB_20taxa100reads_LLog.txt
# 45,439  apprx 3/Tag
