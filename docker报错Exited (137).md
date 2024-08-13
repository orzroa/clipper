docker container启动失败，报错：Exited (137) \*\*\* ago，比如

> Exited (137) 16 seconds ago

这时通过docker logs查不到任何日志，从mesos上看stderr相关的只有一句

> I0409 16:56:26.408077 8583 executor.cpp:736\] Container exited with status 137

通过docker inspect查看container状态为

        "State": { "Status": "exited", "Running": false, "Paused": false, "Restarting": false, "OOMKilled": true, "Dead": false, "Pid": 0, "ExitCode": 137, "Error": "", "StartedAt": "2019-04-09T08:50:48.058583459Z", "FinishedAt": "2019-04-09T08:50:55.456317695Z" },

可见是因为OOMKilled，通过journalctl查看oom日志如下：

# journalctl -k | grep -i -e memory -e oom
Apr 09 16:00:14 cdp-test-server-05.bj kernel: java invoked oom-killer: gfp\_mask=0xd0, order=0, oom\_score\_adj=0 Apr 09 16:00:14 cdp-test-server-05.bj kernel:  \[<ffffffff8c3ba524>\] oom\_kill\_process+0x254/0x3d0 Apr 09 16:00:14 cdp-test-server-05.bj kernel:  \[<ffffffff8c435346>\] mem\_cgroup\_oom\_synchronize+0x546/0x570 Apr 09 16:00:14 cdp-test-server-05.bj kernel:  \[<ffffffff8c3badb4>\] pagefault\_out\_of\_memory+0x14/0x90 Apr 09 16:00:14 cdp-test-server-05.bj kernel: memory: usage 524288kB, limit 524288kB, failcnt 8430 Apr 09 16:00:14 cdp-test-server-05.bj kernel: memory+swap: usage 524288kB, limit 1048576kB, failcnt 0 Apr 09 16:00:14 cdp-test-server-05.bj kernel: Memory cgroup stats for /docker/3aafdee2b919fa936815fbb88ebd8bb3131c185690284491f583c62ff382b1fe: cache:20KB rss:524268KB rss\_huge:0KB mapped\_file:8KB swap:0KB inactive\_anon:0KB active\_anon:524236KB inactive\_file:8KB active\_file:8KB unevictable:0KB
Apr 09 16:00:14 cdp-test-server-05.bj kernel: \[ pid \]   uid  tgid total\_vm      rss nr\_ptes swapents oom\_score\_adj name
Apr 09 16:00:14 cdp-test-server-05.bj kernel: Memory cgroup out of memory: Kill process 10768 (java) score 1021 or sacrifice child

![](https://assets.cnblogs.com/images/copycode.gif)

原因是container只分配了512M，但是需要1G（比如配置文件里设置-Xms1G -Xmx1G）

  
参考：https://success.docker.com/article/what-causes-a-container-to-exit-with-code-137
