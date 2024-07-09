# 3D body reconstruction and measurement from the reconstructed 3D body model.

<p align="center"><p align="center">

This is the official Pytorch implementation of 3D body reconstruction and measurement. 

This repo reconstructs an expressive body with detailed face shape and hand articulation from a single image.
This repo does this by regressing the body, face and hands directly from image pixels using a neural network that includes a novel moderator, which attends to add weights information about the different body parts. After that it caculates measurements of the body part based on 3D SMPL/SMPLX body model 

## Getting started
install all necessary packages and download the data.

conda create -n 3d_body python=3.8
conda activate 3d_body
pip install torch==1.13.1+cu116 torchvision==0.14.1+cu116 torchaudio==0.13.1 --extra-index-url https://download.pytorch.org/whl/cu116
pip install numpy>=1.23.0 scipy>=1.4.1 chumpy>=0.69 scikit-image>=0.15 opencv-python>=4.1.1 scikit-image>=0.15 PyYAML==5.1.1 face-alignment kornia yacs ninja matplotlib trimesh
pip install "git+https://github.com/facebookresearch/pytorch3d.git@stable"

pip install "git+https://github.com/MPI-IS/mesh.git"
pip install torch==1.13.1+cu116 torchvision==0.14.1+cu116 torchaudio==0.13.1 --extra-index-url https://download.pytorch.org/whl/cu116
pip install numpy==1.23.0 trimesh==3.15.1 smplx==0.1.26 scipy==1.5.4 scikit-learn==1.0.2 plotly==5.10.0 pandas==1.3.5 tqdm==4.65.0 panel==1.2.0 open3d==0.17.0 chumpy==0.70 loguru==0.7.0 omegaconf==2.3.0

## Running

You can put front image captured by phone or camera into 3d-body/inputs folder.
After that you can run following python script.

python start.py --height 178

You have to enter height.
After running, you will get the 3D body model in output folder and body measurement data.

Then, the measurements dictionary can be obtained with `measurer.measurements` and the labeled measurements can be obtained with `measurer.labeled_measurements`. The list of the predefined measurements along with its standard literature labels are:

```
STANDARD_MEASUREMENT = {
    'A': 'head circumference',
    'B': 'neck circumference',
    'C': 'shoulder to crotch height',
    'D': 'chest circumference',
    'E': 'waist circumference',
    'F': 'hip circumference',
    'G': 'wrist right circumference',
    'H': 'bicep right circumference',
    'I': 'forearm right circumference',
    'J': 'arm right length',
    'K': 'inside leg height',
    'L': 'thigh left circumference',
    'M': 'calf left circumference',
    'N': 'ankle left circumference',
    'O': 'shoulder breadth',
    'P': 'height'
    }
```

All the measurements are expressed in cm.

## 📝 Notes

### Measurement definitions
There are two types of measurements: lenghts and circumferences.
1. Lengths are defined as distances between landmark points defined on the body model
2. Circumferences are defiend as plane cuts of the body model

To define a new measurement:
1. Open `measurement_definitions.py`
1. add the new measurement to the `MEASUREMENT_TYPES` dict and set its type:
   `LENGTH` or `CIRCUMFERENCE`
2. depending on the measurement type, define the measurement in the `LENGTHS` or 
   `CIRCUMFERENCES` dict of the appropriate body model (`SMPLMeasurementDefinitions` or `SMPLXMeasurementDefinitions`)
   - `LENGTHS` are defined using 2 landmarks - the measurement is 
            found as the distance between the landmarks
   - `CIRCUMFERENCES` are defined with landmarks and joints - the 
            measurement is found by cutting the body model with the 
            plane defined by a point (landmark point) and normal (
            vector connecting the two joints)
3. If the measurement is a `CIRCUMFERENCE`, a possible issue that arises is
   that the plane cutting results in multiple body part slices. To alleviate
   that, define the body part where the measurement should be located in 
   `CIRCUMFERENCE_TO_BODYPARTS` dict. This way, only the slice in the corresponding body part is
   used for finding the measurement. The body parts are defined by the 
   face segmentation located in `data/smpl_body_parts_2_faces.json` or `data/smplx_body_parts_2_faces.json`.

<br>

