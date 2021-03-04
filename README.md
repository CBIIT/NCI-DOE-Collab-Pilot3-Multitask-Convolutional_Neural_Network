## Multi Task-Convolutional Neural Networks (MT-CNN)

##### Author: Biomedical Sciences, Engineering, and Computing (BSEC) Group; Computer Sciences and Engineering Division; Oak Ridge National Laboratory

### Description
MT-CNN is a CNN for natural language processing (NLP) and information Extraction from free-form texts. BSEC group designed the model for information extraction from cancer pathology reports.

### User Community
Data scientists interested in classifying free form texts (such as pathology reports, clinical trials, abstracts, and so on). 

### Usability
The provided untrained model can be used by data scientists to be trained on their own data, or use the trained model to classify the provided test samples. The provided scripts use a pathology report that has been downloaded from the Genomics Data Commons, converted to text format, cleaned, and preprocessed. Here is an example [report](https://portal.gdc.cancer.gov/legacy-archive/files/a9a42650-4613-448d-895e-4f904285f508).

### Uniqueness
Classification of unstructured text is a classical problem in natural language processing. The community has developed state-of-the-art models like BERT, Bio-BERT, and Transformer. This model has the advantage of working on a relatively long report (that is, over 400 words) and shows scalability in terms of accuracy and speed with relatively small number of unstructured pathology reports. 

### Components
* Original and processed training, validation, and test data.
* Untrained neural network model.
* Trained model weights and topology to be used in inference.

### Completed Model Trans_Validate Template
| Attribute  | Value |
| ------------- | ------------- |
| Model Developer/POC  | Hong-Jun Yoon |
| Model Name | MT-CNN |
| Inputs  | Indices of tokenized text  |
| Outputs  | softmax  |
| Training Data  | sample data available in the repo  |
| Uncertainty Quantification  | N/A  |
| Platform  | Keras/Tensorflow   |


### Software Setup
To set up the Python environment needed to train and run this model:
1. Install [conda](https://docs.conda.io/en/latest/) package manager.
2. Clone this repository.
3. Create the environment as shown below.
```bash
   conda env create -f environment.yml -n mt-cnn
   conda activate mt-cnn
   ```
### Data Setup
To download the data needed to train and test the model, and the trained model files:
1. Create an account on the Model and Data Clearinghouse ([MoDaC](modac.cancer.gov)).
2. Run the script [data_handler.py](./data_hander.py), which will do the following: 
   * Download the GDC pathology reports from MoDaC.
   * Split the data into training/validation/test datasets.
   * Clean up punctuation and unecessary tokens from the reports.
   * Train a word embedding network to convert every word in the reports to an embedding vector of size 300.
   * Use the trained word embedding model to encode every report into a numpy array of size (1500 * 300).
   * Generate numpy arrays for the training/validation/test datasets.

For more information about the original, cleaned, and generated data, refer to the README.txt file that will be downloaded in the ./data directory.

### Training

To train a MT-CNN model with the sample data, execute the script `mt_cnn_exp.py`. This script calls MT-CNN implementation in `keras_mt_shared_cnn.py`.

```
$ python mt_cnn_exp.py
Using TensorFlow backend.
....
Number of classes:  [25, 117]
Model file:  mt_cnn_model.h5
__________________________________________________________________________________________________
Layer (type)                    Output Shape         Param #     Connected to                     
==================================================================================================
Input (InputLayer)              (None, 1500)         0                                            
__________________________________________________________________________________________________
embedding (Embedding)           (None, 1500, 300)    6948900     Input[0][0]                      
__________________________________________________________________________________________________
0_thfilter (Conv1D)             (None, 1500, 100)    90100       embedding[0][0]                  
__________________________________________________________________________________________________
1_thfilter (Conv1D)             (None, 1500, 100)    120100      embedding[0][0]                  
__________________________________________________________________________________________________
2_thfilter (Conv1D)             (None, 1500, 100)    150100      embedding[0][0]                  
__________________________________________________________________________________________________
global_max_pooling1d_1 (GlobalM (None, 100)          0           0_thfilter[0][0]                 
__________________________________________________________________________________________________
global_max_pooling1d_2 (GlobalM (None, 100)          0           1_thfilter[0][0]                 
__________________________________________________________________________________________________
global_max_pooling1d_3 (GlobalM (None, 100)          0           2_thfilter[0][0]                 
__________________________________________________________________________________________________
concatenate_1 (Concatenate)     (None, 300)          0           global_max_pooling1d_1[0][0]     
                                                                 global_max_pooling1d_2[0][0]     
                                                                 global_max_pooling1d_3[0][0]     
__________________________________________________________________________________________________
dropout_1 (Dropout)             (None, 300)          0           concatenate_1[0][0]              
__________________________________________________________________________________________________
site (Dense)                    (None, 25)           7525        dropout_1[0][0]                  
__________________________________________________________________________________________________
histology (Dense)               (None, 117)          35217       dropout_1[0][0]                  
==================================================================================================
Total params: 7,351,942
Trainable params: 7,351,942
Non-trainable params: 0
__________________________________________________________________________________________________
None
Train on 4579 samples, validate on 509 samples
.....
.....
Epoch 23/100
 - 6s - loss: 0.6748 - site_loss: 0.0386 - histology_loss: 0.2119 - site_acc: 0.9891 - histology_acc: 0.9378 - val_loss: 1.4682 - val_site_loss: 0.1636 - val_histology_loss: 0.8718 - val_site_acc: 0.9489 - val_histology_acc: 0.8075

Epoch 00023: val_loss did not improve from 1.38362
Prediction on test set
task site test f-score: 0.9654,0.9381
task histology test f-score: 0.8003,0.4069

```

### Disclaimer
UT-BATTELLE, LLC AND THE GOVERNMENT MAKE NO REPRESENTATIONS AND DISCLAIM ALL WARRANTIES, BOTH EXPRESSED AND IMPLIED. THERE ARE NO EXPRESS OR IMPLIED WARRANTIES OF MERCHANTABILITY OR FITNESS FOR A PARTICULAR PURPOSE, OR THAT THE USE OF THE SOFTWARE WILL NOT INFRINGE ANY PATENT, COPYRIGHT, TRADEMARK, OR OTHER PROPRIETARY RIGHTS, OR THAT THE SOFTWARE WILL ACCOMPLISH THE INTENDED RESULTS OR THAT THE SOFTWARE OR ITS USE WILL NOT RESULT IN INJURY OR DAMAGE. THE USER ASSUMES RESPONSIBILITY FOR ALL LIABILITIES, PENALTIES, FINES, CLAIMS, CAUSES OF ACTION, AND COSTS AND EXPENSES, CAUSED BY, RESULTING FROM OR ARISING OUT OF, IN WHOLE OR IN PART THE USE, STORAGE OR DISPOSAL OF THE SOFTWARE.


### Acknowledgments
This work has been supported in part by the Joint Design of Advanced Computing Solutions for Cancer (JDACS4C) program established by the U.S. Department of Energy (DOE) and the National Cancer Institute (NCI) of the National Institutes of Health.
