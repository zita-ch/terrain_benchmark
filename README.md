# Terrain-Robustness Benchmark for Legged Locomotion
A prototype to generate challenging and realistic terrains in simulation.  
With techniques of terrain authoring and active learning, the learned samplers can stably generate diverse high-quality terrains.   

----  
Benchmark download: [this link](https://drive.google.com/file/d/1UhRwr-dWzaZzV3hVsSNXyHTm6ZkUaQJP/view?usp=sharing)    
Author: Chong Zhang, chozhang@ethz.ch    
Non-commercial use only.   

The terrains are of size (225, 225) grids.  
The height values vary in [0,1], which corresponds to [0,10*gridSize].  
The very initial purpose takes gridSize = 2.5cm, but it can be scaled.  

The '/hard' folder in the downloaded benchmark folder is for difficult-to-traverse terrains (with high scores), and the '/medium' folder is for terrains with similar difficulties (score ~0.055).

----  

The repo only contains the codes for benchmark generation.   
Implemented on Ubuntu18.04, python3.6 in anaconda,
with:
+ for terrain generation: pysheds == 0.3.3, GDAL == 2.4.2, georasters == 0.5.23, tensorflow-gpu == 2.6.2, keras == 2.6.0, setuptools == 57.5.0, opencv-contrib-python == 4.6.0.66   
+ for flow learning: tensorflow-gpu == 2.6.2, opencv-contrib-python == 4.6.0.66, torch==1.10.2      

---  
Thanks to https://github.com/nanoxas/sketch-to-terrain for pix2pix implementation of terrain authoring!       

(CC BY-NC-ND 4.0 License inherited from here)

--- 
Todo:
1. add evaluations of different robots and policies  
2. add a bibtex reference after uploading the preprint at arXiv  
3. add code examples to use the benchmark  
