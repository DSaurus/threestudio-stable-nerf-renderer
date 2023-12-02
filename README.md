# threestudio-stable-nerf-renderer
Stable nerf renderer extension of threestudio, which reduces and stabilize VRAM usage in 3D generation process. To use it, please install [ThreeStudio](https://github.com/threestudio-project/threestudio) first and then install this extension in threestudio `custom` directory.

# Installation
```
cd custom
git clone https://github.com/DSaurus/threestudio-stable-nerf-renderer.git
```

## How does it work?
The GPU memory usage during training with high-resolution settings is currently unstable, leading to OOM errors. The primary factor contributing to this issue is the grid_prune function in Nerfacc, resulting in an uncertain number of points in each iteration. To address this issue and stabilize the GPU memory usage, I propose the following methods:

- Limiting the maximum number of sampling points that require gradient computation.
- Dividing the entire rendering image into multiple blocks to stabilize the peak memory usage.

Based on my experiment with NeRF 512x512 rendering, using 6000000 points for gradient calculation consumes about 21GB of memory, while Nerf integration requires about 16 GB. Therefore, my configuration is as follows:

```
renderer_type: "stable-nerf-volume-renderer"
renderer:
base_renderer_type: "mask-nerf-volume-renderer"
base_renderer:
    radius: ${system.geometry.radius}
    num_samples_per_ray: 512
    train_max_nums: 6000000
    block_nums: [3,3]
```

By utilizing the proposed configuration, the memory usage will be stabilized at ~23GB (21 + 16/(3*3)).

![memory](https://github.com/threestudio-project/threestudio/assets/24589363/4bdad3ce-dd92-45d4-9334-0624c65d16fe)

## More examples (coming soon)
<!-- ```
# run 4D-fy
renderer_type: "stable-nerf-volume-renderer"
renderer:
base_renderer_type: "mask-nerf-volume-renderer"
base_renderer:
    radius: ${system.geometry.radius}
    num_samples_per_ray: 512
    train_max_nums: 3000000
``` -->
