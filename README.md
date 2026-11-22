# MSCS 634 Lab 5: Clustering Techniques Using DBSCAN and Hierarchical Clustering

Rahul Solanki, MSCS-634-B01: Advanced Big Data and Data Mining, Professor Satish Penmatsa

## Purpose

Cluster the scikit-learn Wine dataset (178 wines, 13 chemical features, 3 cultivars) with Agglomerative Hierarchical Clustering and DBSCAN, see how their parameters change the clusters, and score the results with the Silhouette, Homogeneity, and Completeness scores (plus the Adjusted Rand Index).

| File | Contents |
|---|---|
| `MSCS_634_Lab_5.ipynb` | All code, outputs, plots, and the written analysis |
| `requirements.txt` | Python packages needed to run the notebook |

## Key insights

All features z-score standardized first; plots show the first two principal components (55.4% of the variance), while all clustering and scoring uses the full 13 features.

| Method | Clusters | Noise | Silhouette | Homogeneity | Completeness | ARI |
|---|---|---|---|---|---|---|
| Agglomerative, Ward, n = 3 | 3 | 0% | 0.277 | 0.790 | 0.783 | 0.790 |
| Agglomerative, complete, n = 3 | 3 | 0% | 0.204 | 0.616 | 0.613 | 0.577 |
| DBSCAN, eps 2.5, min_samples 10 | 2 | 18.0% | 0.323* | 0.504 | 0.560 | 0.424 |
| DBSCAN on 2 PCs, eps 0.7, min_samples 10 | 4 | 11.2% | 0.261* | 0.753 | 0.575 | 0.663 |

\* silhouette over non-noise wines only.

- **Hierarchical (Ward) worked best.** n_clusters = 3 gives the highest silhouette and the best balance of homogeneity and completeness. Fewer clusters merge cultivars (n = 2: homogeneity 0.444); more clusters split them (n = 6: completeness 0.553).
- **The dendrogram points to 3 clusters on its own:** the last two merges are at heights 27.65 and 35.40, while every earlier merge is at 12.57 or below, so any cut between those heights gives exactly three groups.
- **Linkage matters as much as n_clusters.** Ward gives balanced clusters; average and single linkage chain 174 of 178 wines into one cluster.
- **DBSCAN never recovered the three cultivars in 13 dimensions.** Its best setting found 2 clusters: class_2 alone, and class_0 merged with most of class_1. 25 of its 32 noise points are class_1 wines. Its silhouette looks higher than Ward's only because the hardest wines were dropped as noise.
- **eps and min_samples act in opposite directions.** Small eps or large min_samples turn most wines into noise (up to 100%); large eps merges everything into one cluster. The useful range was narrow (eps about 2.25 to 2.75), and the k-distance plot pointed to it.
- **Fewer dimensions help DBSCAN:** on the 2 principal components it finds the three cultivars plus one small extra group (ARI 0.663), still below Ward.

## Challenges and decisions

- **Scaling:** features range from below 1 (hue) to 1,680 (proline), so everything is standardized before clustering.
- **Scoring noise fairly:** DBSCAN's noise points are left out of the silhouette (noise is not a cluster) but kept as their own group for homogeneity and completeness (scikit-learn's convention). Settings where every wine is noise show completeness 1.000, which is meaningless and is pointed out in the notebook.
- **Picking eps:** a k-distance plot (distance to each wine's 10th nearest neighbor) narrowed the search, then a grid of 7 eps values x 4 min_samples values was tested.
- **Comparable plots:** cluster numbers are arbitrary, so clusters are numbered left to right along PC1, and the dendrogram's branch colors are matched to the 3-cluster scatter plot.
- **Extra check:** DBSCAN was also run on the 2 principal components to test whether the 13-dimensional space (where distances between wines become similar) was the reason it struggled.

## How to run

Python 3.11 with the packages in `requirements.txt` (`pip install -r requirements.txt`). Open `MSCS_634_Lab_5.ipynb` and run all cells; the dataset is loaded from scikit-learn, so no data files are needed.
