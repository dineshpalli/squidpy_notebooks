
.. DO NOT EDIT.
.. THIS FILE WAS AUTOMATICALLY GENERATED BY SPHINX-GALLERY.
.. TO MAKE CHANGES, EDIT THE SOURCE PYTHON FILE:
.. "auto_tutorials/tutorial_merfish.py"
.. LINE NUMBERS ARE GIVEN BELOW.

.. only:: html

  .. container:: binder-badge

    .. image:: images/binder_badge_logo.svg
      :target: https://mybinder.org/v2/gh/scverse/squidpy_notebooks/main?filepath=docs/source/auto_tutorials/tutorial_merfish.ipynb
      :alt: Launch binder
      :width: 150 px

.. rst-class:: sphx-glr-example-title

.. _sphx_glr_auto_tutorials_tutorial_merfish.py:

Analyze Merfish data
========================

This tutorial shows how to apply Squidpy for the analysis of Merfish data.

The data used here was obtained from :cite:`Moffitt2018-me`.
We provide a pre-processed subset of the data, in :class:`anndata.AnnData` format.
For details on how it was pre-processed, please refer to the original paper.

.. seealso::

    See :ref:`sphx_glr_auto_tutorials_tutorial_slideseqv2.py` and
    :ref:`sphx_glr_auto_tutorials_tutorial_seqfish.py` for additional analysis examples.

Import packages & data
----------------------
To run the notebook locally, create a conda environment as *conda env create -f environment.yml* using this
`environment.yml <https://github.com/scverse/squidpy_notebooks/blob/main/environment.yml>`_.

.. GENERATED FROM PYTHON SOURCE LINES 22-33

.. code-block:: default


    import scanpy as sc
    import squidpy as sq

    sc.logging.print_header()
    print(f"squidpy=={sq.__version__}")

    # load the pre-processed dataset
    adata = sq.datasets.merfish()
    adata





.. rst-class:: sphx-glr-script-out

 Out:

 .. code-block:: none

    scanpy==1.9.1 anndata==0.8.0 umap==0.5.3 numpy==1.21.6 scipy==1.8.0 pandas==1.4.2 scikit-learn==1.1.0 statsmodels==0.13.2 python-igraph==0.9.10 pynndescent==0.5.7
    squidpy==1.2.1
      0%|          | 0.00/49.2M [00:00<?, ?B/s]      0%|          | 56.0k/49.2M [00:00<02:05, 409kB/s]      0%|          | 240k/49.2M [00:00<00:54, 949kB/s]       2%|2         | 0.98M/49.2M [00:00<00:16, 3.03MB/s]      8%|8         | 4.00M/49.2M [00:00<00:04, 10.6MB/s]     20%|#9        | 9.72M/49.2M [00:00<00:01, 21.9MB/s]     32%|###1      | 15.5M/49.2M [00:00<00:01, 29.0MB/s]     43%|####3     | 21.4M/49.2M [00:00<00:00, 33.5MB/s]     55%|#####5    | 27.2M/49.2M [00:01<00:00, 36.5MB/s]     67%|######7   | 33.1M/49.2M [00:01<00:00, 38.5MB/s]     79%|#######9  | 39.0M/49.2M [00:01<00:00, 40.0MB/s]     91%|######### | 44.7M/49.2M [00:01<00:00, 40.7MB/s]    100%|##########| 49.2M/49.2M [00:01<00:00, 31.2MB/s]

    AnnData object with n_obs × n_vars = 73655 × 161
        obs: 'Cell_ID', 'Animal_ID', 'Animal_sex', 'Behavior', 'Bregma', 'Centroid_X', 'Centroid_Y', 'Cell_class', 'Neuron_cluster_ID', 'batch'
        uns: 'Cell_class_colors'
        obsm: 'spatial', 'spatial3d'



.. GENERATED FROM PYTHON SOURCE LINES 34-38

This datasets consists of consecutive slices from the mouse hypothalamic preoptic region.
It represents an interesting example of how to work with 3D spatial data in Squidpy.
Let's start with visualization: we can either visualize the 3D stack of slides
using :func:`scanpy.pl.embedding`:

.. GENERATED FROM PYTHON SOURCE LINES 38-40

.. code-block:: default

    sc.pl.embedding(adata, basis="spatial3d", projection="3d", color="Cell_class")




.. image-sg:: /auto_tutorials/images/sphx_glr_tutorial_merfish_001.png
   :alt: Cell_class
   :srcset: /auto_tutorials/images/sphx_glr_tutorial_merfish_001.png
   :class: sphx-glr-single-img





.. GENERATED FROM PYTHON SOURCE LINES 41-43

Or visualize a single slide with :func:`scanpy.pl.spatial`. Here the slide identifier
is stored in `adata.obs["Bregma"]`, see original paper for definition.

.. GENERATED FROM PYTHON SOURCE LINES 43-46

.. code-block:: default


    sc.pl.spatial(adata[adata.obs.Bregma == -9], color="Cell_class", spot_size=0.01)




.. image-sg:: /auto_tutorials/images/sphx_glr_tutorial_merfish_002.png
   :alt: Cell_class
   :srcset: /auto_tutorials/images/sphx_glr_tutorial_merfish_002.png
   :class: sphx-glr-single-img





.. GENERATED FROM PYTHON SOURCE LINES 47-60

Neighborhood enrichment analysis in 3D
--------------------------------------
It is important to consider whether the analysis should be performed on the 3D
spatial coordinates or the 2D coordinates for a single slice. Functions that
make use of the spatial graph can already support 3D coordinates, but it is important
to consider that the z-stack coordinate is in the same unit metrics as the x, y coordinates.
Let's start with the neighborhood enrichment score. You can read more on the function
in the docs at :ref:`sphx_glr_auto_examples_graph_compute_spatial_neighbors.py`.
First, we need to compute a neighbor graph with :func:`squidpy.gr.spatial_neighbors`.
If we want to compute the neighbor graph on the 3D coordinate space,
we need to specify ``spatial_key = "spatial3d"``.
Then we can use :func:`squidpy.gr.nhood_enrichment` to compute the score, and visualize
it with :func:`squidpy.gr.nhood_enrichment`.

.. GENERATED FROM PYTHON SOURCE LINES 60-64

.. code-block:: default

    sq.gr.spatial_neighbors(adata, coord_type="generic", spatial_key="spatial3d")
    sq.gr.nhood_enrichment(adata, cluster_key="Cell_class")
    sq.pl.nhood_enrichment(adata, cluster_key="Cell_class", method="single", cmap="inferno", vmin=-50, vmax=100)




.. image-sg:: /auto_tutorials/images/sphx_glr_tutorial_merfish_003.png
   :alt: Neighborhood enrichment
   :srcset: /auto_tutorials/images/sphx_glr_tutorial_merfish_003.png
   :class: sphx-glr-single-img


.. rst-class:: sphx-glr-script-out

 Out:

 .. code-block:: none

      0%|          | 0/1000 [00:00<?, ?/s]      1%|          | 8/1000 [00:00<00:12, 76.49/s]      3%|2         | 26/1000 [00:00<00:07, 129.68/s]      4%|4         | 44/1000 [00:00<00:06, 148.57/s]      6%|6         | 62/1000 [00:00<00:05, 156.98/s]      8%|8         | 80/1000 [00:00<00:05, 162.54/s]     10%|9         | 97/1000 [00:00<00:05, 164.01/s]     12%|#1        | 115/1000 [00:00<00:05, 167.84/s]     13%|#3        | 133/1000 [00:00<00:05, 169.63/s]     15%|#5        | 151/1000 [00:00<00:04, 171.76/s]     17%|#6        | 169/1000 [00:01<00:04, 172.44/s]     19%|#8        | 187/1000 [00:01<00:04, 173.56/s]     20%|##        | 205/1000 [00:01<00:04, 174.43/s]     22%|##2       | 223/1000 [00:01<00:04, 175.09/s]     24%|##4       | 241/1000 [00:01<00:04, 172.41/s]     26%|##5       | 259/1000 [00:01<00:04, 172.03/s]     28%|##7       | 277/1000 [00:01<00:04, 170.93/s]     30%|##9       | 295/1000 [00:01<00:04, 170.88/s]     31%|###1      | 313/1000 [00:01<00:03, 172.09/s]     33%|###3      | 331/1000 [00:01<00:03, 168.41/s]     35%|###4      | 349/1000 [00:02<00:03, 169.77/s]     37%|###6      | 366/1000 [00:02<00:03, 167.79/s]     38%|###8      | 383/1000 [00:02<00:03, 166.50/s]     40%|####      | 401/1000 [00:02<00:03, 169.08/s]     42%|####1     | 419/1000 [00:02<00:03, 170.10/s]     44%|####3     | 437/1000 [00:02<00:03, 168.50/s]     45%|####5     | 454/1000 [00:02<00:03, 168.22/s]     47%|####7     | 472/1000 [00:02<00:03, 169.43/s]     49%|####8     | 489/1000 [00:02<00:03, 169.00/s]     51%|#####     | 506/1000 [00:03<00:02, 167.41/s]     52%|#####2    | 523/1000 [00:03<00:02, 166.19/s]     54%|#####4    | 541/1000 [00:03<00:02, 169.68/s]     56%|#####5    | 558/1000 [00:03<00:02, 165.27/s]     57%|#####7    | 575/1000 [00:03<00:02, 166.29/s]     59%|#####9    | 592/1000 [00:03<00:02, 163.50/s]     61%|######1   | 610/1000 [00:03<00:02, 167.24/s]     63%|######2   | 627/1000 [00:03<00:02, 167.98/s]     64%|######4   | 644/1000 [00:03<00:02, 168.56/s]     66%|######6   | 661/1000 [00:03<00:02, 168.83/s]     68%|######7   | 679/1000 [00:04<00:01, 171.22/s]     70%|######9   | 697/1000 [00:04<00:01, 171.32/s]     72%|#######1  | 715/1000 [00:04<00:01, 169.08/s]     73%|#######3  | 733/1000 [00:04<00:01, 169.76/s]     75%|#######5  | 752/1000 [00:04<00:01, 170.17/s]     77%|#######7  | 770/1000 [00:04<00:01, 170.56/s]     79%|#######8  | 788/1000 [00:04<00:01, 169.37/s]     81%|########  | 806/1000 [00:04<00:01, 170.45/s]     82%|########2 | 824/1000 [00:04<00:01, 171.25/s]     84%|########4 | 843/1000 [00:05<00:00, 171.93/s]     86%|########6 | 861/1000 [00:05<00:00, 174.24/s]     88%|########7 | 879/1000 [00:05<00:00, 170.44/s]     90%|########9 | 897/1000 [00:05<00:00, 171.83/s]     92%|#########1| 915/1000 [00:05<00:00, 171.46/s]     93%|#########3| 933/1000 [00:05<00:00, 172.27/s]     95%|#########5| 951/1000 [00:05<00:00, 173.29/s]     97%|#########6| 969/1000 [00:05<00:00, 173.79/s]     99%|#########8| 988/1000 [00:05<00:00, 173.35/s]    100%|##########| 1000/1000 [00:05<00:00, 168.91/s]




.. GENERATED FROM PYTHON SOURCE LINES 65-68

We can visualize some of the co-enriched clusters with :func:`scanpy.pl.embedding`.
We will set `na_colors=(1,1,1,0)` to make transparent the other observations,
in order to better visualize the clusters of interests across z-stacks.

.. GENERATED FROM PYTHON SOURCE LINES 68-77

.. code-block:: default

    sc.pl.embedding(
        adata,
        basis="spatial3d",
        groups=["OD Mature 1", "OD Mature 2", "OD Mature 4"],
        na_color=(1, 1, 1, 0),
        projection="3d",
        color="Cell_class",
    )




.. image-sg:: /auto_tutorials/images/sphx_glr_tutorial_merfish_004.png
   :alt: Cell_class
   :srcset: /auto_tutorials/images/sphx_glr_tutorial_merfish_004.png
   :class: sphx-glr-single-img


.. rst-class:: sphx-glr-script-out

 Out:

 .. code-block:: none

    /home/runner/work/squidpy_notebooks/squidpy_notebooks/.tox/docs/lib/python3.9/site-packages/scanpy/plotting/_tools/scatterplots.py:1171: FutureWarning: Categorical.replace is deprecated and will be removed in a future version. Use Series.replace directly instead.
      values = values.replace(values.categories.difference(groups), np.nan)




.. GENERATED FROM PYTHON SOURCE LINES 78-80

We can also visualize gene expression in 3D coordinates. Let's perform differential
expression testing with :func:`scanpy.tl.rank_genes_groups` and visualize the results

.. GENERATED FROM PYTHON SOURCE LINES 80-83

.. code-block:: default

    sc.tl.rank_genes_groups(adata, groupby="Cell_class")
    sc.pl.rank_genes_groups(adata, groupby="Cell_class")




.. image-sg:: /auto_tutorials/images/sphx_glr_tutorial_merfish_005.png
   :alt: Ambiguous vs. rest, Astrocyte vs. rest, Endothelial 1 vs. rest, Endothelial 2 vs. rest, Endothelial 3 vs. rest, Ependymal vs. rest, Excitatory vs. rest, Inhibitory vs. rest, Microglia vs. rest, OD Immature 1 vs. rest, OD Immature 2 vs. rest, OD Mature 1 vs. rest, OD Mature 2 vs. rest, OD Mature 3 vs. rest, OD Mature 4 vs. rest, Pericytes vs. rest
   :srcset: /auto_tutorials/images/sphx_glr_tutorial_merfish_005.png
   :class: sphx-glr-single-img





.. GENERATED FROM PYTHON SOURCE LINES 84-85

and the expression in 3D.

.. GENERATED FROM PYTHON SOURCE LINES 85-87

.. code-block:: default

    sc.pl.embedding(adata, basis="spatial3d", projection="3d", color=["Gad1", "Mlc1"])




.. image-sg:: /auto_tutorials/images/sphx_glr_tutorial_merfish_006.png
   :alt: Gad1, Mlc1
   :srcset: /auto_tutorials/images/sphx_glr_tutorial_merfish_006.png
   :class: sphx-glr-single-img





.. GENERATED FROM PYTHON SOURCE LINES 88-91

If the same analysis should be performed on a single slice, then it is advisable to
copy the sample of interest in a new :class:`anndata.AnnData` and use it as
a standard 2D spatial data object.

.. GENERATED FROM PYTHON SOURCE LINES 91-101

.. code-block:: default

    adata_slice = adata[adata.obs.Bregma == -9].copy()
    sq.gr.spatial_neighbors(adata_slice, coord_type="generic")
    sq.gr.nhood_enrichment(adata, cluster_key="Cell_class")
    sc.pl.spatial(
        adata_slice,
        color="Cell_class",
        groups=["Ependymal", "Pericytes", "Endothelial 2"],
        spot_size=0.01,
    )




.. image-sg:: /auto_tutorials/images/sphx_glr_tutorial_merfish_007.png
   :alt: Cell_class
   :srcset: /auto_tutorials/images/sphx_glr_tutorial_merfish_007.png
   :class: sphx-glr-single-img


.. rst-class:: sphx-glr-script-out

 Out:

 .. code-block:: none

      0%|          | 0/1000 [00:00<?, ?/s]      1%|          | 7/1000 [00:00<00:14, 68.81/s]      2%|2         | 25/1000 [00:00<00:07, 130.97/s]      4%|4         | 43/1000 [00:00<00:06, 150.77/s]      6%|6         | 62/1000 [00:00<00:05, 159.72/s]      8%|8         | 80/1000 [00:00<00:05, 164.08/s]     10%|9         | 98/1000 [00:00<00:05, 167.65/s]     12%|#1        | 115/1000 [00:00<00:05, 166.30/s]     13%|#3        | 134/1000 [00:00<00:05, 172.29/s]     15%|#5        | 152/1000 [00:00<00:04, 173.11/s]     17%|#7        | 170/1000 [00:01<00:04, 173.88/s]     19%|#8        | 188/1000 [00:01<00:04, 174.30/s]     21%|##        | 206/1000 [00:01<00:04, 174.66/s]     22%|##2       | 224/1000 [00:01<00:04, 173.14/s]     24%|##4       | 242/1000 [00:01<00:04, 172.49/s]     26%|##6       | 260/1000 [00:01<00:04, 171.94/s]     28%|##7       | 278/1000 [00:01<00:04, 170.99/s]     30%|##9       | 296/1000 [00:01<00:04, 172.82/s]     31%|###1      | 314/1000 [00:01<00:04, 168.63/s]     33%|###3      | 331/1000 [00:01<00:03, 168.09/s]     35%|###4      | 348/1000 [00:02<00:03, 168.29/s]     37%|###6      | 366/1000 [00:02<00:03, 168.26/s]     38%|###8      | 383/1000 [00:02<00:03, 168.18/s]     40%|####      | 402/1000 [00:02<00:03, 169.03/s]     42%|####2     | 420/1000 [00:02<00:03, 170.33/s]     44%|####3     | 438/1000 [00:02<00:03, 171.58/s]     46%|####5     | 456/1000 [00:02<00:03, 171.09/s]     47%|####7     | 474/1000 [00:02<00:03, 171.74/s]     49%|####9     | 492/1000 [00:02<00:02, 171.01/s]     51%|#####1    | 510/1000 [00:03<00:02, 170.46/s]     53%|#####2    | 528/1000 [00:03<00:02, 170.79/s]     55%|#####4    | 546/1000 [00:03<00:02, 170.45/s]     56%|#####6    | 564/1000 [00:03<00:02, 169.97/s]     58%|#####8    | 581/1000 [00:03<00:02, 169.95/s]     60%|#####9    | 599/1000 [00:03<00:02, 171.20/s]     62%|######1   | 617/1000 [00:03<00:02, 172.01/s]     64%|######3   | 635/1000 [00:03<00:02, 172.11/s]     65%|######5   | 653/1000 [00:03<00:02, 172.69/s]     67%|######7   | 671/1000 [00:03<00:01, 166.52/s]     69%|######9   | 690/1000 [00:04<00:01, 171.86/s]     71%|#######   | 708/1000 [00:04<00:01, 170.86/s]     73%|#######2  | 726/1000 [00:04<00:01, 168.85/s]     74%|#######4  | 744/1000 [00:04<00:01, 167.54/s]     76%|#######6  | 761/1000 [00:04<00:01, 167.10/s]     78%|#######7  | 779/1000 [00:04<00:01, 168.22/s]     80%|#######9  | 796/1000 [00:04<00:01, 168.39/s]     81%|########1 | 814/1000 [00:04<00:01, 169.99/s]     83%|########3 | 832/1000 [00:04<00:01, 166.48/s]     85%|########5 | 850/1000 [00:05<00:00, 167.89/s]     87%|########6 | 868/1000 [00:05<00:00, 168.88/s]     88%|########8 | 885/1000 [00:05<00:00, 169.06/s]     90%|######### | 902/1000 [00:05<00:00, 168.32/s]     92%|#########1| 919/1000 [00:05<00:00, 167.95/s]     94%|#########3| 936/1000 [00:05<00:00, 164.33/s]     95%|#########5| 954/1000 [00:05<00:00, 164.33/s]     97%|#########7| 972/1000 [00:05<00:00, 165.15/s]     99%|#########9| 990/1000 [00:05<00:00, 167.07/s]    100%|##########| 1000/1000 [00:05<00:00, 168.18/s]
    /home/runner/work/squidpy_notebooks/squidpy_notebooks/.tox/docs/lib/python3.9/site-packages/scanpy/plotting/_tools/scatterplots.py:1171: FutureWarning: Categorical.replace is deprecated and will be removed in a future version. Use Series.replace directly instead.
      values = values.replace(values.categories.difference(groups), np.nan)




.. GENERATED FROM PYTHON SOURCE LINES 102-113

Spatially variable genes with spatial autocorrelation statistics
----------------------------------------------------------------
With Squidpy we can investigate spatial variability of gene expression.
This is an example of a function that only supports 2D data.
:func:`squidpy.gr.spatial_autocorr` conveniently wraps two
spatial autocorrelation statistics: *Moran's I* and *Geary's C*.
They provide a score on the degree of spatial variability of gene expression.
The statistic as well as the p-value are computed for each gene, and FDR correction
is performed. For the purpose of this tutorial, let's compute the *Moran's I* score.
The results are stored in `adata.uns['moranI']` and we can visualize selected genes
with :func:`scanpy.pl.spatial`.

.. GENERATED FROM PYTHON SOURCE LINES 113-120

.. code-block:: default

    sq.gr.spatial_autocorr(adata_slice, mode="moran")
    adata_slice.uns["moranI"].head()
    sc.pl.spatial(
        adata_slice,
        color=["Cd24a", "Necab1", "Mlc1"],
        spot_size=0.01,
    )



.. image-sg:: /auto_tutorials/images/sphx_glr_tutorial_merfish_008.png
   :alt: Cd24a, Necab1, Mlc1
   :srcset: /auto_tutorials/images/sphx_glr_tutorial_merfish_008.png
   :class: sphx-glr-single-img


.. rst-class:: sphx-glr-script-out

 Out:

 .. code-block:: none

    /home/runner/work/squidpy_notebooks/squidpy_notebooks/.tox/docs/lib/python3.9/site-packages/scanpy/metrics/_gearys_c.py:293: UserWarning: 1 variables were constant, will return nan for these.
      warnings.warn(





.. rst-class:: sphx-glr-timing

   **Total running time of the script:** ( 1 minutes  42.296 seconds)

**Estimated memory usage:**  9 MB


.. _sphx_glr_download_auto_tutorials_tutorial_merfish.py:


.. only :: html

 .. container:: sphx-glr-footer
    :class: sphx-glr-footer-example



  .. container:: sphx-glr-download sphx-glr-download-python

     :download:`Download Python source code: tutorial_merfish.py <tutorial_merfish.py>`



  .. container:: sphx-glr-download sphx-glr-download-jupyter

     :download:`Download Jupyter notebook: tutorial_merfish.ipynb <tutorial_merfish.ipynb>`
