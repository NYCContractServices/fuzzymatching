# fuzzymatching 
##Goal: 
To update the FMS dataset with CROL email addresses using the fuzzy match on the concatenation of the vendor name and the address. 

##Approach: 
Use the Jaro-Winkler edit distance algorithm to calculate each row in the index(FMS data) matched to the reference table (CROL table).
<a href="https://en.wikipedia.org/wiki/Jaro%E2%80%93Winkler_distance">Jaro-Winkler Distance</a> (Winkler, 1990) is "a measure of similarity between two strings. It is a variant of the Jaro distance metric (Jaro, 1989, 1995), a type of string edit distance, and was developed in the area of record linkage (duplicate detection) (Winkler, 1990). The higher the Jaro–Winkler distance for two strings is, the more similar the strings are. The Jaro-Winkler similarity (for equation see below) is given by 1 - Jaro Winkler distance. The Jaro–Winkler distance metric is designed and best suited for short strings such as person names. The score is normalized such that 0 equates to no similarity and 1 is an exact match."

Jaro-Winkler Distance Formula:
<br>
![alt tag](https://www.safaribooksonline.com/library/view/haskell-data-analysis/9781783286331/graphics/6331OS_03_03.jpg)
<br>


##Processing: 
The match is approximately a 30,000 row dataset (2 columns) X 40,000 row dataset CROL (2 columns). Because each row of the index dataset is matched against the reference set. THis was conducted on a x64 bit computer, 8 threads, with 8GBs of RAM. The initial run was slow, so the data was chucnked and clustered to increase processing time. 

##Results
To match the index row to the entire reference row and calculate the edit distance between the two match. Then, to order the match from highest to lowest. The best match was chosen at .865 edit distance or above. Note: Jaro-WInkler is slightly different that the Levensthein Distance in that it returns a number out of the total potential match, meaning that a higher percentage results in a closer match; whereas, a higher Levenstein Distance results in a worse match. 

##Data Sources and Definitions:
In the data set, the City's Financial Management System (FMS) has a number of vendors in its system with associated contact information such as business name, address, vendor code, TIN, email address, etc. This datasource is viewed as the <b>index</b> data source. 

The City also collects information from the City Record Online (CROL) which also contains information on vendors per above. No vendor code or TIN is collected and is the need for the match. 

The source file is fuzzymatching_R.R, which is the file that address the above approach. 

##Data Definitions and Sources of Data Explained
[TO BE ADDED] 
##Jaro-Winkler Formula in R
```
function (str1, str2, W_1 = 1/3, W_2 = 1/3, W_3 = 1/3, r = 0.5) 
{
    if (typeof(str1) != "character" && class(str1) != "factor") 
        stop(sprintf("Illegal data type: %s", typeof(str1)))
    if (class(str1) == "factor") 
        str = as.character(str1)
    if (typeof(str2) != "character" && class(str2) != "factor") 
        stop(sprintf("Illegal data type: %s", typeof(str2)))
    if (class(str2) == "factor") 
        str = as.character(str2)
    if ((is.array(str1) || is.array(str2)) && dim(str1) != dim(str2)) 
        stop("non-conformable arrays")
    if (length(str1) == 0 || length(str2) == 0) 
        return(double(0))
    ans <- .Call("jarowinklerCALL", str1, str2, as.double(W_1), 
        as.double(W_2), as.double(W_3), as.double(r), PACKAGE = "RecordLinkage")
    if (any(is.na(str1), is.na(str2))) 
        ans[is.na(str1) | is.na(str2)] = NA
    if (is.array(str1)) 
        return(array(ans, dim(str1)))
    if (is.array(str2)) 
        return(array(ans, dim(str2)))
    return(ans)
}
```
