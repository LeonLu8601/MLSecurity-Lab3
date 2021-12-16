# Lab 3


```bash
├── lab3
    └── data 
        └── cl
            └── valid.h5 // this is clean validation data used to design the defense
            └── test.h5  // this is clean test data used to evaluate the BadNet
        └── bd
            └── bd_valid.h5 // this is sunglasses poisoned validation data
            └── bd_test.h5  // this is sunglasses poisoned test data
    └── models
        └── bd_net.h5
        └── bd_net.h5
        └── bd_net_prime_2.h5 // this B' model when accuracy drops at least 2%
        └── bd_net_prime_4.h5 // this B' model when accuracy drops at least 4%
        └── bd_net_prime_10.h5 // this B' model when accuracy drops at least 10%
        └── bd_net_prime_30.h5 // this B' model when accuracy drops at least 30%
    └── architecture.py
    └── eval.py // this is the evaluation script
├── MLSecurity_Lab3.ipynb // this is the colab notebook
└── MLSecurity_Lab3.pdf // this is the pdf version of colab notebook which is also the report of the lab
```

## I Dependencies（using in colab notebook）
   1. Python ~~3.6.9~~ 3.7.12
   2. Keras ~~2.3.1~~ 2.7.0
   3. Numpy ~~1.16.3~~ 1.19.5
   4. Matplotlib ~~2.2.2~~ 3.2.2
   5. H5py ~~2.9.0~~ 3.1.0
   6. TensorFlow-gpu ~~1.15.2~~  2.7.0

## II. Data
   1. Download the validation and test datasets from [here](https://drive.google.com/drive/folders/1Rs68uH8Xqa4j6UxG53wzD0uyI8347dSq?usp=sharing) and store them under `data/` directory.
   2. The dataset contains images from YouTube Aligned Face Dataset. We retrieve 1283 individuals and split into validation and test datasets.
   3. bd_valid.h5 and bd_test.h5 contains validation and test images with sunglasses trigger respectively, that activates the backdoor for bd_net.h5. 
   4. The detail of dataset exploration can be find in colab notebook [MLSecurity_Lab3.ipynb](https://github.com/LeonLu8601/MLSecurity-Lab3/blob/0d91f36d8095ce84caa577521196b660e1b1d750/MLSecurity_Lab3.ipynb) or report [MLSecurity_Lab3.pdf](https://github.com/LeonLu8601/MLSecurity-Lab3/blob/0d91f36d8095ce84caa577521196b660e1b1d750/MLSecurity_Lab3.pdf). 

## III. Evaluating the Backdoored Model
   1. The DNN architecture used to train the face recognition model is the state-of-the-art DeepID network. 
   2. To evaluate the backdoored model, execute `eval.py` by running:  
      `python3 eval.py <clean test data directory> <poisoned test data directory> <model directory>`.
      E.g., `python3 eval.py data/cl/valid.h5 data/bd/bd_valid.h5 models/bd_net.h5`. This will output:
      Clean Classification accuracy: 98.64 %
      Attack Success Rate: 100 %

## IV. Evaluating the repaired Models
1. The process of using prune defense to create the B' model and G model can be found in colab notebook  [MLSecurity_Lab3.ipynb](https://github.com/LeonLu8601/MLSecurity-Lab3/blob/0d91f36d8095ce84caa577521196b660e1b1d750/MLSecurity_Lab3.ipynb). 
2. To evaluate the repaired models, execute `eval.py` by running:  
    `python3 eval.py <clean validation data directory> <poisoned validation data directory> <model directory>`.
      E.g., `python3 eval.py data/cl/test.h5 data/bd/bd_test.h5 models/bd_net_prime_2.h5`. This will output:
      Clean Classification accuracy: 95.90023382696803
      Attack Success Rate: 100.0

3. The repaired model is saved and uploaded to github as ```bd_net_prime_2.h5, bd_net_prime_4.h5, bd_net_prime_10.h5, and bd_net_prime_30.h5 ```. 

    When we evaluate these models we will get the results as below:
    |  Repaired Model B' | Clean Classification Accuracy | Attack Success Rate |
    |:------------------:|:-----------------------------:|:-------------------:|
    |  Accuracy drops 2% |       95.90023382696803       |        100.0        |
    |  Accuracy drops 4% |       92.29150428682775       |  99.98441153546376  |
    | Accuracy drops 10% |       84.54403741231489       |  77.20966484801247  |
    | Accuracy drops 30% |       54.762275915822286      |   6.96024941543258  |

    The evaluation result can be found in colab notebook [MLSecurity_Lab3.ipynb](https://github.com/LeonLu8601/MLSecurity-Lab3/blob/0d91f36d8095ce84caa577521196b660e1b1d750/MLSecurity_Lab3.ipynb). 

4. Plot the accuracy on clean test data and the attack success rate (on backdoored test
data) as a function of the fraction of channels pruned.

    ![Lab3_plt](https://github.com/LeonLu8601/MLSecurity-Lab3/blob/ad92b83a4a057ccebae89993143cacb6fa220586/Lab3_plt.png)

5. whether the pruning defense works for this model? If not, why not? 

    The pruning defense does not work well for this model. The adaptive attack may be used to attack the model. The attacker uses the clean and poisoned dataset to train the model on pruned model and depruning to get this model. By attacking by this method, neurons can be activated by both clean dataset and poisoned dataset which means that we can only get the model with low attack success rate with low clean classification accuracy. And in the plot above, we can see that the the attack success rate is dropping after the accuracy begins to drop. 

## V. Important Notes
Please use only clean validation data (valid.h5) to design the pruning defense. And use test data (test.h5 and bd_test.h5) to evaluate the models. 
