# pytextclassifier

[![PyPI version](https://badge.fury.io/py/pytextclassifier.svg)](https://badge.fury.io/py/pytextclassifier)
[![Contributions welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg)](CONTRIBUTING.md)
[![GitHub contributors](https://img.shields.io/github/contributors/shibing624/pytextclassifier.svg)](https://github.com/shibing624/pytextclassifier/graphs/contributors)
[![License Apache 2.0](https://img.shields.io/badge/license-Apache%202.0-blue.svg)](LICENSE)
[![python_vesion](https://img.shields.io/badge/Python-3.5%2B-green.svg)](requirements.txt)
[![GitHub issues](https://img.shields.io/github/issues/shibing624/pytextclassifier.svg)](https://github.com/shibing624/pytextclassifier/issues)
[![Wechat Group](http://vlog.sfyc.ltd/wechat_everyday/wxgroup_logo.png?imageView2/0/w/60/h/20)](#Contact)


pytextclassifier, Python Text Classifier. It can be applied to the fields of sentiment polarity analysis, text risk classification and so on,
and it supports multiple classification algorithms and clustering algorithms.

文本分类器，提供多种文本分类和聚类算法，支持文本极性情感分类，文本风险类型分类等文本分类和聚类应用。兼容python2.7和python3。


**Guide**

- [Feature](#Feature)
- [Install](#install)
- [Usage](#usage)
- [Dataset](#Dataset)
- [Contact](#Contact)
- [Cite](#Cite)
- [Reference](#reference)

# Feature

**pytextclassifier** is a python Open Source Toolkit for text classification. The goal is to implement
text analysis algorithm, so as to achieve the use in the production environment.

**pytextclassifier** has the characteristics
of clear algorithm, high performance and customizable corpus.

Functions：
### Classifier
  - [x] LogisticRegression
  - [x] Random Forest
  - [x] Decision Tree
  - [x] K-Nearest Neighbours
  - [x] Naive bayes
  - [x] Xgboost
  - [x] Support Vector Machine(SVM)
  - [x] Xgboost
  - [x] Xgboost_lr
  - [x] MLP
  - [x] Ensemble
  - [x] Stack
  - [x] TextCNN
  - [x] TextRNN
  - [x] Fasttext

### Evaluate
  - [x] Precision
  - [x] Recall
  - [x] F1

  
### Test
  - [x] Chi-square test
  
### Cluster
  - [x] MiniBatchKmeans

While providing rich functions, **pytextclassifier** internal modules adhere to low coupling, model adherence to inert loading, dictionary publication, and easy to use.

# Install

- Requirements and Installation


```
pip3 install pytextclassifier
```

or

```
git clone https://github.com/shibing624/pytextclassifier.git
cd pytextclassifier
python3 setup.py install
```


# Usage
### Text Classifier

- English Text Classifier

Including model training, saving, predict, test, for example [base_demo.py](examples/base_demo.py):


```python
import sys

sys.path.append('..')
from pytextclassifier import TextClassifier

if __name__ == '__main__':
    m = TextClassifier()
    # model_name is choose classifier, support lr, random_forest, xgboost, svm, mlp, ensemble, stack
    print(m)
    data = [
        ('education', 'Student debt to cost Britain billions within decades'),
        ('education', 'Chinese education for TV experiment'),
        ('sports', 'Middle East and Asia boost investment in top level sports'),
        ('sports', 'Summit Series look launches HBO Canada sports doc series: Mudhar')
    ]
    m.train(data)

    r = m.predict(['Abbott government spends $8 million on higher education media blitz',
                   'Middle East and Asia boost investment in top level sports'])
    print(r)
    m.save()
    del m

    new_m = TextClassifier()
    new_m.load()
    predict_label_prob = new_m.predict_proba(['Abbott government spends $8 million on higher education media blitz'])
    print(predict_label_prob)  # [[0.53337174 0.46662826]]
    print('classes_: ', new_m.model.classes_)  # the classes ordered as prob

    predict_label = new_m.predict(['Abbott government spends $8 million on higher education media blitz',
                                   'Middle East and Asia boost investment in top level sports'])
    print(predict_label)  # ['education', 'sports']

    test_data = [
        ('education', 'Abbott government spends $8 million on higher education media blitz'),
        ('sports', 'Middle East and Asia boost investment in top level sports'),
    ]
    acc_score = new_m.test(test_data)
    print(acc_score)  # 1.0
```

output:

```
TextClassifier instance (LogisticRegression(fit_intercept=False), <pytextclassifier.utils.tokenizer.Tokenizer object at 0x7fde504682b0>, TfidfVectorizer(ngram_range=(1, 2)))
['education' 'sports']
[[0.53782393 0.46217607]]
classes_:  ['education' 'sports']
['education' 'sports']
classify_report : 
               precision    recall  f1-score   support

   education       1.00      1.00      1.00         1
      sports       1.00      1.00      1.00         1

    accuracy                           1.00         2
   macro avg       1.00      1.00      1.00         2
weighted avg       1.00      1.00      1.00         2

confusion_matrix : 
 [[1 0]
 [0 1]]
acc_for_each_class : 
 [1. 1.]
average_accuracy: 1.0000
overall_accuracy: 1.0000
accuracy_score: 1.0000
1.0
```

- Chinese Text Classifier

Text classification compatible with Chinese and English corpora, for example [chinese_text_demo.py](examples/chinese_text_demo.py)

```python
import sys

sys.path.append('..')
from pytextclassifier import TextClassifier

if __name__ == '__main__':
    m = TextClassifier()
    data = [
        ('education', '名师指导托福语法技巧：名词的复数形式'),
        ('education', '中国高考成绩海外认可 是“狼来了”吗？'),
        ('sports', '图文：法网孟菲尔斯苦战进16强 孟菲尔斯怒吼'),
        ('sports', '四川丹棱举行全国长距登山挑战赛 近万人参与'),
        ('sports', '米兰客场8战不败国米10年连胜')
    ]
    m.train(data)

    r = m.predict(['福建春季公务员考试报名18日截止 2月6日考试',
                   '意甲首轮补赛交战记录:米兰客场8战不败国米10年连胜'])
    print(r)
    m.save()
    print(m)
    del m

    new_m = TextClassifier()
    new_m.load()
    predict_label_prob = new_m.predict_proba(['福建春季公务员考试报名18日截止 2月6日考试'])
    print(predict_label_prob)  # [[0.53337174 0.46662826]]
    print('classes_: ', new_m.model.classes_)  # the classes ordered as prob

    predict_label = new_m.predict(['福建春季公务员考试报名18日截止 2月6日考试',
                                   '意甲首轮补赛交战记录:米兰客场8战不败国米10年连胜'])
    print(predict_label)  # ['education', 'sports']

    test_data = [
        ('education', '福建春季公务员考试报名18日截止 2月6日考试'),
        ('sports', '意甲首轮补赛交战记录:米兰客场8战不败国米10年连胜'),
    ]
    acc_score = new_m.test(test_data)
    print(acc_score)  # 1.0
```

output:

```
['education' 'sports']
TextClassifier instance (LogisticRegression(fit_intercept=False), <pytextclassifier.utils.tokenizer.Tokenizer object at 0x7fbac86632b0>, TfidfVectorizer(ngram_range=(1, 2)))
[[0.5 0.5]]
classes_:  ['education' 'sports']
['education' 'sports']
classify_report : 
               precision    recall  f1-score   support

   education       1.00      1.00      1.00         1
      sports       1.00      1.00      1.00         1

    accuracy                           1.00         2
   macro avg       1.00      1.00      1.00         2
weighted avg       1.00      1.00      1.00         2

confusion_matrix : 
 [[1 0]
 [0 1]]
acc_for_each_class : 
 [1. 1.]
average_accuracy: 1.0000
overall_accuracy: 1.0000
accuracy_score: 1.0000
1.0
```

- Visual Feature Importance

Show feature weights of model, and prediction word weight, for example [visual_feature_importance.ipynb](examples/visual_feature_importance.ipynb)
```python
import sys

sys.path.append('..')
from pytextclassifier import TextClassifier
import jieba

tc = TextClassifier()
data = [
    ('education', '名师指导托福语法技巧：名词的复数形式'),
    ('education', '中国高考成绩海外认可 是“狼来了”吗？'),
    ('sports', '图文：法网孟菲尔斯苦战进16强 孟菲尔斯怒吼'),
    ('sports', '四川丹棱举行全国长距登山挑战赛 近万人参与'),
    ('sports', '米兰客场8战不败国米10年连胜')
]
tc.train(data)
import eli5
infer_data = ['高考指导托福语法技巧国际认可',
              '意甲首轮补赛交战记录:米兰客场8战不败国米10年连胜']
eli5.show_weights(tc.model, vec=tc.vectorizer)
seg_infer_data = [' '.join(jieba.lcut(i)) for i in infer_data]
eli5.show_prediction(tc.model, seg_infer_data[0], vec=tc.vectorizer)
```

output:

![img.png](docs/img.png)

### Text Cluster


Text clustering, for example [cluster_demo.py](examples/cluster_demo.py)
```python
import sys

sys.path.append('..')
from pytextclassifier.textcluster import TextCluster

m = TextCluster(n_clusters=2)
print(m)
data = [
    'Student debt to cost Britain billions within decades',
    'Chinese education for TV experiment',
    'Abbott government spends $8 million on higher education',
    'Middle East and Asia boost investment in top level sports',
    'Summit Series look launches HBO Canada sports doc series: Mudhar'
]
X_vec, labels = m.train(data)
r = m.predict(['Abbott government spends $8 million on higher education media blitz',
               'Middle East and Asia boost investment in top level sports'])
print(r)
m.show_clusters(X_vec, labels, image_file='cluster.png')
m.save()
del m

new_m = TextCluster(n_clusters=2)
new_m.load()
r = new_m.predict(['Abbott government spends $8 million on higher education media blitz',
                   'Middle East and Asia boost investment in top level sports'])
print(r)
```

output:

```
TextCluster instance (MiniBatchKMeans(n_clusters=2, n_init=10), <pytextclassifier.utils.tokenizer.Tokenizer object at 0x7f80bd4682b0>, TfidfVectorizer(ngram_range=(1, 2)))
[1 0]
[1 0]
```
clustering plot image:

![cluster_image](docs/cluster_train_seg_samples.png)

### Train your Text Classification Deep Model

1. Preprocess with segment(optional)
```
cd pytextclassifier
python3 preprocess.py
```

2. Train model

you can change model with edit `config.py` and train model.
```
python3 train.py
```

3. Predict with test data
```
python3 infer.py
```


# Contact

- Issue(建议)：[![GitHub issues](https://img.shields.io/github/issues/shibing624/pytextclassifier.svg)](https://github.com/shibing624/pytextclassifier/issues)
- 邮件我：xuming: xuming624@qq.com
- 微信我：加我*微信号：xuming624*, 进Python-NLP交流群，备注：*姓名-公司名-NLP*

<img src="http://42.193.145.218/github_data/nlp_wechatgroup_erweima1.png" width="200" /><img src="http://42.193.145.218/github_data/xm_wechat_erweima.png" width="200" />


# Cite

如果你在研究中使用了pytextclassifier，请按如下格式引用：

```latex
@software{pytextclassifier,
  author = {Xu Ming},
  title = {pytextclassifier: A Tool for Text Classifier},
  year = {2021},
  url = {https://github.com/shibing624/pytextclassifier},
}
```

# License


授权协议为 [The Apache License 2.0](LICENSE)，可免费用做商业用途。请在产品说明中附加pytextclassifier的链接和授权协议。


# Contribute
项目代码还很粗糙，如果大家对代码有所改进，欢迎提交回本项目，在提交之前，注意以下两点：

 - 在`tests`添加相应的单元测试
 - 使用`python setup.py test`来运行所有单元测试，确保所有单测都是通过的

之后即可提交PR。


# Reference

- SentimentPolarityAnalysis
