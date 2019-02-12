# Source documentation

Here are the main scripts to train and run LogoHunter:

+ `logohunter.py`: script to run logo detection and matching on a (set of) input images, given a (set of) input logos to match against. Inputs can be given either as path to single files, to a directory containing images, to a text file with each line having the path to an image, or passed from the prompt.
    ```
    --image               Image detection mode
    --input_images INPUT_IMAGES
                          path to image directory or video to find logos in
                          (default = 'input' for prompt input)
    --input_brands INPUT_BRANDS
                          path to directory with all brand logos to find in input images (default = 'input' for prompt input)

    --batch               Image detection mode for each file specified in input txt
                          (default = False)
    --test                Test routine: run on few images in /data/test/ folder
                          (default = False)
    --output OUTPUT       output path: either directory for single/batch image,
                          or filename for video (default = './')
    --outtxt              save text file with inference results
                          (default = True)    
    --no_save_img         do not save output images with annotated boxes
                          (default = False)
    --fpr FPR             False positive rate target to define similarity
                          cutoffs (default = 0.95).
    --yolo_model MODEL_PATH
                          path to YOLO model weight file
                          (default = 'keras_yolo3/yolo_weights_logos.h5')
    --anchors ANCHORS_PATH
                          path to YOLO anchors
                          (default = 'keras_yolo3/model_data/yolo_anchors.txt')
    --classes CLASSES_PATH
                          path to YOLO class specifications (default = 'data_classes.txt')
    --gpu_num GPU_NUM     Number of GPU to use (default = 2)
    --confidence SCORE    YOLO object confidence threshold above which to show predictions
                          (default = 0.1)
    --features FEATURES   path to LogosInTheWild logos features extracted by InceptionV3
                          (default = 'inception_logo_features.hdf5')
    ```

+ `similarity.py`: functions to compute cosine similarity between input images with predicted bounding boxes, and input logos, as well as similarity cutoffs to decide when two images are a match, and plotting results.

+ `utils.py`: helper functions to extract logos, preprocess images, load/save HDF5 files and draw on images.

+ `train.py`: train YOLOv3 object detection model. Arguments are specified in the file itself. Can run out of the box.

+ `metrics.py`: helper functions for metrics to quantify training quality, such as precision, recall and mean average precision (mAP). Functions can be imported as from a module, and when executed by itself it will produce precision-recall curves for the YOLO logo detection model. One curve is generated by changing the model confidence threshold above which to match a prediction to ground truths, and one can generate multiple curves depending on the minimum intersection-over-union (IoU) threshold between predictions and ground truths.
    ```
    --test_file TEST_FILE
                          path to ground truth text file in keras-yolo3 format
    --pred_file PRED_FILE
                          path to predictions text file in keras-yolo3 format
    --fig_out FIG_OUT     path to save location of precision-recall figure
```

+ `fetch_LogosInTheWild.py`: download Logos In The Wild images given text files containing URLs to them.
    ```
--dir_litw DIR_LITW  path to Logos In The Wild data/ parent folder. Each
                     subfolder contains a url.txt with links to images
```

+ `create_clean_dataset.py`: process Logos In The Wild dataset, remove annotations for dead links, take care of inconsistencies in manual annotations, extract ground truth logos as individual images. Slightly modified from the original version provided with Logos In The Wild database. Licensed under CC-by-SA 4.0 license.

+ `litw_annotation.py`: script to process image annotation from VOC style to keras-yolo3 style. Take XML files and return single text file with one image path per line, followed by annotations specifying ground truth object bounding boxes.
```
path-to-file1.jpg xmin,ymin,xmax,ymax,class_id xmin,ymin,xmax,ymax,class_id
path-to-file2.jpg xmin,ymin,xmax,ymax,class_id
```
Arguments are:
    ```
      -img_path IMG_PATH    path to parent directory containing images and xml
                            annotations (default:
                            ../data/LogosInTheWild-v2/data_cleaned/voc_format)
      -out_name OUT_NAME    name template for output text files (to be appended:
                            _test.txt, _train.txt) (default: data)
      -classes_names CLASSES_NAMES
                            path to txt file listing all possible object classes
                            (default:  '../data/LogosInTheWild-v2/data_cleaned/brands.txt')
      -train_test_split TRAIN_TEST_SPLIT
                            fraction of dataset set apart for test (default: 0.3)
      -split_class_or_file SPLIT_CLASS_OR_FILE
                            Train/test split at class (0) or file level (1)
                            (default: 1)
      -closedset            If specified, annotate logo objects class by class
                            instead of as one class (default: False)
```