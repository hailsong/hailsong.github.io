---
comments: true
title:  "Installing Gaussian Splatting (diff-gaussian-rasterization, simple-knn)"
excerpt: "Installing Gaussian Splatting (diff-gaussian-rasterization, simple-knn)"

categories:
  - Study
tags:
  - Study
  - Gaussian Splating
  - Running Code
last_modified_at: 2025-07-30T012:06:01-05:00
---

### Issue
- diff-gaussian-rasterization window 상에서 빌드할 때 VS 2022 사용하면 오류 있음
- https://github.com/ashawkey/diff-gaussian-rasterization
- https://github.com/graphdeco-inria/diff-gaussian-rasterization


### Solution
- Visual Studio 2019 [Download link](https://aka.ms/vs/16/release/vs_community.exe) 설치 및 x64 Native Tools Command Prompt for VS 2019 이용하여 빌드
- MSVC v142 x64/x86 포함 후 재빌드


---------

### References

- [3DGS issues 865](https://github.com/graphdeco-inria/gaussian-splatting/issues/865)

- [Download VS 2019](https://striban.tistory.com/69)

- [Getting Started With 3D Gaussian Splatting for Windows (Beginner Tutorial)](https://www.youtube.com/watch?v=UXtuigy_wYc)

- [3DGS Code (Windows version)](https://github.com/jonstephens85/gaussian-splatting-Windows/tree/main) 

- [Original 3DGS Code](https://github.com/graphdeco-inria/gaussian-splatting)




