---
title: "回帰分析（単回帰）"
author: "東京国際大学 データサイエンス教育研究所 竹田 恒"
date: "2024-06-06"
output:
  html_document: 
    toc: true
    toc_float: false
    number_sections: true
    df_print: paged
    code_folding: show
    keep_md: true
---



------------------------------------------------------------------------

【注意】\
RStudioのViewerは一部LaTeXを使った数式HTML表示（MathJax）に対応していない。 
MathJaxのエラー「<font color="red">*Math Processing Error*</font>」がでた場合は，knitで出力されたHTMLファイルをChromeなどの**通常のウェブブラウザ**でみる。

# データ


``` r
x <- 1:30
n <- length(x)

b0 <- 20
b1 <- 1.2

set.seed(2)
e <- rnorm(n, mean = 0, sd = 5)

y <- b0 + b1 * x + e

ybar <- mean(y)

# Test data
#x <- c(1, 2, 3, 4, 5)
#y <- c(2, 2, 4, 3, 5)

d <- data.frame(x, y)
d
```

<div data-pagedtable="false">
  <script data-pagedtable-source type="application/json">
{"columns":[{"label":["x"],"name":[1],"type":["int"],"align":["right"]},{"label":["y"],"name":[2],"type":["dbl"],"align":["right"]}],"data":[{"1":"1","2":"16.71543"},{"1":"2","2":"23.32425"},{"1":"3","2":"31.53923"},{"1":"4","2":"19.14812"},{"1":"5","2":"25.59874"},{"1":"6","2":"27.86210"},{"1":"7","2":"31.93977"},{"1":"8","2":"28.40151"},{"1":"9","2":"40.72237"},{"1":"10","2":"31.30606"},{"1":"11","2":"35.28825"},{"1":"12","2":"39.30876"},{"1":"13","2":"33.63652"},{"1":"14","2":"31.60166"},{"1":"15","2":"46.91114"},{"1":"16","2":"27.64465"},{"1":"17","2":"44.79302"},{"1":"18","2":"41.77903"},{"1":"19","2":"47.86414"},{"1":"20","2":"46.16133"},{"1":"21","2":"55.65410"},{"1":"22","2":"40.40037"},{"1":"23","2":"55.54819"},{"1":"24","2":"58.57326"},{"1":"25","2":"50.02469"},{"1":"26","2":"38.94147"},{"1":"27","2":"54.78619"},{"1":"28","2":"50.61721"},{"1":"29","2":"58.76102"},{"1":"30","2":"57.44818"}],"options":{"columns":{"min":{},"max":[10]},"rows":{"min":[10],"max":[10]},"pages":{}}}
  </script>
</div>


``` r
# カラーパレット
# [RGB_Color] https://www.rapidtables.com/web/color/RGB_Color.html
COL <- c(rgb(255,   0,   0,  255, max = 255), # 赤
         rgb(  0,   0, 255,  255, max = 255), # 青
         rgb(  0, 155,   0,  255, max = 255)) # 緑
```

## 散布図


``` r
matplot(x, y, pch = 1, col = COL[1])
grid()
```

![](simple_regression_analysis_files/figure-html/unnamed-chunk-3-1.png)<!-- -->

# 単回帰モデル

$$
\begin{align}
Y_i=&\beta_0 + \beta_1 x_i + \epsilon_i，
\epsilon_i \sim \mathbf{N}(0, \sigma^2)\\
&ここで，\\
&Y_i： 目的変数（確率変数）\\
&x_i： 説明変数（定数）\\
&\beta_0： 切片を表す母回帰係数（定数）\\
&\beta_1： 傾きを表す母回帰係数（定数）\\
&\epsilon_i：誤差項（確率変数）\\
&\sigma^2：誤差分散（定数）
\end{align}
$$

回帰係数$\beta_0, \beta_1$の推定値$b_0, b_1$： $$
\begin{align}
  b_0 &= 20\\
  b_1 &= 1.2
\end{align}
$$

## 回帰係数の推定


``` r
fit <- lm(y ~ x, data = d)
summary(fit)
```

```
## 
## Call:
## lm(formula = y ~ x, data = d)
## 
## Residuals:
##      Min       1Q   Median       3Q      Max 
## -13.5989  -2.8452   0.0335   3.6787   9.2076 
## 
## Coefficients:
##             Estimate Std. Error t value Pr(>|t|)    
## (Intercept)  20.8526     2.2356   9.327 4.38e-10 ***
## x             1.2188     0.1259   9.678 1.97e-10 ***
## ---
## Signif. codes:  0 '***' 0.001 '**' 0.01 '*' 0.05 '.' 0.1 ' ' 1
## 
## Residual standard error: 5.97 on 28 degrees of freedom
## Multiple R-squared:  0.7699,	Adjusted R-squared:  0.7616 
## F-statistic: 93.66 on 1 and 28 DF,  p-value: 1.972e-10
```

## グラフ


``` r
matplot(x, y, pch = 1, col = COL[1], main = '主タイトル')
grid()
matlines(x, fit$fitted, col = COL[2])

library(latex2exp)
legend('topleft', lty = c(NA, 1), pch = c(1, NA), col = COL, 
       legend = c('Data', TeX('$\\hat{y}_i = b_0 + b_1 x_i $')))
```

![](simple_regression_analysis_files/figure-html/unnamed-chunk-5-1.png)<!-- -->

## インタラクティブグラフ


``` r
library(plotly)

plot_ly() |>
  add_trace(x = x, y = y,          mode = 'markers', name = 'Data') |>
  add_trace(x = x, y = fit$fitted, mode = 'lines',   name = '$\\hat{y}_i = b_0 + b_1 x_i $') |>
  layout(font  = list(size = 11, color = 'blue', family = 'UD Digi Kyokasho NK-R'),
         title = '主タイトル',
         xaxis = list(title = 'x軸ラベル［単位］'),
         yaxis = list(title = 'y軸ラベル［単位］')) |>
  config(mathjax = 'cdn')
```

```{=html}
<div class="plotly html-widget html-fill-item" id="htmlwidget-201107a3ecff5d24fbe1" style="width:672px;height:480px;"></div>
<script type="application/json" data-for="htmlwidget-201107a3ecff5d24fbe1">{"x":{"visdat":{"2a1ea479b0bbf":["function () ","plotlyVisDat"]},"cur_data":"2a1ea479b0bbf","attrs":{"2a1ea479b0bbf":{"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"x":[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30],"y":[16.715427266875093,23.324245923233711,31.539226656044118,19.148121628768575,25.598741217245053,27.862101421905471,31.939773646358667,28.4015098791408,40.722369683264631,31.306064939401676,35.288253753962778,39.308763887318307,33.636523222480939,31.601655115255429,46.911144801542875,27.644654576974169,44.793022904606318,41.779033590076125,47.864143460635411,46.161325772698085,55.654096026245732,40.40037090178064,55.548191001450348,58.573258211116269,50.024688884140716,38.941468060769374,54.786186513068081,50.617209156842982,58.761016351498242,57.448183550886739],"mode":"markers","name":"Data","inherit":true},"2a1ea479b0bbf.1":{"alpha_stroke":1,"sizes":[10,100],"spans":[1,20],"x":[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30],"y":[22.071321981208932,23.290083159529217,24.508844337849482,25.727605516169756,26.946366694490031,28.165127872810306,29.383889051130577,30.602650229450852,31.821411407771127,33.040172586091401,34.25893376441168,35.477694942731951,36.696456121052222,37.9152172993725,39.133978477692771,40.35273965601305,41.571500834333321,42.790262012653599,44.00902319097387,45.227784369294149,46.44654554761442,47.665306725934698,48.88406790425497,50.102829082575241,51.321590260895519,52.54035143921579,53.759112617536069,54.97787379585634,56.196634974176618,57.415396152496889],"mode":"lines","name":"$\\hat{y}_i = b_0 + b_1 x_i $","inherit":true}},"layout":{"margin":{"b":40,"l":60,"t":25,"r":10},"font":{"size":11,"color":"blue","family":"UD Digi Kyokasho NK-R"},"title":"主タイトル","xaxis":{"domain":[0,1],"automargin":true,"title":"x軸ラベル［単位］"},"yaxis":{"domain":[0,1],"automargin":true,"title":"y軸ラベル［単位］"},"hovermode":"closest","showlegend":true},"source":"A","config":{"modeBarButtonsToAdd":["hoverclosest","hovercompare"],"showSendToCloud":false},"data":[{"x":[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30],"y":[16.715427266875093,23.324245923233711,31.539226656044118,19.148121628768575,25.598741217245053,27.862101421905471,31.939773646358667,28.4015098791408,40.722369683264631,31.306064939401676,35.288253753962778,39.308763887318307,33.636523222480939,31.601655115255429,46.911144801542875,27.644654576974169,44.793022904606318,41.779033590076125,47.864143460635411,46.161325772698085,55.654096026245732,40.40037090178064,55.548191001450348,58.573258211116269,50.024688884140716,38.941468060769374,54.786186513068081,50.617209156842982,58.761016351498242,57.448183550886739],"mode":"markers","name":"Data","type":"scatter","marker":{"color":"rgba(31,119,180,1)","line":{"color":"rgba(31,119,180,1)"}},"error_y":{"color":"rgba(31,119,180,1)"},"error_x":{"color":"rgba(31,119,180,1)"},"line":{"color":"rgba(31,119,180,1)"},"xaxis":"x","yaxis":"y","frame":null},{"x":[1,2,3,4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22,23,24,25,26,27,28,29,30],"y":[22.071321981208932,23.290083159529217,24.508844337849482,25.727605516169756,26.946366694490031,28.165127872810306,29.383889051130577,30.602650229450852,31.821411407771127,33.040172586091401,34.25893376441168,35.477694942731951,36.696456121052222,37.9152172993725,39.133978477692771,40.35273965601305,41.571500834333321,42.790262012653599,44.00902319097387,45.227784369294149,46.44654554761442,47.665306725934698,48.88406790425497,50.102829082575241,51.321590260895519,52.54035143921579,53.759112617536069,54.97787379585634,56.196634974176618,57.415396152496889],"mode":"lines","name":"$\\hat{y}_i = b_0 + b_1 x_i $","type":"scatter","marker":{"color":"rgba(255,127,14,1)","line":{"color":"rgba(255,127,14,1)"}},"error_y":{"color":"rgba(255,127,14,1)"},"error_x":{"color":"rgba(255,127,14,1)"},"line":{"color":"rgba(255,127,14,1)"},"xaxis":"x","yaxis":"y","frame":null}],"highlight":{"on":"plotly_click","persistent":false,"dynamic":false,"selectize":false,"opacityDim":0.20000000000000001,"selected":{"opacity":1},"debounce":0},"shinyEvents":["plotly_hover","plotly_click","plotly_selected","plotly_relayout","plotly_brushed","plotly_brushing","plotly_clickannotation","plotly_doubleclick","plotly_deselect","plotly_afterplot","plotly_sunburstclick"],"base_url":"https://plot.ly"},"evals":[],"jsHooks":[]}</script>
```

# Python

## データ


``` python
import numpy as np
import pandas as pd

b0 = 20
b1 = 1.2 

np.random.seed(3)
e = np.random.normal(loc = 0, scale = 5, size = 30).reshape(-1,1)

x = np.arange(1, 31, 1).reshape(-1,1)
y = b0 + b1*x + e
```

## 回帰分析


``` python
from sklearn.linear_model import LinearRegression

model_lr = LinearRegression()
model_lr.fit(x, y)
```

```{=html}
<style>#sk-container-id-1 {
  /* Definition of color scheme common for light and dark mode */
  --sklearn-color-text: black;
  --sklearn-color-line: gray;
  /* Definition of color scheme for unfitted estimators */
  --sklearn-color-unfitted-level-0: #fff5e6;
  --sklearn-color-unfitted-level-1: #f6e4d2;
  --sklearn-color-unfitted-level-2: #ffe0b3;
  --sklearn-color-unfitted-level-3: chocolate;
  /* Definition of color scheme for fitted estimators */
  --sklearn-color-fitted-level-0: #f0f8ff;
  --sklearn-color-fitted-level-1: #d4ebff;
  --sklearn-color-fitted-level-2: #b3dbfd;
  --sklearn-color-fitted-level-3: cornflowerblue;

  /* Specific color for light theme */
  --sklearn-color-text-on-default-background: var(--sg-text-color, var(--theme-code-foreground, var(--jp-content-font-color1, black)));
  --sklearn-color-background: var(--sg-background-color, var(--theme-background, var(--jp-layout-color0, white)));
  --sklearn-color-border-box: var(--sg-text-color, var(--theme-code-foreground, var(--jp-content-font-color1, black)));
  --sklearn-color-icon: #696969;

  @media (prefers-color-scheme: dark) {
    /* Redefinition of color scheme for dark theme */
    --sklearn-color-text-on-default-background: var(--sg-text-color, var(--theme-code-foreground, var(--jp-content-font-color1, white)));
    --sklearn-color-background: var(--sg-background-color, var(--theme-background, var(--jp-layout-color0, #111)));
    --sklearn-color-border-box: var(--sg-text-color, var(--theme-code-foreground, var(--jp-content-font-color1, white)));
    --sklearn-color-icon: #878787;
  }
}

#sk-container-id-1 {
  color: var(--sklearn-color-text);
}

#sk-container-id-1 pre {
  padding: 0;
}

#sk-container-id-1 input.sk-hidden--visually {
  border: 0;
  clip: rect(1px 1px 1px 1px);
  clip: rect(1px, 1px, 1px, 1px);
  height: 1px;
  margin: -1px;
  overflow: hidden;
  padding: 0;
  position: absolute;
  width: 1px;
}

#sk-container-id-1 div.sk-dashed-wrapped {
  border: 1px dashed var(--sklearn-color-line);
  margin: 0 0.4em 0.5em 0.4em;
  box-sizing: border-box;
  padding-bottom: 0.4em;
  background-color: var(--sklearn-color-background);
}

#sk-container-id-1 div.sk-container {
  /* jupyter's `normalize.less` sets `[hidden] { display: none; }`
     but bootstrap.min.css set `[hidden] { display: none !important; }`
     so we also need the `!important` here to be able to override the
     default hidden behavior on the sphinx rendered scikit-learn.org.
     See: https://github.com/scikit-learn/scikit-learn/issues/21755 */
  display: inline-block !important;
  position: relative;
}

#sk-container-id-1 div.sk-text-repr-fallback {
  display: none;
}

div.sk-parallel-item,
div.sk-serial,
div.sk-item {
  /* draw centered vertical line to link estimators */
  background-image: linear-gradient(var(--sklearn-color-text-on-default-background), var(--sklearn-color-text-on-default-background));
  background-size: 2px 100%;
  background-repeat: no-repeat;
  background-position: center center;
}

/* Parallel-specific style estimator block */

#sk-container-id-1 div.sk-parallel-item::after {
  content: "";
  width: 100%;
  border-bottom: 2px solid var(--sklearn-color-text-on-default-background);
  flex-grow: 1;
}

#sk-container-id-1 div.sk-parallel {
  display: flex;
  align-items: stretch;
  justify-content: center;
  background-color: var(--sklearn-color-background);
  position: relative;
}

#sk-container-id-1 div.sk-parallel-item {
  display: flex;
  flex-direction: column;
}

#sk-container-id-1 div.sk-parallel-item:first-child::after {
  align-self: flex-end;
  width: 50%;
}

#sk-container-id-1 div.sk-parallel-item:last-child::after {
  align-self: flex-start;
  width: 50%;
}

#sk-container-id-1 div.sk-parallel-item:only-child::after {
  width: 0;
}

/* Serial-specific style estimator block */

#sk-container-id-1 div.sk-serial {
  display: flex;
  flex-direction: column;
  align-items: center;
  background-color: var(--sklearn-color-background);
  padding-right: 1em;
  padding-left: 1em;
}


/* Toggleable style: style used for estimator/Pipeline/ColumnTransformer box that is
clickable and can be expanded/collapsed.
- Pipeline and ColumnTransformer use this feature and define the default style
- Estimators will overwrite some part of the style using the `sk-estimator` class
*/

/* Pipeline and ColumnTransformer style (default) */

#sk-container-id-1 div.sk-toggleable {
  /* Default theme specific background. It is overwritten whether we have a
  specific estimator or a Pipeline/ColumnTransformer */
  background-color: var(--sklearn-color-background);
}

/* Toggleable label */
#sk-container-id-1 label.sk-toggleable__label {
  cursor: pointer;
  display: block;
  width: 100%;
  margin-bottom: 0;
  padding: 0.5em;
  box-sizing: border-box;
  text-align: center;
}

#sk-container-id-1 label.sk-toggleable__label-arrow:before {
  /* Arrow on the left of the label */
  content: "▸";
  float: left;
  margin-right: 0.25em;
  color: var(--sklearn-color-icon);
}

#sk-container-id-1 label.sk-toggleable__label-arrow:hover:before {
  color: var(--sklearn-color-text);
}

/* Toggleable content - dropdown */

#sk-container-id-1 div.sk-toggleable__content {
  max-height: 0;
  max-width: 0;
  overflow: hidden;
  text-align: left;
  /* unfitted */
  background-color: var(--sklearn-color-unfitted-level-0);
}

#sk-container-id-1 div.sk-toggleable__content.fitted {
  /* fitted */
  background-color: var(--sklearn-color-fitted-level-0);
}

#sk-container-id-1 div.sk-toggleable__content pre {
  margin: 0.2em;
  border-radius: 0.25em;
  color: var(--sklearn-color-text);
  /* unfitted */
  background-color: var(--sklearn-color-unfitted-level-0);
}

#sk-container-id-1 div.sk-toggleable__content.fitted pre {
  /* unfitted */
  background-color: var(--sklearn-color-fitted-level-0);
}

#sk-container-id-1 input.sk-toggleable__control:checked~div.sk-toggleable__content {
  /* Expand drop-down */
  max-height: 200px;
  max-width: 100%;
  overflow: auto;
}

#sk-container-id-1 input.sk-toggleable__control:checked~label.sk-toggleable__label-arrow:before {
  content: "▾";
}

/* Pipeline/ColumnTransformer-specific style */

#sk-container-id-1 div.sk-label input.sk-toggleable__control:checked~label.sk-toggleable__label {
  color: var(--sklearn-color-text);
  background-color: var(--sklearn-color-unfitted-level-2);
}

#sk-container-id-1 div.sk-label.fitted input.sk-toggleable__control:checked~label.sk-toggleable__label {
  background-color: var(--sklearn-color-fitted-level-2);
}

/* Estimator-specific style */

/* Colorize estimator box */
#sk-container-id-1 div.sk-estimator input.sk-toggleable__control:checked~label.sk-toggleable__label {
  /* unfitted */
  background-color: var(--sklearn-color-unfitted-level-2);
}

#sk-container-id-1 div.sk-estimator.fitted input.sk-toggleable__control:checked~label.sk-toggleable__label {
  /* fitted */
  background-color: var(--sklearn-color-fitted-level-2);
}

#sk-container-id-1 div.sk-label label.sk-toggleable__label,
#sk-container-id-1 div.sk-label label {
  /* The background is the default theme color */
  color: var(--sklearn-color-text-on-default-background);
}

/* On hover, darken the color of the background */
#sk-container-id-1 div.sk-label:hover label.sk-toggleable__label {
  color: var(--sklearn-color-text);
  background-color: var(--sklearn-color-unfitted-level-2);
}

/* Label box, darken color on hover, fitted */
#sk-container-id-1 div.sk-label.fitted:hover label.sk-toggleable__label.fitted {
  color: var(--sklearn-color-text);
  background-color: var(--sklearn-color-fitted-level-2);
}

/* Estimator label */

#sk-container-id-1 div.sk-label label {
  font-family: monospace;
  font-weight: bold;
  display: inline-block;
  line-height: 1.2em;
}

#sk-container-id-1 div.sk-label-container {
  text-align: center;
}

/* Estimator-specific */
#sk-container-id-1 div.sk-estimator {
  font-family: monospace;
  border: 1px dotted var(--sklearn-color-border-box);
  border-radius: 0.25em;
  box-sizing: border-box;
  margin-bottom: 0.5em;
  /* unfitted */
  background-color: var(--sklearn-color-unfitted-level-0);
}

#sk-container-id-1 div.sk-estimator.fitted {
  /* fitted */
  background-color: var(--sklearn-color-fitted-level-0);
}

/* on hover */
#sk-container-id-1 div.sk-estimator:hover {
  /* unfitted */
  background-color: var(--sklearn-color-unfitted-level-2);
}

#sk-container-id-1 div.sk-estimator.fitted:hover {
  /* fitted */
  background-color: var(--sklearn-color-fitted-level-2);
}

/* Specification for estimator info (e.g. "i" and "?") */

/* Common style for "i" and "?" */

.sk-estimator-doc-link,
a:link.sk-estimator-doc-link,
a:visited.sk-estimator-doc-link {
  float: right;
  font-size: smaller;
  line-height: 1em;
  font-family: monospace;
  background-color: var(--sklearn-color-background);
  border-radius: 1em;
  height: 1em;
  width: 1em;
  text-decoration: none !important;
  margin-left: 1ex;
  /* unfitted */
  border: var(--sklearn-color-unfitted-level-1) 1pt solid;
  color: var(--sklearn-color-unfitted-level-1);
}

.sk-estimator-doc-link.fitted,
a:link.sk-estimator-doc-link.fitted,
a:visited.sk-estimator-doc-link.fitted {
  /* fitted */
  border: var(--sklearn-color-fitted-level-1) 1pt solid;
  color: var(--sklearn-color-fitted-level-1);
}

/* On hover */
div.sk-estimator:hover .sk-estimator-doc-link:hover,
.sk-estimator-doc-link:hover,
div.sk-label-container:hover .sk-estimator-doc-link:hover,
.sk-estimator-doc-link:hover {
  /* unfitted */
  background-color: var(--sklearn-color-unfitted-level-3);
  color: var(--sklearn-color-background);
  text-decoration: none;
}

div.sk-estimator.fitted:hover .sk-estimator-doc-link.fitted:hover,
.sk-estimator-doc-link.fitted:hover,
div.sk-label-container:hover .sk-estimator-doc-link.fitted:hover,
.sk-estimator-doc-link.fitted:hover {
  /* fitted */
  background-color: var(--sklearn-color-fitted-level-3);
  color: var(--sklearn-color-background);
  text-decoration: none;
}

/* Span, style for the box shown on hovering the info icon */
.sk-estimator-doc-link span {
  display: none;
  z-index: 9999;
  position: relative;
  font-weight: normal;
  right: .2ex;
  padding: .5ex;
  margin: .5ex;
  width: min-content;
  min-width: 20ex;
  max-width: 50ex;
  color: var(--sklearn-color-text);
  box-shadow: 2pt 2pt 4pt #999;
  /* unfitted */
  background: var(--sklearn-color-unfitted-level-0);
  border: .5pt solid var(--sklearn-color-unfitted-level-3);
}

.sk-estimator-doc-link.fitted span {
  /* fitted */
  background: var(--sklearn-color-fitted-level-0);
  border: var(--sklearn-color-fitted-level-3);
}

.sk-estimator-doc-link:hover span {
  display: block;
}

/* "?"-specific style due to the `<a>` HTML tag */

#sk-container-id-1 a.estimator_doc_link {
  float: right;
  font-size: 1rem;
  line-height: 1em;
  font-family: monospace;
  background-color: var(--sklearn-color-background);
  border-radius: 1rem;
  height: 1rem;
  width: 1rem;
  text-decoration: none;
  /* unfitted */
  color: var(--sklearn-color-unfitted-level-1);
  border: var(--sklearn-color-unfitted-level-1) 1pt solid;
}

#sk-container-id-1 a.estimator_doc_link.fitted {
  /* fitted */
  border: var(--sklearn-color-fitted-level-1) 1pt solid;
  color: var(--sklearn-color-fitted-level-1);
}

/* On hover */
#sk-container-id-1 a.estimator_doc_link:hover {
  /* unfitted */
  background-color: var(--sklearn-color-unfitted-level-3);
  color: var(--sklearn-color-background);
  text-decoration: none;
}

#sk-container-id-1 a.estimator_doc_link.fitted:hover {
  /* fitted */
  background-color: var(--sklearn-color-fitted-level-3);
}
</style><div id="sk-container-id-1" class="sk-top-container"><div class="sk-text-repr-fallback"><pre>LinearRegression()</pre><b>In a Jupyter environment, please rerun this cell to show the HTML representation or trust the notebook. <br />On GitHub, the HTML representation is unable to render, please try loading this page with nbviewer.org.</b></div><div class="sk-container" hidden><div class="sk-item"><div class="sk-estimator fitted sk-toggleable"><input class="sk-toggleable__control sk-hidden--visually" id="sk-estimator-id-1" type="checkbox" checked><label for="sk-estimator-id-1" class="sk-toggleable__label fitted sk-toggleable__label-arrow fitted">&nbsp;&nbsp;LinearRegression<a class="sk-estimator-doc-link fitted" rel="noreferrer" target="_blank" href="https://scikit-learn.org/1.5/modules/generated/sklearn.linear_model.LinearRegression.html">?<span>Documentation for LinearRegression</span></a><span class="sk-estimator-doc-link fitted">i<span>Fitted</span></span></label><div class="sk-toggleable__content fitted"><pre>LinearRegression()</pre></div> </div></div></div></div>
```

## グラフ


``` python
import matplotlib.pyplot as plt

# ラベル
plt.title('主タイトル')
plt.xlabel('x軸ラベル［単位］')
plt.ylabel('y軸ラベル［単位］')

# 格子線（grid lines）
plt.grid(linestyle = '--', color = (0.9, 0.9, 0.9, 0.25))
plt.plot(x, y, 'o', label = 'Data points')
plt.plot(x, model_lr.predict(x), linestyle = 'solid', 
         label = '$\hat{y}_i = b_0 + b_1 x_i$')

# 凡例（はんれい）
plt.legend(loc = 'lower right')

plt.show()
```

<img src="simple_regression_analysis_files/figure-html/unnamed-chunk-9-1.png" width="672" />

---
