DTI (pre)processing is an opinionated process with many opinions on exactly what to do. Here are some ideas. 

## Preprocessing with FSL

Certainly look to the [FDT user guide](http://fsl.fmrib.ox.ac.uk/fsl/fslwiki/FDT/UserGuide).

If you're using `eddy_correct` and `dtifit` to correct and generate your tensor, we have a simple script to do the appropriate steps for an image: [dtifit.sh](https://github.com/TIGRLab/datman/blob/master/assets/dtifit.sh)

### Rotate your bvecs? 

When you run `eddy_correct` on your image, you end up rotating your volumes to align them in some way. It's an open question as to whether you should also then rotate the associated bvecs. See [our discussion](https://github.com/TIGRLab/admin/issues/508) on the topics. FSL comes with a script to do this rotation based on the log files that `eddy_correct` makes: ` fdt_rotate_bvecs`.


## The ENIGMA / JHU White Matter ROIs

Read more about the ENIGMA Protocol from [their website](http://enigma.usc.edu/protocols/dti-protocols/)

##### The ENIGMA Protocol Paper

Acheson, A., Wijtenburg, S. A., Rowland, L. M., Winkler, A., Mathias, C. W., Hong, L. E., … Dougherty, D. M. (2017). Reproducibility of tract‐based white matter microstructural measures using the ENIGMA‐DTI protocol. Brain and Behavior, 7(2), e00615. http://doi.org/10.1002/brb3.615
[PMC Full Text](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC5318368/) 

Open the atlas in [NeuroVault](http://neurovault.org/collections/264/)

| Integer|Label   |Tract Name                                                                                                 |
|-------:|:-------|:----------------------------------------------------------------------------------------------------------|
|       3|GCC     |Genu of corpus callosum                                                                                    |
|       4|BCC     |Body of corpus callosum                                                                                    |
|       5|SCC     |Splenium of corpus callosum                                                                                |
|       6|FX      |Fornix (column and body of fornix)                                                                         |
|       7|CST-R   |Corticospinal tract right                                                                                  |
|       8|CST-L   |Corticospinal tract left                                                                                   |
|       9|ML-R    |Medial lemniscus right                                                                                     |
|      10|ML-L    |Medial lemniscus left                                                                                      |
|      11|ICP-R   |Inferior cerebellar peduncle right                                                                         |
|      12|ICP-L   |Inferior cerebellar peduncle left                                                                          |
|      13|SCP-R   |Superior cerebellar peduncle right                                                                         |
|      14|SCP-L   |Superior cerebellar peduncle left                                                                          |
|      15|CP-R    |Cerebral peduncle right                                                                                    |
|      16|CP-L    |Cerebral peduncle left                                                                                     |
|      17|ALIC-R  |Anterior limb of internal capsule right                                                                    |
|      18|ALIC-L  |Anterior limb of internal capsule left                                                                     |
|      19|PLIC-R  |Posterior limb of internal capsule right                                                                   |
|      20|PLIC-L  |Posterior limb of internal capsule left                                                                    |
|      21|RLIC-R  |Retrolenticular part of internal capsule right                                                             |
|      22|RLIC-L  |Retrolenticular part of internal capsule left                                                              |
|      23|ACR-R   |Anterior corona radiata right                                                                              |
|      24|ACR-L   |Anterior corona radiata left                                                                               |
|      25|SCR-R   |Superior corona radiata right                                                                              |
|      26|SCR-L   |Superior corona radiata left                                                                               |
|      27|PCR-R   |Posterior corona radiata right                                                                             |
|      28|PCR-L   |Posterior corona radiata left                                                                              |
|      29|PTR-R   |Posterior thalamic radiation (include optic radiation) right                                               |
|      30|PTR-L   |Posterior thalamic radiation (include optic radiation) left                                                |
|      31|SS-R    |Sagittal stratum (include inferior longitidinal fasciculus and inferior fronto-occipital fasciculus) right |
|      32|SS-L    |Sagittal stratum (include inferior longitidinal fasciculus and inferior fronto-occipital fasciculus) left  |
|      33|EC-R    |External capsule right                                                                                     |
|      34|EC-L    |External capsule left                                                                                      |
|      35|CGC-R   |Cingulum (cingulate gyrus) right                                                                           |
|      36|CGC-L   |Cingulum (cingulate gyrus) left                                                                            |
|      37|CGH-R   |Cingulum (hippocampus) right                                                                               |
|      38|CGH-L   |Cingulum (hippocampus) left                                                                                |
|      39|FX/ST-R |Fornix (cres) / Stria terminalis (can not be resolved with current resolution) right                       |
|      40|FX/ST-L |Fornix (cres) / Stria terminalis (can not be resolved with current resolution) left                        |
|      41|SLF-R   |Superior longitudinal fasciculus right                                                                     |
|      42|SLF-L   |Superior longitudinal fasciculus left                                                                      |
|      43|SFO-R   |Superior fronto-occipital fasciculus (could be a part of anterior internal capsule) right                  |
|      44|SFO-L   |Superior fronto-occipital fasciculus (could be a part of anterior internal capsule) left                   |
|      45|IFO-R   |Inferior fronto-occipital fasciculus right                                                                 |
|      46|IFO-L   |Inferior fronto-occipital fasciculus left                                                                  |
|      47|UNC-R   |Uncinate fasciculus right                                                                                  |
|      48|UNC-L   |Uncinate fasciculus left                                                                                   |
 
