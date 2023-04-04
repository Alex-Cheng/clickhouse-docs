---
slug: /en/manage/security/ip-public-api
sidebar_label: Public IP API
title: Public IP API
---

## ClickHouse Cloud public API

The ClickHouse Cloud public API endpoint is available on [`https://api.clickhouse.cloud/static-ips.json`](https://api.clickhouse.cloud/static-ips.json).

This API provides public IPs and endpoints for ClickHouse services, such as ingress/egress IPs and AWS S3 endpoints per region and cloud.

## Integrations could require egress access

If you are using integration like the MySQL or PostgreSQL Engine, it is possible that you need to authorize ClickHouse Cloud to access your instances.

You could use this API of public IPs in `firewalls` or `Authorized networks` in GCP or in `Security Groups` for Azure, AWS or in any other infrastructure egress management system you are using.

For example, to allow access from a ClickHouse Service hosted on AWS in the region ap-south-1, you can add the `egress_ips` addresses for that region:

```
❯ curl -s https://api.clickhouse.cloud/static-ips.json | jq '.'
{
  "aws": [
    {
      "cell": "cell0",
      "egress_ips": [
        "3.110.39.68",
        "15.206.7.77",
        "3.6.83.17"
      ],
      "ingress_ips": [
        "15.206.78.111",
        "3.6.185.108",
        "43.204.6.248"
      ],
      "region": "ap-south-1",
      "s3_endpoint": "vpce-0a975c9130d07276d"
    },
...
```

For example a ClickHouse cloud service running in `us-east-2` using the integration to connect to an RDS in AWS, should have the following Inbound security group rules:

![AWS Security group rules](@site/docs/en/_snippets/images/aws-rds-mysql.png) 

For the same ClickHouse cloud service running in `us-east-2`, but this time connected to an MySQL in GCP, the `Authorized networks` should look like this:

<img src={require("@site/docs/en/_snippets/images/gcp-authorized-network.png").default} style={{width:'40%', padding:'20px'}} alt="GCP Authorized networks"/>
