# Root Cause Analysis


## Defect 1: Proxy image fails to build
### Symptom / error output
 Errors were encountered while processing: nginx-common, nginx
 E: Sub-process /usr/bin/dpkg returned an error code (1)
 This indicates that the package manager (dpkg) crashed at the post-installation stage of Nginx, preventing the image from building.


### How I diagnosed it
I ran "docker compose build proxy" and inspected the build output above the final exit code to see where "apt-get install" failed.


### Root cause
"COPY nginx.conf" was placed before "RUN apt-get install" in proxy/Dockerfile, causing a file conflict when the package installer tried to set up the default Nginx configuration.


### Fix
I reordered proxy/Dockerfile so that "apt-get install" runs first, and "COPY nginx.conf" runs afterward.


## Defect 2: Proxy cannot reach the backend
### Symptom / error output
 connect() failed (111: Connection refused) while connecting to upstream ... upstream: "http://127.0.0.1:8080/api/info"
 TEST RUNNER CRASHED: HTTP Error 502: Bad Gateway
While the proxy container is running, its connection to the backend is refused, causing it to return a 502 error to the test runner.


### How I diagnosed it
I ran "./scripts/run-tests.sh" and checked the proxy error logs alongside proxy/nginx.conf file.


### Root cause
proxy/nginx.conf routed traffic to "127.0.0.1:8080", which points to the proxy container's own loopback interface rather than the backend container.


### Fix
I changed the upstream address in proxy/nginx.conf from "127.0.0.1:8080" to "backend:8080" to resolve the backend service via Docker DNS.


## Defect 3: Tests still fail after fixing defect 2
### Symptom / error output
 test-runner-1 | TEST RUNNER CRASHED: HTTP Error 502: Bad Gateway
 backend-1     | [Backend] loaded config key: service
This timeline shows that the test runner crashes with a 502 error while the backend container is still in the middle of loading its initial configuration.


### How I diagnosed it
I compared the container timestamps in the logs and inspected backend/app.py, where I found a "time.sleep" loop causing an 8-second startup delay before port 8080 opens.


### Root cause
A startup race condition: "depends_on" in the docker-compose.yml only waits for containers to start, not for the backend HTTP server to finish initializing and listen on port 8080.


### Fix
I added healthchecks to "backend" (/health) and "proxy" (/nginx-health) in docker-compose.yml, and updated "depends_on" to use "condition: service_healthy".


## Defect 4: CI pipeline fails
### Symptom / error output
 ./scripts/run-tests.sh: Permission denied (Exit code 126)
This symptom occurs when the GitHub Actions Ubuntu runner tries to execute the test script and is blocked by Linux OS permissions, even though the script passes locally on Windows.


### How I diagnosed it
I inspected .github/workflows/ci.yml and checked the file mode stored in the Git index using "git ls-files -s scripts/run-tests.sh".


### Root cause
scripts/run-tests.sh file was committed with mode "100644" (non-executable). Windows ignores this locally, but the Ubuntu runner requires the "+x" executable bit.


### Fix
I ran "git update-index --chmod=+x scripts/run-tests.sh" to update the file mode to "100755" in the Git index.


## Bonus questions

## Defect 1: Image Size Optimization
### Symptom / error output
The original proxy container posed significant performance and security risks. It was contained unnecessary shell tools. 


### How I diagnosed it
While reviewing proxy/Dockerfile, I noticed it used a general-purpose OS base (ubuntu:24.04), manually installed nginx alongside dangerous utilities like curl and git.


### Root cause
Using a heavy Ubuntu image for a single-service proxy violates the principle of minimal footprint.


### Fix
**Size Optimization:** I replaced ubuntu:24.04 with the official, minimalistic nginx:alpine image. This inherently stripped out unnecessary utilities and slashed the image size from **121MB** down to **28.8MB**.

## General comment
I chose not to submit parts 2 and 3 because I felt that my answers would rely too heavily on AI, which didn't feel entirely honest.
I do have a general understanding of the suggested solutions, but figuring out where and how to introduce a variable to restrict permissions was completely new to me.
I'd be happy to do my best to answer any questions about these parts if needed.


>

---

## Anything you would do differently with more time
I would dive deeper into the core container and CI/CD concepts behind these operations. Maybe I would try to build a similar project from scratch by myself, relying mostly on official documentation rather than AI.