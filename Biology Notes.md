# Finding Ori - Locating the origin of replication in bacterial genomes
## Background
Before a bacterial cell divides into two, it needs to create two copies of its DNA (one for each of the resulting daughter cells). DNA replication creates a new copy of the bacterial genome by using the original DNA strands as templates. **DNA polymerase** attaches to a template strand of DNA and syntheises a **reverse complement strand** (an anti-parallel strand of DNA with complementary bases). DNA polymerase binds to the replication origin of the circular genome (aka. **the *ori* region**).

### reverse complement functions: 
1. [Generate the reverse complement of a DNA strand](https://github.com/ClarissaPereira/Finding-Ori/blob/master/reverse%20complement%20generator.py)

DNA replication is triggered by the binding of an initiation factor (DnaA protein). Within the ori region, there is a short non-coding base sequence known as the **DnaA box** which acts as a point of attachment for the DnaA protein. The DnaA box can therefore act as a landmark in our quest to locate the ori.

## Locating Techniques
Locating bacterial ori regions is important for developing viral vector gene therapies and constructing phylogenetic trees.

A biologist's approach to locating ori would involve deleting various short segments from the genome. If one of these deleted segments stops replication, then that segment must have played an important role in replication (i.e. it is likely to be a DnaA box). This 'knockout' approach is very time-consuming and interpreting the results of such experiments requires computational analysis. A much faster computational approach is needed. 

## K-mers
The DnaA box can be considered a k-mer (a sub-sequence of length *k* within the genome base sequence). The k-mer string consists solely of characters 'A', 'G', 'C', and 'T'. 
For instance, 'AGCTA' contains:
  * five 1-mers *('A', 'G', 'C', 'T', 'A')*
  * four 2-mers *('AG', 'GC', 'CT', 'TA')*
  * three 3-mers *('AGC', 'GCT', 'CTA')*
  * two 4-mers *('AGCT', 'GCTA')*
  * one 5-mer *('AGCTA')*

In general, a genome of length *L* has (*L-k+1*) k-mers. The total possible k-mer permutations = 4<sup>*k*</sup>. Since the number of possible k-mers grows exponentially as k increases, the task of identifying k-mers where k>10 becomes computationally challenging - in this project, I have aimed to increase program efficiency to minimise runtime. 

### k-mer finding functions:
1. [Find frequency of a particular k-mer in genome](https://github.com/ClarissaPereira/Finding-Ori/blob/master/basic%20k-mer%20search.py)
2. [Find most frequent k-mers when given k](https://github.com/ClarissaPereira/Finding-Ori/blob/master/find%20k-mer%20by%20k.py)
3. [Find the location of a specific k-mer in a genome](https://github.com/ClarissaPereira/Finding-Ori/blob/master/find%20k-mer%20location.py)
4. [Find frequent k-mers that cluster together into clumps in the genome](https://github.com/ClarissaPereira/Finding-Ori/blob/master/find%20k-mer%20clumps.py)

## Replication Mutations
Although DNA strands run in opposite directions (one in 3' -> 5' direction, the other in 5' -> 3'), DNA polymerase is unidirectional. The enzyme can only read template strands in the 3' -> 5' directions so it relies on primer-binding to copy the 5' -> 3' strand. This results in asymmetric replication in which the leading strand (3' -> 5') is replicated swiftly whereas the lagging strand replication is frequently interrupted. 

These interruptions result in the lagging strand remaining unpaired for longer than the leading strand - leaving it vulnerable to mutation. Without the stability of base stacking and hydrogen bonds, cytosine *C* bases spontaneously deaminate into thymine *T*. The loss of *C* from the lagging strand changes the ratio of *G*:*C* - this ratio change can be calculated as a **skew value**. In a section of a bacterial genome, if skew is decreasing the section must have been taken from the leading strand. And vice versa for the lagging strand. 

Example of a Skew Plot for a section of the lagging strand of E.coli genome: 

(generated using [the skew plotter I created](https://github.com/ClarissaPereira/Finding-Ori/blob/master/skew%20plotter.py))
![skewplotex](https://user-images.githubusercontent.com/68158694/87874378-4911dd00-c9c1-11ea-885a-94aab51bd3cd.png)

Ori can be found at the skew plot minima - the point where the lagging strand and leading strand meet and the skew switches from decreasing to increasing. In the plot above, possible oris are located at positions 520, 521, 522, 523, and 530 in the genome.

The ori location identified by this technique is not precise since the circular bacterial genome is linearised and an arbitrary starting position is chosen. However, the skew technique can help narrow down the searching region in the genome. (In the example above, a k-mer search could be focused on positions 470 to 580).

### skew plot functions:
1. [Plot skew with Matplotlib](https://github.com/ClarissaPereira/Finding-Ori/blob/master/skew%20plotter.py)
2. [Find the minima of a skew plot](https://github.com/ClarissaPereira/Finding-Ori/blob/master/find%20skew%20minima.py)

## Imperfect K-mers
A consequence of lagging strand deamination is that the DnaA box we wish to detect could have mutated. So exact pattern matching will not yield the true base sequence of the DnaA box. As a result, k-mer identification needs to account for a certain number of mutations per k-mer.  

### functions that account for mutations:
1. [Find the number of mutated bases in a replicated DNA strand](https://github.com/ClarissaPereira/Finding-Ori/blob/master/find%20mutated%20bases.py)
2. [Locate approximate k-mer matches](https://github.com/ClarissaPereira/Finding-Ori/blob/master/locate%20approximate%20k-mer%20matches.py)

## Limitations of a computational method
  * Some bacterial genomes have very few DnaA boxes which complicates pattern matching and finding frequent k-mers
  * Ori region will be imprecise if leading and lagging half-strand lengths are inequal
  * Skew plots could have multiple minima which expands the ori searching region

  
