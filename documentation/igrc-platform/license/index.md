---
title: "License inventory"
description: "License inventory"
---

# IDA License inventory

A Web service is available to allow IDA end users to have a more detailed view of the licence status, providing:

- The expiration date and the remaining days
- The maximum/current/remaining identities
- The list of features included in the licence

To download the IDA licence inventory PDF, replace the `/portal` string by `/get_lic_info`.

For example, the URL:

- `http://localhost:8080/demo/portal`

Should become:

- `http://localhost:8080/demo/get_lic_info.`
