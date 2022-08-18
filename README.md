# Hemodialysis_multitask_learning

# A.	多任務學習方法(Multi-Task Learning)
![alt text](https://github.com/qqq951009/Hemodialysis_multitask_learning/blob/master/image_folder/multitask%20learning%20architecture.png)
###  &emsp; 多任務學習(Multi-Task Learning) 方法如圖一所示，主要是透過共享一部分的網路結構(Shared Layer)，並輸出多個節點(Task - Specific Layer)，每一個節點即代表了一項任務。和常見的單任務模型的區別在於，多任務學習僅用一個模型就能夠處理多個任務，如此一來可以減少使用的模型參數數量，能夠有效的減少訓練成本。而在本計畫的情境當中，每一個任務就代表了欲調整的透析器參數。而在訓練開始前除了做資料的清洗之外也要進行資料的篩選，由於要讓模型學習到足夠透析的參數調整方法，我們要選取kt/v大於1.2的病人狀態資料來做訓練 (kt/v>1.2 才有足夠透析)，我們將每一筆病人狀態資料輸入到模型中做訓練並使用對應的透析參數資料來當作是預測目標。
### &emsp; 在多任務學習當中，如何處理不同任務的Loss值是一大問題，最簡單的方法是將各任務的Loss值相加，不過這種方法將會產生幾個問題像是 (1)每個任務的訓練難度不同，導致收斂速度不同, (2)各任務的Loss值的量級不同，舉例來說目前類別型任務採用的是BinaryCrossEntropy Loss function，連續型任務採用的是MeanSquareError Loss function，而使用MeanSquareError Loss function 會產生較大的 Loss值 。上述這些問題都會導致最後模型在訓練的時候難以收斂，為了解決這個問題，我們目前暫時採取了兩個方法 : (1) GradNorm 和(2) Uncertainty to weight losses來處理上述Loss值的問題。GradNorm方法是一種透過將梯度做標準化的方式，以平衡每一個任務的Loss值，使得各任務的能夠以相似的速度學習。而Uncertainty to weight losses方法是基於任務的不確定性去給予各任務不同的權重，希望給予確定性高預測、結果可靠的任務較高的加權，反之給予較低的加權。

| (layer1): Linear(in_features=36, out_features=64, bias=True)| 
| ----------- | 
| (relu): ReLU()|
| (layer2): Linear(in_features=64, out_features=32, bias=True)|
| (layer3): Linear(in_features=32, out_features=16, bias=True)|
| (branch1): Linear(in_features=16, out_features=4, bias=True) <br/>(branch2): Linear(in_features=16, out_features=3, bias=True) <br/> (branch3): Linear(in_features=16, out_features=10, bias=True)  <br/> (branch4): Linear(in_features=16, out_features=10, bias=True) <br/> (branch5): Linear(in_features=16, out_features=1, bias=True) <br/> (branch6): Linear(in_features=16, out_features=1, bias=True) <br/> (branch7): Linear(in_features=16, out_features=6, bias=True) <br/> (branch8): Linear(in_features=16, out_features=7, bias=True) <br/> (branch9): Linear(in_features=16, out_features=3, bias=True) |

###  &emsp; 我們將清理完的229筆資料輸入到多任務模型裡面做訓練，上表為我們目前的模型架構，可以看到layer1( neural = 64 ), layer2( neural = 32 )和layer3( neural = 16 )都是共享層，而branch1,2,3…則代表了各個任務的分支。Optimizer使用的是Adam並且learning rate設定成0.01。並分別使用GradNorm和Uncertainty to weight losses兩種方法來和Baseline比較模型訓練的Loss值成效 (Baseline 只對各任務的Loss值加總做平均來更新Loss值)。下圖是訓練的結果，其顯示了Uncertainty和GradNorm方法可以下降到比baseline來要來的低的結果。雖然在訓練期間GradNorm先比Uncertainty下降到比較低的Loss 值，然而在epoch = 100的時候Uncertainty( 98.82 )下降到了比GradNorm( 238.29 )還要低的Loss 值。
![alt text](https://github.com/qqq951009/Hemodialysis_multitask_learning/blob/master/image_folder/training%20loss.png)
