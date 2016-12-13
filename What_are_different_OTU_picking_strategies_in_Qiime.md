
## What are the differences among de novo, closed-reference and open-reference OTU picking?

### de novo OTU picking
##### In this strategy, all the reads are clustered against one another and based on the differences in the sequences themselves, reads are assigned OTUs.

### Closed-reference OTU picking
##### Reads are clustered against a reference database and ones without hits are discarded. 

### Open-reference OTU picking (recommended!)
##### Reads are clusterest against a reference database and ones without hits are clustered de novo. 