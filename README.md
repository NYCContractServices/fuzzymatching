# fuzzymatching 
##Goal: 
To update the FMS dataset with CROL email addresses using the fuzzy match on the concatenation of the vendor name and the address. 

##Approach: 
Use the Jaro-Winkler edit distance algorithm to calculate each row in the index(FMS data) matched to the reference table (CROL table).
<a href="https://en.wikipedia.org/wiki/Jaro%E2%80%93Winkler_distance">Jaro-Winkler Distance</a> (Winkler, 1990) is "a measure of similarity between two strings. It is a variant of the Jaro distance metric (Jaro, 1989, 1995), a type of string edit distance, and was developed in the area of record linkage (duplicate detection) (Winkler, 1990). The higher the Jaro–Winkler distance for two strings is, the more similar the strings are. The Jaro-Winkler similarity (for equation see below) is given by 1 - Jaro Winkler distance. The Jaro–Winkler distance metric is designed and best suited for short strings such as person names. The score is normalized such that 0 equates to no similarity and 1 is an exact match."

'''The Jaro distance {\displaystyle d_{j}} d_{j} of two given strings {\displaystyle s_{1}} s_{1} and {\displaystyle s_{2}} s_{2} is
{\displaystyle d_{j}=\left\{{\begin{array}{l l}0&{\text{if }}m=0\\{\frac {1}{3}}\left({\frac {m}{|s_{1}|}}+{\frac {m}{|s_{2}|}}+{\frac {m-t}{m}}\right)&{\text{otherwise}}\end{array}}\right.} d_{j}=\left\{{\begin{array}{ll}0&{\text{if }}m=0\\{\frac  {1}{3}}\left({\frac  {m}{|s_{1}|}}+{\frac  {m}{|s_{2}|}}+{\frac  {m-t}{m}}\right)&{\text{otherwise}}\end{array}}\right.
Where:
{\displaystyle m} m is the number of matching characters (see below);
{\displaystyle t} t is half the number of transpositions (see below).
'''



##Processing: 
The match is approximately a 30,000 row dataset (2 columns) X 40,000 row dataset CROL (2 columns). Because each row of the index dataset is matched against the reference set. THis was conducted on a x64 bit computer, 8 threads, with 8GBs of RAM. The initial run was slow, so the data was chucnked and clustered to increase processing time. 

##Results
to match the index row to the entire reference row and calculate the edit distance between the two match. Then, to order the match from highest to lowest. The best match was chosen at .875 edit distance or above. Note: Jaro-WInkler is slightly different that the Levensthein Distance in that it returns a number out of the total potential match, meaning that a higher percentage results in a closer match; whereas, a higher Levenstein Distance results in a worse match. 
