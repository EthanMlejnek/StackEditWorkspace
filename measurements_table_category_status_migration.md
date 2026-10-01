
## Tech Stack Context

In our current tech stack, we have two IIS servers (development/production). Each server hosts a version of our backend/frontend applications:
* **Backend API**: 
	* Framework: ASP.NET Core
	* Language: C#/LINQ
	* .NET Version: 10.0.201
* **Frontend Web App**:
	* Framework: Next.js 13.5.11 / React framework with SSR/SSG
	* Language: TypeScript 4.9.4

Currently, we only have a **single** production database:
* **Database:** Microsoft SQL Server 16.0.4215.2

To summarize our tech stack in short, our backend API utilizes C#/LINQ queries with Entity Framework Core to make queries again our database, the frontend in turn queries the defined backend API methods to fetch/post/update content to/from the frontend web app.

In our development process, we make changes to the frontend/backend code and test them locally before merging the changes to their respective frontend or backend `staging` branch, automatically triggers a release to the `development` IIS server. Once changes are fully tested on the dev server, we merge them to their respective frontend or backend `main` branch, triggering an automatic release to the `production` IIS server. 

**Important Database Note:**
* 




<!--stackedit_data:
eyJoaXN0b3J5IjpbLTU3OTkwOTk0Nl19
-->