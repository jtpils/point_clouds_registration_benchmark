# A benchmark for point clouds registration algorithms
The goal of this project is to develop a benchmark for point clouds registration algorithms. It is composed of the following publicly available datasets:
- the ETH datasets;
- the Canadian Planetary Emulation Terrain 3D Mapping datasets;
- the TUM Vision Groud RGBD datasets;
- the KAIST Urban datasets.

The benchmark aims at covering all the possible use cases of point clouds registration algorithms in robotics; this will allow a fair and objective comparison between different approaches. We provide a set of scripts to ease the setup of the testing environment and a set of registration problems (with ground truth) that cover the various degrees of initial misalignment and overlap. Before using the benchmark, please use read the corresponding paper and cite it in your work.

## Benchmark structure
To configure the environment, run the script setup.py. This will download the point clouds, convert them to a common format, and save them in the following directory tree:

- eth
  - apartment
  - stairs
  - gazebo_winter
  - gazebo_summer
  - hauptgebaude
  - plain
  - wood_summer
  - wood_autumn
    
- planetary
  - box_met
  - p2at_met

- tum
  - pioneer_slam
  - pioneer_slam2
  - pioneer_slam3
  - desk
  - long_office_household

- kaist
  - urban16

Each directory corresponds to a daset and contains:
- the point clouds;
- a file named `<dataset>_local.txt`;
- a file named `<dataset>_global.txt`.
    
The `_local.txt` and `_global.txt` files contain the registration problem to solve. They are composed of the following fields:
- **id** A unique identifier of the registration problem;
- **source name** The file name of the source point cloud;
- **target name** The file name of the target point cloud;
- **overlap** The percentage of overlap between the two point clouds;
- **t1..t12** The elements of the 4x4 transformation matrix representing the initial misplacement to apply. The last line is implicit, since for a rototranslation is always the same; therefore, the matrix is
```
| t1 t2  t3  t4  |
| t5 t6  t7  t8  |
| t9 t10 t11 t12 |
| 0  0   0   1   |
```

The transformation matrix represents the initial misplacement of the source point cloud and have to be applied before proceeding to the solution of the problem. That is, it is the variable that a registration algorithm has to estimate.

There are two files for each sequence: one is relative to local registration problems, the other to global. The file for global registration contains only a single initial perturbation for each pair of point clouds, while the for local registration experiments there are several different initial perturbations to apply to the source point cloud.

The p2at_met folder contains also two other files, named `planetary_map_{global,local}.txt` that contain the registration problems for the "registration with respect to a map" use case.
    
The devel folder contains scripts we used to calculate the overlaps and to generate the `_local.txt` and `_global.txt` files. You should not use them, however we keep it in the repository for documentation purposes.
    
 ## How to use the benchmark
1. Download or clone this repository.
2. Execute the script `setup.py` to download and configure the testing environment.
3. Each folder of the workspace contains a set of `_local.txt` and `_global.txt` files, one for each sequence. The sequences are contained in a subfolder with the same name.
4. For each `_local.txt` or `_global.txt` file (depending on whether you are testing a local or global registration algorithm) and for each line in the file:
    1. Load the source and target point clouds.
    2. Transform the source point cloud with the initial transformation.
    3. Align the point clouds with the algorithm to test.
    4. Compare the aligned source point cloud with the original source point cloud, using our metric. You can use either our python script, or our C++ library or even a code developed by yourself, as long as it implements the metric described in the corresponding paper.

