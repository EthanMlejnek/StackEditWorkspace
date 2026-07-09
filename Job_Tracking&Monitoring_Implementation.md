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
* As described above, once the `/start` URL has been requested, you may poll `/api/jobs/{jobId}` or the `statusUrl` at some interval to get live updates on query stages/progess
* **Example Response(s):**
	* `{ "state": "Running", "stage": "Resolving Chamber Modules (1)", ..., "itemsProcessed": 0 }`
	* ...
	* `{ "state": "Completed", "stage": "Completed (8)", "itemsProcessed: 1,234,567 }`

**Full Response Object Example:**
```json
{
  "jobId": "8fa67a88ef4f44da8e6480276d8f91cd",
  "type": "chart-data-v2",
  "state": "Running",
  "percentComplete": null,
  "stage": "Streaming rows (5)",
  "message": "Streaming Rows for Group: 18_1490 Channel: 224R_23A_224W_Black_8 total rows processed: 587,000",
  "itemsProcessed": 587000,
  "totalItems": null,
  "startedAtUtc": "2026-07-09T14:40:28.708607Z",
  "updatedAtUtc": "2026-07-09T14:40:55.8045504Z",
  "error": null
}
```
<!--stackedit_data:
eyJoaXN0b3J5IjpbOTAxMTk3MjIxXX0=
-->