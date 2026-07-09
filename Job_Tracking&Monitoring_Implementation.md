### Job Status Tracking for Charts:
1. Obtain JWT token
  * JWT token returned by `/api/authentication/authenticate`
  * Should be returned after authenticating user
2. Start "Background Job"
* Request URL is the same as the original but with `/start` at the end
    * `http://localhost:53097/api/measurement/chartDataFilteredV2/start?testIds=1,2,3`
 * Add authorization header and append JWT token:
     * Authorization: Bearer <JWT_token>
 * Will immediately return:
     * `jobId`:  Value that can be passed to `api/jobs/{jobId}` to receive a live status update of the job
     * `statusUrl`: Provides URL for `api/jobs/{jobId}` but with `jobId` already included
         * Example:  `/api/jobs/a38c1d0e2b4`
     * `resultUrl`: The URL that is used to fetch the actual results of the query once it completes
	     * Example: Once `stage` attribute from the item returned by `statusUrl` is "Completed", fetch the `resultUrl` to get the actual
<!--stackedit_data:
eyJoaXN0b3J5IjpbLTEzNjc0OTEwMF19
-->