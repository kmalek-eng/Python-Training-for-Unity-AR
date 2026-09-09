# Crack Segmentation Training

This folder contains a U-Net-based crack segmentation pipeline.

Run all commands from inside `Training/Crack_Segmentation`, so the relative paths work correctly.

## Files

`main.py`: trains the U-Net model using paired crack images and masks.

`model.py`: defines the U-Net segmentation model.

`transform_into_C#Unity.py`: final step that converts the trained U-Net checkpoint into C# weight arrays compatible with the C#-Unity platform for AR headset deployment.

`dataset.py`, `metrics.py`, `evaluation.py`: support files for dataset loading, metrics, and evaluation.

## Dataset

This project uses the [Concrete Crack Conglomerate Dataset](https://www.kaggle.com/datasets/aravindnagarajan/crack-segmentation-dataset/data), created by Eric Bianchi and Matthew Hebdon at Virginia Tech. The original dataset combines several public concrete-crack datasets, including CFD, Crack500, CrackTree200, DeepCrack, GAPs, Rissbilder, non-crack images, and Volker.

The dataset itself is not included in this repository.

| Split    | Images | Ground-truth masks |
| -------- | -----: | -----------------: |
| Training |  9,063 |              9,063 |
| Testing  |  1,619 |              1,619 |

Expected local structure:

```text
dataset/
├── train_images/
├── train_masks/
├── test_images/
└── test_masks/
```

Each image and its corresponding mask must have the same filename stem. Images are resized to `448 × 448` pixels before being passed to the U-Net.

## Train

```bash
python main.py \
  --image-dir dataset/train_images \
  --mask-dir dataset/train_masks \
  --save-dir checkpoints \
  --batch-size 8 \
  --max-epochs 50 \
  --num-folds 5 \
  --patience 10 \
  --device cpu
```

Training uses 5-fold cross-validation. The best model for each fold is saved based on validation F1 score. Early stopping stops training for a fold after 10 consecutive epochs without improvement in validation F1.

During validation, thresholds from `0.10` to `0.90` in steps of `0.05` are evaluated. The threshold with the highest validation F1 is stored with the best checkpoint.

A `last_checkpoint.pth` file is saved after each epoch so interrupted training can be resumed using the same command with:

```bash
--resume
```

## Export C# weights

```bash
python transform_into_C#Unity.py \
  --checkpoint-path checkpoints/fold_1/best_model.pth \
  --output-dir csharp_export
```

## Model architecture

The model uses a U-Net architecture for binary crack segmentation.

* Input: RGB image, `3 × 448 × 448`
* Output: binary crack mask, `1 × 448 × 448`
* Encoder: convolution blocks with max pooling
* Decoder: transposed convolutions with skip connections
* Final layer: `1 × 1` convolution

![U-Net architecture](images/Picture1.svg)

## Example results

The figure below shows four test examples with the input image, ground-truth mask, and predicted crack mask.

![Example segmentation results](images/Figure_4.png)

## Evaluate

The best Fold 1 checkpoint uses a validation-selected threshold of `0.30`.

```bash
python evaluation.py \
  --test-image-dir dataset/test_images \
  --test-mask-dir dataset/test_masks \
  --checkpoint-path checkpoints/fold_1/best_model.pth \
  --threshold 0.30 \
  --device cpu
```

## Model performance

Performance of the trained U-Net model on the held-out test set using a threshold of `0.30`.

| Metric    |  Score |
| --------- | -----: |
| F1 Score  | 0.7030 |
| Recall    | 0.7100 |
| Precision | 0.6067 |
| mIoU      | 0.7414 |

Best Fold # checkpoint saved during training: `checkpoints/fold_#/best_model.pth`

Best model manually selected and placed in the repository: `best_model/best_model.pth`
