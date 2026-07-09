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
     * `jobI
<!--stackedit_data:
eyJoaXN0b3J5IjpbOTgyNDU5MjkxXX0=
-->