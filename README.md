This paper was accepted by USENIX Security '24. The source code and datasets provided are for research use only and not for commercial use.

# EatVul-Resources
EaTVul: ChatGPT-based Evasion Attack Against Software Vulnerability Detection

This paper was accepted by USENIX Security '24. The source code and datasets provided are for research use only and not for commercial use.

## Idea
Machine learning-as-a-service has been widely applied in software security. However, Adversarial learning has long been a threat for cybersecurity. In the cybersecurity domain, especiaily within the topic of automatic vulnerability detection with deep learning techniques, there is lack of thorough assessment the security
issues when facing adversarial learning. In this paper, we explored the susceptibility of machine learning/deep neural vulnerability detector to adversarial attacks and developed an effective scheme to generate adversarial code and inject it into vulnerable samples to bypass deep neural detection systems.

Our proposed attackinv strategy consists of two main phases: adversarial data generation (①) and adversarial learning (②). And our work are based on the assumption that the adversary has no knowledge of the target model and cannot manipulate the training data. In the first stage, we train a surrogate model based on BiLSTM with an attention mechanism with knowleddge distaillation to approximate the target model. We identify significant non-vulnerable samples using SVM. Then, we retrieve the averaged attention scores from the attention layer to identify the key features that contribute significantly to the prediction. We further utilize chatGPT to generate adversarial data. In the second phase, we utilize a fuzzy genetic algorithm to select the best seed data, which is added to the vulnerable test case, aiming to bypass the machine learning-based software vulnerability detection system.

## About this repository

The "model" folder contains the Python code for:  
&emsp;&emsp;1. Loading the targte model -- ori_model.py & ori_model_run.py  
&emsp;&emsp;2. Train the surrogate model -- surrogate_model.py & surrogate_train.py  
&emsp;&emsp;3. Obtain the support vectors -- function get_support_vector_idx (in ori_model_run.py)  
&emsp;&emsp;4. Get the key tokens from the support vectors -- key_token_capture.py

To fine-tune the target model (in this case, CodeBERT works as an example.)via the following command:
```
python ori_model_run.py\
  --output_dir=./saved_newbap_models/model\
  --model_type=roberta \
  --tokenizer_name=microsoft/codebert-base \
  --model_name_or_path=microsoft/codebert-base \
  --do_train \
  --train_data_file=./example_train.json \
  --eval_data_file=./example_test.json \
  --test_data_file=./example_test.json \
  --epoch 10 \
  --block_size 400 \
  --train_batch_size 16 \
  --eval_batch_size 32 \
  --learning_rate 2e-5 \
  --max_grad_norm 1.0 \
  --evaluate_during_training \
  --seed 123456 2>&1 | tee train.log
```  
To train the surrogate model with knowledge distillation, please run the follwoing commands:
```
python surrogate_train.py
```
To evaluate the performance of the surrogate model, run the following commands:
```
python surrogate_test.py
```
The "data" folder conatins the data samples used in this paper to validate the findings, where "xxxxx_train.json" is the train dataset and "xxxxx_test.json" works as the test dataset. And the “xxxxx_test_ADV.json” is the testcase set with adversarial code snippets (with code snippet size of 1).  

The "code" folder contains two functional modules:  
&emsp;&emsp;1. Generate adversarial code snippets with chatGPT. -- adversarial_code_generation.py  
&emsp;&emsp;2. Use FGA to select the seed adversarial samples. -- fga_selection.py  


Remark: The data file suffixed with ‘train’ is used for training the model, and the file suffixed with ‘adv’ is used as the adversarial file.
Python version: Python3.7


