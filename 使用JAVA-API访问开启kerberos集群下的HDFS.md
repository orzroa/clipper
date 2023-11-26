[使用JAVA-API访问开启kerberos集群下的HDFS - 简书](https://www.jianshu.com/p/2243abf0fe48) 

2021.02.03 18:40:15字数 241阅读 1,438

hadoop集群(cdh集群)在开启kerberos安全认证方式后，通常如果在集群shell客户端通过hadoop dfs命令访问的，经过kinit登录kerberos认证即可 ，如下所示

如果不进行kinit登录kerberos用户，则不能进行hdfs操作，如图直接会报安全异常!

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231126/2c4b2673-a60f-4ecd-b6f2-512f02b954dc.webp)

20170722105233283.jpg

而如果进行kinit登录后就能进行hdfs操作了，通过kinit user@YOU-REALM 然后输出密码就能在当前交互下获取kerberos票据授权票据

  

![](https://clipper-1322362908.cos.ap-shanghai.myqcloud.com/images/20231126/aa04f44c-9878-4b11-bccb-b530b5ca2572.webp)

20170722105558934.jpg

而如果通过程序在远程进行访问，显然不能再通过kinit来进行登录了，此时需要通过keytab文件来进行访问，keytab文件生成这里不在进行说明，主要说明获取keytab文件后如果通过代码来进行访问

```
package com.test.hdfs;
import org.apache.hadoop.conf.Configuration;
import org.apache.hadoop.fs.FileSystem;
import org.apache.hadoop.fs.LocatedFileStatus;
import org.apache.hadoop.fs.Path;
import org.apache.hadoop.fs.RemoteIterator;
import org.apache.hadoop.security.UserGroupInformation;

import javax.security.auth.Subject;
import java.io.IOException;
import java.net.URI;
import java.security.PrivilegedExceptionAction;

public class HdfsTest {
    public static void main(String[] args) throws Exception {
        test1();
    }
    
    public static void test1() throws Exception{
        
        
        
        
        System.setProperty("java.security.krb5.conf", "E:\\test\\krb5.conf")
        Configuration conf = new Configuration();
        
        conf.set("fs.defaultFS", "hdfs://namenode:8020");
        
        conf.setBoolean("hadoop.security.authorization", true);
        
        conf.set("hadoop.security.authentication", "Kerberos");
        
        conf.set("dfs.namenode.kerberos.principal", "hdfs/_HOST@YOU-REALM.COM");
        
        conf.set("dfs.datanode.kerberos.principal", "hdfs/_HOST@YOU-REALM.COM");
        
        UserGroupInformation.setConfiguration(conf);
        
        UserGroupInformation.loginUserFromKeytab("user01@YOU-REALM.COM","E:\\test\\user01.keytab");
        
        FileSystem fileSystem1 = FileSystem.get(conf);
        
        Path path=new Path("hdfs://namenodehost:8020/user/user01");
        if(fileSystem1.exists(path)){
            System.out.println("===contains===");
        }
        RemoteIterator<LocatedFileStatus> list=fileSystem1.listFiles(path,true);
        while (list.hasNext()){
            LocatedFileStatus fileStatus=list.next();
            System.out.println(fileStatus.getPath());
        }
    } 
 } 
```

注意：

```
 conf.set("dfs.namenode.kerberos.principal", "hdfs/_HOST@YOU-REALM.COM");
        
        conf.set("dfs.datanode.kerberos.principal", "hdfs/_HOST@YOU-REALM.COM"); 
```

这俩项的值在hdfs-site.xml配置文件中

最后编辑于

：2021.02.03 18:46:38
