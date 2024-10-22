# fbx-extract

This program parses an FBX file and writes three types of output files.

1. An OBJ file of the mesh
2. An ascii file with skinning information
3. An ascii file with the skeletal animation information

The input FBX file can be downloaded from [Mixamo](mixamo.com).


## Building and running

This project builds on top of the following projects:

- [GLM](https://glm.g-truc.net/0.9.9/)
- [OpenFBX](https://github.com/nem0/OpenFBX) (included)
- [miniz](https://github.com/richgel999/miniz) (included)

To build the project, run `cmake` and `make`, and then run with the fbx file as an argument:

```
> mkdir build
> cd build
> cmake ..
> make
> ./fbx-extract ../data/repo_test_file/bigvegas_Walking.fbx
```

## Example File
### Example model distributed by sueda (original author)
```
> ./fbx-extract ../data/repo_test_file/bigvegas_Walking.fbx # 実行可能
```
### Failure example model 
```
> ./fbx-extract ../data/bone_fail/bone.fbx # 実行不可：アニメーション（≒キーフレーム）がY軸のみに割り当てられており、X・Z軸には情報がないため、アニメーションの情報が取れない。
```
### Successfully working model
Modified model: add Key Frames to X and Y axis of "moving" -> check the added keyframes with Graph Editor in Maya.
```
> ./fbx-extract ../data/bone_ok/bone.fbx # 実行可能：キーフレームをX・Z軸にも情報を追加
```


## Note for keyframes
* Example of good keyframes (keyframes are applied all the X/Y/Z axes)
<img width="734" alt="image" src="https://user-images.githubusercontent.com/63697745/150630975-81a4eff2-60aa-4257-b4ea-8a4f8740e50c.png">
* Example of bad keyframe (keyframe is applied to the Y axis)
<img width="733" alt="image" src="https://user-images.githubusercontent.com/63697745/150631061-0bbcd88c-2725-468a-8997-fef7793760a6.png">
* To interpolate keyframes, excute "bake animation" from (you may remove some unnecessary keyframes (i.e., rotate) after the interpolation)
<img width="720" alt="image" src="https://user-images.githubusercontent.com/63697745/150631186-a4101d69-f922-493a-bed0-4c605cd6222b.png">


## Output files

### Geomety
- A standard OBJ file with vertex positions, normals, and texture coordinates.
- Faces are triangulated.
- Common vertices are merged.


### Skinning weights

- Comments start with '#'.
- The first line has three integers:
  - Vertex count: should be the same as the corresponding OBJ file
  - Bone count: the number of bones in the animation
  - Max influences: this is the maximum number of non-zero weights per vertex
- Each subsequent line corresponds to a vertex.
  - In each line, the first number is the number of bone influences for the vertex.
  - The next numbers are "influence" pairs of {bone index, bone weight}.

```
4583 82 9
1 20 1.000000 
1 20 1.000000 
2 19 0.037664 20 0.962336 
```


### Skeletal animation

- Comments start with '#'.
- The first line has two integers:
  - Frame count: the number of frames in the animation
  - Bone count: the number of bones in the animation
- Each subsequent line is a frame with "boneCount" sets of quaternions (x,y,z,w) and positions (x,y,z).
- The first frame is for the rest pose, so there are actually "frame count + 1" lines.

```
27 82
0.000000 0.000000 0.000000 1.000000 0.000000 96.330055 9.825774 ...
0.008630 0.039924 0.024404 0.998867 0.657645 87.535782 2.391427 ...
```


## TODO

- Extract textures from the FBX file. Currently, you need to extract them manually using an external program, such as Blender.
- Add support for using FBX files with only skeletal animation data. Currently, the FBX file must contain geometry, skinning, and animation data. This means that to download new animations for an existing character, you need to download another FBX file with redundant information.
