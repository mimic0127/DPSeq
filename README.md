# DPSeq

This is an implementation of our paper ***DPSeq: A Novel and Efficient Digital Pathology Model for Predicting Cancer Biomarkers using Sequencer 
Architecture***

## Pipeline of Our Methods

The pipeline of our method is shown in Pipeline Figure:

![Pipeline Figure](/figures/pipeline.png)

Pipelines with DPSeq for predicting molecular pathways and gene mutations in CRC. MCO-CRC and TCGA-CRC-DX were used to train and test for prediction of 
molecular biomarkers in CRC (i.e., MSI, BRAF mutation, and CIMP). The whole-slide images were tessellated into non-overlapping tiles of 512 × 512 pixels at a resolution of 0.5 µm. The resulting tiles were then resized to 224 × 224 pixels and color normalized. Tumor tissues (tiles) were subsequently selected by a Swin-T-based tissue-type classifier. Up to 500 tumor tiles were randomly selected for each slide. DPSeq fine-tuned by tissue classification task were trained to predict tile-level biomarkers. The predictive slide labels were obtained via tile score aggregation. At the bottom right of the figure is the core structure of BiLSTM2D in our DPSeq

## Requirements

- ``python 3.8``
- ``torch 1.8.1+cu111``
- ``torchvision 0.9.1+cu111``
- ``timm 0.5.4``

## Code

Firstly should:

```bash
cd DPSeq
```

### **Extract Tiles**

We extracted tiles by **extractTiles.py**. The origin of this code is from [kather lab](https://github.com/KatherLab/preProcessing).

```bash
python extractTiles.py -s slide_path -o out_path -ps pic_save_path
```

### **Tissue Classification**

(Swin-T)-based Tissue classifier can be trained by from [our lab](https://github.com/Boomwwe/SOTA_MSI_prediction).

```bash
python Tissue_classfier.py -tr train_path -te test_path -ps model_save_path 
```

### **Fine-tuning of DPSeq**

```bash
python DPSeq_finetuning.py -tr train_path -te test_path -ps model_save_path
```

### **Tile-Level Label Training**

Take MSI status prediction as an example. BRAF muation and CIMP status are similar as MSI (Only should change the ground truth file path in patch_dataloader.py). 

Code of Sequencer folder is cited from [Sequencer](https://github.com/okojoalg/sequencer)

```bash
python biomarker_prediction/train/train_external.py --TCGA_folder_path test_folder_path\
                                                    --MCO_MSS_path train_folder_path_with_MSS\
                                                    --MCO_MSI_path train_folder_path_with_MSI\
                                                    --model_name model_name\
                                                    --output_dir output_folder\
                                                    --model_save_path model_save_path
```


### **Patient-Level Label Prediction**

```bash
python biomarker_prediction/pred/patient_pred.py --TCGA_folder_path prediction_folder_path\
                                                  --model_name model_name\
                                                  --model_save_path model_save_path\
                                                  --output_dir output_folder
```
