# Bronze Winning Solution: HMS Harmful Brain Activity Classification

**Competition Link:** [HMS Harmful Brain Activity Classification](https://www.kaggle.com/competitions/hms-harmful-brain-activity-classification/overview)

Our solution for this Kaggle competition was a weighted ensemble of efficient nets, WaveNet, and CatBoost.

## Approach Overview:

### EEG Signal Conversion and Spectrogram Processing:
- Conversion of EEG signals to spectrogram.
- Image processing on spectrograms, including removal of normalization to preserve peak information.
- Introduction of a clip value threshold based on manual inspection to identify harmful brain activity. We found that retaining peaks in the spectrograms facilitated the identification of key brain activities by experts.
- Dataset used was made available public by Chris Deotte : [brain_eeg_spectrograms](https://www.kaggle.com/datasets/cdeotte/brain-eeg-spectrograms) and [brain_spectrograms](https://www.kaggle.com/datasets/cdeotte/brain-spectrograms) 

### Efficient Nets:
- Application of EfficientNet on Kaggle spectrograms and EEG spectrograms.
- Average predictions from multiple EfficientNet models (b0, b1, b2, b3) for final prediction. Leveraging the power of ensemble learning, we combined the predictions from various EfficientNet architectures to enhance the robustness of our model.

### WaveNet Model:
- Utilization of WaveNet model for processing 1-D signals.
- Incorporation of butter pass filters and feature engineering based on appropriate electrodes.
- Introduction of channel flipping to account for abnormalities in LPD and LRDA. By training on both original and flipped datasets, we aimed to capture abnormalities present on one side of the head.

### CatBoost Model:
- Utilization of mean and min of spectrogram features on 10-second ,20-second and 10 minutes frames.
- Inclusion of EEG spectrogram frame for every 10-second frame.
- Implementation of Multi cross-entropy loss to improve performance. We employed this loss function as it closely aligns with the competition metric, KL divergence, and significantly enhanced our cross-validation scores.

### Two-Stage Training:
- Application of 2-step training on each model, involving training on the entire dataset and fine-tuning on a subset with a higher number of voters. Recognizing the potential quality disparity within the dataset, we adopted a two-step training approach to refine our models on higher quality data.

(Note: Direct Fine-tuning is not possible for CatBoost, hence high-quality data was added twice for training purposes).

Weights for the weighted average was calculated via training 1 layer CNN 

| Models(in 2 Stage training) | Public LB| Private LB|
|-----------------|-----------------|-----------------|
| Weighted Ensemble| 0.30 | 0.36 |
| Efficient b0 | 0.31 | 0.38 |
| Wavenet| 0.37 | 0.48 |
| Catboost| 0.51 | 0.60 |
