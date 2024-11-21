# :tada:Our modified below:

### Differential Gaussian Rasterization for GS-SLAM

This is a modified Differential 3DGS(3D Gaussian Splatting) Rasterization version based on [orgin 3DGS Rasterization implementation](https://github.com/graphdeco-inria/diff-gaussian-rasterization/tree/59f5f77e3ddbac3ed9db93ec2cfe99ed6c5d121d).

We add some addition support for the SLAM(Simultaneous Localization And Mapping) task:

- Depth forward rendering 
- Depth rendering backward
- Pose estimation backward
- **Two mode:** tracking_mode/mapping_mode used for choose tracking/mapping process in SLAM task.

:warning: **NOTE**: The  input to rasterization are slightly different. It is recommended to create a new environment to avoid conflict.

##### Install our modified Rasterization:

```bash
# Install our modified code (cuda)
git clone git@github.com:yanchi-3dv/diff-gaussian-rasterization-for-gsslam.git
cd diff-gaussian-rasterization-for-gsslam
python setup.py install
pip install .
```

##### Calling method:

```python
rendered_image, radii, rendered_depth, rendered_alpha, render_depth_var = rasterizer(
    means3D = means3D,
    means2D = means2D,
    shs = shs,
    colors_precomp = colors_precomp,
    opacities = opacity,
    scales = scales,
    rotations = rotations,
    cov3D_precomp = cov3D_precomp,
    cam_q_w2c = cam_q_w2c,
    cam_t_w2c = cam_t_w2c)
```

##### Camera setting setup:

```python
cam = Camera(
    image_height=h,
    image_width=w,
    tanfovx=w / (2 * fx),
    tanfovy=h / (2 * fy),
    bg=torch.tensor([1, 1, 1], dtype=torch.float32, device="cuda"),
    scale_modifier=1.0,
    viewmatrix=w2c.squeeze(0),
    projmatrix=full_proj.squeeze(0),
    sh_degree=0,
    campos=cam_center,
    prefiltered=False,
    mapping_mode=mapping_mode,
    tracking_mode=tracking_mode,
    debug=False
)
```

:warning: **NOTE**:

- cam_q_w2c is a quaternion(w, x, y, z).
- **Inelegant Implement:** Only cam_q_w2c and cam_t_w2c have grad, viewmatrix and projmatrix don't have grad, so after the cam_q_w2c and cam_t_w2c updated, viewmatrix and projmatrix must be update manual.
- mapping_mode/tracking_mode are two bools, the pose grads will be calculated only when tracking_mode = True.
- the grad from depth loss to pose still has some problem in some cases, so like the tracking formula in the GS-SLAM paper, we only use RGB loss to optimize pose. The authors have tried for a long time to resolve this issue, but unfortunately, it has not worked. If anyone discovers the underlying problem, feel free to submit a pull request or email me :smiley:.

If you want more details and feel free to [mail](mailto:yanchi.3dv@gmail.com) me:



## Citation

If you find this project helpful, please consider citing the following our paper and original Differential Gaussian Rasterization:

```
@inproceedings{yan2024gs,
  title={Gs-slam: Dense visual slam with 3d gaussian splatting},
  author={Yan, Chi and Qu, Delin and Xu, Dan and Zhao, Bin and Wang, Zhigang and Wang, Dong and Li, Xuelong},
  booktitle={Proceedings of the IEEE/CVF Conference on Computer Vision and Pattern Recognition},
  pages={19595--19604},
  year={2024}
}
```



```
@article{kerbl20233d,
  title={3D Gaussian Splatting for Real-Time Radiance Field Rendering.},
  author={Kerbl, Bernhard and Kopanas, Georgios and Leimk{\"u}hler, Thomas and Drettakis, George},
  journal={ACM Trans. Graph.},
  volume={42},
  number={4},
  pages={139--1},
  year={2023}
}
```



