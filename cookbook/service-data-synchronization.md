# Service Data Synchronization

# Problem

A service that has data dependencies on another service can run into data synchronization issues when sporadic data refreshes occur across the services. This problem is compounded when refreshes happen inconsistently across service environments. This can affect any meaningful use of the service.

# Solution

Data refreshes should only be applied to a *ServiceDataSet*. A *ServiceDataSet* is an atomic unit of services that have dependent data.

-   Data refreshes must be automated and should only occur between the following environments
    -   *Production to Staging*
    -   *Production to UAT*
-   Data refreshes should not happen in a test environment. All tests should be responsible for creating their test data sets

# Discussion

The most important part of this solution is defining a ServiceDataSet. All refreshes should happen on a ServiceDataSet. This ensures that data is synch. It is also important that this process be automated so we guarantee that the refresh process is repeatable and consistent. A Service can only belong to one ServiceDataSet.

To limit the confusion that arises between refreshes across environments, limiting the refresh points from *Production to Staging* and *Production to UAT* is reasonable. Staging and UAT are environments that should mimic production like scenarios. This may be extended to the testing environment if need be.

The reason for not refreshing data into the test environment is because the test environment is only responsible for functional scope. All tests should be able to be run in isolation, which means that every test should be responsible for their test data. This eliminates the problem where testing is dependent on a data refresh.

*Contributors*

Itari Ighoroje

Mark Morga
