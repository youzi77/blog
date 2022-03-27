- 查找选取某个值

​		df.loc[df['webname']=='中国货币网','id']   *# 选取webname列是中国货币网，id列的数据*
​        df.loc[df['webname']=='中国货币网',['id','title']]  *# 选取webname列是中国货币网，id和title列的数据*