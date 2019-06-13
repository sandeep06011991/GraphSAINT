# GraphSAINT: Graph <u>SA</u>mpling Based <u>IN</u>ductive Learning <u>T</u>echniques for Large Scale Graphs

This is the open source implementation for the "GraphSAINT" paper submitted to KDD 2019.

Note: training time for the four datasets have been significantly improved after the submission (due to some simple optimization on GPU). See below:

![table](https://github.com/GraphSAINT/GraphSAINT/blob/master/readme_table.png)

## Dependencies

* python >= 3.6.8
* tensorflow >=1.12.0
* cython >=0.29.2
* numpy >= 1.14.3
* scipy >= 1.1.0
* scikit-learn >= 0.19.1
* pyyaml >= 3.12
* zython (https://github.com/ZimpleX/zython)

## Dataset

Currently available datasets:

* ppi
* reddit
* flickr
* yelp
  
They are available at [gdrive](https://drive.google.com/open?id=1zycmmDES39zVlbVCYs88JTJ1Wm5FbfLz). Rename the folder to `data` at the root directory.  The root directory should be

```
GraphSAINT
│   README.md
│   run_graphsaint.sh
│   ... 
│
└───graphsaint
│   │   supervised_models.py
│   │   supervised_train.py
│   │   ...
│   
└───data
│   └───ppi
│   │   │    adj_train.npz
│   │   │    adj_full.npz
│   │   │    ...
│   │   
│   └───reddit
│   │   │    ...
│   │
│   └───...
│
```

We also have a script that convert our dataset format to GraphSAGE format. To run the script,

`python convert.py <dataset name>`

For example `python convert.py ppi` will convert dataset PPI and will save GraphSAGE format to `/data.ignore/ppi`
  


## Cython

We have a cython module which need compile before running. Compile the module by

`python graphsaint/setup.py build_ext --inplace`

## Train Config

The hyperparameters needs in training is givin in `/train_config/<dataset><num_layer>.yml`.

For detailed description of the config, please see `/train_config/README.yml`

## Run

To run the code on cpu

`./run_graphsaint.sh <dataset_name> <path to train_config yml>`

To run the code on gpu

`./run_graphsaint.sh <dataset_name> <path to train_config yml> --gpu <GPU number>`

For example `--gpu 0` will run on the first GPU. 


## TODO

* change `epoch` to `iteration`. Remember there is no strict definition of `epoch`.
* split the pre-proc and training procedure. Pre-proc can be pure C++ if necessary.
* explain the minor difference in various arch (S-GCN/GraphSAGE...) in num of order 1 layers
* change 'lin', 'relu' in yml to 'l', 'r'
* layer 1 dim?? can config as max(1/2*inputdim,ymldim) -- ensure graph conv layers are operating on at least features of hidden dim. Also, we ensure the first conv layer don't lose much info in input feature
* additional order 0 layer: this is only the case for reddit and ppi. so state that we add this mlp due to s-gcn. for flickr and yelp, we don't add this additional order 0 layer
* activation is not unified: you can default to lin. so Flickr will then be missing that act field. All others will have relu. The only problem is Reddit first layer -- it cannot have relu??

* add number of epochs for termination (to make the setup more clear)
* add 2-layer convergence curve

* Integrate with GAT, High-Order GCN, JK-Net
* Solve the runtime error of AS-GCN
* add MRW algorithm block, clarify the subgraph nodes for PPI is just a budget, but not the actual node count
