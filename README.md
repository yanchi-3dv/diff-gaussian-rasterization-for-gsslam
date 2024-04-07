# :tada:Our modified below:

### Differential Gaussian Rasterization for GS-SLAM

This is a modified Differential 3DGS(3D Gaussian Splatting) Rasterization version based on [orgin 3DGS Rasterization implementation](https://github.com/graphdeco-inria/diff-gaussian-rasterization/tree/59f5f77e3ddbac3ed9db93ec2cfe99ed6c5d121d).

We add some addition support for the SLAM(Simultaneous Localization And Mapping) task:

- Depth rendering forward
- Depth rendering backward
- Pose estimation backward
- **Two mode:** tracking_mode/mapping_mode used for choose tracking/mapping process in SLAM task.

:warning: **NOTE**: The  input to rasterization are slightly different. It is recommended to create a new environment to avoid conflict.

##### Install our modified Rasterization:

```bash
# Install our modified code (cuda)
git clone git@github.com:npu-yanchi/diff-gaussian-rasterization-for-gsslam.git
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

If you want more details, please see in the following paper and feel free to [mail](mailto:yan_chi@sjtu.edu.cn) me:

<section class="section" id="BibTeX">
  <div class="container is-max-desktop content">
    <h2 class="title">BibTeX</h5>
    <pre><code>
@inproceedings{yan2023gs,
  author    = {Yan, Chi and Qu, Delin and Xu, Dan and Zhao, Bin and Wang, Zhigang and Wang, Dong and Li, Xuelong},
  title     = {GS-SLAM: Dense Visual SLAM with 3D Gaussian Splatting},
  booktitle = {CVPR},
  year      ={2024},
}
</code></pre>
  </div>
</section>

#### TODO:

<!-- - [] Camera-ready for GS-SLAM. -->
- [ ] Release the code for GS-SLAM.
- [ ] Unify the form of pose input for efficiency and beauty.


# :snowflake:Original readme below:

### Differential Gaussian Rasterization

Used as the rasterization engine for the paper "3D Gaussian Splatting for Real-Time Rendering of Radiance Fields". If you can make use of it in your own research, please be so kind to cite us.

<section class="section" id="BibTeX">
  <div class="container is-max-desktop content">
    <h2 class="title">BibTeX</h5>
    <pre><code>@Article{kerbl3Dgaussians,
      author       = {Kerbl, Bernhard and Kopanas, Georgios and Leimk{\"u}hler, Thomas and Drettakis, George},
      title        = {3D Gaussian Splatting for Real-Time Radiance Field Rendering},
      journal      = {ACM Transactions on Graphics},
      number       = {4},
      volume       = {42},
      month        = {July},
      year         = {2023},
      url          = {https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/}
}</code></pre>
  </div>
</section>