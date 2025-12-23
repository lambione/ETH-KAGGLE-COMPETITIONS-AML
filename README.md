# 🧠 Age Prediction from MRI Brain Scans

We adopted a **regression stacking strategy**, integrating several base learners into a single predictive ensemble.

---

## 🧹 Data Preprocessing

- Missing values were handled using **KNN Imputer** to fill incomplete features.  
- **Outlier detection**:
  - Removed features with **zero variance**.  
  - Projected data onto **2D using PCA** and fitted a **Gaussian Mixture Model** to identify and remove outliers.  

- **Feature selection**:
  - Used **Mutual Information** and **F-regression** to capture both **non-linear and linear relationships**.  
  - Applied transformations (**square root, square, log**) to extract more signal from the data.

---

## 🧠 Model Training

To predict age from MRI scans, we developed a **stacking ensemble** composed of:
- **Support Vector Regressor (SVR)**  
- **Gaussian Process Regressor**  
- **XGBoost Regressor**

This ensemble leveraged the strengths of each model to improve predictive performance.

---

## 🎯 Age Prediction

- The trained ensemble was used to predict the age of test samples.  
- To correct for systematic bias in predictions, a **soft nonparametric LOWESS correction** was applied.  

---

## 📊 Performance

- Public R² score: **0.7249**  
- Private R² score: **0.7148**  
- Public leaderboard rank: **45th out of 136 teams**  
- Final private leaderboard rank: **70th out of 136 teams** (a slight overfit 😔) 

---

## 🫀 Mitral Valve Segmentation from Echocardiography

We decided to undertake **state-of-the-art supervised methodologies**. More specifically, we developed a **U-Net–based segmentation pipeline**.

---

## 🧹 Preprocessing and Data Preparation

The first step was to retrieve all labeled frames, i.e. frames with their corresponding masks.  
Given that **U-Nets perform best with a large number of annotated images**, and that we only had **195 labeled frames** from the provided training set, we decided to **augment the data**.

The following **data augmentation techniques** were applied:
- Horizontal flip  
- Vertical flip  
- Elastic deformation  
- Brightness and contrast enhancing  
- Gaussian blurring  
- Brightness and contrast dehancing  

Since augmenting every frame with **all** of the above techniques would have resulted in a very large training set and significantly increased computational cost, we implemented a **random augmentation strategy**.  
For each frame, every augmentation was applied with a **50% probability**. This allowed us to reduce computational requirements while keeping the training process **efficient and effective** ⚖️.

Because the videos had different frame sizes, all frames were **resized and padded to a fixed resolution of (256, 256)**.  
After segmentation, frames were **resized back and unpadded** for the test data to ensure a coherent submission format.

---

## 🧠 Model Training

We trained the **U-Net** using **Focal Loss** on the full training set.  
Training was performed with a **batch size of 4** to ensure computational efficiency.

We fine-tuned several parameters such as:
- kernel size  
- padding  
- stride  
- number of output channels  

Most importantly, we fine-tuned the **number of training epochs**. Training for too many epochs resulted in **gradient vanishing and mask degradation**.  
To address this, we saved multiple model checkpoints at different epochs and evaluated them, finally selecting the model that **performed best** ✅.

---

## 🛠️ Post-Processing

The most critical part was **frame post-processing**.

We intentionally used a U-Net trained for a **limited number of epochs**, allowing it to correctly segment the region around the mitral valve even if the predicted mask was not perfect. This resulted in **larger masks** centered in the correct anatomical region.

To obtain a tighter and more accurate segmentation, we applied a **medium-high threshold of 0.4**, effectively reducing mask size and improving mitral valve localization.

Additionally, to further improve mask quality, we applied a standard image-processing technique commonly used to **fill holes in segmented objects**:
1. A **single iteration of dilation** to fill gaps and connect regions  
2. A subsequent **erosion iteration** to counteract the dilation expansion  

Both operations used a **5×5 kernel**.

---

## ✅ Final Outcome

This pipeline resulted in a **robust and accurate mitral valve segmentation** across the full set of test videos.

---

## 📊 Performance

- Public IoU score: **50.355%**  
- Private IoU score: **52.312%**  
- Public leaderboard rank: **43rd out of 99 teams**  
- Final private leaderboard rank: **37th out of 99 teams** (+6 positions 😄)

---

## 👥 Contributors

- Lamberto Ragnolini (lragnolini@ethz.ch)  
- Ivan Angjelovski (iangjelovski@ethz.ch)
