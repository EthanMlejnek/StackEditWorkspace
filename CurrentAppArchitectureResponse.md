
**Problem Statement:** The production Next.js frontend and ASP.NET Core backend both run as manually-managed processes on a single Azure VM, using custom ports and manually configured TLS certificates. 

A "pet server" is infrastructure that's manually configured, unique, and irreplaceable. This is not a redundant design and therefore if it goes down it must be manually fixed, unlike the "cattle" architecture which is disposable/repeatable. 

**Cons of the "Pet Server" Architecture:**
* **Manually Managed:** Someone has to RDP/PowerShell into the box to deploy, patch the OS, renew certs, restart services, etc. 
* **Single Point of Failure:** It's a single point of failure, if the VM goes down, both services go down together
* **Difficult to Scale:** Scaling either tier independently isn't possible without re-architecting (e.g., more frontend capacity can't be integrated without more backend capacity) 

**Suggestion:** App Services for both tiers with Private Endpoints - replaces the VM with two managed PaaS App Service instances. 
> **PaaS:** Platform as a Service is a cloud computing model where a provider delivers a complete, on-demand environment for developing, testing, and managing software applications. 

**Solution:** Move Frontend + Backend to App Service
> **Azure App Service:** Is a fully managed Platform-as-a-Service (PaaS) that lets you build. deploy, and scale web apps, mobile backends, and RESTful APIs without managing the underlying infrastructure. It handles OS patching, load balancing, and automated scailing, letting you focus purely on development. 

**Difficulty:** Medium-High
**Estimated Relative Effort:** Weeks
**Requirements:**
* Two App Service resources (frontend, backend) provisioned 
<!--stackedit_data:
eyJoaXN0b3J5IjpbMjA1NjEzMjcyMSw1NDQyMzkyMTBdfQ==
-->