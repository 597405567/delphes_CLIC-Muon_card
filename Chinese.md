在使用ma5做重构事例水平分析的时候，程序一般会使用LHCO文件作为输入。

要想得到LHCO文件，我们需要使用delphes自带的root2lhco，将delphes生成的root文件转为LHCO文件。

如果大家用到CLIC以及muon对撞机的delphes卡片，在将root转化为LHCO文件时就会发现程序会提示一个错误以及警告：
```
** WARNING: cannot access branch 'Jet', return NULL pointer
** ERROR: ROOT file doesn't contain all required branches
```
这个时候并没有成功的产生LHCO文件。原因是：root2lhco命令在生成LHCO文件之前，会先判断root文件中的branch是否含有以下的branch：
```
if(!fBranchEvent || !fBranchTrack || !fBranchTower || !fBranchPhoton || !fBranchElectron || !fBranchMuon || !fBranchJet || !fBranchMissingET)
   {
     throw runtime_error("ROOT file doesn't contain all required branches");
   }
```
结合报错我们可以知道使用CLIC或者muon等delphes_card产生的root文件中并不包含Branchjet。

我们在这里提供一种解决方法：

在浏览对比大量的delphes的卡片后，我们发现CLIC或者mu对撞机的卡片使用的是fastjetfinderkt来识别jet，除此之外其他的卡片使用fastjetfinder识别jet。

在输出fastjetfinder识别的jet时，一般会将这些jet命名为ktjets，

在输出fastjetfinder识别的jet时，一般会将其命名为Jet。

这一点不同就是导致报错的原因。

在这里我么建议将fastjetfinderkt输出的ktjets修改为Jet。

在root tree writer下边有这样一行命令：

```
add Branch FastJetFinderKt/KTjets KTjet Jet
```
我们需要将这一行命令修改为：
```
add Branch FastJetFinderKt/KTjets Jet Jet
```
在做完这个操作后，root文件就可以成功的转化为LHCO文件。

在这里我们有必要声明：我们只是修改了输出的变量名，并没有修改CLIC默认的jet识别算法。
