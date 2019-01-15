# BERT_implement
使用BERT模型进行文本分类，相似句子判断，以及中文命名实体识别（序列标注任务）

## 说明

1. 谷歌提供的run_classify.py本身就是针对句子配对与分类的，所以，文本分类任务和句子配对任务对 代码修改不多，只用重写接口，就可以达到先进的效果
1. 序列标注不仅要重写接口，由于中文序列标注数据集格式问题，还要重写读取数据的方式，同时，原本run_classify.py在create_model函数中提供的是提取[CLS]编码的方式用来分类，序列标注要返回最后一层所有隐层值而不是仅返回[cls]的编码
1. 序列标注任务中还要注意当`mode==eval`时，对评价函数的修改

## 使用
### 下载预训练模型

```
wget https://storage.googleapis.com/bert_models/2018_11_03/chinese_L-12_H-768_A-12.zip  
```

将下载好的预训练模型存放在目录中

准备好数据

### 参数说明

参数                      说明

data_dir        训练数据的地址

task_name        processor的名字

vocab_file        字典地址，用默认提供的就可以了，当然也可以自定义

bert_config_file        配置文件

output_dir        模型的输出

### 文本分类

```python
export BERT_BASE_DIR=你的模型保存目录
export DATA_DIR=数据保存目录
export OUTPUT_DIR=结果保存目录

# 使用官方提供的参数进行训练
python BERT_implement.py \
  --task_name=text_classify \
  --do_train=true \
  --do_eval=true \
  --data_dir=$DATA_DIR/ \
  --vocab_file=$BERT_BASE_DIR/vocab.txt \
  --bert_config_file=$BERT_BASE_DIR/bert_config.json \
  --init_checkpoint=$BERT_BASE_DIR/bert_model.ckpt \
  --max_seq_length=128 \
  --train_batch_size=32 \
  --learning_rate=2e-5 \
  --num_train_epochs=3.0 \
  --output_dir=$OUTPUT_DIR
```
#### 结果
!()[https://github.com/fennuDetudou/BERT_implement/blob/master/result/屏幕快照%202019-01-14%20上午7.47.40.png?raw=true]

### 句子配对

```python
export BERT_BASE_DIR=你的模型保存目录
export DATA_DIR=数据保存目录
export OUTPUT_DIR=结果保存目录

# 使用官方提供的参数进行训练
python BERT_implement.py \
  --task_name=pair_sentence \
  --do_train=true \
  --do_eval=true \
  --data_dir=$DATA_DIR/ \
  --vocab_file=$BERT_BASE_DIR/vocab.txt \
  --bert_config_file=$BERT_BASE_DIR/bert_config.json \
  --init_checkpoint=$BERT_BASE_DIR/bert_model.ckpt \
  --max_seq_length=128 \
  --train_batch_size=32 \
  --learning_rate=2e-5 \
  --num_train_epochs=3.0 \
  --output_dir=$OUTPUT_DIR
```

### tips:

1. 最大序列长度（max sequence length）对模型的效果影响比较大。随着最大序列长度增加，效果有所提升，但模型的训练时间也相应增加。当最大序列长度变小后（如截取信息），模型的准确率下降
1.  批次大小（batch size）对模型的效果影响也比较，如从64下降到16后，模型的准确率下降幅度较大。
1. fine-tuning模式下略微提高训练轮次（epoch） ，效果可进一步提高。

### 命名实体识别（序列标注）

```python
export BERT_BASE_DIR=你的模型保存目录
export DATA_DIR=数据保存目录
export OUTPUT_DIR=结果保存目录

# 使用官方提供的参数进行训练
python BERT_implement.py \
  --task_name=ner \
  --do_train=true \
  --do_eval=true \
  --data_dir=$DATA_DIR/ \
  --vocab_file=$BERT_BASE_DIR/vocab.txt \
  --bert_config_file=$BERT_BASE_DIR/bert_config.json \
  --init_checkpoint=$BERT_BASE_DIR/bert_model.ckpt \
  --max_seq_length=128 \
  --train_batch_size=32 \
  --learning_rate=2e-5 \
  --output_dir=$OUTPUT_DIR
```

## 在线预测

在输出文件中可以得到BERT经过fine tunning 后的检查点ckpt,但是可以看到检查点大小与谷歌原本检查点大小相比非常大，是因为权重文件中包含了动量和方差，可以使用提供的脚本进行模型精简

```
python compress_ckpt.py  --input_file YOUR_CKPT  --output_file OUTPUT_FILE

```

## 其他NLP任务

待补充。。。
