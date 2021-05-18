# OpenRooms Dataset Release

Zhengqin Li, Ting-Wei Yu, Shen Sang, Sarah Wang, Meng Song, Yuhan Liu, Yu-Ying Yeh, Rui Zhu, Nitesh Gundavarapu, Jia Shi, Sai Bi, Zexiang Xu, Hong-Xing Yu, Kalyan Sunkavalli, Miloš Hašan, Ravi Ramamoorthi, Manmohan Chandraker

## Dataset Overview
This is the webpage for downloading the OpenRooms dataset. We will first introduce the rendered images and various ground-truths. Later, we will introduce how to render your own images based on the OpenRooms dataset creation pipeline. For each type of data, we offer two kinds of formats, zip files and individual folders, so that users can choose whether to download the whole dataset more efficiently or download individual folders for different scenes. To download the file, we recommend the tool Rclone, otherwise users may suffer from slow downloading speed and instability. If you have any questions, please email to openroomsdataset@gmail.com. 

We render six versions of images for all the scenes. Those rendered results are saved in 6 folders: main_xml, main_xml1, mainDiffMat_xml, mainDiffMat_xml1, mainDiffLight_xml and mainDiffLight_xml1. All 6 versions are built with the same CAD models. main_xml, mainDiffMat_xml, mainDiffLight_xml share one set of camera views while main_xml1, mainDiffMat_xml1 and mainDiffLight_xml1 share the other set of camera views. main_xml(1) and mainDiffMat_xml(1) have the same lighting but different materials while main_xml(1) and mainDiffLight_xml(1) have the same materials but different lighting. Both the lighting and material configuration of main_xml and main_xml1 are different. We believe this configuration can potentially help us develope novel applications for image editing. An example scene from main_xml, mainDiffMat_xml and mainDiffLight_xml is shown in the video sequence below. 

## Rendered Images and Ground-truths
All rendered images and the corresponding ground-truths are saved in folder data/rendering/data/. In the following, we will detail each type of rendered data and how to read and interpret them. Two example scenes with images and all ground-truths are included in Demo and Demo.zip. 

1. Images and Images.zip: The 480 × 640 HDR images (im_\*.hdr), which can be read with the python command. 
    ```python
    im = cv2.imread('im_1.hdr', -1)[:, :, ::-1]
    ```
    We render images for main_xml(1), mainDiffMat_xml(1) and mainDiffLight_xml(1).

1. Material and Material.zip: The 480 × 640 diffuse albedo maps (imbaseColor_\*.png) and roughness map (imroughness_\*.png). Note that the diffuse albedo map is saved in sRGB space. To load it into linear RGB space, we can use the following python commands. The roughness map is saved in linear space and can be read directly. 
    ```python
    im = cv2.imread('imbaseColor_1.hdr')[:, :, ::-1]
    im = (im.astype(np.float32 ) / 255.0) ** (2.2)
    ```
    We only render the diffuse albedo maps and roughness maps for main_xml(1) and mainDiffMat_xml(1) because mainDiffLight_xml(1) share the same material maps with the main_xml(1).

1. Geometry and Geometry.zip: The 480 × 640 normal maps (imnomral_\*.png) and depth maps (imdepth_\*.dat). The R, G, B channel of the normal map corresponds to right, up, backward direction of the image plane. To load the depth map, we can use the following python commands. 
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
    We render normal maps for main_xml(1) and mainDiffMat_xml(1), and depth maps for main_xml(1).

1. Mask and Mask.zip: The 480 × 460 grey scale mask (immask_\*.png) for light sources. The pixel value 0 represents the region of environment maps. The pixel value 0.5 represents the region of lamps. Otherwise, the pixel value will be 1. We render the ground-truth masks for main_xml(1) and mainDiffLight_xml(1). 

1. SVLighting: The (120 × 16) × (160 × 32) per-pixel environment maps (imenv_\*.hdr). The spatial resolution is 120 × 160 while the environment map resolution is 16 & times 32. To read the per-pixel environment maps, we can use the following python commands. 
    ```python
    # Read the envmap of resolution 1920 x 5120 x 3 in RGB format 
    env = cv2.imread('imenv_1', -1)[:, :, ::-1]
    # Reshape and permute the per-pixel environment maps
    env = env.reshape(120, 16, 160, 32, 3)
    env = env.transpose(0, 2, 1, 3, 4)
    ```
    We render per-pixel environment maps for main_xml(1), mainDiffMat_xml(1) and mainDiffLight_xml(1). Since the total size of per-pixel environment maps is 4.0 TB, we do not provide an extra .zip format for downloading. Please consider using the tool Rclone if you hope to download all the per-pixel environment maps.

1. SVSG and SVSG.zip: The ground-truth spatially-varying spherical Gaussian (SG) parameters (imsgEnv_\*.h5), computed from this optimization code. We generate the ground-truth SG parameters for main_xml(1), mainDiffMat_xml(1) and mainDiffLight_xml(1). For the detailed format, please refer to the optimization code. 

1. Shading and Shading.zip: The 120 × 160 diffuse shading (imshading_\*.hdr) computed by intergrating the per-pixel environment maps. We render shading for main_xml(1), mainDiffMat_xml(1) and mainDiffLight_xml(1). 

1. SVLightingDirect and SVLightingDirect.zip: The (30 × 16) × (40 × 32) per-pixel environment maps with direct illumination (imenvDirect_\*.hdr) only. The spatial resolution is 30 × 40 while the environment maps resolution is 16 × 32. The direct per-pixel environment maps can be load the same way as the per-pixel environment maps. We only render direct per-pixel environment maps for main_xml(1) and mainDiffLight_xml(1) because the direct illumination of mainDiffMat_xml(1) is the same as main_xml(1). 

1. ShadingDirect and ShadingDirect.zip: The 120 × 160 direct shading (imshadingDirect_\*.rgbe). To load the direct shading, we can use the following python command. 
    ```python
    im = cv2.imread('imshadingDirect_1.rgbe', -1)[:, :, ::-1]
    ```

1. SemanticLabel and SemanticLabel.zip: The 480 × 640 semantic segmentation label (imsemLabel_\*.npy). We provide semantic labels for 45 classes of commonly seen objects and layout for indoor scenes. The 45 classes can be found in semanticLabels.txt. We only render the semantic labels for main_xml(1). 

1. LightSource and LightSource.zip: The light source information, including geometry, shadow and direct shading of each light source. In each scene directory, light_x directory corresponds to im_x.hdr. In each light_x directory, you will see files with numbers in their names. The numbers correspond to the light source ID, i.e. if the IDs are from 0 to 4, then there are 5 light sources in this scene. 
    * Geometry: We provide geometry annotation for windows and lamps (box_\*.dat). To read the annotation, we can use the following python commmands. 
        ```python
        with open('box_0.dat', 'rb')  as fIn:
            info = pickle.load(fIn )
        ```
        There are 3 items saved in the dictionary, which we list blow.
        * isWindow: True if the light source is a window, false if the light source is a lamp. 
        * box3D: The 3D bounding box of the light source, including center (center), orientation (xAxis, yAxis, zAxis) and size (xLen, yLen, zLen). 
        * box2D: The 2D bounding box of the light source on the image plane (x1, y1, x2, y2). 
       We only provide the light source geometry annotation for main_xml(1) 
    * Mask: The 120 × 160 2D binary masks for light sources (mask\*.png). We only provide the masks for main_xml(1). 
    * Direct shading: The 120 × 160 direct shading for each light source (imDS\*.rgbe). We provide the direction shading for main_xml(1) and mainDiffLight_xml(1). 
    * Direct shading without occlusion: The 120 × 160 direct shading with outocclusion for each light source (imNoOcclu\*.rgbe). We provide the direction shading for main_xml(1) and mainDiffLight_xml(1). 
    * Shadow: The 120 × 160 shadow maps for each light source (imShadow\*.png). We render the shadow map for main_xml(1) only. 

## Dataset Creation 
1. GPU renderer: Our Optix-based GPU path tracer is available for downloading. 
The CAD models, environment maps, materials and code required to recreate the dataset will be released soon. 

## Applications

## Related Datasets
The OpenRooms dataset is built on the datasets listed below. We thank their creators for the excellent contribution. Please refer to prior datasets for license issues and terms of use if you hope to use them to create your own dataset. 
ScanNet dataset: The real 3D scans of indoor scenes. 
Scan2cad dataset: The alignment of CAD models to the scanned point clouds. 
Laval outdoor lighting dataset: HDR outdoor environment maps 
HDRI Haven lighting dataset: HDR outdoor environment maps 
PartNet dataset: CAD models 


