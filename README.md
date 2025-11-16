# 📘 **Clustering Algorithm Benchmark Study**






This repository provides a comprehensive benchmark of classical clustering algorithms using different preprocessing techniques applied to a non-linear synthetic dataset. The goal is to evaluate how preprocessing (scaling, PCA, UMAP) affects clustering performance on complex geometric structures such as moon-shaped clusters.

<img width="499" height="320" alt="image" src="https://github.com/user-attachments/assets/420a0ef4-9875-4900-8b64-01dfea3881af" />

📌 **Objectives**

This study aims to:

1. Compare several clustering algorithms on non-linear synthetic data
2. Evaluate the impact of scaling, PCA, and UMAP on clustering performance
3. Measure cluster quality using silhouette scores and WSS (Elbow Method)
4. Visualize results and interpret algorithm behavior on challenging dataset shapes
5. Produce a reproducible benchmark that can be extended for future research

🧠 **Algorithms Evaluated**

| Algorithm                    | Description                                                                |
| ---------------------------- | -------------------------------------------------------------------------- |
| **K-Means**                  | Partition-based clustering using Euclidean distance                        |
| **Agglomerative Clustering** | Hierarchical merging using Ward / single linkage                           |
| **DBSCAN**                   | Density-based clustering, good for arbitrary shapes                        |
| **Spectral Clustering**      | Graph-based clustering suitable for non-linear boundaries                  |
| **HDBSCAN**                  | Hierarchical density-based clustering; detects varying densities and noise |


🧰 **Preprocessing Variants**

The same dataset is run through different preprocessing pipelines:

1. No preprocessing (raw data)
2. Scaling only (standardization)
3. PCA 
4. UMAP (non-linear dimensionality reduction)
5. Each preprocessing method affects distance calculations and cluster separability differently.

🎯 **Dataset**

The benchmark uses synthetic non-linear datasets:

  - mlbench.smiley – a dataset with moon-shaped clusters and noise.
  - Beneficial for testing algorithm robustness on complex geometric structures.

Characteristics:

  - Fundamentally, the moon-shaped dataset is expected to naturally form three to four clusters based on its underlying structure.
  - Non-linearly separable
  - Contains noise and curved clusters
  - Ideal for comparing clustering algorithms

📊 **Evaluation Metrics**
1. WSS (Within-Cluster Sum of Squares) — Elbow Method : Used to determine the optimal cluster count for K-Means.
2. Silhouette Score : Measures cluster separation and cohesion. A higher score = better clustering.
3. Visual Assessment. All clustering results are visualized using:
   - ggplot2
   - factoextra
   - UMAP/PCA scatterplots

🔍 **Baseline Clustering Without Scaling, PCA, or UMAP**

<img width="521" height="326" alt="image" src="https://github.com/user-attachments/assets/1255828c-b05d-463d-8d92-a3f3dc9e0d42" />

This experiment applies K-Means directly to the original moon-shaped dataset. Because the data has non-linear, curved boundaries, K-Means is expected to struggle when no preprocessing is applied.

Both the Elbow Method and Silhouette Score misidentify the optimal number of clusters—suggesting k = 5–6, even though the true structure is closer to 3–4 clusters. This happens because K-Means relies on Euclidean distance and assumes spherical clusters, making it unreliable for this type of geometry.

Key Findings:

  1. Elbow Method points to k=5, not the expected 3–4.

<img width="538" height="321" alt="image" src="https://github.com/user-attachments/assets/c7a28b32-bcd1-44df-87f5-1a0a002aa178" />

  2. Highest Silhouette Score appears at k=6.

<img width="512" height="305" alt="image" src="https://github.com/user-attachments/assets/94ff0b3d-a999-4578-be6c-18afc0aeb4b7" />

  3. No scaling/PCA/UMAP → K-Means fails to capture the non-linear structure.
  4. K-Means is highly sensitive to curved, non-spherical shapes.
  5. After applying UMAP, K-Means aligns much better with the true cluster structure (will be shown later).


📝**Explanation: K-Means on Scaled Data (No PCA, No UMAP)**

When applying K-Means to the moon-shaped (“Smiley”) dataset using only feature scaling, the algorithm still struggles to correctly separate the natural structures in the data. Although scaling standardizes feature magnitude—making optimization more stable—it does not address the underlying issue:
the dataset contains non-linear, curved shapes that are incompatible with K-Means’ Euclidean-distance assumptions.

**Key Observations** (Scaled Only):

<img width="505" height="310" alt="image" src="https://github.com/user-attachments/assets/632e3bba-ba89-4de0-8785-35a3e89aac43" />

  1. The eye and mouth regions merge into the same cluster.
  2. Scaling adjusts feature ranges but does not reshape the curved geometry. As a result, K-Means still attempts to partition the data into spherical regions, causing unrelated parts (like the eyes and mouth) to fall into one cluster.
  3. Cluster boundaries remain inaccurate.
  4. The curved moon shape cannot be captured well by straight-line Voronoi partitions, even after scaling.
  5. Silhouette Scores remain low.
  6. The silhouette score reflects the mismatch between the algorithm’s assumptions and the actual structure. Even though the data is scaled, the score indicates that cluster assignments are not well separated.
<img width="488" height="300" alt="image" src="https://github.com/user-attachments/assets/6d6513b9-c13b-4f01-8c96-39a0c5e16ce9" />

  7. Elbow and silhouette optimization still point to the wrong k.
  8. Similar to the raw-data experiment, the best k from both methods typically shifts to 5 or 6, not the expected 3–4 clusters, highlighting that scaling alone does not reveal the true shape of the data.

<img width="491" height="298" alt="image" src="https://github.com/user-attachments/assets/40fb7752-1048-4745-83e1-0e73410d4399" />

**Why Scaling Alone Is Not Enough?**

Scaling helps when cluster separation is influenced by differences in feature magnitude—not when the data’s geometry is fundamentally non-linear.
Since the moon-shaped dataset contains curved manifolds, distance-based algorithms like K-Means cannot properly interpret the structure without:

- PCA (partial improvement but still insufficient), or
- UMAP (fully reshapes the manifold into a form more compatible with K-Means).

Thus, scaling is beneficial but not a solution for non-linear shapes.

📝 **Explanation: PCA + Scaling Before K-Means**

Applying scaling followed by PCA improves numerical stability and removes correlated variance—but it still does not fix the core issue: the moon-shaped dataset contains non-linear structures that PCA cannot unfold.

<img width="508" height="317" alt="image" src="https://github.com/user-attachments/assets/bbb7d059-da89-4b99-acee-159f5e908107" />

PCA is a linear transformation. It rotates and projects the data onto axes of maximum variance, but it cannot “uncurve” the moon-shaped patterns. As a result, K-Means continues to struggle despite the cleaner feature space.

**Key Observations** (Scaled + PCA):

1. The moon-shaped arcs remain intertwined after PCA.
2. PCA cannot separate curved manifolds. It only projects them into orthogonal axes, so the underlying geometry remains tangled.
3. Clusters are still inaccurately formed.
4. K-Means still tries to enforce spherical partitions. Even though PCA makes the features orthogonal and centered, the algorithm cannot identify curved boundaries.
5. The eye and mouth regions often remain in the same cluster. While PCA may slightly shift or stretch the structure, it does not meaningfully isolate the components of the smiley face.
6. Silhouette Scores improve slightly but remain low. Because PCA reduces noise and decorrelates features, silhouette performance increases a bit compared to raw or scaled-only input. However, the improvement is not enough to reflect the true cluster structure.
7. Elbow and Silhouette methods still suggest incorrect k.

<img width="497" height="284" alt="image" src="https://github.com/user-attachments/assets/085d3eeb-5bc8-41c6-871b-c40e028eed77" />

<img width="497" height="281" alt="image" src="https://github.com/user-attachments/assets/bc76a665-0a5e-43e2-9eb0-0c5f20e8b758" />

8. Similar to the previous experiments:
   - The Elbow often points to k = 5
   - Silhouette may peak around k = 6
  These recommendations continue to contradict the expected 3–4 clusters, reinforcing that PCA alone is insufficient.

**Why PCA Still Fails on Moon-Shaped Data?**

PCA assumes that important information is captured along linear axes of variance.
But the moon shape’s essential structure is non-linear and depends on its curvature—not its axes of maximum variance.
Therefore, the data’s geometry survives PCA largely intact.
Unlike UMAP, PCA does not reshape the manifold; it only rotates and scales it.

📝**Explanation: Scaling + UMAP Before K-Means**

Applying Scaling + UMAP transforms the moon-shaped dataset into a representation that aligns well with K-Means’ assumptions.

<img width="498" height="317" alt="image" src="https://github.com/user-attachments/assets/9d5e134c-18d1-40c6-b825-0eda0ff813b1" />

<img width="499" height="314" alt="image" src="https://github.com/user-attachments/assets/dda943bf-d7c5-4d9e-ab5c-29959d46e629" />

Unlike PCA, which is linear, UMAP is a non-linear manifold learning technique. It captures the intrinsic geometry of the data and preserves local neighborhood relationships.

After scaling and UMAP reduction, the previously curved and intertwined structures become more separable, enabling K-Means to identify clusters that closely match the true underlying pattern.

**Key Observations (Scaled + UMAP)**:

1. The moon-shaped arcs become clearly separated.
2. UMAP unwraps and flattens the curved geometry, making the cluster boundaries more linear and more suitable for K-Means.
3. The eye and mouth regions no longer merge.
4. Unlike the “scaled only” and “PCA + scaled” setups, UMAP distinguishes local neighborhoods effectively, preventing unrelated regions from collapsing into the same cluster.
5. Silhouette Scores improve significantly. Because UMAP restructures the data into a more cluster-friendly space, the silhouette score rises notably compared to all previous preprocessing approaches.
   
   <img width="490" height="285" alt="image" src="https://github.com/user-attachments/assets/a5f5d519-b378-4de4-8347-d2871bb09fe2" />
   
7. Elbow and silhouette now point closer to the expected 3–4 clusters.
   
   <img width="504" height="310" alt="image" src="https://github.com/user-attachments/assets/618c4ba6-7665-4bcd-9c07-6aebd7313bc0" />
   
9. UMAP reduces the distortion in distances, making cluster evaluation metrics more reliable.
10. As a result, both k-selection methods become more aligned with the true structure of the smiley dataset.

**Why UMAP Works (When PCA Fails)**
1. UMAP preserves local topology rather than global variance.
2. It handles non-linear shapes, unrolling the manifold into a simpler form.
3. Distance-based algorithms (like K-Means) perform correctly only after the structure is reshaped into something approximately spherical or linearly separable.

UMAP does exactly this—PCA does not.



**🧪 Clustering Beyond K-Means (Scaled + UMAP)**

After evaluating K-Means extensively, I explored other clustering algorithms on the scaled + UMAP-transformed moon-shaped dataset. All k-based algorithms use k=4.
  1. **Agglomerative Clustering (Ward.D2)**
     - Captures hierarchical relationships and separates main moon and facial features.
     - Clusters on UMAP space are well-formed with minimal overlap.
     - Mapping to original data shows cluster assignments aligned with curved structures.
     - Silhouette Score: ~0.769

<p float="left">
  <img src="https://github.com/user-attachments/assets/6545fbbc-e283-41d5-950f-a1b9135f2cca" width="48%" />
  <img src="https://github.com/user-attachments/assets/b9ab5315-ff9a-4c68-826c-69ae063e2418" width="48%" />
</p>

  2. **Spectral Clustering**
     - Effectively handles non-linear structures after UMAP.
     - Clusters correspond neatly to moon and facial components in UMAP and original data.
     - Silhouette Score: ~0.769

<p float="left">
  <img src="https://github.com/user-attachments/assets/13883a8c-758a-4641-b1a6-18edc180a1e6" width="48%" />
  <img src="https://github.com/user-attachments/assets/edc31f2f-b930-4119-8763-738bffbceb0d" width="48%" />
</p>
       
  3. **DBSCAN (Density-Based)**
     - Detects moon shape without specifying k.
     - Noise points appear scattered; main clusters follow curved structures.
     - Mapping to original data captures moon and eyes/mouth, but some points classified as noise.
     - Silhouette Score: Not reliable due to noise points.
         - Silhouette Score is not calculated for DBSCAN because it requires each point to belong to a cluster.
         - DBSCAN marks some points as noise, which do not belong to any cluster, making silhouette calculation invalid for the full dataset.
         - Alternative evaluation methods include visual inspection or density-based metrics.


<p float="left">
  <img src="https://github.com/user-attachments/assets/462a2219-5436-4348-ba94-bc504318bee8" width="48%" />
  <img src="https://github.com/user-attachments/assets/b4e319d7-cefd-4bfa-8124-3acd8a10af72" width="48%" />
</p>


  4. **HDBScan**
     Why HDBScan is actually popular: HDBSCAN is widely used because it is flexible, noise-aware, density-adaptive, and does not require a predefined number of clusters.
     Observation: HDBSCAN was applied on scaled + UMAP-transformed moon-shaped data.
     Result on UMAP: HDBSCAN struggled to separate the moon and facial features correctly; eyes and mouth were often merged or labeled as noise.
     Result on Original Data: Cluster assignments did not match the expected “smiley” structure.
     Silhouette Score: Not calculated due to noise points.
     Notes:
     - HDBSCAN is generally effective for datasets with varying density and non-linear clusters.
     - In this specific case, the moon-shaped dataset has curved clusters that are challenging for density-based clustering, even after UMAP.
     - Scaling is used and UMAP helps, but the underlying density distribution still causes HDBSCAN to fail in producing meaningful clusters.
     - Visual inspection is recommended to assess cluster quality, rather than relying on Silhouette Score.
    
<p float="left">
  <img src="https://github.com/user-attachments/assets/1782cfe1-21d6-43cb-8bd5-ca8f74c07d1d" width="48%" />
  <img src="https://github.com/user-attachments/assets/cf1792a4-e494-46bd-9fcc-cef9573a37be" width="48%" />
</p>

🏁 **Conclusion**

Proper preprocessing, specifically Scaling combined with UMAP, is essential for accurately clustering non-linear moon-shaped data. 
K-Means, Agglomerative, and Spectral clustering (k=4) effectively capture the underlying structure, while density-based methods like DBSCAN and HDBSCAN are less reliable in this case due to noise handling and cluster merging. 
Overall, k-based clustering on a preprocessed feature space provides the most accurate and interpretable results for non-linear cluster patterns.


  👩‍💻 **Author**

Debrina Silviana Dewi Sugiharto

debrinasilviana@gmail.com

Data Scientist — 10+ years of experience in geophysics and banking analytics.
