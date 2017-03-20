#AUC

##参考

http://alexkong.net/2013/06/introduction-to-auc-and-roc/

http://www.xiutx.cn/archives/170

https://spark.apache.org/docs/latest/mllib-evaluation-metrics.html#binary-classification

##是什么
ROC曲线和AUC是表征二值分类器优劣的有效方法。

ROC曲线的横轴是false negative概率，纵轴是true positive概率

根本上是对每个样例产生一个属于正例的概率值，选取不同的阈值绝对高于某个值就判定样例为正例。不同的阈值会对分类结果产生不同的影响。在ROC曲线上产生不同的点。所以一个分类器可以产生一个ROC曲线，阈值选取的越多，曲线越平滑。

AUC是ROC曲线下的面积，一般都在0.5-1之间。表征随机给出一个正例和一个负例，分类器给出的分数正例高于负例的概率。

##为什么要用它衡量点击率预估质量

1、ctr预估是特殊的回归问题

ctr预估这种回归问题实质上也是特殊的分类问题（soft classification），求解的是分类概率。

CTR预估的目标函数为：```f(x)=P(+1|x)```,特殊之处在于目标函数的值域为```[0,1]```， 而且由于是条件概率，具有如下特性 ```P(y|x)= {f(x) for y=+1 , 1−f(x) for y=−1｝```

如果将ctr预估按照一般的回归问题处理（如使用linear regression)，面临的问题是一般的linear regression的值域范围的是实数域，对于整个实数域的敏感程度是相同的，所以直接使用一般的linear regression来建立ctr预估模型很容易受到noise的影响。以Andrew Ng课程中的例子图所示，增加一个噪音点后，拟合的直线马上偏移。

另外，由于目标函数是条件概率，训练样本中会存在特征x完全相同，y为+1和-1的样本都出现的问题，在linear regression 看来是一个矛盾的问题，而Logistic Regression很好的解决解决了这个问题。

2、测试任意给一个正类样本和一个负类样本，正类样本的score有多大的概率大于负类样本的score。强调的是预测score的偏序关系（rank）。

##如何在PySpark上面计算

```
from __future__ import print_function
import sys
import time
from pyspark import SparkContext
from pyspark.mllib.regression import LabeledPoint
from pyspark.mllib.feature import HashingTF,ChiSqSelector
from pyspark.mllib.classification import LogisticRegressionWithSGD, LogisticRegressionWithLBFGS
from pyspark.mllib.common import callMLlibFunc, inherit_doc
from pyspark.mllib.linalg import Vectors, SparseVector, _convert_to_vector
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pytoolkit import TDWProvider
from itertools import groupby
from pytoolkit import TableDesc
from pytoolkit import TableInfo
from pytoolkit import TDWUtil
import datetime


if __name__ == "__main__":
	sc = SparkContext(appName="zjm_testTrainAUC")

	tdw = TDWProvider(sc, "tdw_bininezhang", "198399", "wx_ux")
	lines = tdw.table(tblName="zjm_minusRedunUniNomal", priParts=pri_parts, subParts=None, use_unicode=False)
	
	parsed = lines.map(lambda l: MLUtils._parse_libsvm_line(l[1]))
	
	parsed.cache()
	numFeatures = parsed.map(lambda x: -1 if x[1].size == 0 else x[1][-1]).reduce(max) + 1
	clickdata = parsed.map(lambda x: LabeledPoint(x[0], Vectors.sparse(numFeatures, x[1], x[2])))
	
    # Split data into training (60%) and test (40%)
	training, test = clickdata.randomSplit([0.6, 0.4], seed=11)       
    
	training.cache()

	# Run training algorithm to build the model
	model = LogisticRegressionWithLBFGS.train(training)

	# Compute raw scores on the test set
	predictionAndLabels = test.map(lambda lp: (float(model.predict(lp.features)), lp.label))

	# Instantiate metrics object
	metrics = BinaryClassificationMetrics(predictionAndLabels)

	# Area under precision-recall curve
	print("Area under PR = %s" % metrics.areaUnderPR)

	# Area under ROC curve
	print("Area under ROC = %s" % metrics.areaUnderROC)

	sc.stop()

```


