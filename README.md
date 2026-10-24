# UDrive: Localization and Mapping in Town02

Final deliverables for Intro2AV Project 2: a reconstructed LiDAR map, a one-page technical report, and editable Overleaf sources. This repository contains the deliverables and report sources; it is not the complete estimator source repository.

The recording covers 198.05 seconds and 3,962 scan epochs. A custom Python ROS 2 frontend uses robust point-to-plane ICP against a rolling submap, fixing gyro-integrated yaw while estimating roll, pitch and translation. IMU measurements provide motion prediction and acceleration constraints. An offline backend refines 200 keyframe poses and velocities, gravity and constant IMU biases using ten validated LiDAR revisit constraints. Raw returns are motion-compensated at their original firing times using corrected poses and supplied mounting transforms. LiDAR ranges provide metric scale. GNSS and the supplied reference are used only for evaluation; the teacher map is not used to construct this map.

## Files and map download

- [technical_report.pdf](technical_report.pdf): the one-page report.
- [overleaf_source.zip](overleaf_source.zip): an upload-ready Overleaf project.
- [overleaf/](overleaf/): the same five report source/figure files, unpacked.
- [SHA256SUMS](SHA256SUMS): SHA-256 checksums for the map, report and source ZIP.

Download **map.pcd** from the [v1.0.0 GitHub Release](https://github.com/VioletGo319/udrive-town02-mapping/releases/tag/v1.0.0). The 164,360,055-byte PCD is a Release asset rather than a normal Git file. To verify the downloads with `SHA256SUMS`, place `map.pcd`, `technical_report.pdf` and `overleaf_source.zip` in the same directory.

## Map conventions and viewing

The binary PCD v0.7 contains **10,272,490 XYZ+RGB points** in metres in `udrive_map`. The origin is the first estimated vehicle pose at the first scan end; axes follow the initial vehicle frame: x forward, y left, z up, with a small initial tilt assumed. The map is not georeferenced.

Points are retained centroids of measured returns in 0.05 m voxels. A structure-protecting isolation filter removes 1.16% of raw map points without moving retained coordinates. Gray RGB is height-based display color, not reflectance or semantic labels. Open `map.pcd` in CloudCompare using **File > Open**; no companion map file is required. Only observed portions of the route are represented.

## Evaluation and limitations

All 3,962 estimated poses were evaluated at original timestamps after estimation was frozen. One fixed rigid SE(3) alignment was fitted over the full trajectory, with scale fixed to 1; the report gives the transform.

| Final trajectory metric | Value |
| --- | ---: |
| Position ATE RMSE | 0.24969 m |
| Position error P95 | 0.40188 m |
| 1 s translation RPE RMSE | 0.07978 m |

At the inspected revisit region, ground/wall point-to-plane residuals improve from 5.18/6.72 cm to 1.84/4.50 cm. Global ATE improves by only 2.6 mm, while P95 becomes slightly worse. For the submitted filtered map, fixed low-surface returns have a nearest-point distance P95 of 4.783 cm. Structural-cohort RMSE worsens from 6.83 cm before isolation filtering to 19.61 cm afterward: removing sparse points damages some tail matches.

These map checks measure internal consistency, not independent absolute accuracy: scans excluded from map insertion still contributed to localization, and local diagnostics informed configuration selection. Sparse geometry can be removed by filtering; scan-line gaps, residual wall bands and unobserved structures remain. Correlated frontend/IMU constraints and mostly planar motion limit gravity/bias separation. Dark areas in a height-colored point-cloud view do not by themselves confirm SLAM failure. The input bag, teacher map, reference trajectory and private local files are not included.

## Compile the report

In Overleaf, choose **New Project > Upload Project**, upload `overleaf_source.zip`, select `main.tex` as the main document, and use **pdfLaTeX**. Recompile and confirm that any edits, including group member names, still fit on one page. The supplied PDF is compiled from these sources. Algorithm and library references are included in the report.
