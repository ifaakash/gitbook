# Docker scout

What's next: View a summary of image vulnerabilities and recommendations → docker scout quickview aakashmac@Aakashs-MacBook-Air oct5 % docker scout quickview i New version 1.14.0 available (installed version is 1.13.0) at https://github.com/docker/scout-cli ✓ Image stored for indexing ✓ Indexed 158 packages

```
i Base image was auto-detected. To get more accurate results, build images with max-mode provenance attestations.
  Review docs.docker.com ↗ for more information.
  
```

Target │ local://hello-python:latest │ 0C 0H 0M 28L\
digest │ 040129705c87 │\
Base image │ python:3-slim │ 0C 0H 0M 28L\
Updated base image │ python:alpine │ 0C 0H 0M 0L\
│ │ -28

What's next: View vulnerabilities → docker scout cves local://hello-python:latest View base image update recommendations → docker scout recommendations local://hello-python:latest Include policy results in your quickview by supplying an organization → docker scout quickview local://hello-python:latest --org



<figure><img src=".gitbook/assets/Screenshot 2024-10-05 at 10.33.00 PM.png" alt=""><figcaption></figcaption></figure>
