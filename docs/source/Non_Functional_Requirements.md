<!-- Copied from Google Doc "Non Functional Requirements" (Siliconics Drive folder) on 2026-11-23.
     Source: https://docs.google.com/document/d/1-OgWhubzuvXJcc9vubzuTVOODGe7aANuYul59ZauQsI/edit -->

# Non Functional Requirements

List of all non-functional requirements for the project:

- The system should have API firewalls.
- The system should be able to handle on average 5 to 10 requests per second and up to 1000 requests per second for events like product launch.
- Database with at least 200mb storage. Should be able to scale later.
- Ensure 99.9% uptime.
- Less than 3 seconds response time for every request (P95).
- Health monitor to check live status of working of the app. It will give all kinds of meta data and metrics of live requests, check all types of logins, signups, and do a dummy purchase. Basically it will check each and every feature of the app without causing any disturbance in its working because health monitoring should run on production as well. Only Admin can access health monitoring.
- The platform should be highly secured and authorized  wherever needed. Any kind of possibility of personal data leak should be taken seriously and must be eliminated.
- The UI should be color blind friendly and easy/fast to load for all users types.
- Rate limiter and load balancer must be used to take care of API requests in an efficient way. If there are an overwhelming number of requests it should deal in a way that such servers are not crashed.
