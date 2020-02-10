# classification-t107368523
電子系在職專班碩二 T107368523 陳建隆

資料集內訓練的照片分為20個角色，總共19548張圖片，測試資料為990張。
資料集的分類如下，資料夾名稱為人物角色的名子，其內為各角色的圖片。<br />
<img src="https://github.com/chen-chien-lung/Deep-Learning-Classification/blob/master/1.png" width="600" height="440"><br />

首先引入需要的程式工具<br />
<img src="https://github.com/MachineLearningNTUT2018/classification-t107368523/blob/master/2.png" width="660" height="260"><br />

其中os.environ['TF_ENABLE_AUTO_MIXED_PRECISION'] = '1'，是為了要讓GPU使用fp16半經度運算，相關的文件顯示此設定會讓訓練的速度加快。<br />

接下來要把訓練集內其中20%資料加入驗證集。<br />
<img src="https://github.com/MachineLearningNTUT2018/classification-t107368523/blob/master/3.png" width="400" height="300"><br />

首先用os.walk把train_dir內的文件名稱(角色名稱)加到directory_list，接下來把validation資料集路徑加上角色名稱並生成角色名稱的資料夾。
再來將train_dir加上角色名稱，來讀取資料夾內的資料，這邊要將train_dir(訓練集)內的資料的20%拷貝到validation(驗證集)內，並刪除在train_dir內被拷貝到驗證集的檔案。<br />

將下來要將圖片(image)和名稱(label)讀取以便後面的訓練操作。<br />
首先建立一個dictionary內存放全部角色名稱。<br />
<img src="https://github.com/MachineLearningNTUT2018/classification-t107368523/blob/master/4.png" width="800" height="100"><br />

接下來依序把照片讀進來，並分成train和label資料。<br />
<img src="https://github.com/MachineLearningNTUT2018/classification-t107368523/blob/master/5.png" width="650" height="580"><br />

在將照片讀進來後，並使用OpenCV來將照片縮小為64x64大小。<br />

因為資料為RGB(0~255)的像素圖片，我們必須先進行Normalization以便後面的訓練。<br />
<img src="https://github.com/MachineLearningNTUT2018/classification-t107368523/blob/master/6.png" width="232" height="72"><br />

接著把label資料做成one hot encoding格式。<br />
<img src="https://github.com/MachineLearningNTUT2018/classification-t107368523/blob/master/7.png" width="418" height="53"><br />

這邊使用到ImageDataGenerator方法，它可以將圖片資料批次的讀進來，另外可以設定對圖片進行的操作，這邊對圖片進行了資料擴增(Data augmentation)。<br />
<img src="https://github.com/MachineLearningNTUT2018/classification-t107368523/blob/master/8.png" width="325" height="200"><br />

這邊使用了兩種方法來訓練資料，一種是自己設計CNN來訓練，另一種是使用Transfer learning的方式導入VGG16來訓練。<br />

首先適用VGG16來訓練，<br />
<img src="https://github.com/MachineLearningNTUT2018/classification-t107368523/blob/master/9.png" width="341" height="232"><br />

下圖是VGG16內的結構，我們把VGG16內最後面的3層重新訓練，其他層都鎖起來保持原本的weight，因為CNN深層的網路是在擷取圖片比較大個特徵(人，橋等等…)，淺層是擷取細微的特徵(線條，斜線等等..)，所以解鎖後面幾層後，讓這幾層來學習Simpson人物的資料。<br />
<img src="https://github.com/MachineLearningNTUT2018/classification-t107368523/blob/master/10.png" width="527" height="789"><br />

下面程式把VGG16加到模型內，後面再加上幾層密集連結層來訓練，因為最後輸出為20個機率值，最高的機率值代表圖片被判斷為哪一個角色。<br />
<img src="https://github.com/MachineLearningNTUT2018/classification-t107368523/blob/master/11.png" width="392" height="106"><br />
<img src="https://github.com/MachineLearningNTUT2018/classification-t107368523/blob/master/12.png" width="607" height="227"><br />

多類別分類所以loss=categorical_crossentropy，優化器為RMSprop，使用fit_generator開始來訓練網路。<br />
經過幾次訓練，發現差不多再10圈左右準確度就到達一個定值，最後驗證的準確度為0.85左右，這邊網路看來準確度還可以提升，往後會試試其他設定來卻練看看。<br />
<img src="https://github.com/MachineLearningNTUT2018/classification-t107368523/blob/master/13.png" width="632" height="800"><br />

另外是重新寫CNN來訓練網路，結構如下。<br />
<img src="https://github.com/MachineLearningNTUT2018/classification-t107368523/blob/master/14.png" width="561" height="463"><br />

此model先前已經訓練完並儲存，這邊重新讀取進來並評估驗證集資料，可以看到準確度可以達到約94%。<br />
<img src="https://github.com/MachineLearningNTUT2018/classification-t107368523/blob/master/15.png" width="427" height="135"><br />

接著來預測測試資料，把每張圖片預測機率取最高的來代表，for loop內從one hot encoding的值讀取成角色名稱，之後整理成.csv檔後儲存。<br />
<img src="https://github.com/MachineLearningNTUT2018/classification-t107368523/blob/master/16.png" width="601" height="287"><br />




