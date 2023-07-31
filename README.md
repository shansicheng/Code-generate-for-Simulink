# Code-generate-for-Simulink
直接编写C代码有时候很麻烦。基于机器学习的MATLAB的代码生成可以直接写m文件训练后生成也可以通过使用Classification Learner工具箱导出训练后的模型再在simulink中导出C代码（MATLAB版本为R2020b）

Classification Learner工具箱的特性和训练数据方法可见：https://blog.csdn.net/weixin_45649913/article/details/107998061?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-1-107998061-blog-115793882.235%5Ev38%5Epc_relevant_anti_vip&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-1-107998061-blog-115793882.235%5Ev38%5Epc_relevant_anti_vip&utm_relevant_index=2


先使用MATLAB中的Classification Learner工具箱选择参数生成模型
![image](https://github.com/shansicheng/Code-generate-for-Simulink/assets/100584217/bf079870-5fe7-4a64-bca2-99af30cebf33)

注意Y要使用double型的数据，否则Simulink中的MATLAB Fun模块输出部分无法识别

然后选择需要的分类器进行训练，导出训练后的模型
![image](https://github.com/shansicheng/Code-generate-for-Simulink/assets/100584217/b54666f4-3e41-489b-aab1-c9ae2bf71399)

此时工作区会出现刚才导出的结构体
![image](https://github.com/shansicheng/Code-generate-for-Simulink/assets/100584217/b2220d7e-ab30-4e0f-a080-8e7ad37b93e8)

命令行输入

saveLearnerForCoder(trainedModel_1.ClassificationTree, 'trainedModel_1')

生成名为trainedModel_1.mat文件（里面的结构体不再是原来的trainedModel_1）。注意不是在工作区右键另存为后保存，因为这样的结构体没办法在Simulink中使用

构建一个Simulink文件，选择MATLAB Fun模块
![image](https://github.com/shansicheng/Code-generate-for-Simulink/assets/100584217/d0d1ab26-acad-475f-aa4d-f565232811c1)

双击该模块，将m文件代码改为

function label = svmIonospherePredict_1(x1,x2) %#codegen

Mdl = loadLearnerForCoder('trainedModel_1');
label = predict(Mdl,[x1,x2]);
% score = bothscores(:,2);

end


点击运行
然后Simulink中点击Code Generation或者ctrl+B生成C代码 
![image](https://github.com/shansicheng/Code-generate-for-Simulink/assets/100584217/df00a562-cb8e-40ef-83c4-c4f2ae2198de)
该文件夹中即为生成的C代码
