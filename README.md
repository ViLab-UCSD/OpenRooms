# OpenRooms Dataset Release

[Zhengqin Li](https://sites.google.com/view/zhengqinli), [Ting-Wei Yu](https://www.linkedin.com/in/ting-wei-yu/), [Shen Sang](https://www.linkedin.com/in/shen-sang-ab6b0217a/), [Sarah Wang](https://www.linkedin.com/in/sawang07/), [Meng Song](https://sites.google.com/site/mengsong1130/), Yuhan Liu, [Yu-Ying Yeh](https://yuyingyeh.github.io/), [Rui Zhu](https://jerrypiglet.github.io/), [Nitesh Gundavarapu](https://scholar.google.com/citations?user=v19p_0oAAAAJ&hl=en), Jia Shi, [Sai Bi](https://sai-bi.github.io/), [Zexiang Xu](https://cseweb.ucsd.edu/~zex014/), [Hong-Xing Yu](https://kovenyu.com/), [Kalyan Sunkavalli](http://www.kalyans.org/), [Miloš Hašan](http://www.miloshasan.net/), [Ravi Ramamoorthi](http://cseweb.ucsd.edu/~ravir/), [Manmohan Chandraker](https://cseweb.ucsd.edu/~mkchandraker/)

## News
\[03/26/24]. We update the download link and put OpenRooms on a new data server. 

\[06/18/23]. We released all scene configuration xml files, camera poses, furniture and layout geometry.

\[09/10/21\]. All the rendered ground-truths are available for downloading. 

\[08/07/21\]. Please send an email to [OpenRoomsDataset@gmail.com](README.md) if you hope to receive the newest update. 

\[05/19/21\]. We released all rendered images ([Images](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/Image/)) and [Images.zip](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/Image.zip)).

## About
This is the release webpage for the [OpenRooms](https://vilab-ucsd.github.io/ucsd-openrooms/) dataset. We first introduce the rendered images and various ground-truths. Next, we introduce how to render your own images based on the OpenRooms dataset creation framework. For each type of data, we offer two kinds of formats, zip files and individual folders, so users may choose whether to download the whole dataset more efficiently or download individual folders for different scenes. We recommend using [Rclone](https://rclone.org/) to avoid slow or unstable downloads.

OpenRooms is a collaboration between researchers from UCSD and Adobe. We acknowledge generous support from NSF, ONR, Adobe and Google. For any questions, please email: openroomsdataset@gmail.com.


## Dataset Overview

![pipeline](http://cseweb.ucsd.edu/~viscomp/projects/CVPR21OpenRooms/Sum.png)

We render six versions of images for all the scenes. Those rendered results are saved in 6 folders: `main_xml`, `main_xml1`, `mainDiffMat_xml`, `mainDiffMat_xml1`, `mainDiffLight_xml` and `mainDiffLight_xml1`. All 6 versions are built with the same CAD models. `main_xml`, `mainDiffMat_xml`, `mainDiffLight_xml` share one set of camera views while `main_xml1`, `mainDiffMat_xml1` and `mainDiffLight_xml1` share the other set of camera views. `main_xml(1)` and `mainDiffMat_xml(1)` have the same lighting but different materials while `main_xml(1)` and `mainDiffLight_xml(1)` have the same materials but different lighting. Both the lighting and material configuration of `main_xml` and `main_xml1` are different. We believe this configuration can potentially help us develope novel applications for image editing. Two example scenes from `main_xml`, `mainDiffMat_xml` and `mainDiffLight_xml` are shown in the below.

![config](https://vilab-ucsd.github.io/ucsd-openrooms/index_files/config.png)

## Rendered Images and Ground-truths
All rendered images and the corresponding ground-truths are saved in folder [data/rendering/data/](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/). In the following, we will detail each type of rendered data and how to read and interpret them. The training/testing split of the scenes can be found in [train.txt](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/train.txt) and [test.txt](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/test.txt). 

1. **[Image](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/Image/)** and **[Image.zip](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/Image.zip)**: The 480 × 640 HDR images `im_*.hdr`, which can be read with the python command.
    ```python
    im = cv2.imread('im_1.hdr', -1)[:, :, ::-1]
    ```
    We render images for `main_xml(1)`, `mainDiffMat_xml(1)` and `mainDiffLight_xml(1)`.

2. **[Material](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/Material/)** and **[Material.zip](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/Material.zip)**: The 480 × 640 diffuse albedo maps `imbaseColor_*.png` and roughness map `imroughness_*.png`. Note that the diffuse albedo map is saved in sRGB space. To load it into linear RGB space, we can use the following python commands. The roughness map is saved in linear space and can be read directly.
    ```python
    im = cv2.imread('imbaseColor_1.hdr')[:, :, ::-1]
    im = (im.astype(np.float32 ) / 255.0) ** (2.2)
    ```
    We only render the diffuse albedo maps and roughness maps for `main_xml(1)` and `mainDiffMat_xml(1)` because `mainDiffLight_xml(1)` share the same material maps with the `main_xml(1)`.

3. **[Geometry](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/Geometry/)** and **[Geometry.zip](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/Geometry.zip)**: The 480 × 640 normal maps `imnomral_*.png` and depth maps `imdepth_*.dat`. The R, G, B channel of the normal map corresponds to right, up, backward direction of the image plane. To load the depth map, we can use the following python commands.
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

4. **[Mask](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/Mask/)** and **[Mask.zip](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/Mask.zip)**: The 480 × 460 grey scale mask `immask_*.png` for light sources. The pixel value 0 represents the region of environment maps. The pixel value 0.5 represents the region of lamps. Otherwise, the pixel value will be 1. We render the ground-truth masks for `main_xml(1)` and `mainDiffLight_xml(1)`.

5. **[SVLighting](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/SVLighting/)**: The (120 × 16) × (160 × 32) per-pixel environment maps `imenv_*.hdr`. The spatial resolution is 120 x 160 while the environment map resolution is 16 x 32. To read the per-pixel environment maps, we can use the following python commands.
    ```python
    # Read the envmap of resolution 1920 x 5120 x 3 in RGB format
    env = cv2.imread('imenv_1', -1)[:, :, ::-1]
    # Reshape and permute the per-pixel environment maps
    env = env.reshape(120, 16, 160, 32, 3)
    env = env.transpose(0, 2, 1, 3, 4)
    ```

6. **[SVSG](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/SVSG/)**: The ground-truth spatially-varying spherical Gaussian (SG) parameters `imsgEnv_*.h5`, computed from this optimization [code](https://github.com/lzqsd/SphericalGaussianOptimization). We generate the ground-truth SG parameters for `main_xml(1)`, `mainDiffMat_xml(1)` and `mainDiffLight_xml(1)`. For the detailed format, please refer to the optimization [code](https://github.com/lzqsd/SphericalGaussianOptimization).

7. **[Shading](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/Shading/)** and **[Shading.zip](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/Shading.zip)**: The 120 × 160 diffuse shading `imshading_*.hdr` computed by intergrating the per-pixel environment maps. We render shading for `main_xml(1)`, `mainDiffMat_xml(1)` and `mainDiffLight_xml(1)`.

8. **[SVLightingDirect](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/SVLightingDirect/)** and **[SVLightingDirect.zip](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/SVLightingDirect.zip)**: The (30 × 16) × (40 × 32) per-pixel environment maps with direct illumination `imenvDirect_*.hdr` only. The spatial resolution is 30 × 40 while the environment maps resolution is 16 × 32. The direct per-pixel environment maps can be load the same way as the per-pixel environment maps. We only render direct per-pixel environment maps for `main_xml(1)` and `mainDiffLight_xml(1)` because the direct illumination of `mainDiffMat_xml(1)` is the same as `main_xml(1)`.

9. **[ShadingDirect](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/ShadingDirect/)** and **[ShadingDirect.zip](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/SVLightingDirect.zip)**: The 120 × 160 direct shading `imshadingDirect_*.rgbe`. To load the direct shading, we can use the following python command.
    ```python
    im = cv2.imread('imshadingDirect_1.rgbe', -1)[:, :, ::-1]
    ```
    Again, we only render direct shading for `main_xml(1)` and `mainDiffLight_xml(1)`

10. **[SemanticLabel](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/SemanticLabel/)** and **[SemanticLabel.zip](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/SemanticLabel.zip)**: The 480 × 640 semantic segmentation label `imsemLabel_*.npy`. We provide semantic labels for 45 classes of commonly seen objects and layout for indoor scenes. The 45 classes can be found in `semanticLabels.txt`. We only render the semantic labels for `main_xml(1)`.

11. **[LightSource](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/LightSource/)** and **[LightSource.zip](https://mclab.ucsd.edu/OpenRooms_public/data/rendering/data/LightSource.zip)**: The light source information, including geometry, shadow and direct shading of each light source. In each scene directory, `light_x` directory corresponds to `im_x.hdr`, where x = 0, 1, 2, 3 ... In each `light_x` directory, you will see files with numbers in their names. The numbers correspond to the light source ID, i.e. if the IDs are from 0 to 4, then there are 5 light sources in this scene.
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
4. **[Furniture geometry](https://mclab.ucsd.edu/OpenRooms_public/uv_mapped.zip)**: The mesh of every furniture in OpenRooms. The initial geometry comes from [PartNet](https://github.com/daerduoCarey/partnet_dataset). We first aligned those 3D models with the corresponding models from [ShapeNet](https://shapenet.org/) in order to be consistent with the [Scan2cad](https://github.com/skanti/Scan2CAD) annotations. Then, we labeled material parts for each model and created uv maps using [Blender's](https://www.blender.org/) cube mapping tool. The final meshes are saved as `uv_mapped.obj`. 
5. **[Layout](https://mclab.ucsd.edu/OpenRooms_public/layoutMesh.zip)**: The layout of each scene in the OpenRooms dataset. We designed an annotation tool to create the layout mesh from [ScanNet](http://www.scan-net.org/) point clouds. For scene `scene0xyz_uv`, its corresponding layout mesh can be found in `scene0xyz_uv/uv_mapped.obj`. 
6. **[Scene configuration files and camera poses](https://mclab.ucsd.edu/OpenRooms_public/scenes.zip)**: We provide scene configuration xml files as well as camera poses for each scene. Those files can be directly consumed by our [Optix renderer](https://github.com/lzqsd/OptixRenderer), if all the required 3D assets are prepared properly. To under the definition of these files, please refer to the github page of our renderer. 
7. **[Environment maps](https://mclab.ucsd.edu/OpenRooms_public/EnvDataset.zip)**: We released 209 outdoor HDR environment maps from [HDRi Heaven](https://hdri-haven.com/). For the other 205 outdoor HDR environment maps, please download them from the [Laval dataset](http://outdoor.hdrdb.com/). After downloading from the [Laval dataset](http://outdoor.hdrdb.com/), please run `transformEnvmap.py` to align the environment maps intensity so that they are ready to be consumed by the renderer. 
8. **[Spatially varying BRDF maps](https://mclab.ucsd.edu/OpenRooms_public/BRDFOriginDataset.zip)**: In the `matList.txt`, we list the 1329 kinds of spatially varying BRDF from [Adobe Stock](https://stock.adobe.com/search?filters%5Bcontent_type%3A3d%5D=1&filters%5B3d_type_id%5D%5B0%5D=3&load_type=3d+lp) that has been used to create the OpenRooms dataset. To re-render images from OpenRooms, please purchase those materials from Adobe.
9. **[Tutorial](https://drive.google.com/file/d/1d751UulbaCMqo0cKo_HDPU2UyVKWWwP1/view?usp=drive_web)**: We provide the tutorial that explains the dataset creation process. Please feel free to contact us through sending emails to [OpenRoomsDataset@gmail.com](README.md) or [lizhengqin2012@gmail.com](README.md) if you meet any question. 

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





