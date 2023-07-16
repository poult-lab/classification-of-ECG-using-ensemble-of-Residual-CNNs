#### 2023/07/04

```
python train_model.py ../../python-classifier-2021-main/training_data/cpsc_2018/g1 model
```

There is a bug when the program is executed.

##### Bug information: 

```
Traceback (most recent call last):
  File "train_model.py", line 15, in <module>
    training_code(data_directory, model_directory) ### Implement this function!
  File "/media/jiang/ECG/physionet.org/files/challenge-2021/1.0.3/training/ISIBrnoAIMT_from_challenge/cinc2021_with_attention/team_code.py", line 548, in training_code
    _training_code(data_directory, model_directory, str(0))
  File "/media/jiang/ECG/physionet.org/files/challenge-2021/1.0.3/training/ISIBrnoAIMT_from_challenge/cinc2021_with_attention/team_code.py", line 668, in _training_code
    train_auprc = train_part(model, train, lossBCE, opt)
  File "/media/jiang/ECG/physionet.org/files/challenge-2021/1.0.3/training/ISIBrnoAIMT_from_challenge/cinc2021_with_attention/team_code.py", line 541, in train_part
    auprc = average_precision_score(y_true=targets, y_score=outputs)
  File "/home/jiang/anaconda3/envs/opencv/lib/python3.8/site-packages/sklearn/metrics/_ranking.py", line 232, in average_precision_score
    return _average_binary_score(
  File "/home/jiang/anaconda3/envs/opencv/lib/python3.8/site-packages/sklearn/metrics/_base.py", line 79, in _average_binary_score
    y_score = check_array(y_score)
  File "/home/jiang/anaconda3/envs/opencv/lib/python3.8/site-packages/sklearn/utils/validation.py", line 800, in check_array
    _assert_all_finite(array, allow_nan=force_all_finite == "allow-nan")
  File "/home/jiang/anaconda3/envs/opencv/lib/python3.8/site-packages/sklearn/utils/validation.py", line 114, in _assert_all_finite
    raise ValueError(
ValueError: Input contains NaN, infinity or a value too large for dtype('float32').

```

The output contains a lot of NaN value in terms of Traceback.

The known condition: the shape of output: (799, 26).

The number of non-nan shape can be obtained through the below code.

```python
import numpy as np

print("This is the amount of non-nan: ",np.count_nonzero(np.isnan(outputs)))     
print("This is the outputs:",outputs.shape, outputs)
```

output:

```
This is the amount of non-nan:  17446
This is the outputs: (799, 26) [[0.5321707  0.43235782 0.4715055  ... 0.45695052 0.4081965  0.48694718]
 [0.47317877 0.4010562  0.46544215 ... 0.4037611  0.47383404 0.45108387]
 [0.54693353 0.4313376  0.4695851  ... 0.4619308  0.4680299  0.5045725 ]
 ...
 [       nan        nan        nan ...        nan        nan        nan]
 [       nan        nan        nan ...        nan        nan        nan]
 [       nan        nan        nan ...        nan        nan        nan]]

```

According to the output,  this is the amount of non-nan: **(671, 26)**. So the amount of NaN: **(128, 26)**

Whatever the dataset is changed the amounts of non-NaN and NaN are not changed. 
The **target**  and **processing of data** both need to be checked in terms of above description. 



The output of target is weird,  

```
This is the targets: (799, 26) 
[[0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0.
  0. 0.]
 [0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0.
  0. 0.]
 [0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0.
  0. 0.]
 [0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0.
  0. 0.]
 [0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0.
  0. 0.]
 [0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0. 0.
  0. 0.]
  ......

```



I found two bugs from this code until now 

1.  when we make the list of  tmp['dx'], the code was wrong which is provide by official website. 

   ```python
   below is wrong code:
   # Get labels from header.
   def get_labels(header):
       labels = list()
       for l in header.split('\n'):
           if l.startswith('#Dx'):
               try:
                   entries = l.split(': ')[1].split(',')
                   for entry in entries:
                       labels.append(entry.strip())
               except:
                   pass
       return labels
      
   # the right code should be like below:
   # Get labels from header.
   def get_labels(header):
       labels = list()
       for l in header.split('\n'):
           if l.startswith('# Dx'):
               try:
                   entries = l.split(': ')[1].split(',')
                   for entry in entries:
                       labels.append(entry.strip())
               except:
                   pass
       return labels
   ```

   

2. we have to download all dataset which are contain 26 categories, otherwise the bug will appear bug. 
