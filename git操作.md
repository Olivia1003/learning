merge操作

#### 使用 merge squash
1 git co rel/8.7_dev  
2 git merge rel/8.6_dev_lowprice --squash  
3 git commit -m 'feat:...'  
4 git push ...  


#### reset
1. git reset --soft 7c5a658
撤销commit

2. git reset --mixed 7c5a658（默认）
撤销add commit

3. git reset --hard 7c5a658
撤销所有修改



### 参考
https://www.jianshu.com/p/684a8ae9dcf1
