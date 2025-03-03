---
{"title":"WGCNA","time":"2024-12-25 周三","tags":null,"dg-publish":true,"permalink":"/400 科研/生信/WGCNA/","dgPassFrontmatter":true,"created":"2024-12-25T16:34:03.874+08:00","updated":"2024-12-26T12:33:40.281+08:00"}
---

# WGCNA
[WGCNA 挑选软阈值 - 简书](https://www.jianshu.com/p/1804fb6d2ed8)
[(21 封私信 / 24 条消息) 如何理解WGCNA中的最优软阈值？ - 知乎](https://www.zhihu.com/question/561905241?write)

在构建[共表达网络](https://zhida.zhihu.com/search?content_id=168046612&content_type=Article&match_order=2&q=%E5%85%B1%E8%A1%A8%E8%BE%BE%E7%BD%91%E7%BB%9C&zhida_source=entity)之前，还有一步比较关键的步骤——寻找最优软阈值（soft thresholding或power），使构建的网络更符合[无标度拓扑](https://zhida.zhihu.com/search?content_id=168046612&content_type=Article&match_order=1&q=%E6%97%A0%E6%A0%87%E5%BA%A6%E6%8B%93%E6%89%91&zhida_source=entity)结构。这时，我们会看到如下图：确定最优软阈值主要参考左图，即在不同软阈值（x轴）情况下的无标度拟合指数（scale-free fit index，y轴）。其中红线表示主观选择的无标度拟合指数取值，如图为0.9，一般我们选择0.8~0.95之间较为稳妥。从左图看，当无标度拟合指数在0.9时，符合构建[无标度网络](https://zhida.zhihu.com/search?content_id=168046612&content_type=Article&match_order=1&q=%E6%97%A0%E6%A0%87%E5%BA%A6%E7%BD%91%E7%BB%9C&zhida_source=entity)的最小软阈值为6，因此可以选择6作为最优软阈值用于后续分析。右图为不同软阈值情况下的网络连通度。

基于最优软阈值构建共表达网络，将基因划分到不同模块后，可以绘制基因聚类树：
这个图可以分为两部分看：上半部分是基因的[层次聚类](https://zhida.zhihu.com/search?content_id=168046612&content_type=Article&match_order=1&q=%E5%B1%82%E6%AC%A1%E8%81%9A%E7%B1%BB&zhida_source=entity)树状图，下半部分是基因模块，也就是网络模块。上下对应，可以看到距离较近的基因（聚类到同一条分支）被划分到了同一模块。



参数 beta 取值默认是 1 到 30，上述图形的横轴均代表权重参数β，左图纵轴代表对应的网络中 log(k) 与 log(p(k)) 相关系数的平方。相关系数的平方越高，说明该网络越逼近无网路尺度的分布。右图的纵轴代表对应的基因模块中所有基因邻接函数的均值。最佳的 beta 值就是 sft$powerEstimate，已经被保存到变量了，不需要知道具体是什么，后面的代码都用这个即可，在本例子里面是 6。

这里用不同的颜色来代表那些所有的模块，其中灰色默认是无法归类于任何模块的那些基因，如果灰色模块里面的基因太多，那么前期对表达矩阵挑选基因的步骤可能就不太合适。



**Module-trait-relationship****图**

n 先回顾下Module的意义，它本质是高度内连的基因集，模块内是高度相关的基因。在WGCNA分析中把基因聚类成模块后，对每个模块进行了功能富集分析、模块与性状进行关联分析和模块与样本进行关联分析，分别可以去查找基因的功能特征，筛选出表型与性状（比如肿瘤进展、预后良好、转移、xx疾病等）关联较高的的模块和查找样本中特异表达基因。

Module-trait-realtionship是==模块与表型特征关联图谱==，也是WGCNA中我们最关注的结果。横坐标是表型性状（trait），上图中显示有两个表型性状，一个是Control，另一个是COVID-19。纵坐标是对应模块，用每个模块的特征基因（eigengene）来表示这个模块（ps：回顾一下，eigengene代表的是每个模块的第一主成分）。在上图中有10个模块，分别用不同颜色表示。格子代表横坐标的表型性状和纵坐标的每个模块的相关性以及p-value值，黄的越深，越正相关；蓝色越深，越负相关。最右边的图例，代表的是相关性，越接近1黄色越深，代表正相关，越接近-1蓝色越深，代表负相关。根据上图可以看出分析得到的10个模块中，MEpink（r = 0.57，P = 4E−20）、MEtan（r = 0.33，P = 6E−07）与COVID-19的相关性较高。

***


**MM-GS相关性图**

模块与指定性状的相关性我们已经分析得到了，但是一个模块通常有很多基因。我们如何找到最相关的基因呢？WGCNA通过GS和MM给出了一个合理的解决办法。

首先我们将模块中的每个基因与eigengenes进行相关性分析，得到的结果就是module membership（MM）。当结果越接近于0，则我们认为该基因与其所在的模块越不相关。而结果越接近于1或者-1，则我们认为该基因与模块基因高度正或负相关。此外，MM与模块内的连通性是高度相关的，所以高连通性的hub genes倾向于有更高的MM值。

每一个值代表这个基因与模块之间的关系。如果这个值的绝对值接近0，那么这个基因就不是这个模块中的一部分，如果这个值的绝对值接近1，那么这个基因就与这个模块高度相关。**其实这个值与后面[hub基因](https://zhida.zhihu.com/search?content_id=132380152&content_type=Article&match_order=1&q=hub%E5%9F%BA%E5%9B%A0&zhida_source=entity)的选择相关，这里以后再讲。**

模块内的基因，我们不仅想知道它与模块的相关性，还想知道它与模块对应的性状的相关性。于是WGCNA定义了一个新的变量GS (gene significance)，即计算模块内gene 与对应性状的显著性。GS为：基因和表型性状比如体重之间的相关性的绝对值。

MM-GS相关性散点图代表的是MM与GS的相关性，从上图我们可以看到，GS和MM相关性很高（cor=0.79，p&lt;1 × 10−200），这表明与性状高度相关的基因，通常也是该性状对应模块内比较重要的基因。因此当我们选择感兴趣的重要基因时，推荐选取散点图右上角部分的基因。




图中的每一个点代表一个基因，应该有3600个点。[横坐标值](https://zhida.zhihu.com/search?content_id=132380152&content_type=Article&match_order=1&q=%E6%A8%AA%E5%9D%90%E6%A0%87%E5%80%BC&zhida_source=entity)表示基因与模块的相关性，纵坐标值表示基因与表型性状的相关性，这里可以看出与性状高度显著相关的基因往往是与这个性状显著相关的模块中的重要元素。

大家可以去验证一下自己的结果，如果一个性状与模块显著相关，那么这里GS与MM也会显著相关。下图展示了F-48小时性状的GS值与blue模块MM值的相关性，这里可以看出来是高度相关。与模块高度相关的那些点（右上角）同时也是与性状高度相关的基因；而左下角，与模块相关性不高的那些基因同时也是与性状不相关的那些基因，他们的相关性一致，这里才会出现GS与MM值高度相关。

也就是说第一个图是相关的，那么在第二个图里大概率cor（GS和MM的相关性)比较高，点在对角线

































































































































































































































































































































































































































