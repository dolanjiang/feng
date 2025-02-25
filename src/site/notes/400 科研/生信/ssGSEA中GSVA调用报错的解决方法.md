---
{"title":"ssGSEA中GSVA调用报错的解决方法","time":"2024-12-23 周一","tags":null,"dg-publish":true,"permalink":"/400 科研/生信/ssGSEA中GSVA调用报错的解决方法/","dgPassFrontmatter":true,"created":"2024-12-23T16:19:21.692+08:00","updated":"2024-12-23T17:24:59.267+08:00"}
---

# ssGSEA中GSVA调用报错的解决方法
## 问题
expr是表达谱，表达数据矩阵，行是基因，列是样本。
cellMarker是一个包含基因集的列表。

在R中使用GSVA的2.0.2版本
```R
gsva_data <- gsva(expr,cellMarker, method = "ssgsea")
```
会报错：Calling gsva(expr=., gset.idx.list=., method=., ...) is defunct; use a method-specific parameter object (see '?gsva').
## 答案
老版本一步，新版本要两步，第一步要给出方法特定的参数对象。
```R
gsvaPar <- ssgseaParam(exprData = expr, 
                       geneSets = cellMarker,
                       normalize = TRUE)
gsva_data <- gsva(gsvaPar, verbose = FALSE)
```
## 解决过程
### 1. 先百度，发现是版本更新之后不可用的问题。
### 2. 试图下载老版本，无果，且该方法感觉不如改代码一劳永逸，故另寻他法。
### 3. 自己什么代码都不会，先找免费的AI，b站都是广告，一般只能免费用三次左右。找到一个浏览器插件版免费ChatGPT，开始问AI。
```R
#假设expr是表达数据，cellMarker是标记基因的列表
#创建参数对象
gsva_params <- GSVA::gsvaControl()

# 调用gsva函数，使用参数对象
gsva_data <- gsva(expr, gset.idx.list = cellMarker, gset.name.type = "entrez", method = "gsva", control = gsva_params)
```
'gsvaControl' is not an exported object from 'namespace:GSVA'

```R
# 假设 expr 是您的表达矩阵，cellMarker 是一个 list，包含标记基因的集
# 你需要传递的参数可能包括：
#   expr：表达数据矩阵，行是基因，列是样本
#   gset.idx.list：一个包含基因集的列表（在此是cellMarker)
#   method：选择 GSVA 方法

# 使用gsva函数
gsva_data <- gsva(
  expr,                          # 表达数据矩阵
  gset.idx.list = cellMarker,    # 包含基因集的列表
  method = "gsva"                # 使用GSVA方法
)

# 查看结果
head(gsva_data)
```
错误于gsva(expr, gset.idx.list = cellMarker, method = "gsva"): 
  Calling gsva(expr=., gset.idx.list=., method=., ...) is defunct; use a method-specific parameter object (see '?gsva').

```R
# 转换基因集为基因索引
gene_ids <- rownames(expr)
gset.idx.list <- lapply(cellMarker, function(genes) which(gene_ids %in% genes))

# 创建 gsva 参数对象
gsva_params <- gsvaParamFactory()

# 运行 gsva
gsva_result <- gsva(expr, gset.idx.list = gset.idx.list, param = gsva_params)

# 查看结果
head(gsva_result)
```
错误于gsvaParamFactory(): 没有"gsvaParamFactory"这个函数


```R
result <- gsva(exprs, gene_set, method = "gsva", min.sz = 1, max.sz = 1000,   
                mx.diff = TRUE, verbose = TRUE)  
```
错误于gsva(expr, cellMarker, method = "ssgsea", min.sz = 1, max.sz = 1000, : 
  Calling gsva(expr=., gset.idx.list=., method=., ...) is defunct; use a method-specific parameter object (see '?gsva').

```R
# 创建一个方法特定的参数对象  
gsva_params <- gsvaParameter(method = "ssgsea", min.sz = 1, max.sz = 1000)  

# 在分析前创建 gset.idx.list，这里作为示例，仅展示变量名  
# cellMarker 需为列表格式，可以根据你的数据设置  
# cellMarker <- list("Sample1" = c("GeneA", "GeneB"), "Sample2" = c("GeneC"))  

# 运行 gsva 函数  
result <- gsva(expr, gset.idx.list = cellMarker, params = gsva_params)  
```
错误于gsvaParameter(method = "ssgsea", min.sz = 1, max.sz = 1000): 
  没有"gsvaParameter"这个函数
  
```R
result <- gsva(expr, gset.idx.list = cellMarker, method = "ssgsea", min.sz = 1, max.sz = 1000)
```
错误于gsva(expr, gset.idx.list = cellMarker, method = "ssgsea", min.sz = 1, : 
  Calling gsva(expr=., gset.idx.list=., method=., ...) is defunct; use a method-specific parameter object (see '?gsva').
  
```R
# 创建方法特定的参数对象  
params <- GSVA::GSVAParameter(method = "ssgsea", min.sz = 1, max.sz = 1000)  
# 运行 gsva 函数  
result <- gsva(expr, gset.idx.list = cellMarker, params = params)  
```
params <- GSVA::GSVAParameter(method = "ssgsea", min.sz = 1, max.sz = 1000)  
错误: 'GSVAParameter' is not an exported object from 'namespace:GSVA'
### 4.AI太愚蠢了，绕圈子。继续百度
感谢知乎博主：被炸熟的虾
[(20 封私信 / 22 条消息) 新版本GSVA中使用ssgsea最新代码是啥啊？ - 知乎](https://www.zhihu.com/question/817469142/answer/46542996147)
更新前的分析方法只要一步：
```R
##We can calculate GSVA enrichment scores as follows:
library(GSVA)
gsva.es <- gsva(expr = X, 
                gset.idx.list = gs,
                method = 'gsva',
                kcdf = 'Gaussian',
                verbose = FALSE)
dim(gsva.es)#[1] 100  30
gsva.es[1:5, 1:5]
#              s1          s2          s3           s4           s5
#gs1 -0.163169867 -0.18230994  0.36746012 -0.010434415 -0.026349618
#gs2  0.007954471 -0.05686215 -0.20594532 -0.009643796 -0.057696047
#gs3 -0.161205687  0.24624689  0.09306792 -0.033363937 -0.002946779
#gs4  0.126560368 -0.08559508 -0.26097093 -0.181676230 -0.307544679
#gs5  0.076199136 -0.01887931 -0.02707651 -0.069945789 -0.086908249
```

```R
##First we should build a parameter object for the desired methodology.R
gsvaPar <- gsvaParam(exprData = X, 
                     geneSets = gs,
                     kcdf = "Gaussian")
gsvaPar
##Second, we call the gsva() function with the parameter object as first argument. 
gsva.es <- gsva(gsvaPar, verbose = FALSE)
dim(gsva.es)#[1] 100  30
gsva.es[1:5, 1:5]
#              s1          s2          s3           s4           s5
#gs1 -0.163169867 -0.18230994  0.36746012 -0.010434415 -0.026349618
#gs2  0.007954471 -0.05686215 -0.20594532 -0.009643796 -0.057696047
#gs3 -0.161205687  0.24624689  0.09306792 -0.033363937 -0.002946779
#gs4  0.126560368 -0.08559508 -0.26097093 -0.181676230 -0.307544679
#gs5  0.076199136 -0.01887931 -0.02707651 -0.069945789 -0.086908249
```
照着修改即可解决
```R
gsvaPar <- gsvaParam(exprData = expr, 
                     geneSets = cellMarker,
                     kcdf = "Gaussian")
gsva_data <- gsva(gsvaPar, verbose = FALSE)
```
数据不对
```R
gsvaPar <- gsvaParam(exprData = expr, 
                     geneSets = cellMarker,
                     kcdf = "Gaussian")
gsva_data <- gsva(gsvaPar, verbose = TRUE)
```
还是不对

到这里我发现自己其实没看全，继续看博主网页
```R
library(GSVA)
##First we should build a parameter object for the desired methodology.R
gsvaPar <- ssgseaParam(exprData = as.matrix(uni_matrix), 
                       geneSets = geneset,
                       normalize = TRUE)
##Second, we call the gsva() function with the parameter object as first argument. 
ssGSEA_matrix <- gsva(gsvaPar, verbose = FALSE)
dim(ssGSEA_matrix)#[1] 100  30
ssGSEA_matrix[1:5, 1:5]
###normalize参数用最大值与最小值间的绝对差对ssGSEA分数进行标准化,默认为TRUE
```
修改后：
```R
gsvaPar <- ssgseaParam(exprData = expr, 
                       geneSets = cellMarker,
                       normalize = TRUE)
gsva_data <- gsva(gsvaPar, verbose = FALSE)
```
终于数据完全一致




































































































































































































































































































































































































