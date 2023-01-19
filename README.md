# Generated Terrain-Robustness Benchmark (GTRB) for Legged Locomotion  
A prototype to generate diverse, challenging, and realistic unstructured terrains in simulation.  
With techniques of terrain authoring and active learning, the learned samplers can stably generate diverse high-quality terrains.   
You may watch our video attachment for paper submission: [video file](icra_video_attachment_submit.mp4), [watch on youtube](https://youtu.be/pdXERZzdsdM)    
This work has been accepted by ICRA 2023. [preprint](https://arxiv.org/abs/2208.07681)   

![image](https://user-images.githubusercontent.com/54518250/185016398-cbbd36d1-745e-4712-b962-bab8874f4043.png)


For citation:
```
  @inproceedings{terrain_benchmark,
    title = {Generating a Terrain-Robustness Benchmark for Legged Locomotion: A Prototype via Terrain Authoring and Active Learning},
    author = {Zhang, Chong and Yang, Lizhi},
    booktitle = {IEEE International Conference on Robotics and Automation (ICRA)},
    year = {2022},
  }
``` 

----  
### Usage  
Benchmark download: [GTRB-v1 at this link](https://drive.google.com/file/d/1UhRwr-dWzaZzV3hVsSNXyHTm6ZkUaQJP/view?usp=sharing)    
Main author: Chong Zhang, chozhang@ethz.ch    
Non-commercial use only: CC BY-NC-SA 4.0 License     

The terrains are of size (225, 225) grids.  
The height values vary in [0,1], which corresponds to [0,10*gridSize].  
The very initial purpose takes gridSize = 2.5cm, but it can be scaled.  

The '/hard' folder in the downloaded benchmark folder is for difficult-to-traverse terrains (with high scores), and the '/medium' folder is for terrains with similar difficulties (score ~0.055).

#### Evaluate a policy  

1) How well a base-velocity-tracking policy performs over challenging terrains? See [the Tracking Challenge](tracking_challenge).  
2) How well a fall-recovery policy performs over challenging terrains? See [the Fall Recovery Challenge](fall_rec_challenge).   
3) How to generate more challenging terrains? Try the tricks below!
  - Add slopes. 15 degrees can make the terrains quite challenging.
  - Get a larger gridSize. May rescale it to 5 cm.
  - Specify varying friction coefficients. Imagine the wet stones after rain.  

#### Importing a terrain  

Import a terrain in PyBullet:
```
import pandas as pd

height_file = r"[your filepath]/hard/elevation0002.txt"
heightfieldData = pd.read_csv(height_file,sep=' ',header=None).values[::-1,:].flatten('C')
gridsize = .035  # rescaled from .025
terrainShape = p.createCollisionShape(shapeType=p.GEOM_HEIGHTFIELD, meshScale=[gridsize, gridsize, gridsize*10],
                                      heightfieldTextureScaling=128, heightfieldData=heightfieldData, 
                                      numHeightfieldRows=225,numHeightfieldColumns=225)
terrain = p.createMultiBody(0, terrainShape)
p.resetBasePositionAndOrientation(terrain, [0, 0, -gridsize*10], [0, 0, 0, 1])
```
<img src="https://user-images.githubusercontent.com/54518250/185296654-ffe728d5-e998-41ea-9e01-c0013c4d7e88.png" width="640">

Import a terrain in Isaac Gym:   
Directly assign new height values to the subterrain height field, but requiring 2d interpolation to downsample the terrain to the typically low resolution in Isaac Gym
```
import pandas as pd
from scipy.interpolate import interp2d
envWidth, downsampleRes, myRes = 80, 0.1, 0.035
myCorStart, mySize = int(-112*myRes/downsampleRes), int(225*myRes/downsampleRes)
myGrids = np.linspace(-112*myRes, 112*myRes, num=225)
interpGrids = np.linspace(myCorStart*downsampleRes,(myCorStart+mySize-1)*downsampleRes, num=mySize)
algoHeightScale, myHeightScale = 0.005, myRes * 10

myMap = pd.read_csv(r'[your filepath]/hard/elevation0014.txt', sep=' ', header=None).values[::-1,:].T
myDS = interp2d(myGrids, myGrids, myMap)
interpHeight = myDS(interpGrids, interpGrids)* myHeightScale / algoHeightScale
self.height_field_raw[start_x: end_x, start_y:end_y] = 0.
self.height_field_raw[start_x + envWidth // 2 + myCorStart: start_x + envWidth // 2 + myCorStart + mySize,
            start_y + envWidth // 2 + myCorStart:start_y + envWidth // 2 + myCorStart + mySize] = interpHeight

```
<img src="https://user-images.githubusercontent.com/54518250/185459884-db3cf78f-82e1-4d0d-9523-07dbbc9cee72.png" width="640">

----  

#### Repro  

The repo only contains the codes for benchmark generation, and some "challenges".   
Implemented on Ubuntu18.04, python3.6 in anaconda,
with:
+ for terrain generation: pysheds == 0.3.3, GDAL == 2.4.2, georasters == 0.5.23, tensorflow-gpu == 2.6.2, keras == 2.6.0, setuptools == 57.5.0, opencv-contrib-python == 4.6.0.66   
+ for flow learning: tensorflow-gpu == 2.6.2, opencv-contrib-python == 4.6.0.66, torch==1.10.2      


Thanks to https://github.com/nanoxas/sketch-to-terrain for pix2pix implementation of terrain authoring!       

(CC BY-NC-SA 4.0 License inherited from here)

--- 
#### Todo:  
1. Make the generation goal-conditioned.   
2. Achieve the non-rigid terrain generation.   
3. Try data-driven training with these terrains.  



