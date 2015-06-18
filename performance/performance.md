# Performance

This section contains Performance related content.

# SLAs

1.  Service authors are required to define publish SLAs for their Services
2.  Service Standard SLA Response Times
    1.  If the Service can not support these standards, the exception should be documented and alternative implementations explored.
    2.  Fetch Record: 250 Miliseconds
    3.  Queries (Searches):
        1.  1 sec for results less than 5000
        2.  Greater than 5000 records or 1 Ms, use Pagination

    4.  Perform Operation: 2 Seconds

# Capacity and Scaling

1.  1.  Number of concurrent users
    2.  Number of transactions per minute/hour/day
    3.  Load levels that trigger a scaling operation (manual or automated)

# Availability

1.  24 / 7 - 99.5% Availability
2.  Ideal: Globablly Deployed / Minimum 2 Datacenters

3.  30 Minute Response time to Incidents

## Section Contents

-   [Caching](/pages/createpage.action?spaceKey=RISS&title=Caching&linkCreation=true&fromPageId=86213559)

-   [Zero Down Time](/pages/createpage.action?spaceKey=RISS&title=Zero+Down+Time&linkCreation=true&fromPageId=86213559)

    -   [Live Deployment](/pages/createpage.action?spaceKey=RISS&title=Live+Deployment&linkCreation=true&fromPageId=86213559)

    -   [Monitoring and Visibility](/pages/createpage.action?spaceKey=RISS&title=Monitoring+and+Visibility&linkCreation=true&fromPageId=86213559)
