# API Service

| Category     | SLI | SLO                                                                                                         |
|--------------|-----|-------------------------------------------------------------------------------------------------------------|
| Availability | Percentage of total successful requests (status != 5xx) over 2 days | 99%                                                                                                         |
| Latency      | 90th percentile (request_duration_seconds over 5 minutes) | 90% of requests below 100ms                                                                                 |
| Error Budget | % remaining error budget = 1- % error used over 7 days | Error budget is defined at 20%. This means that 20% of the requests can fail and still be within the budget |
| Throughput   | Number of requests/second over 1 minute | 5 RPS indicates the application is functioning                                                              |
