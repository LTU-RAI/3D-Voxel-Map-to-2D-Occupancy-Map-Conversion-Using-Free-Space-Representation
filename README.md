### 3D Voxel Map to 2D Occupancy Map Conversion Using Free Space Representation

This ros package converts voxel maps generated by the [UFOMap](https://github.com/UnknownFreeOccupied/ufomap) mapping solution into 2D occupancy maps for unmanned aerial vehicles (UAVs) and unmanned ground vehicles (UGV).  The package also provides a method to convert 2D paths to 3D path for UAVs and UAVs.

<img src="https://raw.githubusercontent.com/LTU-RAI/3D-Voxel-Map-to-2D-Occupancy-Map-Conversion-Using-Free-Space-Representation/Media/Media/indoor.gif" style="zoom:80%;" />

The method uses free space in the voxel map to find navigable space in the voxel map, as well as extracting a height map to create a traversability slope map, which is used to detect obstacles in the environment and include them in the map generated for the UGVs. 

The Height map can also be used to convert paths from 2D to 3D using the path_converter_node provided by the package. 

<img src="https://raw.githubusercontent.com/LTU-RAI/3D-Voxel-Map-to-2D-Occupancy-Map-Conversion-Using-Free-Space-Representation/Media/Media/cave.gif" style="zoom:80%;" />



## Paper and Citation

This ROS package is based on the following article:

**3D Voxel Maps to 2D Occupancy Maps for Efficient Path Planning for Aerial and Ground Robots** [[ArXiv](https://arxiv.org/abs/2406.07270)]

```
@misc{fredriksson20243d,
      title={3D Voxel Maps to 2D Occupancy Maps for Efficient Path Planning for Aerial and Ground Robots}, 
      author={Scott Fredriksson and Akshit Saradagi and George Nikolakopoulos},
      year={2024},
      eprint={2406.07270},
      archivePrefix={arXiv},
      primaryClass={cs.RO}
}
```

If you use this ROS package in a scientific publication, please cite the paper.

## Installation

1. [Install UFOMap for ros](https://github.com/UnknownFreeOccupied/ufomap/wiki/Setup#installation)
2. Navigate to src folder in workspace `cd ~/catkin_ws/src`
3. Clone the GitHub  to ros workspace: `git clone https://github.com/LTU-RAI/3D-Voxel-Map-to-2D-Occupancy-Map-Conversion-Using-Free-Space-Representation`
4. `cd ..`
5. Build project: `catkin build mapconversion`

## ROS Launch Files

`map_conversion.launch`: Example launch file for launching map conversion node

`path_conversion.launch`: Example launch file for launching map path conversion node

## ROS Nodes

### `map_conversion_node`

A node that converts the input 3D voxel map to a 2D occupancy map and a height map with the height of the floor and ceiling. 

#### Ros Parameters 

`map_frame`: Name of frame map.

`map_position_z`: Z position used for the outputted 2D maps. 

`minimum_z`: Minimum height of free space the robot can traverse. 

`max_slope_ugv`: Maximum slope ground robot can climb, used to detect and represent obstacles in the 2D occupancy map

#### Subscribed Topics

`ufomap`: Input voxel map.

#### Published Topics

`mapUGV`: Map for ground robot, includes obstacles detected using `max_slope_ugv`.

`mapUAV`: Map for aerial robot, containing free space and bounding walls. 

`heightMap`: 2D map containing the height of the floor and ceiling of the environment. 

`slopeMap`: 2D map containing the slope value for the floor in the environment. 

`visualization_floor_map`: Rviz cost map, for showing a heat map of the floor height in the `heightMap`. **This is only for visualization.** 

`visualization_ceiling_map`: Rviz cost map, for showing a heat map of the ceiling height in the `heightMap`. **This is only for visualization.** 

`visualization_slope_map`: Rviz cost map, for showing a heat map of the slope of the map in the range 0 to 2*`max_slope_ugv`. **This is only for visualization.** 



### `path_converter_node`

A node that converts inputed path generated using a 2D planer to a 3D path using `heightMap` generated by `map_conversion_node`.

#### Ros Parameters 

`use_collision_sphere`: Add collision sphere around robot, use `true` for aerial robots and `false` for ground robot. If a collision with either the floor or ceiling is detected during path conversion, the path will be adjusted so that no collision occurs.

`collision_radius`: Radius for collision sphere, only used if `use_collision_sphere` is  `true`. 

`path_offset`: Desired height above ground for the robot.

`path_smothing_length`: The amount of step before and after robot used for path smoothing.  

#### Subscribed Topics

`heightMap`: Hight map used for 2D to 3D conversion generated by `map_conversion_node`.

`pathIn`: 2D path that will be converted into 3D.

#### Published Topics

`pathOut`: 3D path converted from 2D using the `heightMap`.