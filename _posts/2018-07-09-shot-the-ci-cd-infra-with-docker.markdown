---
title:  "Shot the CI/CD Infra with Docker"
date:   2018-07-09 12:15:40 +0330
tags:
  - deployment
  - docker
  - ci
  - cd
  - infrastructure
toc: true
last_modified_at: 2018-07-09T12:15:40-03:30
---
Having an standard Software Infrastructure can help to make reliable software.

### Audience
Developers, DevOps Engineers, and SysAdmins.

### Challenges
- Version Control : Git
- Tests           : CI (Jenkins)
- Code Quality    : SonarQube
- Make Releases   : CD (Jenkins)

### Solution
I've use Docker Compose to integrate all above technologies in Enterprise Level.

<script src="https://gist.github.com/coditori/901c7411dcae74066b64bb379bfdb16d.js"></script>
