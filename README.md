# Hemodialysis_multitask_learning

# A.	多任務學習方法(Multi-Task Learning)
![alt text](https://github.com/qqq951009/Hemodialysis_multitask_learning/blob/master/image_folder/multitask%20learning%20architecture.png)
###  &emsp; 多任務學習(Multi-Task Learning) 方法如圖一所示，主要是透過共享一部分的網路結構(Shared Layer)，並輸出多個節點(Task - Specific Layer)，每一個節點即代表了一項任務。和常見的單任務模型的區別在於，多任務學習僅用一個模型就能夠處理多個任務，如此一來可以減少使用的模型參數數量，能夠有效的減少訓練成本。而在本計畫的情境當中，每一個任務就代表了欲調整的透析器參數。而在訓練開始前除了做資料的清洗之外也要進行資料的篩選，由於要讓模型學習到足夠透析的參數調整方法，我們要選取kt/v大於1.2的病人狀態資料來做訓練 (kt/v>1.2 才有足夠透析)，我們將每一筆病人狀態資料輸入到模型中做訓練並使用對應的透析參數資料來當作是預測目標。
### &emsp; 在多任務學習當中，如何處理不同任務的Loss值是一大問題，最簡單的方法是將各任務的Loss值相加，不過這種方法將會產生幾個問題像是 (1)每個任務的訓練難度不同，導致收斂速度不同, (2)各任務的Loss值的量級不同，舉例來說目前類別型任務採用的是BinaryCrossEntropy Loss function，連續型任務採用的是MeanSquareError Loss function，而使用MeanSquareError Loss function 會產生較大的 Loss值 。上述這些問題都會導致最後模型在訓練的時候難以收斂，為了解決這個問題，我們目前暫時採取了兩個方法 : (1) GradNorm 和(2) Uncertainty to weight losses來處理上述Loss值的問題。GradNorm方法是一種透過將梯度做標準化的方式，以平衡每一個任務的Loss值，使得各任務的能夠以相似的速度學習。而Uncertainty to weight losses方法是基於任務的不確定性去給予各任務不同的權重，希望給予確定性高預測、結果可靠的任務較高的加權，反之給予較低的加權。

