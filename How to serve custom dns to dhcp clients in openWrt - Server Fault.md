This answer is useful

14

This answer is not useful

Save this answer.

[](/posts/663367/timeline)

Show activity on this post.

DNS server adress corresponds to DHCP option 006. According to the [OpenWRT Wiki](http://wiki.openwrt.org/doc/uci/dhcp#dhcp_option_example_to_set_an_alternative_default_gateway) your `/etc/config/dhcp` should look like

```
config 'dhcp' 'lan'
    ...
    list 'dhcp_option' '6,yourDNSIP'

```

[Share](/a/663367 "Short permalink to this answer")

Share a link to this answer

Copy link[CC BY-SA 3.0](https://creativecommons.org/licenses/by-sa/3.0/ "The current license for this post: CC BY-SA 3.0")

[Improve this answer](/posts/663367/edit)

Follow

Follow this answer to receive notifications

answered Jan 29, 2015 at 12:29


[duenni](/users/78437/duenni)duenni

2,94911 gold badge2323 silver badges3838 bronze badges

1

*   Seems like you beat me to it by a few seconds, I'll leave my answer open as it is dnsmasq specific :)
    
    – [Reaces](/users/218888/reaces "5,617 reputation")
    
    [Jan 29, 2015 at 12:30](#comment809099_663367)
    

[Add a comment](# "Use comments to ask for more information or suggest improvements. Avoid comments like “+1” or “thanks”.")  | [](# "Expand to show all comments on this post")
