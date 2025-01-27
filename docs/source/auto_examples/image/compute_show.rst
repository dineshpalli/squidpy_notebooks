
.. DO NOT EDIT.
.. THIS FILE WAS AUTOMATICALLY GENERATED BY SPHINX-GALLERY.
.. TO MAKE CHANGES, EDIT THE SOURCE PYTHON FILE:
.. "auto_examples/image/compute_show.py"
.. LINE NUMBERS ARE GIVEN BELOW.

.. only:: html

  .. container:: binder-badge

    .. image:: images/binder_badge_logo.svg
      :target: https://mybinder.org/v2/gh/theislab/squidpy_notebooks/master?filepath=docs/source/auto_examples/image/compute_show.ipynb
      :alt: Launch binder
      :width: 150 px

.. rst-class:: sphx-glr-example-title

.. _sphx_glr_auto_examples_image_compute_show.py:

Show layers of the ImageContainer
---------------------------------

This example shows how to use :meth:`squidpy.im.ImageContainer.show`.

This function is useful to visualize statically different layers of the
:class:`squidpy.im.ImageContainer` class.

.. seealso::

    - See :ref:`sphx_glr_auto_examples_image_compute_crops.py` and
      :ref:`sphx_glr_auto_examples_image_compute_smooth.py` for additional
      examples on methods of the :class:`squidpy.im.ImageContainer`.

.. GENERATED FROM PYTHON SOURCE LINES 17-20

.. code-block:: default

    import scanpy as sc
    import squidpy as sq








.. GENERATED FROM PYTHON SOURCE LINES 21-22

Load the Mibitof dataset.

.. GENERATED FROM PYTHON SOURCE LINES 22-24

.. code-block:: default

    adata = sq.datasets.mibitof()





.. rst-class:: sphx-glr-script-out

 Out:

 .. code-block:: none

      0%|          | 0.00/19.3M [00:00<?, ?B/s]      0%|          | 56.0k/19.3M [00:00<00:49, 411kB/s]      1%|1         | 240k/19.3M [00:00<00:21, 952kB/s]       5%|5         | 0.98M/19.3M [00:00<00:06, 3.05MB/s]     21%|##        | 4.00M/19.3M [00:00<00:01, 10.6MB/s]     50%|#####     | 9.69M/19.3M [00:00<00:00, 21.9MB/s]     80%|#######9  | 15.5M/19.3M [00:00<00:00, 29.0MB/s]    100%|##########| 19.3M/19.3M [00:00<00:00, 21.9MB/s]




.. GENERATED FROM PYTHON SOURCE LINES 25-26

We can briefly visualize the data to understand the type of images we have.

.. GENERATED FROM PYTHON SOURCE LINES 26-31

.. code-block:: default

    for library_id in adata.uns["spatial"].keys():
        sc.pl.spatial(
            adata[adata.obs["library_id"] == library_id], color="Cluster", library_id=library_id, title=library_id
        )




.. rst-class:: sphx-glr-horizontal


    *

      .. image-sg:: /auto_examples/image/images/sphx_glr_compute_show_001.png
         :alt: point16
         :srcset: /auto_examples/image/images/sphx_glr_compute_show_001.png
         :class: sphx-glr-multi-img

    *

      .. image-sg:: /auto_examples/image/images/sphx_glr_compute_show_002.png
         :alt: point23
         :srcset: /auto_examples/image/images/sphx_glr_compute_show_002.png
         :class: sphx-glr-multi-img

    *

      .. image-sg:: /auto_examples/image/images/sphx_glr_compute_show_003.png
         :alt: point8
         :srcset: /auto_examples/image/images/sphx_glr_compute_show_003.png
         :class: sphx-glr-multi-img





.. GENERATED FROM PYTHON SOURCE LINES 32-35

We have three different tissue samples. We also have segmentation masks for each tissue sample.
Let's extract the image from the :class:`anndata.AnnData` object and create a
:class:`squidpy.im.ImageContainer` object.

.. GENERATED FROM PYTHON SOURCE LINES 35-43

.. code-block:: default

    imgs = []
    for library_id in adata.uns["spatial"].keys():
        img = sq.im.ImageContainer(adata.uns["spatial"][library_id]["images"]["hires"], library_id=library_id)
        img.add_img(adata.uns["spatial"][library_id]["images"]["segmentation"], library_id=library_id, layer="segmentation")
        img["segmentation"].attrs["segmentation"] = True
        imgs.append(img)
    img = sq.im.ImageContainer.concat(imgs)








.. GENERATED FROM PYTHON SOURCE LINES 44-45

We can visualize each image of the object with :meth:`squidpy.im.ImageContainer.show`.

.. GENERATED FROM PYTHON SOURCE LINES 45-47

.. code-block:: default

    img.show("image")




.. image-sg:: /auto_examples/image/images/sphx_glr_compute_show_004.png
   :alt: image, library_id:point16, image, library_id:point23, image, library_id:point8
   :srcset: /auto_examples/image/images/sphx_glr_compute_show_004.png
   :class: sphx-glr-single-img





.. GENERATED FROM PYTHON SOURCE LINES 48-49

:meth:`squidpy.im.ImageContainer.show` also allows to overlay the results of segmentation.

.. GENERATED FROM PYTHON SOURCE LINES 49-50

.. code-block:: default

    img.show("image", segmentation_layer="segmentation", segmentation_alpha=0.5)



.. image-sg:: /auto_examples/image/images/sphx_glr_compute_show_005.png
   :alt: image, library_id:point16, image, library_id:point23, image, library_id:point8
   :srcset: /auto_examples/image/images/sphx_glr_compute_show_005.png
   :class: sphx-glr-single-img






.. rst-class:: sphx-glr-timing

   **Total running time of the script:** ( 0 minutes  23.702 seconds)

**Estimated memory usage:**  228 MB


.. _sphx_glr_download_auto_examples_image_compute_show.py:


.. only :: html

 .. container:: sphx-glr-footer
    :class: sphx-glr-footer-example



  .. container:: sphx-glr-download sphx-glr-download-python

     :download:`Download Python source code: compute_show.py <compute_show.py>`



  .. container:: sphx-glr-download sphx-glr-download-jupyter

     :download:`Download Jupyter notebook: compute_show.ipynb <compute_show.ipynb>`
