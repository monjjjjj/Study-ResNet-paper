# Study-ResNet-paper
## 歷屆ILSVRC 冠軍的圖片物體分辨錯誤率

![image](https://user-images.githubusercontent.com/62006029/191455428-5e62fdc9-30be-4f59-88b1-0a3664c46c86.png)

(reference: https://blog.jaysinha.me/train-your-first-neural-network-for-image-captioning-using-transfer-learning/)

由上圖可以發現，model的層數從2014年的GoogleNet 22層暴增至2015年的ResNet 152層！  
這個結果驗證了，在沒有overfitting的情況下，越深的網路，訓練出來的結果是越好的！  
但單純加深模型的深度，會更容易導致vanishing/exploding gradients問題的發生，會影響模型的收斂！  
下圖為ResNet papaer中的實驗，可以看到網絡的深度越深，training error and testing error不會下降反而提升了！  
<img width="553" alt="截圖 2022-09-21 下午4 37 08" src="https://user-images.githubusercontent.com/62006029/191457031-46943b82-20cd-4617-b5da-530d9cf67471.png">

當深度比較深的網路可以開始收斂的時候，degradation的問題就會出現，model的準確率會開始飽和並且開示快速的退化。  
但這並不是因為overfitting的原因所造成的，是來自越深的網路所疊加出來的error。

上述原因促使了ResNet的出現！

## ResNet
作者想出了一個解法：  
加入的層數為恆等映射（identity mapping），而其他層則是從學習中其他較淺層複製來！

也就是說，當有一個input x，其輸出H(x)若等於x，則暗示不管網絡有多深，其學習出來的結果都不應該比什麼權重都沒有乘來得差！  
例如：一個50層（較深的網路）訓練出來的誤差，不應該比一個20層的網路訓練出來的還高(因為20層網路只是50層網路的一個子集)。  
但這個方法合理的時間內找到更好的準確率，因此作者又提出了另一個作法，稱之為deep residual learning framework（如下圖所示）。

<img width="339" alt="截圖 2022-09-21 下午4 51 35" src="https://user-images.githubusercontent.com/62006029/191460253-e84d8f77-4816-401a-b7dc-8625ff0a2f57.png">

假設今天模型要學習的映射函數為H(x)，如果模型直接近似訓練樣本x，輸出為H(x)，這就是恆等映射。

在ResNet中最關鍵的一步，就是將模型要學習的目標從H(x)換成F(x):=H(x) - x。

（作者假設了優化殘差會比學習恆等映射來得容易。）

我理解的是，只要每一次的殘差學習出來的結果，其殘差值為0的話，就不用去學習恆等映射的函式，只要將算式移項就可以了！

（移項後可得: H(x)=F(x)+x ，而我們原本要學的映射函數H(x)就等價F(x)+x）

## 為何殘差映射是可行的？
假設input x = 2.9, H(x) = 3.0. 
-> 殘差值 = 0.1

如果經過模型第一層所得到的H(x) = 3.0 -> F(x) = 3.0 - 2.9 = 0.1        
   經過模型第二層所得到的H(x) = 3.1 -> F(x) = 3.1 - 2.9 = 0.2
   
一般網路層的誤差變化率會是(3.1–3.0)/3.0=3.33%   
殘差網路層的誤差變化率會是(0.2–0.1)/0.1=100%
    
作者應該是以上述的概念，因此認為優化殘差會比較容易！

