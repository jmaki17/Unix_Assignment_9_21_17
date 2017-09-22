Unix assignment workflow:

-after the creation of any new files, the created file was inspected using head -n 3 or tail -n 3

Inspection:

0. cd into Unix assignment folder

1. $ git init
	
	-initialize a new git repository

2. $ git add README.md

	-add a readme file

3. $ git commit -m "initial commit (README.md)"

	-make initial commit to the repository

4. 		$ head fang_et_al_genotypes.txt

	-inspect head of fang data set

5. 		$ head –n 3 fang_et_al_genotypes.txt

	-inspect top 3 lines of fang

6. 		tail -n 3 fang_et_al_genotypes.txt

	-inspecting tail of the fang data set
	
7. 		$ less fang_et_al_genotypes.txt

	-inspect fang data set with less and scroll through file

8. 		$ du -h fang_et_al_genotypes.txt

	-inspecting the file size of fang (11M)

9. 		$ grep -v "^#" fang_et_al_genotypes.txt | awk -F "\t" '{print NF; exit}'

	-get the number of columns in fang (986)


10. 		$ file fang_et_al_genotypes.txt

	-determine if there are non-ASCII characters present in the file (ASCII text, with very long lines)

11. Repeat above inspection with snp_position.txt

	-inspect the snp position file with the above commands (84K, 15 columns, ASCII text)


12. 		$ wc fang_et_al_genotypes.txt snp_position.txt

	-get the lines, words, and bytes in both files (2783  2744038 11051939 fang_et_al_genotypes.txt
     984    13198    82763 snp_position.txt
    3767  2757236 11134702 total)

Data Processing:

13. 		$ awk 'NR==1 || /ZMMIL|ZMMLR|ZMMMR/' fang_et_al_genotypes.txt > maize_genotypes.txt

	-use awk take all the maize genotypes (ZMMIL, ZMMLR, and ZMMMR) and the header from the fang data set and place them in a new file called "maize_genotypes.txt"

10. 		$ awk 'NR==1 || /ZMPBA|ZMPIL|ZMPJA/' fang_et_al_genotypes.txt > teosinte_genotypes.txt

	-repeat the above awk command for the teosinte genotypes


11. 		$ awk -f transpose.awk maize_genotypes.txt > transposed_maize_genotypes.txt

	-transpose the maize genotypes file so it is ready to be joined with the snp positions file

12.		 $ awk -f transpose.awk teosinte_genotypes.txt > transposed_teosinte_genotypes.txt

	-transpose the teosinte genotypes file so it is ready to be joined with the snp positions file

13.		 $ sort -k1,1 snp_position.txt > sorted_snp_position.txt

	-sorting the snp positions file based on the first column "SNP_ID"

14.		 $ sort -k1,1 transposed_maize_genotypes.txt > sorted_transposed_maize_genotypes.txt

	-sorting the transposed maize genotypes based on the first column "Sample_ID"

15. 		$ join -t $'\t' -1 1 -2 1 sorted_snp_position.txt sorted_transposed_maize_genotypes.txt > joined_maize_genotypes.txt

	-character join (tab-delimited) using the first column of file 1 (sorted snps) and the first column of file 2 (sorted maize genotypes) to create a new file 

16. 		$ cut -f1,3,4 joined_maize_genotypes.txt > reordered_joined_maize_genotypes.txt

	-cutting out the first column (snp id), 3rd column (chromosome number), and 4th column (chromosome position) to properly order them for the assignment and placing them in a new file

17. 		$ cut -f2,5- joined_maize_genotypes.txt > reordered_joined_maize_genotypes_2.txt

	-cutting out all the other files in the joined maize file and placing them into a new file 

18. 		$ paste reordered_joined_maize_genotypes.txt reordered_joined_maize_genotypes_2.txt > both_reordered_joined_maize_genotypes.txt

	-I used the "paste" command to combine the 2 files creatd in the previous 2 steps to get the proper order of columns for the assignment
	-https://unix.stackexchange.com/questions/117568/adding-a-column-of-values-in-a-tab-delimited-file (used to learn how to “paste” files)

19. 		$ sort -k1,1 transposed_teosinte_genotypes.txt > sorted_transposed_teosinte_genotypes.txt

	-begin running the exact same set of steps (14-18) for the teosinte genotypes file 

20. 		$ join -t $'\t' -1 1 -2 1 sorted_snp_position.txt sorted_transposed_teosinte_genotypes.txt > joined_teosinte_genotypes.txt

21. 		$ cut -f1,3,4 joined_teosinte_genotypes.txt > reordered_joined_teosinte_genotypes.txt

22. 		$ cut -f2,5- joined_teosinte_genotypes.txt > reordered_joined_teosinte_genotypes_2.txt

23. 		$ paste reordered_joined_teosinte_genotypes.txt reordered_joined_teosinte_genotypes_2.txt > both_reordered_joined_teosinte_genotypes.txt

24. 		$ sed 's/?/-/g' both_reordered_joined_maize_genotypes.txt > hyph_both_reordered_joined_maize_genotypes.txt

	-used the sed command to create a file where all the ?'s in the reordered joined maize genotypes file are replaced by - 

25. 		$ sed 's/?/-/g' both_reordered_joined_teosinte_genotypes.txt > hyph_both_reordered_joined_teosinte_genotypes.txt

	-used to complete the step above for teosinte

26. 		$ for i in {1..10}; do awk '$2 == '$i'' both_reordered_joined_teosinte_genotypes.txt | sort -k3,1n > chromo"$i"_both_reordered_joined_teosinte_genotypes.txt;done

	-on the teosinte file (?): using a for loop for each chromosome number (from column 2) using awk and then sorting that file based on increasing position value using sort on column 3 and then producing a file for each 

27. 		$ for i in {1..10}; do awk '$2 == '$i'' both_reordered_joined_maize_genotypes.txt | sort -k3,1n > chromo"$i"_both_reordered_joined_maize_genotypes.txt;done

	-using the above for loop for the maize (?) file

28. 		$ for i in {1..10}; do awk '$2 == '$i'' hyph_both_reordered_joined_maize_genotypes.txt | sort -k3,1nr > chromo"$i"_hyph_both_reordered_joined_maize_genotypes.txt;done

	-using the same loop on the maize (-) file but sorting by descending position value

30. 		$ for i in {1..10}; do awk '$2 == '$i'' hyph_both_reordered_joined_teosinte_genotypes.txt | sort -k3,1nr > chromo"$i"_hyph_both_reordered_joined_teosinte_genotypes.txt;done

	-using the same loop as above but on the teosinte (-) file

31. 		$ awk '$2 ~ /\unknown/' both_reordered_joined_maize_genotypes.txt > unknown_both_reordered_joined_maize_genotypes.txt

	-using and awk command to take the "unknown" values from the chromosome column in the joined maize genotype file and place them in their own file 

32. 		$ awk '$2 ~ /\unknown/' both_reordered_joined_teosinte_genotypes.txt > unknown_both_reordered_joined_teosinte_genotypes.txt

	-same as above but for the "unknown"s in the teosinte file

33. 		$ awk '$2 ~ /\multiple/' both_reordered_joined_teosinte_genotypes.txt > multiple_both_reordered_joined_teosinte_genotypes.txt

	-same as above but for the "multiple" value in the teosinte file

34. 		$ awk '$2 ~ /\multiple/' both_reordered_joined_maize_genotypes.txt > multiple_both_reordered_joined_maize_genotypes.txt

	-same as above but for the "multiple" value in the maize file 

Final files were inspected using the head -n 5 and tail -n 5 commands to ensure they were in the proper orientation. 

How to read the final files:

Files that use hyphens for missing data and have the snps ordered based on decreasing  position values are all labeled: 
chromo"$i"_both_reordered_joined_maize_genotypes.txt

Files that use hyphens for missing data and have the snps ordered based on decreasing  position values are all labeled: chromo"$i"_hyph_both_reordered_joined_teosinte_genotypes.txt 
