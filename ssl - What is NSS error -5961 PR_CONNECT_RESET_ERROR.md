Can someone enlighten me the meaning of "NSS error -5961 (PR\_CONNECT\_RESET_ERROR)"?

I am trying to connect to bitbucket.org with "https" protocol but got a refuse from the server. Then, I try to use curl on the command line and see this output.

```
# curl -v https://bitbucket.org
* About to connect() to bitbucket.org port 443 (#0)
*   Trying 131.103.20.168...
* Connected to bitbucket.org (131.103.20.168) port 443 (#0)
* Initializing NSS with certpath: sql:/etc/pki/nssdb
*   CAfile: /etc/pki/tls/certs/ca-bundle.crt
  CApath: none
* NSS error -5961 (PR_CONNECT_RESET_ERROR)
* TCP connection reset by peer
* Closing connection 0
curl: (35) TCP connection reset by peer
```

With openssl, I got this output.

```
# openssl s_client -connect bitbucket.org:443 -msg
CONNECTED(00000003)
>>> TLS 1.2 Handshake [length 00f4], ClientHello
    01 00 00 f0 03 03 55 59 80 fa 72 25 f4 a5 84 49
... <I suspended this Hex value>
write:errno=104
---
no peer certificate available
---
No client certificate CA names sent
---
SSL handshake has read 0 bytes and written 249 bytes
---
New, (NONE), Cipher is (NONE)
Secure Renegotiation IS NOT supported
Compression: NONE
Expansion: NONE
---
```

*   [ssl](/questions/tagged/ssl "show questions tagged 'ssl'")
*   [openssl](/questions/tagged/openssl "show questions tagged 'openssl'")

asked May 18, 2015 at 6:21
[microstrip](/users/449151/microstrip)microstrip
35311 gold badge33 silver badges44 bronze badges

1
*   first try to see if you can telnet to the ip on the same port. if you can't that is the challenge, engage your security team to permit the access and then try again.
        – [Princewill Ezeidei](/users/1055429/princewill-ezeidei "1 reputation")
        [Jun 27, 2019 at 8:39](#comment2192517_916077)

[Add a comment](# "Use comments to ask for more information or suggest improvements. Avoid answering questions in comments.")  | [](# "Expand to show all comments on this post")

2 Answers 2
-----------

```
> TCP connection reset by peer

This error from NSS is the same error you get with openssl (errno=104: ECONNRESET). This simply means, that the peer or some middlebox in between (firewall) is terminating the connection.

Since the site is reachable from my place I would suggest, that there is a firewall on your site blocking the connection. The behavior is fairly typical for DPI firewalls in that the initial TCP connection is allowed but once you send the first data (ClientHello from TLS handshake) it will determine if your access is allowed by policy and let it pass or deny it by injecting a TCP RST.

answered May 18, 2015 at 6:57
[Steffen Ullrich](/users/291741/steffen-ullrich)Steffen Ullrich
5,6721717 silver badges2121 bronze badges
```

2

*   Thanks a lot for your respond. I am trying to contact network admin to look at firewall configuration. Hope that they will find something in the policy.
    – [microstrip](/users/449151/microstrip "353 reputation")
    [May 18, 2015 at 13:38](#comment1233681_916086)
    
*   Steffen Ullrich: After an update from network admin on the firewall configuration, the ssl communication back to work as normal. Thanks a lot for your idea and support.
    – [microstrip](/users/449151/microstrip "353 reputation")
    [May 19, 2015 at 6:15](#comment1234244_916086)
    

