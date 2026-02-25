# [ICLR 2026] Phantom-Data: Towards a General Subject-Consistent Video Generation Dataset


<div align="center">
  
[![arXiv](https://img.shields.io/badge/arXiv%20paper-2502.11079-b31b1b.svg)](https://arxiv.org/abs/2506.18851)&nbsp;
[![project page](https://img.shields.io/badge/Project_page-More_visualizations-green)](https://phantom-video.github.io/Phantom-Data/)&nbsp;
</div>

> [**Phantom-Data: Towards a General Subject-Consistent Video Generation Dataset**](https://arxiv.org/abs/2502.11079)<br>
> [Zhuowei Chen](https://scholar.google.com/citations?user=ow1jGJkAAAAJ)<sup> * </sup>, [Bingchuan Li](https://scholar.google.com/citations?user=ac5Se6QAAAAJ)<sup> * &dagger;</sup>, [Tianxiang Ma](https://tianxiangma.github.io/)<sup> * </sup>, [Lijie Liu](https://liulj13.github.io/)<sup> * </sup>, [Mingcong Liu](https://onion-liu.github.io), Yi Zhang, Gen Li, Xinghui Li, Siyu Zhou, [Qian He](https://scholar.google.com/citations?user=9rWWCgUAAAAJ), Xinglong Wu
> <br><sup> * </sup>Equal contribution,<sup> &dagger; </sup>Project lead
> <br>Intelligent Creation Lab, ByteDance<br>


<!-- # Phantom-Data
Phantom-Data: Towards a General Subject-Consistent Video Generation Dataset -->
## 📑 Todo List
- [x] We released the dataset, built upon koala-36M, on Huggingface [Phantom-data-Koala36M](https://huggingface.co/datasets/ZhuoweiChen/Phantom-data-Koala36M).
- [x] Add more detailed instruction on how to use this dataset after the national vacation.

## Usage
### Step 1: Download the Meta Info Parquet Files
Download the meta info from [Phantom-data-Koala36M](https://huggingface.co/datasets/ZhuoweiChen/Phantom-data-Koala36M). There are two files:

1. **`koala36M_multi_ref_meta_info_merged.parquet`**: This file contains the metadata of all clips. The columns are mostly from Koala36M, with one additionally added column `vid` to uniquely identify each clip.

2. **`koala36M_multi_ref_merged_filtered.parquet`**: This file contains the training data meta info. The columns are:
    - `vid`: The target clip identifier.
    - `video_caption`: The caption describing the clip content.
    - `cross_pair`: A dictionary mapping noun phrases from `video_caption` to cross-modal reference data. Each entry contains:
        - `obj_from_tgt_video`: Source objects detected in the target clip
        - `refer_result`: List of matching reference images with bounding boxes


### Step 2: Download all clips from the meta info parquet
Download all clips from `koala36M_multi_ref_meta_info_merged.parquet`. The clips can be downloaded using `youtube_url` + `timestamp`. We refer to [Panda-70M](https://github.com/snap-research/Panda-70M/tree/main/dataset_dataloading) for the download implementation.

### Step 3: Extract Reference Images
After downloading the clips, extract reference images from `koala36M_multi_ref_merged_filtered.parquet`. The `refer_result` field contains lists of reference images with their corresponding bounding boxes. 

1. Get the refer frame: The frame is from the `vid` and the index can be calculated as: `frame_index = int(num_frames * frame_idx)` and resize with `resize_image(frame_list[frame_index], long_size=768)`. 
The `resize_image` func is
```
def resize_image(img_pil, long_size=1024):
    width, height = img_pil.size
    
    # Check if the longest side exceeds the limit (long_size)
    if max(width, height) > long_size:
        # Calculate new dimensions
        if width > height:
            new_width = long_size
            new_height = int((new_width / width) * height)
        else:
            new_height = long_size
            new_width = int((new_height / height) * width)
            
        # Resize the image
        img_pil = img_pil.resize((new_width, new_height), Image.LANCZOS)
        
    return img_pil
```

2. Get refer subjects with the bounding box. The bounding box in the parquet is organized as `<x_min, y_min, x_max, y_max>`. 

Finally we can get the <reference objects, video_caption> ===> target videos triplet pairs.


## Acknowledgement
We would like to thank the [Koala-36M team](https://koala36m.github.io/) for their valuable work. And we would like to thank our excellent engineering team Ronggui Peng, Bingqian Yi, Xiaojun Lin for their engineering support.

## Disclaimer
Our team does not use this dataset for any commercial purposes. The Phantom-Data dataset is released for non-commercial research purposes only. 

## ⭐ Citation

If Phantom-Data is helpful, please help to ⭐ the repo.

If you find this project useful for your research, please consider citing our [paper](https://arxiv.org/abs/2506.18851).

### BibTeX
```bibtex
@article{chen2025phantom-data,
      title={Phantom-Data: Towards a General Subject-Consistent Video Generation Dataset},
      author={Chen, Zhuowei and Li, Bingchuan and Ma, Tianxiang and Liu, Lijie and Liu, Mingcong and Zhang, Yi and Li, Gen and Li, Xinghui and Zhou, Siyu and He, Qian and Wu, Xinglong},
      journal={arXiv preprint arXiv:2506.18851},
      year={2025}
    }
```

## 📧 Contact
If you have any comments or questions regarding this open-source project, please open a new issue or contact [Zhuowei Chen](chenzw01@mail.ustc.edu.cn).
