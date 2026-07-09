## Job Status Tracking for Charts:

**1. Obtain JWT token**
  * JWT token returned by `/api/authentication/authenticate`
  * Should be returned after authenticating user
  
**2. Start "Background Job"**
* **Request URL is the same as the original but with `/start` at the end**
    * `http://localhost:53097/api/measurement/chartDataFilteredV2/start?testIds=1,2,3`
 * **Add authorization header and append JWT token:** (may change)
     * Authorization: Bearer <JWT_token>
 * **Will immediately return:**
     * `jobId`:  Value that can be passed to `api/jobs/{jobId}` to receive a live status update of the job
     * `statusUrl`: Provides URL for `api/jobs/{jobId}` but with `jobId` already included
         * **Example:**  `/api/jobs/a38c1d0e2b4`
     * `resultUrl`: The URL that is used to fetch the actual results of the query once it completes
	     * **Example:** Once `stage` attribute from the item returned by `statusUrl` is "Completed", fetch the `resultUrl` to get the actual response
	     * **Example:** `/api/jobs/a38c1d0e2b4/result`
	     * Results are stored/cached on the server itself until one of the following occurs:
	         1. Results are fetched using `resultUrl`
	         2. The job has been cleaned up. Every 15min all completed & not fetched or failed jobs are automatically cleared from memory.

**Full Response Object Example:**
```json
{
  "jobId": "a3f8c1d0e2b4...",
  "statusUrl": "/api/jobs/a3f8c1d0e2b4...",
  "resultUrl": "/api/jobs/a3f8c1d0e2b4.../result"
}
```

**3. Poll for Live Progress:**
* Poll `/api/jobs/{jobId}` or the `statusUrl` at some interval to get live updates on query stages/progess
* **Example Response(s):**
	* `{ "state": "Running", "stage": "`
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTI2MTI0NTc2NV19
-->