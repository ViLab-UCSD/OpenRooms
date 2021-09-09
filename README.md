# OpenRooms Dataset Release

[Zhengqin Li](https://sites.google.com/a/eng.ucsd.edu/zhengqinli), [Ting-Wei Yu](https://www.linkedin.com/in/ting-wei-yu/), [Shen Sang](https://www.linkedin.com/in/shen-sang-ab6b0217a/), [Sarah Wang](https://www.linkedin.com/in/sawang07/), [Meng Song](https://sites.google.com/site/mengsong1130/), Yuhan Liu, [Yu-Ying Yeh](https://yuyingyeh.github.io/), [Rui Zhu](https://jerrypiglet.github.io/), [Nitesh Gundavarapu](https://scholar.google.com/citations?user=v19p_0oAAAAJ&hl=en), Jia Shi, [Sai Bi](https://sai-bi.github.io/), [Zexiang Xu](https://cseweb.ucsd.edu/~zex014/), [Hong-Xing Yu](https://kovenyu.com/), [Kalyan Sunkavalli](http://www.kalyans.org/), [Miloš Hašan](http://www.miloshasan.net/), [Ravi Ramamoorthi](http://cseweb.ucsd.edu/~ravir/), [Manmohan Chandraker](https://cseweb.ucsd.edu/~mkchandraker/)

## News
\[08/07/21\]. Please send an email to [OpenRoomsDataset@gmail.com](README.md) if you hope to receive the newest update. 

\[06/29/21\]. We released two demo scenes with all ground-truths ([Demo](https://drive.google.com/drive/folders/1SNgrVUhXrKTt8pftdQpUj80mXtK2FyeB?usp=sharing) and [Demo.zip](https://openroomsdataset.s3.us-west-1.amazonaws.com/Demo.zip)). Please use the two scenes to determine usage for your applications.

\[05/19/21\]. We released all rendered images ([Images](https://drive.google.com/drive/folders/1-DhVDNjPc1l8Vu9Qt_yMbHnpC9OEUa-q?usp=sharing) and [Images.zip](https://openroomsdataset.s3.us-west-1.amazonaws.com/Image.zip)).

## About
This is the release webpage for the [OpenRooms](https://vilab-ucsd.github.io/ucsd-openrooms/) dataset. We first introduce the rendered images and various ground-truths. Next, we introduce how to render your own images based on the OpenRooms dataset creation framework. For each type of data, we offer two kinds of formats, zip files and individual folders, so users may choose whether to download the whole dataset more efficiently or download individual folders for different scenes. We recommend using [Rclone](https://rclone.org/) to avoid slow or unstable downloads.

OpenRooms is a collaboration between researchers from UCSD and Adobe. We acknowledge generous support from NSF, ONR, Adobe and Google. For any questions, please email: openroomsdataset@gmail.com.


## Dataset Overview

![pipeline](http://cseweb.ucsd.edu/~viscomp/projects/CVPR21OpenRooms/Sum.png)

We render six versions of images for all the scenes. Those rendered results are saved in 6 folders: `main_xml`, `main_xml1`, `mainDiffMat_xml`, `mainDiffMat_xml1`, `mainDiffLight_xml` and `mainDiffLight_xml1`. All 6 versions are built with the same CAD models. `main_xml`, `mainDiffMat_xml`, `mainDiffLight_xml` share one set of camera views while `main_xml1`, `mainDiffMat_xml1` and `mainDiffLight_xml1` share the other set of camera views. `main_xml(1)` and `mainDiffMat_xml(1)` have the same lighting but different materials while `main_xml(1)` and `mainDiffLight_xml(1)` have the same materials but different lighting. Both the lighting and material configuration of `main_xml` and `main_xml1` are different. We believe this configuration can potentially help us develope novel applications for image editing. Two example scenes from `main_xml`, `mainDiffMat_xml` and `mainDiffLight_xml` are shown in the below.

![config](https://vilab-ucsd.github.io/ucsd-openrooms/index_files/config.png)

**News**: We currently only release the rendered images of the dataset. All ground-truths will be released in a few days. The dataset creation pipeline will also be released soon.

## Rendered Images and Ground-truths
All rendered images and the corresponding ground-truths are saved in folder [data/rendering/data/](README.md). In the following, we will detail each type of rendered data and how to read and interpret them. Two example scenes with images and all ground-truths are included in [Demo](https://drive.google.com/drive/folders/1SNgrVUhXrKTt8pftdQpUj80mXtK2FyeB?usp=sharing) and [Demo.zip](https://openroomsdataset.s3.us-west-1.amazonaws.com/Demo.zip). The training/testing split of the scenes can be found in [train.txt](https://openroomsdataset.s3.us-west-1.amazonaws.com/train.txt) and [test.txt](https://openroomsdataset.s3.us-west-1.amazonaws.com/test.txt). 

1. **[Image](https://drive.google.com/drive/folders/1-DhVDNjPc1l8Vu9Qt_yMbHnpC9OEUa-q?usp=sharing)** and **[Image.zip](https://drive.google.com/file/d/11046xQ9L6SkahoxF0W3cn__pmFZQYKyv/view?usp=sharing)**: The 480 × 640 HDR images `im_*.hdr`, which can be read with the python command.
    ```python
    im = cv2.imread('im_1.hdr', -1)[:, :, ::-1]
    ```
    We render images for `main_xml(1)`, `mainDiffMat_xml(1)` and `mainDiffLight_xml(1)`.

2. **[Material](https://drive.google.com/drive/folders/1E8wMJHDqnN7rArQH28_alTe870078Qpp?usp=sharing)** and **[Material.zip](https://drive.google.com/file/d/1yPFzSMxStjZtPN2vC43-p45Gq9NGFNPF/view?usp=sharing)**: The 480 × 640 diffuse albedo maps `imbaseColor_*.png` and roughness map `imroughness_*.png`. Note that the diffuse albedo map is saved in sRGB space. To load it into linear RGB space, we can use the following python commands. The roughness map is saved in linear space and can be read directly.
    ```python
    im = cv2.imread('imbaseColor_1.hdr')[:, :, ::-1]
    im = (im.astype(np.float32 ) / 255.0) ** (2.2)
    ```
    We only render the diffuse albedo maps and roughness maps for `main_xml(1)` and `mainDiffMat_xml(1)` because `mainDiffLight_xml(1)` share the same material maps with the `main_xml(1)`.

3. **[Geometry](https://drive.google.com/drive/folders/1i7IMlGf_LDjuHbM6sutwOO5LjzXcbVN2?usp=sharing)** and **[Geometry.zip](https://drive.google.com/file/d/1FRPMKIDzFN1puk27MMGaIxCoi6HmLGyE/view?usp=sharing)**: The 480 × 640 normal maps `imnomral_*.png` and depth maps `imdepth_*.dat`. The R, G, B channel of the normal map corresponds to right, up, backward direction of the image plane. To load the depth map, we can use the following python commands.
    ```python
    with open('imdepth_1.dat', 'rb') as fIn:
        # Read the height and width of depth
        hBuffer = fIn.read(4)
        height = struct.unpack('i', hBuffer)[0]
        wBuffer = fIn.read(4)
        width = struct.unpack('i', wBuffer)[0]
        # Read depth
        dBuffer = fIn.read(4 * width * height )
        depth = np.array(
            struct.unpack('f' * height * width, dBuffer ),
            dtype=np.float32 )
        depth = depth.reshape(height, width)
    ```
    We render normal maps for `main_xml(1)` and `mainDiffMat_xml(1)`, and depth maps for `main_xml(1)`.

4. **[Mask](https://drive.google.com/drive/folders/1GkLnZTIOSeIeewol0wyllP3opdMxdH1i?usp=sharing)** and **[Mask.zip](https://drive.google.com/file/d/1TxejwEi7v7wxTSJjXZnTAX2WhMK1MZF2/view?usp=sharing)**: The 480 × 460 grey scale mask `immask_*.png` for light sources. The pixel value 0 represents the region of environment maps. The pixel value 0.5 represents the region of lamps. Otherwise, the pixel value will be 1. We render the ground-truth masks for `main_xml(1)` and `mainDiffLight_xml(1)`.

5. **[SVLighting](https://drive.google.com/drive/folders/12Y13IVJcxeu8IblB9OIfQDiy3CEIQHeI?usp=sharing)** and **[SVLighting.zip](https://drive.google.com/drive/folders/12Y13IVJcxeu8IblB9OIfQDiy3CEIQHeI?usp=sharing)**: The (120 × 16) × (160 × 32) per-pixel environment maps `imenv_*.hdr`. The spatial resolution is 120 x 160 while the environment map resolution is 16 x 32. To read the per-pixel environment maps, we can use the following python commands.
    ```python
    # Read the envmap of resolution 1920 x 5120 x 3 in RGB format
    env = cv2.imread('imenv_1', -1)[:, :, ::-1]
    # Reshape and permute the per-pixel environment maps
    env = env.reshape(120, 16, 160, 32, 3)
    env = env.transpose(0, 2, 1, 3, 4)
    ```
    We render per-pixel environment maps for `main_xml(1)`, `mainDiffMat_xml(1)` and `mainDiffLight_xml(1)`. Since the total size of per-pixel environment maps is 4.0 TB, we do not provide an extra .zip format for downloading. Please consider using the tool [Rclone](https://rclone.org/) if you hope to download all the per-pixel environment maps.

6. **[SVSG](https://drive.google.com/drive/folders/10mTrrCQXmEC-4wvlQTh-hTBETu2x1SxN?usp=sharing)** and **[SVSG.zip](https://drive.google.com/file/d/1MltE_Hoyb1jNV4p3H6n7NogCWQ9cFFQ_/view?usp=sharing)**: The ground-truth spatially-varying spherical Gaussian (SG) parameters `imsgEnv_*.h5`, computed from this optimization [code](https://github.com/lzqsd/SphericalGaussianOptimization). We generate the ground-truth SG parameters for `main_xml(1)`, `mainDiffMat_xml(1)` and `mainDiffLight_xml(1)`. For the detailed format, please refer to the optimization [code](https://github.com/lzqsd/SphericalGaussianOptimization).

7. **[Shading](https://drive.google.com/drive/folders/1WJApmMLh0wM64fhkKA4L7iJot6Y86VXW?usp=sharing)** and **[Shading.zip](https://drive.google.com/file/d/1lZsGEyeeUbUl-i68nIMuuT85Sptc6XPi/view?usp=sharing)**: The 120 × 160 diffuse shading `imshading_*.hdr` computed by intergrating the per-pixel environment maps. We render shading for `main_xml(1)`, `mainDiffMat_xml(1)` and `mainDiffLight_xml(1)`.

8. **[SVLightingDirect](https://drive.google.com/drive/folders/16LgLOPg-E9cTMDnK-4ysS5FxA5aT4Jdz?usp=sharing)** and **[SVLightingDirect.zip](https://drive.google.com/file/d/13aBQ-1xDZ0kjt80KmfWvMmS76Q9Y0NSv/view?usp=sharing)**: The (30 × 16) × (40 × 32) per-pixel environment maps with direct illumination `imenvDirect_*.hdr` only. The spatial resolution is 30 × 40 while the environment maps resolution is 16 × 32. The direct per-pixel environment maps can be load the same way as the per-pixel environment maps. We only render direct per-pixel environment maps for `main_xml(1)` and `mainDiffLight_xml(1)` because the direct illumination of `mainDiffMat_xml(1)` is the same as `main_xml(1)`.

9. **[ShadingDirect](https://drive.google.com/drive/folders/1AiUeU0VsPvQlaBwEyqOkotDSTM1JsNZo?usp=sharing)** and **[ShadingDirect.zip](https://drive.google.com/file/d/1KMbo5e5lAPLztEiM301dPBPFQ97mWhTA/view?usp=sharing)**: The 120 × 160 direct shading `imshadingDirect_*.rgbe`. To load the direct shading, we can use the following python command.
    ```python
    im = cv2.imread('imshadingDirect_1.rgbe', -1)[:, :, ::-1]
    ```
    Again, we only render direct shading for `main_xml(1)` and `mainDiffLight_xml(1)`

10. **[SemanticLabel](https://drive.google.com/drive/folders/17bp8K8gCrcfoXi_RDrtIN_uCTq2MW6VG?usp=sharing)** and **[SemanticLabel.zip](https://drive.google.com/file/d/1GU5PgHrYg-N3kRla6dhBr-oT6LuOxSmp/view?usp=sharing)**: The 480 × 640 semantic segmentation label `imsemLabel_*.npy`. We provide semantic labels for 45 classes of commonly seen objects and layout for indoor scenes. The 45 classes can be found in `semanticLabels.txt`. We only render the semantic labels for `main_xml(1)`.

11. **[LightSource](https://drive.google.com/drive/folders/1mBX3yoe7XSuDCW7jLPfnT7xfJI6NRwLU?usp=sharing)** and **[LightSource.zip](https://drive.google.com/file/d/1L-oUvZ9bD89fcqUyl14qgIQaCZHE_obo/view?usp=sharing)**: The light source information, including geometry, shadow and direct shading of each light source. In each scene directory, `light_x` directory corresponds to `im_x.hdr`, where x = 0, 1, 2, 3 ... In each `light_x` directory, you will see files with numbers in their names. The numbers correspond to the light source ID, i.e. if the IDs are from 0 to 4, then there are 5 light sources in this scene.
    * **Geometry**: We provide geometry annotation for windows and lamps `box_*.dat` for `main_xml(1)` only. To read the annotation, we can use the following python commmands.
        ```python
        with open('box_0.dat', 'rb')  as fIn:
            info = pickle.load(fIn )
        ```
        There are 3 items saved in the dictionary, which we list blow.
        * **isWindow**: True if the light source is a window, false if the light source is a lamp.
        * **box3D**: The 3D bounding box of the light source, including center `center`, orientation `xAxis, yAxis, zAxis` and size `xLen, yLen, zLen`.
        * **box2D**: The 2D bounding box of the light source on the image plane `x1, y1, x2, y2`.
    * **Mask**: The 120 × 160 2D binary masks for light sources `mask*.png`. We only provide the masks for `main_xml(1)`.
    * **Direct shading**: The 120 × 160 direct shading for each light source `imDS*.rgbe`. We provide the direction shading for `main_xml(1)` and `mainDiffLight_xml(1)`.
    * **Direct shading without occlusion**: The 120 × 160 direct shading without occlusion for each light source `imNoOcclu*.rgbe`. We provide the direction shading for `main_xml(1)` and `mainDiffLight_xml(1)`.
    * **Shadow**: The 120 × 160 shadow maps for each light source `imShadow*.png`. We render the shadow map for `main_xml(1)` only.

12. **[Friction](README.md)** and **[Friction.zip](README.md)**: The friction coefficients computed from our SVBRDF following the method proposed by [Zhang et al.](https://arxiv.org/abs/1603.07998) We compute the friction coefficients for `main_xml(1)` and `mainDiffLight_xml(1)`


## Dataset Creation
1. **[GPU renderer](https://github.com/lzqsd/OptixRenderer)**: The Optix-based GPU path tracer for rendering. Please refer to the github repository for detailed instructions.
2. **[Tileable texture synthesis](https://github.com/lzqsd/TileableTextureSynthesis)**: The tielable texture synthesis code to make sure that the SVBRDF maps are tileable. Please refer to the github repository for more details.
3. **[Spherical gaussian optimization](https://github.com/lzqsd/SphericalGaussianOptimization)**: The code to fit per-pixel environment map with spherical Gaussian lobes, using LBFGS optimization. Please refer to the github repository for detailed instructions.

The CAD models, environment maps, materials and code required to recreate the dataset will be released soon.

## Applications
1. **[Inverse Rendering](https://github.com/lzqsd/InverseRenderingOfIndoorScene)**: Trained on our dataset, we achieved state-of-the-arts on some inverse rendering metrics, especially the lighting estimation. Please refer to our github repository for the training and testing code.
2. **[Robotics](README.md)**: Our robotics applications will come soon.

## Related Datasets
The OpenRooms dataset is built on several prior works, as noted below. Please refer to them for the respective license details and terms of use when creating your own dataset using OpenRooms.
1. **[ScanNet dataset](http://www.scan-net.org/)**: The real 3D scans of indoor scenes.
1. **[Scan2cad dataset](https://github.com/skanti/Scan2CAD)**: The alignment of CAD models to the scanned point clouds.
1. **[Laval outdoor lighting dataset](http://outdoor.hdrdb.com/)**: HDR outdoor environment maps
1. **[HDRI Haven lighting dataset](https://hdrihaven.com/)**: HDR outdoor environment maps
1. **[PartNet dataset](https://partnet.cs.stanford.edu/)**: CAD models
2. **[Adobe Stock](https://stock.adobe.com/search?filters%5Bcontent_type%3A3d%5D=1&filters%5B3d_type_id%5D%5B0%5D=3&load_type=3d+lp)**: High-quality microfacet SVBRDF texture maps. Please license the materials from Adobe Stock.





