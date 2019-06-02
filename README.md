[![Travis Build Status](https://img.shields.io/travis/uptakeopensource/autofocus.svg?label=travis&logo=travis&branch=master)](https://travis-ci.org/uptakeopensource/autofocus)

# Autofocus

This project uses deep learning computer vision to label images taken by motion-activated "camera traps" according to the animals they contain. Accurate models for this labeling task can address a major bottleneck for wildlife conservation efforts.

## Further Reading

- [Uptake.org Autofocus Case Study](https://www.uptake.org/impact/special-projects)
- [Machine Learning Meets Wildlife Conservation](https://www.lpzoo.org/blog/machine-learning-meets-wildlife-conservation)

### Getting the Data

If necessary, create an AWS account, install the AWS CLI tool (`pip install awscli`), and set up your AWS config and credentials (`aws configure`).

All of the commands below are written to run from the repo root.

The work that has been done for this project so far has focused on a set of images provided by the Lincoln Park Zoo's Urban Wildlife Institute that were taken in the Chicago area in mid-summer 2016 and 2017. Use this commend to download a preprocessed version of that dataset to `autofocus/data/` (you can change the destination directory if you like):

```bash
FILENAME=lpz_2016_2017_processed.tar.gz
aws s3 cp s3://autofocus/lpz_data/${FILENAME} $(pwd)/data/
```

Unpack the tarfile:

```bash
tar -xvf $(pwd)/data/${FILENAME} -C $(pwd)/data/lpz_2016_2017/ 
```

Delete the tarfile:

```bash
rm $(pwd)/data/${FILENAME}
```

This dataset contains approximately 80,000 images and a CSV of labels and image metadata. It occupies 17.1GB uncompressed, so you will need about 40GB free for the downloading and untarring process. The images have been preprocessed by trimming the bottom 198 pixels (which often contains a metadata footer that could only mislead a machine learning model) and resizing to be 512 pixels along their shorter dimension. In addition, the labels have been cleaned up and organized.

If you would like to work with data that has not been preprocessed as described above, replace `FILENAME=lpz_2016_2017_processed.tar.gz` with `FILENAME=data_2016_2017.tar.gz`. You will need to have about 100GB free to download and untar the raw data. `autofocus/build_dataset/lpz_2016_2017/process_raw.py` contains the code that was used to generate the processed data from the raw data.

A second dataset from the Lincoln Park Zoo's Urban Wildlife Institute contains approximately 75,000 images (227 x 227 pixels) and a CSV of labels and image metadata from the Chicago area in 2012-2014. It takes up 7.9GB uncompressed. To get this data, follow the same steps as for the 2016-2017 dataset, but replace `FILENAME=lpz_2016_2017_processed.tar.gz` with `FILENAME=lpz_2012-2014.tar.gz`, and use this command to unpack the tarfile.

```bash
tar -xvf $(pwd)/data/${FILENAME} -C $(pwd)/data/
```

A third dataset from the Lincoln Park Zoo's Urban Wildlife Institute contains unlabeled three-image bursts from 2018. It takes up 5.7GB uncompressed. To get this data, follow the same steps as for the 2012-2014 dataset, but replace `FILENAME=lpz_2016_2017_processed.tar.gz` with `FILENAME=lpz_2018.tar.gz`.

### Getting a Model

Download a multilabel fast.ai model: 

```bash
aws s3 cp s3://autofocus/models/multilabel_model_20190407.pkl $(pwd)/autofocus/predict/models
```

`autofocus/train_model/train_multilabel_model.ipynb` contains the code that was used to train and evaluate this model.

### Serving Predictions

`autofocus/predict` contains code for a Flask app that serves predictions from a trained fast.ai model. See the README in that directory for more information.
