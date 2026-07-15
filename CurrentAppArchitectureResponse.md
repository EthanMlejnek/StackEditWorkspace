
**Problem Statement:** The production Next.js frontend and ASP.NET Core backend both run as manually-managed processes on a single Azure VM, using custom ports and manually configured TLS certificates. 

A "pet server" is infrastructure that's manually configured, unique, and irreplaceable. This is not a redundant design and therefore if it goes down it must be manually fixed, unlike the "cattle" architecture which is disposable/repeatable. 

Cons of the "pet server" architecture:
* Someone has to RDP/PowerShell into the box to deploy, patch the OS, renew certs, restart services, etc. 
* It's a single point of failure, if the VM goes down, both services go down together
* Scaling either tier independently isn't possible without re-architecting (e.g., more frontend capacity without more
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTE2NTA2Mjg1MDJdfQ==
-->