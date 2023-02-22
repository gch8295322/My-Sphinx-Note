.. index::
   single: ChatGPT
==========
ChatGPT Tips
==========

论文润色
==========
简单改写：
    - Rewrite and expand on the passage in the style of a college-educated native English speaker. Use simple syntax and idiomatic expressions.


用复杂的句法和词汇改写：
    - Rewrite and expand on the passage in the style of an established scholar. Use complex syntax and advanced vocabulary. Make sure the new version is significantly different from the original.

用某某作家/教授的风格改写：
    - Rewrite the passage in the style of Steven Pinker.

背单词
==========

 `雅思乱序单词表 <https://github.com/fanhongtao/IELTS/blob/master/IELTS%20Word%20List.txt>`_

写一则100词的故事，使用以下单词.
    - Write a story of 100 words using the following words.

接着，把它翻译成中文（速度较慢，且目前水平不如Google/Deep L）:
    - Then, translate the story into English.

在新单词后加上汉语注释:
    - Write a story of 100 words using the following words.  Each time when a word from the following list is used, insert after the word its meaning in Chinese. I will give you an example after EG. Follow the example: 

     rig       /rɪg/        vt. 操纵，垄断  n. 船桅（或船帆等）的装置；成套器械

     input*    /ˈɪnput/     n. 投入，输入；输入的数据  vt. 把……输入计算机
     
     merely*   【ˈmiəli】     ad. 仅仅，只不过



EG:
    - This sentence (句子) is an example.

使用下列单词创作故事，以双语双行形式呈现，每行英语下，附上一行汉语翻译:
    - Write a story of 100 words using the following words.  Make each sentence a separate line and give me its Chinese translation. Adopt a bilingual inter-linear format.



.. math::
    单因素方差分析（One-way ANOVA）用于确定一个因素（即独立变量）是否对一个连续型结果（即因变量）产生显著影响。该方法基于比较因变量在不同水平的因素下的方差大小，来判断该因素是否显著影响因变量。

    以下是单因素方差分析的具体过程和相关公式：

    计算总平方和（Total sum of squares, SST）
    $SST = \sum\limits_{i=1}^{n} \sum\limits_{j=1}^{k} (x_{ij} - \bar{x})^2$

    其中，n是样本总数，k是因素的水平数，$x_{ij}$是第 i 个样本在第 j 个水平下的得分，$\bar{x}$是所有样本得分的均值。

    计算因素组间平方和（Between-group sum of squares, SSB）
    $SSB = \sum\limits_{j=1}^{k} n_j (\bar{x}_j - \bar{x})^2$

    其中，$n_j$是第 j 个水平下的样本数，$\bar{x}_j$是第 j 个水平下的样本得分的平均值。

    计算误差组内平方和（Error sum of squares, SSE）
    $SSE = \sum\limits_{i=1}^{n} \sum\limits_{j=1}^{k} (x_{ij} - \bar{x}_j)^2$

    其中，$\bar{x}_j$是第 j 个水平下的样本得分的平均值。

    计算自由度
    总自由度：$df_{Total} = n - 1$

    因素组间自由度：$df_{Between} = k - 1$

    误差组内自由度：$df_{Error} = df_{Total} - df_{Between}$

    计算因素组间均方（Between-group mean square, MSB）
    $MSB = \dfrac{SSB}{df_{Between}}$

    计算误差组内均方（Error mean square, MSE）
    $MSE = \dfrac{SSE}{df_{Error}}$

    计算 F 比率
    $F = \dfrac{MSB}{MSE}$

    判断显著性
    使用计算得到的 F 比率与显著性水平进行比较，如果 F 比率大于临界值，则可以拒绝原假设，即认为因素对因变量产生显著影响。

    在进行单因素方差分析时，还需要满足方差齐性和正态分布等假设条件。如果这些假设不成立，可能需要使用非参数方法进行分析。
