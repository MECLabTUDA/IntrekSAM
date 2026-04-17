<div id="top" align="center">

# IntrekSAM: An Interactive SAM2-Based Tool for Video Segmentation
  Ssharvien Kumar Sivakumar & Anirudh Dhingra
</div>


## 💡Key Features
IntrekSAM is an interactive SAM2-based video segmentation annotation tool. To our knowledge, no existing SAM2-based interface for video segmentation annotation simultaneously provides the following: free and open-source access, easy setup, local deployment, easy customization, and multi-class segmentation support. 

Therefore, we rewrote the graphical interface in Python while retaining the original SAM2 backend. Our tool addresses all the requirements mentioned above and is designed to be easily modified for different use cases. This tool makes it easier to annotate video with jsut few prompt points!


## 🛠 Setup
```bash
1. Create a virtual environment and activate it: `conda create -n sam2_ann python=3.11 && conda activate sam2_ann`
2. Install `torch>=2.5.1` and `torchvision>=0.20.1` following the instructions in [here](https://pytorch.org/get-started/previous-versions/)
3. Install the dependencies using `pip install -r requirements.txt`
4. Install SAM2 using `cd src/sam2 && pip install -e .`
5. Install SAM2 Predictor using `pip install -e ".[notebooks]"`
6. Download the checkpoint of SAM2 (sam2.1_hiera_large) from [here](https://github.com/facebookresearch/sam2/tree/main#model-description). Then place it in this [folder](./src/sam2/checkpoints)
7. If you face this error - Could not load the Qt platform: Delete the libqxcb from "../anaconda3/envs/sam2_ann/lib/python3.11/site-packages/cv2/qt/plugins/platforms"
```


## Important Notes
For fastest speed run IntrekSAM's GUI locally. You can also run the GUI over SSH by enabling the X11 forwarding. The easiest way to set this up is by opening an SSH terminal in MobaXTerm.

```bash
cd IntrekSAM
python IntrekSAM.py --ann_info_path ./ann/sample_classes.json --output_ann_dir ./output --input_video_dir (optional - to auto-load next video)
```

In `ann_info_path`, you can define the **class names**, **class IDs**, and the **color palette** for the masks. The GUI buttons are generated dynamically based on the classes defined in this file. Annotated masks are saved using `PIL.putpalette`, which stores the masks with colors for visualization. When loading these masks with PIL, they are automatically mapped back to their corresponding **class IDs**.

You can either load:

- a **video file** (`.mp4` only), or  
- a **folder containing extracted frames** (`.jpg` only).

Directly loading the video has the downside being limited to the length of video depending on RAM resources. There is no limitation in length, if you load folder containing the extracted frames. On a NVIDIA RTX 3090 Ti, inference runs at approximately 10–15 FPS.

Frame files must be named using 10-digit zero-padded numbering. Use the following ffmpeg command, to extract the frames: 

```bash
ffmpeg -i "VIDEO_PATH" -qscale:v 1 -vf "fps=8,scale=512:288:flags=lanczos,setsar=1" -start_number 0 "VIDEO_FRAME_FOLDER/%010d.jpg"
```


## 📈 Learning Curve ([Instructions](HOWTO.md))
 > <details>
 > <summary><b>▶️ CLICK HERE TO WATCH:</b> <i>Demo Video</i></summary>
 > <br>
 > <div align="left">
 >   <video src="https://github.com/user-attachments/assets/b43e5877-3437-4eff-a9a2-3f64ebb466a8" controls width="100%">
 >   </video>
 > </div>
 > </details>



## 📜 Citations
If you are using IntrekSAM to annotate your video, please cite the following two papers:
```
@article{sivakumar2025sasvi,
  title={SASVi: segment any surgical video},
  author={Sivakumar, Ssharvien Kumar and Frisch, Yannik and Ranem, Amin and Mukhopadhyay, Anirban},
  journal={International Journal of Computer Assisted Radiology and Surgery},
  pages={1--11},
  year={2025},
  publisher={Springer}
}

@inproceedings{sivakumar2025sg2vid,
  title={Sg2vid: Scene graphs enable fine-grained control for video synthesis},
  author={Sivakumar, Ssharvien Kumar and Frisch, Yannik and Ghazaei, Ghazal and Mukhopadhyay, Anirban},
  booktitle={International Conference on Medical Image Computing and Computer-Assisted Intervention},
  pages={511--521},
  year={2025},
  organization={Springer}
}
```