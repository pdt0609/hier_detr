
---
## Hier-DETR

### Download COCO 2017 Dataset:
Please download [COCO 2017](https://cocodataset.org/) dataset and organize them as following:
```
COCODIR/
  ├── train2017/
  ├── val2017/
  └── annotations/
  	├── instances_train2017.json
  	└── instances_val2017.json
```

### Download Mapillary Traffic Sign Dataset:
Please download [MTSD](https://www.mapillary.com/dataset/trafficsign) dataset and organize them as following:
```
COCODIR/
  └── mtsd_fully_annotated_train_images/
   └──images
  └── mtsd_v2_fully_annotated_images.val.zip/
   └── images
```
### Join-training:

1. 
   ```
   cd ../jointraining/Hier-DETR/
   ```
2. Install:
   ```
   pip install -r requirements.txt
   pip install pytorch-metric-learning
   ```
3. Change dataset + annotations directory in /configs/dataset/coco_detection.yml

4. Run Hier-DETR:
   ```
   python tools/train.py -c configs/rtdetr/rtdetr_r50vd_6x_coco.yml
   ```

### Continual-settings:

1. Navigate to the "cod" directory:
   ```
   cd ../continual/cod
   ```
2. Install:
   ```
   pip install -r requirements.txt
   pip install pytorch-metric-learning
   ``` 

3. Change dataset + annotations directory in /cod/configs/dataset/coco_detection.yml

Change "num_classes, "''img_folder'' and ''ann_file'' in both ''train_dataloader'' and ''val_dataloader''
   
   For MTSD dataset:
   - Schema 1 (150+71):

    num_classes: 221

    train_dataloader:
    /home/kc/cl/MTSD/mtsd_fully_annotated_val_images/images
     \annotations\MTSD 150+71\train_output_file_coco.json
    
    val_dataloader:
    /home/kc/cl/MTSD/mtsd_fully_annotated_train_images/images
       \annotations\MTSD 150+71\val_output_file_coco.json
   
   
   
   - Schema 2 (129+92):

    num_classes: 221

    train_dataloader:
    img_folder:/home/kc/cl/MTSD/mtsd_fully_annotated_train_images/images
     ann_file:\annotations\MTSD 129+92\train_output_file_coco.json

    img_folder:/home/kc/cl/MTSD/mtsd_fully_annotated_val_images/images 
      ann_file:\annotations\MTSD 129+92\val_output_file_coco.json
   


   For COCO dataset:

     num_classes: 80
     
    train_dataloader:
     img_folder:/kaggle/input/coco2017/train2017
     ann_file:/kaggle/input/coco2017/annotations/instances_train2017.json

    val_dataloader:
     img_folder:/kaggle/input/coco2017/val2017
     ann_file:/kaggle/input/coco2017/annotations/instances_val2017.json


4. Change setting corresponding to specific task id: 
   ```
   cd /cod/configs/rtdetr/include/dataloader.yml
   ```
   Change `data_ratio: "15071"` depend on schema.
   
   Task 0:
      Set `task_idx: 0`, `buffer_mode: False`
   
   Task 1:
      Set `task_idx: 1`, `buffer_mode: True`, `buffer_rate: 0.1`

      ```
      cd /cod/configs/cl_pipeline.yml
      ```
      Change the teacher path `teacher_path: "/kaggle/input/checkpoint-for-mtsd-model/ckp_mtsd_cl_task0_schema_2.pth"`
   
5. Training:
   ```
  python scripts/train.py --config /kaggle/working/hier_detr/continual/cod/configs/rtdetr/rtdetr_r50vd_coco.yml 
  ```


