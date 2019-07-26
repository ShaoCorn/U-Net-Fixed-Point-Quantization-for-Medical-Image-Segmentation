![alt text](https://github.com/hossein1387/Fixed-Point-U-Net-Quantization-for-Medical-Image-Segmentation/blob/master/Figs/nih.png)
![alt text](https://github.com/hossein1387/Fixed-Point-U-Net-Quantization-for-Medical-Image-Segmentation/blob/master/Figs/em.png)
![alt text](https://github.com/hossein1387/Fixed-Point-U-Net-Quantization-for-Medical-Image-Segmentation/blob/master/Figs/gm.png)


# Fixed Point U-Net Quantization For Medical Image Segmentation

This repository contains code for "Fixed-Point U-Net Quantization for Medical Image Segmentation" paper to be appeared at MICCAI2019. It contains our experiments on three different datasets namely: [The Spinal Cord Gray Matter Segmentation (GM)](https://www.sciencedirect.com/science/article/pii/S1053811917302185), [The ISBI challenge for segmentation of neuronal structures in Electron Microscopic (EM)](https://journals.plos.org/plosbiology/article?id=10.1371/journal.pbio.1000502) and [The public National Institute of Health (NIH) dataset for pancreas segmentation in abdominal CT scans](https://wiki.cancerimagingarchive.net/display/Public/Pancreas-CT).


## Data preprocessing:

For each dataset, we used a preprocessing script that can be found in . Please follow instructions for each dataset.
You can also download the pre-processed data from [this link](https://drive.google.com/file/d/1kjc3HLVuGdMa9wBF1SHaNicH9Y-maDzZ/view?usp=sharing).

## Running code:

Every dataset contains a main directory called \*\*\*\_BASE. This directory contains the original code for that dataset. The files found in folders in the dataset directory are symbolically linked to the files in BASE directory except the config file. The configuration file is a YAML file that shows what configuration is used for this specific experiment. For instance, for EM dataset, to run an experiment with a specific integer quantization precision (lets try Q4.4 bit for weight and Q4.4 bit for activation), you first need to modify the configuration as follow:

```yaml

UNET:
    dataset: 'emdataset'
    lr: 0.001
    num_epochs: 200
    model_type: "unet"
    init_type: glorot
    quantization: "FIXED"
    activation_f_width: 4
    activation_i_width: 4
    weight_f_width: 4
    weight_i_width: 4
    gpu_core_num: 1
    trained_model: "/path/to/trained/models/em_a4_4_w4_4.pkl"
    experiment_name: "em_a4_4_w4_4"
    log_output_dir: "/path/to/output/folder"
    operation_mode: "normal"
```

All datasets use the same configuration format. The following are most of the configuration that can be used:

* Currently we only have tested our quantizer on UNET architecture hence, the first line must be `UNET` and it must be passed as argument to the main script.
* `dataset`: defines the dataset name. It can be named anything. The scripts use this string for outputting readable information. 
* `lr`: shows an initial learning rate. We use Annealing Cosine scheduler for reducing the learning rate. 
* `num_epochs`: defines how many epochs you wish to run your model.
* `model_type`: is a string that shows what model we use, currently, the only option is `unet`. 
* `init_type`: is the initialization function that is used for initializing parameters. Currently the only option is `glorot`.
* `quantization`: defines what type of quantization you want to use. You can choose: 
    * `INT`: Integer quantization
    * `BNN`: Binary quantization
    * `Normal`: No quantization
    * `FIXED`: Fized point quantization
    * `Ternary`: Ternary Quantization
* `activation_[f-i]_width`: is used to define how many bits you want to use for quantizing the floating (`f`) or integer (`i`) part of the activation values. This option is used only for `INT` and `FIXED` quantization types.
* `weight_[f-i]_width`: is used to define how many bits you want to use for quantizing the floating (`f`) or integer (`i`) part of the parameter values. This option is used only for `INT` and `FIXED` quantization types.
`gpu_core_num`: Defines which gpu core you want to run your model. Parallel computing (multiple gpus) is not supported for the moment.
`trained_model`: "/path/to/trained/models/em_a4_4_w4_4.pkl"
`experiment_name`: "em_a4_4_w4_4"
`log_output_dir`: "/path/to/output/folder"
`operation_mode`: "normal"
