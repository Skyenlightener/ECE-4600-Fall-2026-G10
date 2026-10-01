# Campus 3D Reconstruction and Point-Cloud Classification Project

## 1. Project Objective

This project uses aerial photographs and campus point clouds to build a 3D campus environment. An AI model trained in ArcGIS classifies the points in LAS files into roads, low vegetation, trees, and buildings. The classified data can then support GIS analysis, scene separation, and UE5/VR visualization.

This document covers the project up to AI model version 1 (v1).

| Class Code | Class |
|---:|---|
| 0 | Road and other hard-paved surfaces |
| 1 | Low vegetation, including grass |
| 2 | Trees and other tall vegetation |
| 3 | Buildings and designated built objects |

## 2. Main Software and Tools

### ArcGIS Pro

ArcGIS Pro is used to view, edit, and manage LAS point clouds, create manual classification labels, export training data, and train and run the point-cloud classification model. The model predicts a class for each point and writes the result to the LAS `Classification` field.

### ArcGIS Deep Learning Libraries / PyTorch

These libraries provide the deep-learning environment and GPU acceleration required for AI training and inference. The v1 model uses an ArcGIS point-cloud classification network to identify roads, vegetation, trees, and buildings from point positions, surrounding geometry, and RGB colour information.

### Python

Python is used to process LAS data, check manual labels, preserve or restore RGB information, and automate parts of the data-preparation and classification workflow. It supports the ArcGIS training process but does not generate the final VR environment.

### Gaussian Splatting

Gaussian Splatting reconstructs a 3D scene from photographs taken from multiple viewpoints and their camera positions. The scene is represented by many 3D Gaussians containing position, scale, orientation, colour, and opacity information. It is well suited to photorealistic appearance and real-time viewing. Its purpose is to reproduce the visual appearance of the campus, not to classify objects as buildings, roads, or trees.

### Mesh / Photogrammetry Model

A mesh converts photographs, depth information, or point clouds into a 3D surface made of vertices and triangles. Meshes are suitable for collision detection, physical interaction, model editing, and UE5 scene construction. This project combines the stable geometry of a mesh with the realistic appearance of Gaussian Splatting.

### SpeedTree

SpeedTree is used to create and edit trees, shrubs, and other vegetation models. It can generate trunks, branches, leaves, wind animation, and multiple levels of detail (LODs) for export to UE5. SpeedTree assets can replace incomplete trees in point clouds or Gaussian Splatting, producing vegetation that is clearer, lighter, and more suitable for real-time rendering.

### MLSLabsRenderer Interface

The MLSLabsRenderer interface is used to display and manage Gaussian Splatting models in UE5 and to coordinate the rendering relationship between Gaussian and mesh models. When both models occupy similar positions, they may compete for the same pixel depth, causing incorrect occlusion, flickering, or unstable front-to-back relationships. MLSLabsRenderer helps address these depth conflicts so that Gaussian and mesh models can be displayed together more reliably in one UE5 scene.

### Unreal Engine 5 / VR

Unreal Engine 5 is used to combine Gaussian Splatting, meshes, SpeedTree vegetation, materials, lighting, and interactive functions into the final navigable campus VR environment.

## 3. Training Dataset Source

The original point-cloud data used by this project was downloaded from:

**[FE Studio Point Cloud Research](https://festudio.ca/research/pointcloud)**

This website was created by **Professor Yuhao of the University of Manitoba's Department of Architecture** to publish and introduce the related point-cloud research data.

Representative areas were selected from the campus point clouds provided on the website. Their classification labels were then created and corrected manually in ArcGIS Pro. The training samples include buildings, roads, low vegetation, trees, and mixed-class areas. XYZ coordinates and RGB colour information are preserved during data preparation, after which ArcGIS divides and exports the processed LAS data into the point-cloud training format required by the v1 model.

## 4. AI Model v1

The colour point-cloud v1 model used at this stage is located at:

**[FE Studio Point Cloud Research](https://festudio.ca/research/pointcloud)**

The main files are:

- `.emd`: model structure, class definitions, and input information;
- `.pth`: trained PyTorch model weights;
- `.dlpk`: complete model package for use in ArcGIS Pro;
- `model_metrics.html`: training curves and model evaluation information.

The primary inputs to v1 are the XYZ coordinates and RGB colours stored in the LAS files. ArcGIS divides the point cloud into smaller spatial blocks. The model learns the colour, shape, height, and neighbourhood characteristics of each class and then predicts a classification for every point.

## 5. Relationship Between Project Components

```text
Aerial photographs
   ↓
Gaussian Splatting and mesh reconstruction
   ↓
RGB point cloud / LAS
   ↓
Manual ArcGIS labels and AI v1 classification
   ↓
Classification results, mesh, Gaussian model, and SpeedTree vegetation
   ↓
MLSLabsRenderer depth coordination
   ↓
UE5 / VR campus environment
```

Gaussian Splatting and meshes describe what the campus looks like. The ArcGIS AI identifies what the individual point-cloud points represent. SpeedTree creates vegetation suitable for real-time rendering, while MLSLabsRenderer coordinates the display and depth relationship between Gaussian and mesh models in UE5.

Original LAS files, manual labels, AI outputs, and 3D models should be stored separately. Original data should never be overwritten.
