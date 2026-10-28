1. Why two upstream calls are needed

Two upstream calls are needed because the NWS API accepts only the latitude/longitude, so it needs the other API to convert the zipcode to the coordinates.

---

2. A sentence explaining one weather_success line

One of the weather_success lines shows that it had a cache result of a hit on zipcode 60616 in 2 ms.

---

3. Write one sentence on why a short cache matters more for a service that calls a rate-limited upstream, and one on why CACHE_SECONDS should not be very long for weather

A short cache matters for rate-limited upstreams because it limits how often the service must make API calls to prevent the service from hitting quotas and to keep it responsive. CACHE_SECONDS should not be very long for weather because forecasts can change quickly, so a long cache would give outdated information to users.

---

4. Write one sentence on what an attacker who takes over the advisor tier gets, and what they do not get.

An attacker who takes over the advisor tier gets access to the Gemini API key which can allow them to make AI requests or incur cost by abusing the service. They do not get access to the database password or the ability to query the database, because the advisor’s IAM role is restricted to only read the Gemini key parameter and has no permissions for SSM access to the database secret.

---

5. Explain in one sentence what a security group rule that names another security group as its source does that a CIDR range cannot.

A security group rule that names another security group as its source allows traffic based on the identity of the instances rather than their IP addresses. So if the instances are replaced, the database access rule does not need to be updated with the new IP.

---

6. Write down why the 30 second pause exists, and what would happen to every request without it while the database is down.

The 30 second pause exists to prevent a problem when all of the pending requests attempt to reconnect to the database at the same time which can overwhelm it and can cause it to crash again.

---

7. Write down one problem with a design where users never see an error and the only evidence is a log line that nobody reads.

A problem with a design where users never see an error is that users receive a fallback answer without knowing that it's not from the AI. So this means that they cannot tell if the suggestion is coming from the AI or a generic rule-based one, which may lead to incorrect decisions.

---

8. Write down what that tells you about where state should live in a stack where instances are replaced.

State should live in an external persistent storage like the Amazon RDS database rather than on the instance, so that when an instance is terminated and replaced, the new instance can access the existing data and use it.

