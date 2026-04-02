<div id="top" align="center">

# LEARNING CURVE
</div>

<img src="asset/flow_chart.png" width="1000">

## Annotation Guide: Standard Operating Procedure

Follow these phases to generate high-fidelity ground truth masks. This workflow is optimized for the **Cataract-1K** dataset to ensure temporal consistency and physical accuracy.

---

### Phase 1: Ingestion & Tool Identification
1. **Initialize:** Launch the **IntrekSAM** application.
2. **Data Loading:** Click **Load Auto** to automatically retrieve the next unannotated video from your input directory.
   * Alternatively, use **Load Video** to select a specific sequence manually.
   * Or, use **Load Video frames** to load pre-extracted image directories. This method is optimized for long-duration videos that exceed standard hardware memory buffers.
3. **Define Target:** From the **Class Selection Sidebar**, select the label corresponding to the instrument you intend to track (e.g., *Phaco Tip*).
 

---

### Phase 2: Semantic Seeding (Initial Frame)
1. **Locate Clear Frame:** Use the **Frame Slider** to find a frame where the target tool is clearly visible and its boundaries are distinct.
2. **Interactive Prompting:**
   * **Positive Prompts (Left-Click):** Place points on the tool body to define the mask area.
   * **Negative Prompts (Right-Click):** Place points on specular reflections, fluid bubbles, or background tissue to refine the mask edges.
3. **Verify Mask:** If a "transient mis-click" occurs, use **Undo Points** to remove all points in the current frame for selected class. Ensure the mask perfectly encapsulates the physical tool before proceeding.


---

### Phase 3: Temporal Alignment & Tracking
1. **The Rewind Rule:** For the **initial setup** of a tool, drag the slider back to the frame immediately *before* the tool enters the field of view.
2. **Initiate Tracking:** Press **Play**. The SAM 2 engine will begin tracking the tool from its point of entry using the established semantic memory.
3. **Monitor Propagation:** Observe the **Main Display Canvas** as the mask propagates forward through the sequence.



---

### Phase 4: Verification & Multi-Tool Iteration

1. **Observe Tracking:** Monitor the **Main Display Canvas** as the mask propagates. Use the **Left/Right Arrow Keys** for precise frame-by-frame verification of the mask's fidelity.
2. **Drift Correction:** If the mask deviates from the tool boundary or an occlusion occurs:
   * **Pause** the video at the **first incorrect frame** using frame steppers.
   * Click **Clear Annotations** to flush the predicted memory buffer from the current frame to the end of the sequence ($t \to \infty$).
   * **Re-prompt** at that exact frame to "re-seed" the model and press **Play** to resume tracking. (**Note:** Do not rewind for mid-sequence corrections).
3. **Iteration Check:** Once the sequence for the current tool is complete, determine if additional instruments (e.g., *Spatula*) require annotation.
   * **If Next Tool Exists:** Return to **Phase 1** to select the new tool label and begin its semantic seeding process.
4. **Final Export:** After all instruments in the video have been accurately masked and verified, click **Export Annotations**.

---

# Best Practices for High-Fidelity Annotation

To ensure the highest data quality across any visual domain, all annotators should adhere to these professional guidelines.

---

### 1. Geometric Precision (The "Initial Seed")
* **Target the Active Region:** For elongated objects, prioritize placing points on the "active" or functional end. Avoid annotating static handles or base structures unless you would like to include entire tool in your mask.
* **Edge Anchoring:** Do not place points only in the center of the object. Strategically place positive prompts near the physical boundaries. This helps the model lock onto the object's edges against complex backgrounds.
* **The Rewind Rule:** Always initialize a new object by rewinding to the frame prior to its entry into the field of view. This provides a clean starting state for the model’s temporal memory.

(place a short gif)

---

### 2. Managing Environmental Artifacts
* **Negative Prompting for Specular Noise:** High-intensity lighting often creates glare on metallic or reflective surfaces. If a mask bleeds into a reflection, place **Negative Prompts** directly on the glare to force the mask back to the object’s physical boundary.
* **Visual Interference:** Treat bubbles or fluid distortions as background noise. Use negative prompts to ensure the object mask does not expand into these transient visual artifacts.

(place a short gif)

---

### 3. Occlusion & Boundary Protocol
* **The "No-Guessing" Rule:** If an object passes behind another element or is partially obscured, **do not estimate its hidden position.** Annotate only the visually confirmed portion to prevent hallucinated data in the final dataset.
* **Boundary Maintenance:** If an object leaves the field of view, immediately use `Clear Annotations` to stop tracking. This prevents phantom masks from sticking to the edges of the video frame and prevents model to hallucinate tool in other elements of the video.

(place a short gif)

---

### 4. Temporal Verification & Drift Management
* **Active Review:** Pay attention to the masks propagating through the video to identify any drifts in tracking and ensure mask accuracy. 
* **The "First Frame" Correction:** At the **first frame of detected drift**, pause the video and correct the mask. Small errors compound quickly; fixing a minor drift early prevents a total tracking failure later in the sequence.

---

### 5. Multi-Object Strategy: Efficiency vs. Fidelity
* **Sequential Pass (Recommended):** Fully annotate and verify Object A before starting Object B. This is the "Gold Standard" for ensuring the model's memory bank remains focused on a single semantic target.
* **Simultaneous Pass (Advanced):** For clear sequences where objects remain physically separated, you may seed multiple objects at once by providing distinct positive and negative prompts for every active class before initiating propagation.
    * **Warning:** If one object drifts, clearing annotations will wipe progress for *all* active objects in that pass. Use this only for high-quality, low-noise footage.
