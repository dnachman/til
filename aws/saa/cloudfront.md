---
title: "CloudFront"
---


- Content Delivery Network CDN
- Edge Location - where it’s cached (can write to them - put S3)
- Origin - S3, EC2, ELB, R53
- Distribution - name given to the CDN and collection of edge locations
- Web Distribution or RTMP (media streaming)
  - Cached for Time To Live TTL
- Can clear cached objects, but charged
- Signed URLs / Cookies
  - 1 file = 1 URL => signed URL
  - Multiple files => signed cookie
  - Assign policy:
    - URL expiration
    - IP ranges
    - Trusted signers (which AWS accounts can create signed URLs)
  - Origin Access Identity OAI
  - use SDK to generate signed URL and return to client
  - CloudFront Signed URL - different origins (EC2, S3, etc):
    - Key-pair is account wide and managed by root user
    - Can utilize caching
    - Can filter by date, path, IP address, expiration, etc
  - S3 Signed URL - direct access to S3
    - Issues a request as the IAM user who creates the resigned URL
    - Limited lifetime