# Object Detection on the Waymo Open Dataset

## Experiment Summary

I trained and evaluated various object detection models on the Waymo Open Dataset using the Tensorflow Object Detection API on AWS SageMaker. Starting with a baseline EfficientDet D1 model and then experimenting with Faster R-CNN architectures, I explored how data augmentation strategies and hyperparameter tuning influence both overall performance and object-scale-specific metrics (small, medium, and large objects).

### Results by Model and Scale

1. **Baseline (EfficientDet D1 640x640)**  
   - **Overall mAP:** ~0.080  
   - **Small Objects (AP):** ~0.030  
   - **Medium Objects (AP):** ~0.334  
   - **Large Objects (AP):** ~0.292  
   
   While the baseline performed moderately on medium and large objects, it struggled significantly with small objects.
   
   *Loss*:
   - Final Train Loss: 0.2766
   - Eval Loss: 0.4168

   The train loss is lower than the eval loss, which is a typical situation when the model overfits. It indicates that the model is doing well on the training data but struggles to generalize on the validation or test data. The gap isn't large, suggesting that the model is not severely overfitting at this point.


2. **EfficientDet D1 with Augmentations & LR Tuning**  
   - **Overall mAP:** ~0.118  
   - **Small Objects (AP):** ~0.051  
   - **Medium Objects (AP):** ~0.409  
   - **Large Objects (AP):** ~0.505  
   
   By adding brightness, contrast, saturation, scaling, and Gaussian noise augmentations, as well as refining the learning rate, I saw improvements across all object sizes, especially large objects. The stronger performance at all scales suggests that these augmentations helped the model generalize to varied conditions and object sizes.
   
   *Loss*:
   - Final Train Loss: 0.3031
   - Eval Loss: 0.3033

   The train and eval losses are almost the same, indicating that the model has generalized well to the eval dataset. This is a good sign that the model is not overfitting. It indicates that the model is well-tuned for both training and validation data.


3. **Faster R-CNN ResNet101 V1 640x640**  
   - **Overall mAP:** ~0.155  
   - **Small Objects (AP):** ~0.078  
   - **Medium Objects (AP):** ~0.502  
   - **Large Objects (AP):** ~0.785  
   
   Switching to a two-stage detector with a stronger backbone not only boosted the overall mAP but also offered substantial gains across all object sizes. This improvement was most pronounced for medium and large objects, though small-object detection also benefitted.
   
   *Loss*:
   - Final Train Loss: 0.5677
   - Eval Loss: 0.9523

   The train loss is significantly lower than the eval loss. This suggests that the model is overfitting and does not generalize well to unseen data. The model has memorized the training data but fails to perform well on the eval data.


4. **Faster R-CNN ResNet152 V1 640x640**  
   - **Overall mAP:** ~0.122  
   - **Small Objects (AP):** ~0.053  
   - **Medium Objects (AP):** ~0.411  
   - **Large Objects (AP):** ~0.675  
   
   Increasing backbone depth did not automatically yield higher overall performance compared to ResNet101. While large-object detection remained strong, small and medium metrics were not as improved as expected, suggesting that deeper models require even more careful tuning and potentially additional training steps or augmentations.
   
   *Loss*:
   - Final Train Loss: 0.6618
   - Eval Loss: 1.0039

   The train loss is significantly lower than the eval loss. This suggests that the model is overfitting and does not generalize well to unseen data. The model has memorized the training data but fails to perform well on the eval data.


I have decided to go ahead and deploying the Faster R-CNN ResNet101 V1 640x640 model as it has the highest mAP of 0.155 and the best performance on small, medium and large objects, knowing that the model is overfitting and needs more data augmentation to generalize better to unseen data. If I had more time and resources, I would experiment with more data augmentations, hyperparameter tuning, and especially worked more on the learning rate scheduling and regularization to reduce overfitting and improve the model's generalization capabilities.