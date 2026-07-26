## Changes needed for KASM configuration to work behind a reverse proxy

[KASM](https://www.kasmweb.com) is a useful tool for streaming containerized applications to end users. It is one of the enabling technologies for [remote browser isolation](https://www.kasmweb.com/browser_isolation.html). When deployed behind a reverse proxy like Contour, the below additional setting needs to be configured on KASM for it work. You need to edit the default zone and change "upstream auth address" from "$request_host$" to the actual IP of the VM hosting KASM.

![kasm01](kasm01.png)

