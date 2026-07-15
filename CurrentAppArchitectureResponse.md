### Conerns Regarding Current Prod Server & Service Hosting

**Problem Statement:** The production Next.js frontend and ASP.NET Core backend both run as manually-managed processes on a single Azure VM, using custom ports and manually configured TLS certificates. 

A "pet server" is infrastructure that's manually configured, unique, and irreplaceable. This is not a redundant design and therefore if it goes down it must be manually fixed, unlike the "cattle" architecture which is disposable/repeatable. 

**Cons of the "Pet Server" Architecture:**
* **Manually Managed:** Someone has to RDP/PowerShell into the box to deploy, patch the OS, renew certs, restart services, etc. 
* **Single Point of Failure:** It's a single point of failure, if the VM goes down, both services go down together
* **Difficult to Scale:** Scaling either tier independently isn't possible without re-architecting (e.g., more frontend capacity can't be integrated without more backend capacity) 

**Suggestion:** App Services for both tiers with Private Endpoints - replaces the VM with two managed PaaS App Service instances. 
> **PaaS:** Platform as a Service is a cloud computing model where a provider delivers a complete, on-demand environment for developing, testing, and managing software applications. 

**Solution:** Move Frontend + Backend to App Service
**Benefits:**
* Platform-managed TLS: no more manually installing/renewing certs on a VM 
* Slot-based deploys: Deploy to a staging slot, swap to production with near-zero downtime, instead of the current "deploy to dev server, verify, then manually deploy to prod server" workflow
* No custom ports: App Service exposes standard 443/80 with routing handled by the platform, instead of the frontend needing to know where the backend lives at `:53098`
> **Azure App Service:** Is a fully managed Platform-as-a-Service (PaaS) that lets you build. deploy, and scale web apps, mobile backends, and RESTful APIs without managing the underlying infrastructure. It handles OS patching, load balancing, and automated scailing, letting you focus purely on development. 


**Difficulty:** Medium-High
**Estimated Relative Effort:** Weeks
**Requirements:**
* Two App Service resources (frontend, backend) provisioned in the target subscription/resource group
* Update CI/CD pipeline stages: replace the file-copy/artifact-drop steps with `AzureWebApp` deploy task (or similar) targeting each App Service
* Removal of hardcoded custom ports (`:53098`, etc.) from frontend config, since App Service uses standard 443/80 with app-specific hostnames instead
* DNS cutover plan (pointing `orbit.commscope.com` at the new App Service instead of the VM) 

**Questions:**
* How would we go about requesting/accessing the App Service resources? Would we need to be concerned about the cost of these resources? 

**Feasibility:**
Doable, but would take some time to integrate. We would essentially need to migrate the frontend/backend services to App Service resources, update/rewrite the CI/CD pipeline deployment process, and reconfigure DNS cutover to migrate the `orbit.commscope.com` hostname. One of the benefits mentioned was "no more manually installing/renewing certs on a VM," so maybe the DNS would be easier to configure, it would also provide the benefit of not having to worry about server config stuff in the future. I imagine the migration/configuration process would be similar to when I configured the production server, but perhaps easier? 

--- 

### Concerns Regarding Current Prod DB & DB Architecture:

**Problem Statement A**: `TestLabCentral` database name suggests the database is a test/lab database, but diagram shows only a single database backing prod/dev. Concern is that the name suggests "lab/test" data but production traffic depends on it, is production actually resting on a dataset that was never meant to be "authoritative"? 
**Problem Statement B:** "SQL on a VM", the database runs on a SQL server instance that also hosts unrelated databases, we don't manage or have visibility into the server's patching, backups, or capacity

* **Note:** Original concern was likely that we have a SQL server running on a VM and not a managed database and therefore we are responsible for manually patching, performing backups, etc. In reality, the problem statement description is more accurate as we werer granted the SQL server by IT and lack the permissions to perform database management actions. Either way I think this stems from confusion surrounding the DB and where it is hosted exactly, and the suggested solution is the same either way. 

**Suggestion:** Migrate database to Azure SQL
> **Azure SQL:** Similar to App Service,  Azure SQL Database is a fully managed database service where Microsoft handles patching, backups, and uptime, and where your data lives in its own dedicated space rather than sharing the server with other applications. 

**Solution:** Migrate database to Azure SQL
* Set up Private Endpoint so the database is only reachable from your own App Service backend, not exposed on a shared network
* Migrate the existing `TestLabCentral` data into the new Azure SQL database using a standard database migration tool

**Difficulty** Medium. Azure SQL is wire-compatible with SQL Server/EF Core is almost all cases, so the app code likely wouldn't need logic changes, just a new connection string. The biggest risk would be the data migration cutover.
**Estimated Relative Effort:** Weeks
**Requirements:**
* An Azure SQL Database provisioned with a Private Endpoint
* A migration tool run (e.g., Azure Data Migration Assistant/Service) to assess compatibility and move data to new DB. 
* A maintenance/cutover window, since the app can't write to the old and new databases at once without extra work  

---

### Move Development Server to Azure + Integrate Seperate Dev DB 

### Integrate Seperate Development Database

**Problem Statement (Dev DB):** Both development and production servers point at the same, single DB. If something goes wrong in testing, it can effect production. 
* **Suggestion:** Implement a seperate development server
* **Solution:**
	* Create a genuinely seperate development/test database (second Azure SQL DB) that would only be used by non-production deployments. 
	* **Note:** Likely would NOT be able to use existing development DB because:
		* Would be a different DB implementation than the prod DB is prod is migrated to Azure SQL DB, so dev testing environment would not match prod. 
        * Raises the same concern as the "dev server" problem statement but with the DB instead of the servers themselves. 
* **Difficulty/Requirements/Effort:** Similar to migrating prod DB to Azure SQL DB

* ** 

### Migrating Development Server to App Service
**Problem Statement (Dev Server):** Dev server is HTTP-only and is an on-prem server, it runs Windows Server 2019 which is different that the OS run by prod (Windows Server Datacenter 2022) and is out of mainstream support compared to WS2022. 
* **Suggestion:** that if the app is going to live in Azure anyway, a dev slot/environment in the same "landing zone" resolves the "hybrid dependency" 
* **Solution:** 
    * Deploy a second, lower-cost App Service (a "dev" or "staging" environment) inside the same Azure setup as production, instead of using the on-prem server for testing.
    * Point that environment at the *new development database* 
* **Difficulty/Requirements/Effort:** Similar to migrating prod to App Service; second, lower-tier App Service and pipeline branch/trigger rewriting for new dev deployments.

--- 
### Summary

1. Migrate frontend/backend Prod Services to App Service
2. Migrate current DB to Azure SQL 
3. Migrate frontend/backend Dev Services to App Service
4. Create seperate Dev Azure SQL database

All other areas of concern would essentially need to be resolve 
<!--stackedit_data:
eyJoaXN0b3J5IjpbMTY4MzU3NDQ5MCw3NzI0NDIxNTYsLTEyMT
c1OTY4ODUsMTI4NjI5NDgxNSw1NDQyMzkyMTBdfQ==
-->