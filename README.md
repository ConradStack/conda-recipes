# recipes for conda

Recipes for building some of my other repositories in to conda packages.

## Testing through docker

Step 0 below downloads my conda (python 2.7) testing container for dockerhub and starts an interactive session.  Steps 1--6 should be executed within the container

```
# 0. pull docker image for conda (py2.7) from dockerhub and run
docker pull conradstack/testing:conda27
docker run -i -t conradstack/testing:conda27 /bin/bash

# 1. create test Environment (and activate)
mkdir junk && cd junk

# 2. get my conda recipes
git clone https://github.com/ConradStack/conda-recipes.git && cd conda-recipes/bioawk

# 3. build bioawk package (it will download from github)
conda-build .

# 4. install the local build from (3)
conda install -y bioawk --use-local

# 5. test the installation
## 5.a get seqence lengths
printf ">foo\nAAAAAA\n>bar\nGGGGGGGGGGGGGGGG\n" | bioawk -c fastx '{ print $name, length($seq) }' 
##	>foo	6
##	>bar	16

### 5.b find minimum quality value (unique to my fork of bioawk)
printf "@foo\nAAAAAA\n+bar\n000000\n" | bioawk -c fastx 'BEGIN{x=0;MAX=-150} { if(NR <= 10000){ x=maxqual($qual); MAX = (MAX > x ? MAX : x);	} else { exit; } } END{ print MAX+33 }'
## >48

## 6. clean up and exit
exit
```




