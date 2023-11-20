最新推荐文章于 2023-10-11 23:02:10 发布

[徐锦桐]
 于 2022-11-06 20:02:57 发布

### 一，首先创建一个腾讯云的存储桶

*   重要的是将访问权限设置为“公有读私写”或者“公有读写”。
    
*   ![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231113/31f4f8d4-610b-4cc5-92a2-398c84e506e9.png)
    
*   桶的名不能重复，并且创建成功后不能更改
    
    *   ![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231113/f141ef01-8d44-4c6e-9796-cd5b4068d848.png)
        

#### 二，下载PicGo并配置

*   1，再PicGo中图床设置中的腾讯云COS，这些标红的就是必写的。
*   2，配置
    *   **Secretld和SecretKey和APPID：**  这个在腾讯云中的个人用户中的访问管理中有，找到访问管理中的访问密钥，里面有个API密钥就是它，刚开始我们需要新建一个密钥，然后会生成这三个。
        
    *   ![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231113/01798323-4644-4452-b879-257ab1c9036c.png)
        
    *   **Bucket和存储区域：**  这里在创建的桶设置中有。在这桶列表中存储桶名称就是Bucket,这个所属区域就是设定存储区域。
        
        *   ![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231113/a3aba81b-a323-427c-a99a-406b40deba1a.png)
            
    *   **存储路径：**  这个就是在桶中所属的文件夹注意最后加个/
        

### 三，在Obisidian中配置

*   首先在社区中心下载**Image auto upload Plugin插件** 然后设置如图就好了（PicGo必须打开，建议设置成开机自启）。
    *   ![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231113/d89faedc-f732-483d-a12c-661146b4c1ed.png)
        

#### 四，结尾

我们要在我们的账户里面留10快作用（这能用很久了），这个属于每天一付费，花的不是很多。大家的订阅和点赞是对我最大的支持！！！
