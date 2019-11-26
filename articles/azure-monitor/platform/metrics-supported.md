---
title: Azure Monitor supported metrics by resource type
description: List of metrics available for each resource type with Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 30e2b880f32f896098778942deb67d7ced9f5c2d
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484212"
---
# <a name="supported-metrics-with-azure-monitor"></a>Supported metrics with Azure Monitor

Azure Monitor provides several ways to interact with metrics, including charting them in the portal, accessing them through the REST API, or querying them using PowerShell or CLI. Below is a complete list of all metrics currently available with Azure Monitor's metric pipeline. Other metrics may be available in the portal or using legacy APIs. This list below only includes metrics available using the consolidated Azure Monitor metric pipeline. To query for and access these metrics please use the [2018-01-01 api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Wysyłanie metryk wielowymiarowych za pomocą ustawień diagnostycznych nie jest obecnie obsługiwane. Metryki wielowymiarowe są eksportowane jako spłaszczone metryki jednowymiarowe z wartościami zagregowanymi we wszystkich wymiarach.
>
> *Na przykład*: metrykę „Komunikaty przychodzące” w centrum zdarzeń można przeglądać i przedstawiać na wykresie na poziomie pojedynczej kolejki. Jednak w przypadku eksportowania za pomocą ustawień diagnostycznych metryka ta jest przedstawiana jako wszystkie komunikaty przychodzące we wszystkich kolejkach w centrum zdarzeń.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|qpu_metric|QPU|Liczba|Średnia|QPU. Range 0-100 for S1, 0-200 for S2 and 0-400 for S4|ServerResourceType|
|memory_metric|Pamięć|Bytes|Średnia|Memory. Range 0-25 GB for S1, 0-50 GB for S2 and 0-100 GB for S4|ServerResourceType|
|private_bytes_metric|Private Bytes |Bytes|Średnia|The total amount of memory the Analysis Services engine process and Mashup container processes have allocated, not including memory shared with other processes.|ServerResourceType|
|virtual_bytes_metric|Virtual Bytes |Bytes|Średnia|The current size of the virtual address space that Analysis Services engine process and Mashup container processes are using.|ServerResourceType|
|TotalConnectionRequests|Total Connection Requests|Liczba|Średnia|Total connection requests. These are arrivals.|ServerResourceType|
|SuccessfullConnectionsPerSec|Successful Connections Per Sec|CountPerSecond|Średnia|Rate of successful connection completions.|ServerResourceType|
|TotalConnectionFailures|Total Connection Failures|Liczba|Średnia|Total failed connection attempts.|ServerResourceType|
|CurrentUserSessions|Current User Sessions|Liczba|Średnia|Current number of user sessions established.|ServerResourceType|
|QueryPoolBusyThreads|Query Pool Busy Threads|Liczba|Średnia|Number of busy threads in the query thread pool.|ServerResourceType|
|CommandPoolJobQueueLength|Command Pool Job Queue Length|Liczba|Średnia|Number of jobs in the queue of the command thread pool.|ServerResourceType|
|ProcessingPoolJobQueueLength|Processing Pool Job Queue Length|Liczba|Średnia|Number of non-I/O jobs in the queue of the processing thread pool.|ServerResourceType|
|CurrentConnections|Connection: Current connections|Liczba|Średnia|Current number of client connections established.|ServerResourceType|
|CleanerCurrentPrice|Memory: Cleaner Current Price|Liczba|Średnia|Current price of memory, $/byte/time, normalized to 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Memory: Cleaner Memory shrinkable|Bytes|Średnia|Amount of memory, in bytes, subject to purging by the background cleaner.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memory: Cleaner Memory nonshrinkable|Bytes|Średnia|Amount of memory, in bytes, not subject to purging by the background cleaner.|ServerResourceType|
|MemoryUsage|Memory: Memory Usage|Bytes|Średnia|Memory usage of the server process as used in calculating cleaner memory price. Equal to counter Process\PrivateBytes plus the size of memory-mapped data, ignoring any memory which was mapped or allocated by the xVelocity in-memory analytics engine (VertiPaq) in excess of the xVelocity engine Memory Limit.|ServerResourceType|
|MemoryLimitHard|Memory: Memory Limit Hard|Bytes|Średnia|Hard memory limit, from configuration file.|ServerResourceType|
|MemoryLimitHigh|Memory: Memory Limit High|Bytes|Średnia|High memory limit, from configuration file.|ServerResourceType|
|MemoryLimitLow|Memory: Memory Limit Low|Bytes|Średnia|Low memory limit, from configuration file.|ServerResourceType|
|MemoryLimitVertiPaq|Memory: Memory Limit VertiPaq|Bytes|Średnia|In-memory limit, from configuration file.|ServerResourceType|
|Przydział|Memory: Quota|Bytes|Średnia|Current memory quota, in bytes. Memory quota is also known as a memory grant or memory reservation.|ServerResourceType|
|QuotaBlocked|Memory: Quota Blocked|Liczba|Średnia|Current number of quota requests that are blocked until other memory quotas are freed.|ServerResourceType|
|VertiPaqNonpaged|Memory: VertiPaq Nonpaged|Bytes|Średnia|Bytes of memory locked in the working set for use by the in-memory engine.|ServerResourceType|
|VertiPaqPaged|Memory: VertiPaq Paged|Bytes|Średnia|Bytes of paged memory in use for in-memory data.|ServerResourceType|
|RowsReadPerSec|Processing: Rows read per sec|CountPerSecond|Średnia|Rate of rows read from all relational databases.|ServerResourceType|
|RowsConvertedPerSec|Processing: Rows converted per sec|CountPerSecond|Średnia|Rate of rows converted during processing.|ServerResourceType|
|RowsWrittenPerSec|Processing: Rows written per sec|CountPerSecond|Średnia|Rate of rows written during processing.|ServerResourceType|
|CommandPoolBusyThreads|Threads: Command pool busy threads|Liczba|Średnia|Number of busy threads in the command thread pool.|ServerResourceType|
|CommandPoolIdleThreads|Threads: Command pool idle threads|Liczba|Średnia|Number of idle threads in the command thread pool.|ServerResourceType|
|LongParsingBusyThreads|Threads: Long parsing busy threads|Liczba|Średnia|Number of busy threads in the long parsing thread pool.|ServerResourceType|
|LongParsingIdleThreads|Threads: Long parsing idle threads|Liczba|Średnia|Number of idle threads in the long parsing thread pool.|ServerResourceType|
|LongParsingJobQueueLength|Threads: Long parsing job queue length|Liczba|Średnia|Number of jobs in the queue of the long parsing thread pool.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Threads: Processing pool busy I/O job threads|Liczba|Średnia|Number of threads running I/O jobs in the processing thread pool.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Threads: Processing pool busy non-I/O threads|Liczba|Średnia|Number of threads running non-I/O jobs in the processing thread pool.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Threads: Processing pool I/O job queue length|Liczba|Średnia|Number of I/O jobs in the queue of the processing thread pool.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Threads: Processing pool idle I/O job threads|Liczba|Średnia|Number of idle threads for I/O jobs in the processing thread pool.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Threads: Processing pool idle non-I/O threads|Liczba|Średnia|Number of idle threads in the processing thread pool dedicated to non-I/O jobs.|ServerResourceType|
|QueryPoolIdleThreads|Threads: Query pool idle threads|Liczba|Średnia|Number of idle threads for I/O jobs in the processing thread pool.|ServerResourceType|
|QueryPoolJobQueueLength|Threads: Query pool job queue length|Liczba|Średnia|Number of jobs in the queue of the query thread pool.|ServerResourceType|
|ShortParsingBusyThreads|Threads: Short parsing busy threads|Liczba|Średnia|Number of busy threads in the short parsing thread pool.|ServerResourceType|
|ShortParsingIdleThreads|Threads: Short parsing idle threads|Liczba|Średnia|Number of idle threads in the short parsing thread pool.|ServerResourceType|
|ShortParsingJobQueueLength|Threads: Short parsing job queue length|Liczba|Średnia|Number of jobs in the queue of the short parsing thread pool.|ServerResourceType|
|memory_thrashing_metric|Memory Thrashing|Procent|Średnia|Average memory thrashing.|ServerResourceType|
|mashup_engine_qpu_metric|M Engine QPU|Liczba|Średnia|QPU usage by mashup engine processes|ServerResourceType|
|mashup_engine_memory_metric|M Engine Memory|Bytes|Średnia|Memory usage by mashup engine processes|ServerResourceType|
|mashup_engine_private_bytes_metric|M Engine Private Bytes |Bytes|Średnia|The total amount of memory Mashup container processes have allocated, not including memory shared with other processes.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M Engine Virtual Bytes |Bytes|Średnia|The current size of the virtual address space Mashup container processes are using.|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania|Żądania|Liczba|Łącznie|The total number of gateway requests in a given period. It can be sliced by various dimensions to help you diagnose issues. |Location, BackendResponseCode, LastErrorReason, GatewayResponseCode|
|TotalRequests|Total Gateway Requests|Liczba|Łącznie|The total number of gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric. |Location, Hostname|
|SuccessfulRequests|Successful Gateway Requests|Liczba|Łącznie|The total number of successful gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric.|Location, Hostname|
|UnauthorizedRequests|Unauthorized Gateway Requests|Liczba|Łącznie| The total number of unauthorized gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric.|Location, Hostname|
|FailedRequests|Failed Gateway Requests|Liczba|Łącznie|The total number of failed gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric.|Location, Hostname|
|OtherRequests|Other Gateway Requests|Liczba|Łącznie|The total number of gateway requests in a given period that do not fall into the successful, unauthorized, or failed categories. This metric has been deprecated, we recommend using the new `Requests` metric. |Location, Hostname|
|Czas trwania|Overall Duration of Gateway Requests|Milliseconds|Średnia|The time between when API Management receives a request from a client and when it returns a response to the client.|Location, Hostname|
|Pojemność|Pojemność|Procent|Średnia|Indicator of load on an API Management instance for making informed decisions whether to scale the instance to accommodate more load.|Lokalizacja|
|EventHubTotalEvents|Total EventHub Events|Liczba|Łącznie|The total number of events sent to EventHub from API Management in a given period.|Lokalizacja|
|EventHubSuccessfulEvents|Successful EventHub Events|Liczba|Łącznie|The total number of successful EventHub events in a given period.|Lokalizacja|
|EventHubTotalFailedEvents|Failed EventHub Events|Liczba|Łącznie|The total number of failed EventHub events in a given period.|Lokalizacja|
|EventHubRejectedEvents|Rejected EventHub Events|Liczba|Łącznie|The total number of rejected EventHub events (wrong configuration or unauthorized) in a given period.|Lokalizacja|
|EventHubThrottledEvents|Throttled EventHub Events|Liczba|Łącznie|The total number of throttled EventHub events in a given period.|Lokalizacja|
|EventHubTimedoutEvents|Timed Out EventHub Events|Liczba|Łącznie|The total number of timed out EventHub events in a given period.|Lokalizacja|
|EventHubDroppedEvents|Dropped EventHub Events|Liczba|Łącznie|The total number of events skipped because of queue size limit reached in a given period.|Lokalizacja|
|EventHubTotalBytesSent|Size of EventHub Events|Bytes|Łącznie|The total size of EventHub events in bytes in a given period.|Lokalizacja|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalJob|Total Jobs|Liczba|Łącznie|The total number of jobs|Runbook, Status|
|TotalUpdateDeploymentRuns|Total Update Deployment Runs|Liczba|Łącznie|Total software update deployment runs|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Total Update Deployment Machine Runs|Liczba|Łącznie|Total software update deployment machine runs in a software update deployment run|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|CoreCount|Dedicated Core Count|Liczba|Łącznie|Total number of dedicated cores in the batch account|No Dimensions|
|TotalNodeCount|Dedicated Node Count|Liczba|Łącznie|Total number of dedicated nodes in the batch account|No Dimensions|
|LowPriorityCoreCount|LowPriority Core Count|Liczba|Łącznie|Total number of low-priority cores in the batch account|No Dimensions|
|TotalLowPriorityNodeCount|Low-Priority Node Count|Liczba|Łącznie|Total number of low-priority nodes in the batch account|No Dimensions|
|CreatingNodeCount|Creating Node Count|Liczba|Łącznie|Number of nodes being created|No Dimensions|
|StartingNodeCount|Starting Node Count|Liczba|Łącznie|Number of nodes starting|No Dimensions|
|WaitingForStartTaskNodeCount|Waiting For Start Task Node Count|Liczba|Łącznie|Number of nodes waiting for the Start Task to complete|No Dimensions|
|StartTaskFailedNodeCount|Start Task Failed Node Count|Liczba|Łącznie|Number of nodes where the Start Task has failed|No Dimensions|
|IdleNodeCount|Idle Node Count|Liczba|Łącznie|Number of idle nodes|No Dimensions|
|OfflineNodeCount|Offline Node Count|Liczba|Łącznie|Number of offline nodes|No Dimensions|
|RebootingNodeCount|Rebooting Node Count|Liczba|Łącznie|Number of rebooting nodes|No Dimensions|
|ReimagingNodeCount|Reimaging Node Count|Liczba|Łącznie|Number of reimaging nodes|No Dimensions|
|RunningNodeCount|Running Node Count|Liczba|Łącznie|Number of running nodes|No Dimensions|
|LeavingPoolNodeCount|Leaving Pool Node Count|Liczba|Łącznie|Number of nodes leaving the Pool|No Dimensions|
|UnusableNodeCount|Unusable Node Count|Liczba|Łącznie|Number of unusable nodes|No Dimensions|
|PreemptedNodeCount|Preempted Node Count|Liczba|Łącznie|Number of preempted nodes|No Dimensions|
|TaskStartEvent|Task Start Events|Liczba|Łącznie|Total number of tasks that have started|No Dimensions|
|TaskCompleteEvent|Task Complete Events|Liczba|Łącznie|Total number of tasks that have completed|No Dimensions|
|TaskFailEvent|Task Fail Events|Liczba|Łącznie|Total number of tasks that have completed in a failed state|No Dimensions|
|PoolCreateEvent|Pool Create Events|Liczba|Łącznie|Total number of pools that have been created|No Dimensions|
|PoolResizeStartEvent|Pool Resize Start Events|Liczba|Łącznie|Total number of pool resizes that have started|No Dimensions|
|PoolResizeCompleteEvent|Pool Resize Complete Events|Liczba|Łącznie|Total number of pool resizes that have completed|No Dimensions|
|PoolDeleteStartEvent|Pool Delete Start Events|Liczba|Łącznie|Total number of pool deletes that have started|No Dimensions|
|PoolDeleteCompleteEvent|Pool Delete Complete Events|Liczba|Łącznie|Total number of pool deletes that have completed|No Dimensions|
|JobDeleteCompleteEvent|Job Delete Complete Events|Liczba|Łącznie|Total number of jobs that have been successfully deleted.|No Dimensions|
|JobDeleteStartEvent|Job Delete Start Events|Liczba|Łącznie|Total number of jobs that have been requested to be deleted.|No Dimensions|
|JobDisableCompleteEvent|Job Disable Complete Events|Liczba|Łącznie|Total number of jobs that have been successfully disabled.|No Dimensions|
|JobDisableStartEvent|Job Disable Start Events|Liczba|Łącznie|Total number of jobs that have been requested to be disabled.|No Dimensions|
|JobStartEvent|Job Start Events|Liczba|Łącznie|Total number of jobs that have been successfully started.|No Dimensions|
|JobTerminateCompleteEvent|Job Terminate Complete Events|Liczba|Łącznie|Total number of jobs that have been successfully terminated.|No Dimensions|
|JobTerminateStartEvent|Job Terminate Start Events|Liczba|Łącznie|Total number of jobs that have been requested to be terminated.|No Dimensions|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|connectedclients|Connected Clients|Liczba|Maksimum||ShardId|
|totalcommandsprocessed|Total Operations|Liczba|Łącznie||ShardId|
|cachehits|Cache Hits|Liczba|Łącznie||ShardId|
|cachemisses|Cache Misses|Liczba|Łącznie||ShardId|
|getcommands|Gets|Liczba|Łącznie||ShardId|
|setcommands|Sets|Liczba|Łącznie||ShardId|
|operationsPerSecond|Operations Per Second|Liczba|Maksimum||ShardId|
|evictedkeys|Evicted Keys|Liczba|Łącznie||ShardId|
|totalkeys|Total Keys|Liczba|Maksimum||ShardId|
|expiredkeys|Expired Keys|Liczba|Łącznie||ShardId|
|usedmemory|Used Memory|Bytes|Maksimum||ShardId|
|usedmemorypercentage|Used Memory Percentage|Procent|Maksimum||ShardId|
|usedmemoryRss|Used Memory RSS|Bytes|Maksimum||ShardId|
|serverLoad|Server Load|Procent|Maksimum||ShardId|
|cacheWrite|Cache Write|BytesPerSecond|Maksimum||ShardId|
|cacheRead|Cache Read|BytesPerSecond|Maksimum||ShardId|
|percentProcessorTime|Procesor CPU|Procent|Maksimum||ShardId|
|cacheLatency|Cache Latency Microseconds (Preview)|Liczba|Średnia||ShardId, SampleType|
|błędy|Błędy|Liczba|Maksimum||ShardId, ErrorType|
|connectedclients0|Connected Clients (Shard 0)|Liczba|Maksimum||No Dimensions|
|totalcommandsprocessed0|Total Operations (Shard 0)|Liczba|Łącznie||No Dimensions|
|cachehits0|Cache Hits (Shard 0)|Liczba|Łącznie||No Dimensions|
|cachemisses0|Cache Misses (Shard 0)|Liczba|Łącznie||No Dimensions|
|getcommands0|Gets (Shard 0)|Liczba|Łącznie||No Dimensions|
|setcommands0|Sets (Shard 0)|Liczba|Łącznie||No Dimensions|
|operationsPerSecond0|Operations Per Second (Shard 0)|Liczba|Maksimum||No Dimensions|
|evictedkeys0|Evicted Keys (Shard 0)|Liczba|Łącznie||No Dimensions|
|totalkeys0|Total Keys (Shard 0)|Liczba|Maksimum||No Dimensions|
|expiredkeys0|Expired Keys (Shard 0)|Liczba|Łącznie||No Dimensions|
|usedmemory0|Used Memory (Shard 0)|Bytes|Maksimum||No Dimensions|
|usedmemoryRss0|Used Memory RSS (Shard 0)|Bytes|Maksimum||No Dimensions|
|serverLoad0|Server Load (Shard 0)|Procent|Maksimum||No Dimensions|
|cacheWrite0|Cache Write (Shard 0)|BytesPerSecond|Maksimum||No Dimensions|
|cacheRead0|Cache Read (Shard 0)|BytesPerSecond|Maksimum||No Dimensions|
|percentProcessorTime0|CPU (Shard 0)|Procent|Maksimum||No Dimensions|
|connectedclients1|Connected Clients (Shard 1)|Liczba|Maksimum||No Dimensions|
|totalcommandsprocessed1|Total Operations (Shard 1)|Liczba|Łącznie||No Dimensions|
|cachehits1|Cache Hits (Shard 1)|Liczba|Łącznie||No Dimensions|
|cachemisses1|Cache Misses (Shard 1)|Liczba|Łącznie||No Dimensions|
|getcommands1|Gets (Shard 1)|Liczba|Łącznie||No Dimensions|
|setcommands1|Sets (Shard 1)|Liczba|Łącznie||No Dimensions|
|operationsPerSecond1|Operations Per Second (Shard 1)|Liczba|Maksimum||No Dimensions|
|evictedkeys1|Evicted Keys (Shard 1)|Liczba|Łącznie||No Dimensions|
|totalkeys1|Total Keys (Shard 1)|Liczba|Maksimum||No Dimensions|
|expiredkeys1|Expired Keys (Shard 1)|Liczba|Łącznie||No Dimensions|
|usedmemory1|Used Memory (Shard 1)|Bytes|Maksimum||No Dimensions|
|usedmemoryRss1|Used Memory RSS (Shard 1)|Bytes|Maksimum||No Dimensions|
|serverLoad1|Server Load (Shard 1)|Procent|Maksimum||No Dimensions|
|cacheWrite1|Cache Write (Shard 1)|BytesPerSecond|Maksimum||No Dimensions|
|cacheRead1|Cache Read (Shard 1)|BytesPerSecond|Maksimum||No Dimensions|
|percentProcessorTime1|CPU (Shard 1)|Procent|Maksimum||No Dimensions|
|connectedclients2|Connected Clients (Shard 2)|Liczba|Maksimum||No Dimensions|
|totalcommandsprocessed2|Total Operations (Shard 2)|Liczba|Łącznie||No Dimensions|
|cachehits2|Cache Hits (Shard 2)|Liczba|Łącznie||No Dimensions|
|cachemisses2|Cache Misses (Shard 2)|Liczba|Łącznie||No Dimensions|
|getcommands2|Gets (Shard 2)|Liczba|Łącznie||No Dimensions|
|setcommands2|Sets (Shard 2)|Liczba|Łącznie||No Dimensions|
|operationsPerSecond2|Operations Per Second (Shard 2)|Liczba|Maksimum||No Dimensions|
|evictedkeys2|Evicted Keys (Shard 2)|Liczba|Łącznie||No Dimensions|
|totalkeys2|Total Keys (Shard 2)|Liczba|Maksimum||No Dimensions|
|expiredkeys2|Expired Keys (Shard 2)|Liczba|Łącznie||No Dimensions|
|usedmemory2|Used Memory (Shard 2)|Bytes|Maksimum||No Dimensions|
|usedmemoryRss2|Used Memory RSS (Shard 2)|Bytes|Maksimum||No Dimensions|
|serverLoad2|Server Load (Shard 2)|Procent|Maksimum||No Dimensions|
|cacheWrite2|Cache Write (Shard 2)|BytesPerSecond|Maksimum||No Dimensions|
|cacheRead2|Cache Read (Shard 2)|BytesPerSecond|Maksimum||No Dimensions|
|percentProcessorTime2|CPU (Shard 2)|Procent|Maksimum||No Dimensions|
|connectedclients3|Connected Clients (Shard 3)|Liczba|Maksimum||No Dimensions|
|totalcommandsprocessed3|Total Operations (Shard 3)|Liczba|Łącznie||No Dimensions|
|cachehits3|Cache Hits (Shard 3)|Liczba|Łącznie||No Dimensions|
|cachemisses3|Cache Misses (Shard 3)|Liczba|Łącznie||No Dimensions|
|getcommands3|Gets (Shard 3)|Liczba|Łącznie||No Dimensions|
|setcommands3|Sets (Shard 3)|Liczba|Łącznie||No Dimensions|
|operationsPerSecond3|Operations Per Second (Shard 3)|Liczba|Maksimum||No Dimensions|
|evictedkeys3|Evicted Keys (Shard 3)|Liczba|Łącznie||No Dimensions|
|totalkeys3|Total Keys (Shard 3)|Liczba|Maksimum||No Dimensions|
|expiredkeys3|Expired Keys (Shard 3)|Liczba|Łącznie||No Dimensions|
|usedmemory3|Used Memory (Shard 3)|Bytes|Maksimum||No Dimensions|
|usedmemoryRss3|Used Memory RSS (Shard 3)|Bytes|Maksimum||No Dimensions|
|serverLoad3|Server Load (Shard 3)|Procent|Maksimum||No Dimensions|
|cacheWrite3|Cache Write (Shard 3)|BytesPerSecond|Maksimum||No Dimensions|
|cacheRead3|Cache Read (Shard 3)|BytesPerSecond|Maksimum||No Dimensions|
|percentProcessorTime3|CPU (Shard 3)|Procent|Maksimum||No Dimensions|
|connectedclients4|Connected Clients (Shard 4)|Liczba|Maksimum||No Dimensions|
|totalcommandsprocessed4|Total Operations (Shard 4)|Liczba|Łącznie||No Dimensions|
|cachehits4|Cache Hits (Shard 4)|Liczba|Łącznie||No Dimensions|
|cachemisses4|Cache Misses (Shard 4)|Liczba|Łącznie||No Dimensions|
|getcommands4|Gets (Shard 4)|Liczba|Łącznie||No Dimensions|
|setcommands4|Sets (Shard 4)|Liczba|Łącznie||No Dimensions|
|operationsPerSecond4|Operations Per Second (Shard 4)|Liczba|Maksimum||No Dimensions|
|evictedkeys4|Evicted Keys (Shard 4)|Liczba|Łącznie||No Dimensions|
|totalkeys4|Total Keys (Shard 4)|Liczba|Maksimum||No Dimensions|
|expiredkeys4|Expired Keys (Shard 4)|Liczba|Łącznie||No Dimensions|
|usedmemory4|Used Memory (Shard 4)|Bytes|Maksimum||No Dimensions|
|usedmemoryRss4|Used Memory RSS (Shard 4)|Bytes|Maksimum||No Dimensions|
|serverLoad4|Server Load (Shard 4)|Procent|Maksimum||No Dimensions|
|cacheWrite4|Cache Write (Shard 4)|BytesPerSecond|Maksimum||No Dimensions|
|cacheRead4|Cache Read (Shard 4)|BytesPerSecond|Maksimum||No Dimensions|
|percentProcessorTime4|CPU (Shard 4)|Procent|Maksimum||No Dimensions|
|connectedclients5|Connected Clients (Shard 5)|Liczba|Maksimum||No Dimensions|
|totalcommandsprocessed5|Total Operations (Shard 5)|Liczba|Łącznie||No Dimensions|
|cachehits5|Cache Hits (Shard 5)|Liczba|Łącznie||No Dimensions|
|cachemisses5|Cache Misses (Shard 5)|Liczba|Łącznie||No Dimensions|
|getcommands5|Gets (Shard 5)|Liczba|Łącznie||No Dimensions|
|setcommands5|Sets (Shard 5)|Liczba|Łącznie||No Dimensions|
|operationsPerSecond5|Operations Per Second (Shard 5)|Liczba|Maksimum||No Dimensions|
|evictedkeys5|Evicted Keys (Shard 5)|Liczba|Łącznie||No Dimensions|
|totalkeys5|Total Keys (Shard 5)|Liczba|Maksimum||No Dimensions|
|expiredkeys5|Expired Keys (Shard 5)|Liczba|Łącznie||No Dimensions|
|usedmemory5|Used Memory (Shard 5)|Bytes|Maksimum||No Dimensions|
|usedmemoryRss5|Used Memory RSS (Shard 5)|Bytes|Maksimum||No Dimensions|
|serverLoad5|Server Load (Shard 5)|Procent|Maksimum||No Dimensions|
|cacheWrite5|Cache Write (Shard 5)|BytesPerSecond|Maksimum||No Dimensions|
|cacheRead5|Cache Read (Shard 5)|BytesPerSecond|Maksimum||No Dimensions|
|percentProcessorTime5|CPU (Shard 5)|Procent|Maksimum||No Dimensions|
|connectedclients6|Connected Clients (Shard 6)|Liczba|Maksimum||No Dimensions|
|totalcommandsprocessed6|Total Operations (Shard 6)|Liczba|Łącznie||No Dimensions|
|cachehits6|Cache Hits (Shard 6)|Liczba|Łącznie||No Dimensions|
|cachemisses6|Cache Misses (Shard 6)|Liczba|Łącznie||No Dimensions|
|getcommands6|Gets (Shard 6)|Liczba|Łącznie||No Dimensions|
|setcommands6|Sets (Shard 6)|Liczba|Łącznie||No Dimensions|
|operationsPerSecond6|Operations Per Second (Shard 6)|Liczba|Maksimum||No Dimensions|
|evictedkeys6|Evicted Keys (Shard 6)|Liczba|Łącznie||No Dimensions|
|totalkeys6|Total Keys (Shard 6)|Liczba|Maksimum||No Dimensions|
|expiredkeys6|Expired Keys (Shard 6)|Liczba|Łącznie||No Dimensions|
|usedmemory6|Used Memory (Shard 6)|Bytes|Maksimum||No Dimensions|
|usedmemoryRss6|Used Memory RSS (Shard 6)|Bytes|Maksimum||No Dimensions|
|serverLoad6|Server Load (Shard 6)|Procent|Maksimum||No Dimensions|
|cacheWrite6|Cache Write (Shard 6)|BytesPerSecond|Maksimum||No Dimensions|
|cacheRead6|Cache Read (Shard 6)|BytesPerSecond|Maksimum||No Dimensions|
|percentProcessorTime6|CPU (Shard 6)|Procent|Maksimum||No Dimensions|
|connectedclients7|Connected Clients (Shard 7)|Liczba|Maksimum||No Dimensions|
|totalcommandsprocessed7|Total Operations (Shard 7)|Liczba|Łącznie||No Dimensions|
|cachehits7|Cache Hits (Shard 7)|Liczba|Łącznie||No Dimensions|
|cachemisses7|Cache Misses (Shard 7)|Liczba|Łącznie||No Dimensions|
|getcommands7|Gets (Shard 7)|Liczba|Łącznie||No Dimensions|
|setcommands7|Sets (Shard 7)|Liczba|Łącznie||No Dimensions|
|operationsPerSecond7|Operations Per Second (Shard 7)|Liczba|Maksimum||No Dimensions|
|evictedkeys7|Evicted Keys (Shard 7)|Liczba|Łącznie||No Dimensions|
|totalkeys7|Total Keys (Shard 7)|Liczba|Maksimum||No Dimensions|
|expiredkeys7|Expired Keys (Shard 7)|Liczba|Łącznie||No Dimensions|
|usedmemory7|Used Memory (Shard 7)|Bytes|Maksimum||No Dimensions|
|usedmemoryRss7|Used Memory RSS (Shard 7)|Bytes|Maksimum||No Dimensions|
|serverLoad7|Server Load (Shard 7)|Procent|Maksimum||No Dimensions|
|cacheWrite7|Cache Write (Shard 7)|BytesPerSecond|Maksimum||No Dimensions|
|cacheRead7|Cache Read (Shard 7)|BytesPerSecond|Maksimum||No Dimensions|
|percentProcessorTime7|CPU (Shard 7)|Procent|Maksimum||No Dimensions|
|connectedclients8|Connected Clients (Shard 8)|Liczba|Maksimum||No Dimensions|
|totalcommandsprocessed8|Total Operations (Shard 8)|Liczba|Łącznie||No Dimensions|
|cachehits8|Cache Hits (Shard 8)|Liczba|Łącznie||No Dimensions|
|cachemisses8|Cache Misses (Shard 8)|Liczba|Łącznie||No Dimensions|
|getcommands8|Gets (Shard 8)|Liczba|Łącznie||No Dimensions|
|setcommands8|Sets (Shard 8)|Liczba|Łącznie||No Dimensions|
|operationsPerSecond8|Operations Per Second (Shard 8)|Liczba|Maksimum||No Dimensions|
|evictedkeys8|Evicted Keys (Shard 8)|Liczba|Łącznie||No Dimensions|
|totalkeys8|Total Keys (Shard 8)|Liczba|Maksimum||No Dimensions|
|expiredkeys8|Expired Keys (Shard 8)|Liczba|Łącznie||No Dimensions|
|usedmemory8|Used Memory (Shard 8)|Bytes|Maksimum||No Dimensions|
|usedmemoryRss8|Used Memory RSS (Shard 8)|Bytes|Maksimum||No Dimensions|
|serverLoad8|Server Load (Shard 8)|Procent|Maksimum||No Dimensions|
|cacheWrite8|Cache Write (Shard 8)|BytesPerSecond|Maksimum||No Dimensions|
|cacheRead8|Cache Read (Shard 8)|BytesPerSecond|Maksimum||No Dimensions|
|percentProcessorTime8|CPU (Shard 8)|Procent|Maksimum||No Dimensions|
|connectedclients9|Connected Clients (Shard 9)|Liczba|Maksimum||No Dimensions|
|totalcommandsprocessed9|Total Operations (Shard 9)|Liczba|Łącznie||No Dimensions|
|cachehits9|Cache Hits (Shard 9)|Liczba|Łącznie||No Dimensions|
|cachemisses9|Cache Misses (Shard 9)|Liczba|Łącznie||No Dimensions|
|getcommands9|Gets (Shard 9)|Liczba|Łącznie||No Dimensions|
|setcommands9|Sets (Shard 9)|Liczba|Łącznie||No Dimensions|
|operationsPerSecond9|Operations Per Second (Shard 9)|Liczba|Maksimum||No Dimensions|
|evictedkeys9|Evicted Keys (Shard 9)|Liczba|Łącznie||No Dimensions|
|totalkeys9|Total Keys (Shard 9)|Liczba|Maksimum||No Dimensions|
|expiredkeys9|Expired Keys (Shard 9)|Liczba|Łącznie||No Dimensions|
|usedmemory9|Used Memory (Shard 9)|Bytes|Maksimum||No Dimensions|
|usedmemoryRss9|Used Memory RSS (Shard 9)|Bytes|Maksimum||No Dimensions|
|serverLoad9|Server Load (Shard 9)|Procent|Maksimum||No Dimensions|
|cacheWrite9|Cache Write (Shard 9)|BytesPerSecond|Maksimum||No Dimensions|
|cacheRead9|Cache Read (Shard 9)|BytesPerSecond|Maksimum||No Dimensions|
|percentProcessorTime9|CPU (Shard 9)|Procent|Maksimum||No Dimensions|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|The percentage of allocated compute units that are currently in use by the Virtual Machine(s).|No Dimensions|
|Sieć — wejście|Sieć — wejście|Bytes|Łącznie|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic).|No Dimensions|
|Sieć — wyjście|Sieć — wyjście|Bytes|Łącznie|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic).|No Dimensions|
|Disk Read Bytes/Sec|Disk Read|BytesPerSecond|Średnia|Average bytes read from disk during monitoring period.|No Dimensions|
|Disk Write Bytes/Sec|Disk Write|BytesPerSecond|Średnia|Average bytes written to disk during monitoring period.|No Dimensions|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Średnia|Disk Read IOPS.|No Dimensions|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Średnia|Disk Write IOPS.|No Dimensions|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|The percentage of allocated compute units that are currently in use by the Virtual Machine(s).|RoleInstanceId|
|Sieć — wejście|Sieć — wejście|Bytes|Łącznie|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic).|RoleInstanceId|
|Sieć — wyjście|Sieć — wyjście|Bytes|Łącznie|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic).|RoleInstanceId|
|Disk Read Bytes/Sec|Disk Read|BytesPerSecond|Średnia|Average bytes read from disk during monitoring period.|RoleInstanceId|
|Disk Write Bytes/Sec|Disk Write|BytesPerSecond|Średnia|Average bytes written to disk during monitoring period.|RoleInstanceId|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Średnia|Disk Read IOPS.|RoleInstanceId|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Średnia|Disk Write IOPS.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalCalls|Total Calls|Liczba|Łącznie|Total number of calls.|ApiName, OperationName, Region|
|SuccessfulCalls|Successful Calls|Liczba|Łącznie|Number of successful calls.|ApiName, OperationName, Region|
|TotalErrors|Total Errors|Liczba|Łącznie|Total number of calls with error response (HTTP response code 4xx or 5xx).|ApiName, OperationName, Region|
|BlockedCalls|Blocked Calls|Liczba|Łącznie|Number of calls that exceeded rate or quota limit.|ApiName, OperationName, Region|
|ServerErrors|Server Errors|Liczba|Łącznie|Number of calls with service internal error (HTTP response code 5xx).|ApiName, OperationName, Region|
|ClientErrors|Client Errors|Liczba|Łącznie|Number of calls with client side error (HTTP response code 4xx).|ApiName, OperationName, Region|
|DataIn|Data In|Bytes|Łącznie|Size of incoming data in bytes.|ApiName, OperationName, Region|
|DataOut|Data Out|Bytes|Łącznie|Size of outgoing data in bytes.|ApiName, OperationName, Region|
|Opóźnienie|Opóźnienie|MilliSeconds|Średnia|Latency in milliseconds.|ApiName, OperationName, Region|
|CharactersTranslated|Characters Translated|Liczba|Łącznie|Total number of characters in incoming text request.|ApiName, OperationName, Region|
|CharactersTrained|Characters Trained|Liczba|Łącznie|Total number of characters trained.|ApiName, OperationName, Region|
|SpeechSessionDuration|Speech Session Duration|Sekundy|Łącznie|Total duration of speech session in seconds.|ApiName, OperationName, Region|
|TotalTransactions|Total Transactions|Liczba|Łącznie|Total number of transactions.|No Dimensions|
|TotalTokenCalls|Total Token Calls|Liczba|Łącznie|Total number of token calls.|ApiName, OperationName, Region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|The percentage of allocated compute units that are currently in use by the Virtual Machine(s)|No Dimensions|
|Sieć — wejście|Network In Billable|Bytes|Łącznie|The number of billable bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|No Dimensions|
|Sieć — wyjście|Network Out Billable|Bytes|Łącznie|The number of billable bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|No Dimensions|
|Disk Read Bytes|Disk Read Bytes|Bytes|Łącznie|Bytes read from disk during monitoring period|No Dimensions|
|Disk Write Bytes|Disk Write Bytes|Bytes|Łącznie|Bytes written to disk during monitoring period|No Dimensions|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Średnia|Disk Read IOPS|No Dimensions|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Średnia|Disk Write IOPS|No Dimensions|
|CPU Credits Remaining|CPU Credits Remaining|Liczba|Średnia|Total number of credits available to burst|No Dimensions|
|CPU Credits Consumed|CPU Credits Consumed|Liczba|Średnia|Total number of credits consumed by the Virtual Machine|No Dimensions|
|Per Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Średnia|Bytes/Sec read from a single disk during monitoring period|SlotId|
|Per Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Średnia|Bytes/Sec written to a single disk during monitoring period|SlotId|
|Per Disk Read Operations/Sec|Data Disk Read Operations/Sec (Deprecated)|CountPerSecond|Średnia|Read IOPS from a single disk during monitoring period|SlotId|
|Per Disk Write Operations/Sec|Data Disk Write Operations/Sec (Deprecated)|CountPerSecond|Średnia|Write IOPS from a single disk during monitoring period|SlotId|
|Per Disk QD|Data Disk QD (Deprecated)|Liczba|Średnia|Data Disk Queue Depth(or Queue Length)|SlotId|
|OS Per Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Średnia|Bytes/Sec read from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Średnia|Bytes/Sec written to a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Read Operations/Sec|OS Disk Read Operations/Sec (Deprecated)|CountPerSecond|Średnia|Read IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Operations/Sec|OS Disk Write Operations/Sec (Deprecated)|CountPerSecond|Średnia|Write IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk QD|OS Disk QD (Deprecated)|Liczba|Średnia|OS Disk Queue Depth(or Queue Length)|No Dimensions|
|Data Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Preview)|CountPerSecond|Średnia|Bytes/Sec read from a single disk during monitoring period|LUN|
|Data Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Preview)|CountPerSecond|Średnia|Bytes/Sec written to a single disk during monitoring period|LUN|
|Data Disk Read Operations/Sec|Data Disk Read Operations/Sec (Preview)|CountPerSecond|Średnia|Read IOPS from a single disk during monitoring period|LUN|
|Data Disk Write Operations/Sec|Data Disk Write Operations/Sec (Preview)|CountPerSecond|Średnia|Write IOPS from a single disk during monitoring period|LUN|
|Data Disk Queue Depth|Data Disk Queue Depth (Preview)|Liczba|Średnia|Data Disk Queue Depth(or Queue Length)|LUN|
|OS Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Preview)|CountPerSecond|Średnia|Bytes/Sec read from a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Preview)|CountPerSecond|Średnia|Bytes/Sec written to a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Read Operations/Sec|OS Disk Read Operations/Sec (Preview)|CountPerSecond|Średnia|Read IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Write Operations/Sec|OS Disk Write Operations/Sec (Preview)|CountPerSecond|Średnia|Write IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Queue Depth|OS Disk Queue Depth (Preview)|Liczba|Średnia|OS Disk Queue Depth(or Queue Length)|No Dimensions|
|Inbound Flows|Inbound Flows (Preview)|Liczba|Średnia|Inbound Flows are number of current flows in the inbound direction (traffic going into the VM)|No Dimensions|
|Outbound Flows|Outbound Flows (Preview)|Liczba|Średnia|Outbound Flows are number of current flows in the outbound direction (traffic going out of the VM)|No Dimensions|
|Inbound Flows Maximum Creation Rate|Inbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Średnia|The maximum creation rate of inbound flows (traffic going into the VM)|No Dimensions|
|Outbound Flows Maximum Creation Rate|Outbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Średnia|The maximum creation rate of outbound flows (traffic going out of the VM)|No Dimensions|
|Premium Data Disk Cache Read Hit|Premium Data Disk Cache Read Hit (Preview)|Procent|Średnia|Premium Data Disk Cache Read Hit|LUN|
|Premium Data Disk Cache Read Miss|Premium Data Disk Cache Read Miss (Preview)|Procent|Średnia|Premium Data Disk Cache Read Miss|LUN|
|Premium OS Disk Cache Read Hit|Premium OS Disk Cache Read Hit (Preview)|Procent|Średnia|Premium OS Disk Cache Read Hit|No Dimensions|
|Premium OS Disk Cache Read Miss|Premium OS Disk Cache Read Miss (Preview)|Procent|Średnia|Premium OS Disk Cache Read Miss|No Dimensions|
|Network In Total|Network In Total|Bytes|Łącznie|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|No Dimensions|
|Network Out Total|Network Out Total|Bytes|Łącznie|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|No Dimensions|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|The percentage of allocated compute units that are currently in use by the Virtual Machine(s)|VMName|
|Sieć — wejście|Network In Billable|Bytes|Łącznie|The number of billable bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|VMName|
|Sieć — wyjście|Network Out Billable|Bytes|Łącznie|The number of billable bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|VMName|
|Disk Read Bytes|Disk Read Bytes|Bytes|Łącznie|Bytes read from disk during monitoring period|VMName|
|Disk Write Bytes|Disk Write Bytes|Bytes|Łącznie|Bytes written to disk during monitoring period|VMName|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Średnia|Disk Read IOPS|VMName|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Średnia|Disk Write IOPS|VMName|
|CPU Credits Remaining|CPU Credits Remaining|Liczba|Średnia|Total number of credits available to burst|No Dimensions|
|CPU Credits Consumed|CPU Credits Consumed|Liczba|Średnia|Total number of credits consumed by the Virtual Machine|No Dimensions|
|Per Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Średnia|Bytes/Sec read from a single disk during monitoring period|SlotId|
|Per Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Średnia|Bytes/Sec written to a single disk during monitoring period|SlotId|
|Per Disk Read Operations/Sec|Data Disk Read Operations/Sec (Deprecated)|CountPerSecond|Średnia|Read IOPS from a single disk during monitoring period|SlotId|
|Per Disk Write Operations/Sec|Data Disk Write Operations/Sec (Deprecated)|CountPerSecond|Średnia|Write IOPS from a single disk during monitoring period|SlotId|
|Per Disk QD|Data Disk QD (Deprecated)|Liczba|Średnia|Data Disk Queue Depth(or Queue Length)|SlotId|
|OS Per Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Średnia|Bytes/Sec read from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Średnia|Bytes/Sec written to a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Read Operations/Sec|OS Disk Read Operations/Sec (Deprecated)|CountPerSecond|Średnia|Read IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Operations/Sec|OS Disk Write Operations/Sec (Deprecated)|CountPerSecond|Średnia|Write IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk QD|OS Disk QD (Deprecated)|Liczba|Średnia|OS Disk Queue Depth(or Queue Length)|No Dimensions|
|Data Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Preview)|CountPerSecond|Średnia|Bytes/Sec read from a single disk during monitoring period|LUN, VMName|
|Data Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Preview)|CountPerSecond|Średnia|Bytes/Sec written to a single disk during monitoring period|LUN, VMName|
|Data Disk Read Operations/Sec|Data Disk Read Operations/Sec (Preview)|CountPerSecond|Średnia|Read IOPS from a single disk during monitoring period|LUN, VMName|
|Data Disk Write Operations/Sec|Data Disk Write Operations/Sec (Preview)|CountPerSecond|Średnia|Write IOPS from a single disk during monitoring period|LUN, VMName|
|Data Disk Queue Depth|Data Disk Queue Depth (Preview)|Liczba|Średnia|Data Disk Queue Depth(or Queue Length)|LUN, VMName|
|OS Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Preview)|CountPerSecond|Średnia|Bytes/Sec read from a single disk during monitoring period for OS disk|VMName|
|OS Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Preview)|CountPerSecond|Średnia|Bytes/Sec written to a single disk during monitoring period for OS disk|VMName|
|OS Disk Read Operations/Sec|OS Disk Read Operations/Sec (Preview)|CountPerSecond|Średnia|Read IOPS from a single disk during monitoring period for OS disk|VMName|
|OS Disk Write Operations/Sec|OS Disk Write Operations/Sec (Preview)|CountPerSecond|Średnia|Write IOPS from a single disk during monitoring period for OS disk|VMName|
|OS Disk Queue Depth|OS Disk Queue Depth (Preview)|Liczba|Średnia|OS Disk Queue Depth(or Queue Length)|VMName|
|Inbound Flows|Inbound Flows (Preview)|Liczba|Średnia|Inbound Flows are number of current flows in the inbound direction (traffic going into the VM)|VMName|
|Outbound Flows|Outbound Flows (Preview)|Liczba|Średnia|Outbound Flows are number of current flows in the outbound direction (traffic going out of the VM)|VMName|
|Inbound Flows Maximum Creation Rate|Inbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Średnia|The maximum creation rate of inbound flows (traffic going into the VM)|VMName|
|Outbound Flows Maximum Creation Rate|Outbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Średnia|The maximum creation rate of outbound flows (traffic going out of the VM)|VMName|
|Premium Data Disk Cache Read Hit|Premium Data Disk Cache Read Hit (Preview)|Procent|Średnia|Premium Data Disk Cache Read Hit|LUN, VMName|
|Premium Data Disk Cache Read Miss|Premium Data Disk Cache Read Miss (Preview)|Procent|Średnia|Premium Data Disk Cache Read Miss|LUN, VMName|
|Premium OS Disk Cache Read Hit|Premium OS Disk Cache Read Hit (Preview)|Procent|Średnia|Premium OS Disk Cache Read Hit|VMName|
|Premium OS Disk Cache Read Miss|Premium OS Disk Cache Read Miss (Preview)|Procent|Średnia|Premium OS Disk Cache Read Miss|VMName|
|Network In Total|Network In Total|Bytes|Łącznie|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|VMName|
|Network Out Total|Network Out Total|Bytes|Łącznie|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuUsage|CPU Usage|Liczba|Średnia|CPU usage on all cores in millicores.|containerName|
|MemoryUsage|Memory Usage|Bytes|Średnia|Total memory usage in byte.|containerName|
|NetworkBytesReceivedPerSecond|Network Bytes Received Per Second|Bytes|Średnia|The network bytes received per second.|No Dimensions|
|NetworkBytesTransmittedPerSecond|Network Bytes Transmitted Per Second|Bytes|Średnia|The network bytes transmitted per second.|No Dimensions|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalPullCount|Total Pull Count|Liczba|Średnia|Number of image pulls in total|No Dimensions|
|SuccessfulPullCount|Successful Pull Count|Liczba|Średnia|Number of successful image pulls|No Dimensions|
|TotalPushCount|Total Push Count|Liczba|Średnia|Number of image pushes in total|No Dimensions|
|SuccessfulPushCount|Successful Push Count|Liczba|Średnia|Number of successful image pushes|No Dimensions|
|RunDuration|Run Duration|Milliseconds|Łącznie|Run Duration in milliseconds|No Dimensions|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Total number of available cpu cores in a managed cluster|Liczba|Łącznie|Total number of available cpu cores in a managed cluster|No Dimensions|
|kube_node_status_allocatable_memory_bytes|Total amount of available memory in a managed cluster|Bytes|Łącznie|Total amount of available memory in a managed cluster|No Dimensions|
|kube_pod_status_ready|Number of pods in Ready state|Liczba|Łącznie|Number of pods in Ready state|namespace, pod|
|kube_node_status_condition|Statuses for various node conditions|Liczba|Łącznie|Statuses for various node conditions|condition, status, status2, node|
|kube_pod_status_phase|Number of pods by phase|Liczba|Łącznie|Number of pods by phase|phase, namespace, pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API Calls|Liczba|Łącznie||No Dimensions|
|DCIMappingImportOperationSuccessfulLines|Mapping Import Operation Successful Lines|Liczba|Łącznie||No Dimensions|
|DCIMappingImportOperationFailedLines|Mapping Import Operation Failed Lines|Liczba|Łącznie||No Dimensions|
|DCIMappingImportOperationTotalLines|Mapping Import Operation Total Lines|Liczba|Łącznie||No Dimensions|
|DCIMappingImportOperationRuntimeInSeconds|Mapping Import Operation Runtime In Seconds|Sekundy|Łącznie||No Dimensions|
|DCIOutboundProfileExportSucceeded|Outbound Profile Export Succeeded|Liczba|Łącznie||No Dimensions|
|DCIOutboundProfileExportFailed|Outbound Profile Export Failed|Liczba|Łącznie||No Dimensions|
|DCIOutboundProfileExportDuration|Outbound Profile Export Duration|Sekundy|Łącznie||No Dimensions|
|DCIOutboundKpiExportSucceeded|Outbound Kpi Export Succeeded|Liczba|Łącznie||No Dimensions|
|DCIOutboundKpiExportFailed|Outbound Kpi Export Failed|Liczba|Łącznie||No Dimensions|
|DCIOutboundKpiExportDuration|Outbound Kpi Export Duration|Sekundy|Łącznie||No Dimensions|
|DCIOutboundKpiExportStarted|Outbound Kpi Export Started|Sekundy|Łącznie||No Dimensions|
|DCIOutboundKpiRecordCount|Outbound Kpi Record Count|Sekundy|Łącznie||No Dimensions|
|DCIOutboundProfileExportCount|Outbound Profile Export Count|Sekundy|Łącznie||No Dimensions|
|DCIOutboundInitialProfileExportFailed|Outbound Initial Profile Export Failed|Sekundy|Łącznie||No Dimensions|
|DCIOutboundInitialProfileExportSucceeded|Outbound Initial Profile Export Succeeded|Sekundy|Łącznie||No Dimensions|
|DCIOutboundInitialKpiExportFailed|Outbound Initial Kpi Export Failed|Sekundy|Łącznie||No Dimensions|
|DCIOutboundInitialKpiExportSucceeded|Outbound Initial Kpi Export Succeeded|Sekundy|Łącznie||No Dimensions|
|DCIOutboundInitialProfileExportDurationInSeconds|Outbound Initial Profile Export Duration In Seconds|Sekundy|Łącznie||No Dimensions|
|AdlaJobForStandardKpiFailed|Adla Job For Standard Kpi Failed In Seconds|Sekundy|Łącznie||No Dimensions|
|AdlaJobForStandardKpiTimeOut|Adla Job For Standard Kpi TimeOut In Seconds|Sekundy|Łącznie||No Dimensions|
|AdlaJobForStandardKpiCompleted|Adla Job For Standard Kpi Completed In Seconds|Sekundy|Łącznie||No Dimensions|
|ImportASAValuesFailed|Import ASA Values Failed Count|Liczba|Łącznie||No Dimensions|
|ImportASAValuesSucceeded|Import ASA Values Succeeded Count|Liczba|Łącznie||No Dimensions|
|DCIProfilesCount|Profile Instance Count|Liczba|Last||No Dimensions|
|DCIInteractionsPerMonthCount|Interactions per Month Count|Liczba|Last||No Dimensions|
|DCIKpisCount|KPI Count|Liczba|Last||No Dimensions|
|DCISegmentsCount|Segment Count|Liczba|Last||No Dimensions|
|DCIPredictiveMatchPoliciesCount|Predictive Match Count|Liczba|Last||No Dimensions|
|DCIPredictionsCount|Prediction Count|Liczba|Last||No Dimensions|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|NICReadThroughput|Read Throughput (Network)|BytesPerSecond|Średnia|The read throughput of the network interface on the device in the reporting period for all volumes in the gateway.|InstanceName|
|NICWriteThroughput|Write Throughput (Network)|BytesPerSecond|Średnia|The write throughput of the network interface on the device in the reporting period for all volumes in the gateway.|InstanceName|
|CloudReadThroughputPerShare|Cloud Download Throughput (Share)|BytesPerSecond|Średnia|The download throughput to Azure from a share during the reporting period.|Udostępnianie|
|CloudUploadThroughputPerShare|Cloud Upload Throughput (Share)|BytesPerSecond|Średnia|The upload throughput to Azure from a share during the reporting period.|Udostępnianie|
|BytesUploadedToCloudPerShare|Cloud Bytes Uploaded (Share)|Bytes|Średnia|The total number of bytes that is uploaded to Azure from a share during the reporting period.|Udostępnianie|
|TotalCapacity|Total Capacity|Bytes|Średnia|Total Capacity|No Dimensions|
|AvailableCapacity|Available Capacity|Bytes|Średnia|The available capacity in bytes during the reporting period.|No Dimensions|
|CloudUploadThroughput|Cloud Upload Throughput|BytesPerSecond|Średnia|The cloud upload throughput  to Azure during the reporting period.|No Dimensions|
|CloudReadThroughput|Cloud Download Throughput|BytesPerSecond|Średnia|The cloud download throughput to Azure during the reporting period.|No Dimensions|
|BytesUploadedToCloud|Cloud Bytes Uploaded (Device)|Bytes|Średnia|The total number of bytes that is uploaded to Azure from a device during the reporting period.|No Dimensions|
|HyperVVirtualProcessorUtilization|Edge Compute - Percentage CPU|Procent|Średnia|Percent CPU Usage|InstanceName|
|HyperVMemoryUtilization|Edge Compute - Memory Usage|Procent|Średnia|Amount of RAM in Use|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|FailedRuns|Failed Runs|Liczba|Łącznie||pipelineName, activityName|
|SuccessfulRuns|Successful Runs|Liczba|Łącznie||pipelineName, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|PipelineFailedRuns|Failed pipeline runs metrics|Liczba|Łącznie||FailureType, Name|
|PipelineSucceededRuns|Succeeded pipeline runs metrics|Liczba|Łącznie||FailureType, Name|
|ActivityFailedRuns|Failed activity runs metrics|Liczba|Łącznie||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Succeeded activity runs metrics|Liczba|Łącznie||ActivityType, PipelineName, FailureType, Name|
|TriggerFailedRuns|Failed trigger runs metrics|Liczba|Łącznie||Name, FailureType|
|TriggerSucceededRuns|Succeeded trigger runs metrics|Liczba|Łącznie||Name, FailureType|
|IntegrationRuntimeCpuPercentage|Integration runtime CPU utilization|Procent|Średnia||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Integration runtime available memory|Bytes|Średnia||IntegrationRuntimeName, NodeName|
|MaxAllowedResourceCount|Maximum allowed entities count|Liczba|Maksimum||No Dimensions|
|MaxAllowedFactorySizeInGbUnits|Maximum allowed factory size (GB unit)|Liczba|Maksimum||No Dimensions|
|ResourceCount|Total entities count|Liczba|Maksimum||No Dimensions|
|FactorySizeInGbUnits|Total factory size (GB unit)|Liczba|Maksimum||No Dimensions|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|JobEndedSuccess|Successful Jobs|Liczba|Łącznie|Count of successful jobs.|No Dimensions|
|JobEndedFailure|Failed Jobs|Liczba|Łącznie|Count of failed jobs.|No Dimensions|
|JobEndedCancelled|Canceled Jobs|Liczba|Łącznie|Count of canceled jobs.|No Dimensions|
|JobAUEndedSuccess|Successful AU Time|Sekundy|Łącznie|Total AU time for successful jobs.|No Dimensions|
|JobAUEndedFailure|Failed AU Time|Sekundy|Łącznie|Total AU time for failed jobs.|No Dimensions|
|JobAUEndedCancelled|Canceled AU Time|Sekundy|Łącznie|Total AU time for canceled jobs.|No Dimensions|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|TotalStorage|Całkowita ilość miejsca do magazynowania|Bytes|Maksimum|Total amount of data stored in the account.|No Dimensions|
|DataWritten|Data Written|Bytes|Łącznie|Total amount of data written to the account.|No Dimensions|
|DataRead|Data Read|Bytes|Łącznie|Total amount of data read from the account.|No Dimensions|
|WriteRequests|Write Requests|Liczba|Łącznie|Count of data write requests to the account.|No Dimensions|
|ReadRequests|Read Requests|Liczba|Łącznie|Count of data read requests to the account.|No Dimensions|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Procent|Średnia|CPU percent|No Dimensions|
|memory_percent|Memory percent|Procent|Średnia|Memory percent|No Dimensions|
|io_consumption_percent|IO percent|Procent|Średnia|IO percent|No Dimensions|
|storage_percent|Storage percent|Procent|Średnia|Storage percent|No Dimensions|
|storage_used|Użyty magazyn|Bytes|Średnia|Użyty magazyn|No Dimensions|
|storage_limit|Storage limit|Bytes|Średnia|Storage limit|No Dimensions|
|serverlog_storage_percent|Server Log storage percent|Procent|Średnia|Server Log storage percent|No Dimensions|
|serverlog_storage_usage|Server Log storage used|Bytes|Średnia|Server Log storage used|No Dimensions|
|serverlog_storage_limit|Server Log storage limit|Bytes|Średnia|Server Log storage limit|No Dimensions|
|active_connections|Active Connections|Liczba|Średnia|Active Connections|No Dimensions|
|connections_failed|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|No Dimensions|
|seconds_behind_master|Replication lag in seconds|Liczba|Średnia|Replication lag in seconds|No Dimensions|
|backup_storage_used|Backup Storage used|Bytes|Średnia|Backup Storage used|No Dimensions|
|network_bytes_egress|Sieć — wyjście|Bytes|Łącznie|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Sieć — wejście|Bytes|Łącznie|Network In across active connections|No Dimensions|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Procent|Średnia|CPU percent|No Dimensions|
|memory_percent|Memory percent|Procent|Średnia|Memory percent|No Dimensions|
|io_consumption_percent|IO percent|Procent|Średnia|IO percent|No Dimensions|
|storage_percent|Storage percent|Procent|Średnia|Storage percent|No Dimensions|
|storage_used|Użyty magazyn|Bytes|Średnia|Użyty magazyn|No Dimensions|
|storage_limit|Storage limit|Bytes|Średnia|Storage limit|No Dimensions|
|serverlog_storage_percent|Server Log storage percent|Procent|Średnia|Server Log storage percent|No Dimensions|
|serverlog_storage_usage|Server Log storage used|Bytes|Średnia|Server Log storage used|No Dimensions|
|serverlog_storage_limit|Server Log storage limit|Bytes|Średnia|Server Log storage limit|No Dimensions|
|active_connections|Active Connections|Liczba|Średnia|Active Connections|No Dimensions|
|connections_failed|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|No Dimensions|
|seconds_behind_master|Replication lag in seconds|Liczba|Średnia|Replication lag in seconds|No Dimensions|
|backup_storage_used|Backup Storage used|Bytes|Średnia|Backup Storage used|No Dimensions|
|network_bytes_egress|Sieć — wyjście|Bytes|Łącznie|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Sieć — wejście|Bytes|Łącznie|Network In across active connections|No Dimensions|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Procent|Średnia|CPU percent|No Dimensions|
|memory_percent|Memory percent|Procent|Średnia|Memory percent|No Dimensions|
|io_consumption_percent|IO percent|Procent|Średnia|IO percent|No Dimensions|
|storage_percent|Storage percent|Procent|Średnia|Storage percent|No Dimensions|
|storage_used|Użyty magazyn|Bytes|Średnia|Użyty magazyn|No Dimensions|
|storage_limit|Storage limit|Bytes|Średnia|Storage limit|No Dimensions|
|serverlog_storage_percent|Server Log storage percent|Procent|Średnia|Server Log storage percent|No Dimensions|
|serverlog_storage_usage|Server Log storage used|Bytes|Średnia|Server Log storage used|No Dimensions|
|serverlog_storage_limit|Server Log storage limit|Bytes|Średnia|Server Log storage limit|No Dimensions|
|active_connections|Active Connections|Liczba|Średnia|Active Connections|No Dimensions|
|connections_failed|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|No Dimensions|
|backup_storage_used|Backup Storage used|Bytes|Średnia|Backup Storage used|No Dimensions|
|network_bytes_egress|Sieć — wyjście|Bytes|Łącznie|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Sieć — wejście|Bytes|Łącznie|Network In across active connections|No Dimensions|
|pg_replica_log_delay_in_seconds|Replica Lag|Sekundy|Maksimum|Replica lag in seconds|No Dimensions|
|pg_replica_log_delay_in_bytes|Max Lag Across Replicas|Bytes|Maksimum|Lag in bytes of the most lagging replica|No Dimensions|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Procent|Średnia|CPU percent|No Dimensions|
|memory_percent|Memory percent|Procent|Średnia|Memory percent|No Dimensions|
|iops|Operacje wejścia/wyjścia|Liczba|Średnia|IO Operations per second|No Dimensions|
|storage_percent|Storage percent|Procent|Średnia|Storage percent|No Dimensions|
|storage_used|Użyty magazyn|Bytes|Średnia|Użyty magazyn|No Dimensions|
|active_connections|Active Connections|Liczba|Średnia|Active Connections|No Dimensions|
|network_bytes_egress|Sieć — wyjście|Bytes|Łącznie|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Sieć — wejście|Bytes|Łącznie|Network In across active connections|No Dimensions|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Telemetry message send attempts|Liczba|Łącznie|Number of device-to-cloud telemetry messages attempted to be sent to your IoT hub|No Dimensions|
|d2c.telemetry.ingress.success|Telemetry messages sent|Liczba|Łącznie|Number of device-to-cloud telemetry messages sent successfully to your IoT hub|No Dimensions|
|c2d.commands.egress.complete.success|Commands completed|Liczba|Łącznie|Number of cloud-to-device commands completed successfully by the device|No Dimensions|
|c2d.commands.egress.abandon.success|Commands abandoned|Liczba|Łącznie|Number of cloud-to-device commands abandoned by the device|No Dimensions|
|c2d.commands.egress.reject.success|Commands rejected|Liczba|Łącznie|Number of cloud-to-device commands rejected by the device|No Dimensions|
|devices.totalDevices|Total devices (deprecated)|Liczba|Łącznie|Number of devices registered to your IoT hub|No Dimensions|
|devices.connectedDevices.allProtocol|Connected devices (deprecated) |Liczba|Łącznie|Number of devices connected to your IoT hub|No Dimensions|
|d2c.telemetry.egress.success|Routing: telemetry messages delivered|Liczba|Łącznie|The number of times messages were successfully delivered to all endpoints using IoT Hub routing. If a message is routed to multiple endpoints, this value increases by one for each successful delivery. If a message is delivered to the same endpoint multiple times, this value increases by one for each successful delivery.|No Dimensions|
|d2c.telemetry.egress.dropped|Routing: telemetry messages dropped |Liczba|Łącznie|The number of times messages were dropped by IoT Hub routing due to dead endpoints. This value does not count messages delivered to fallback route as dropped messages are not delivered there.|No Dimensions|
|d2c.telemetry.egress.orphaned|Routing: telemetry messages orphaned |Liczba|Łącznie|The number of times messages were orphaned by IoT Hub routing because they didn't match any routing rules (including the fallback rule). |No Dimensions|
|d2c.telemetry.egress.invalid|Routing: telemetry messages incompatible|Liczba|Łącznie|The number of times IoT Hub routing failed to deliver messages due to an incompatibility with the endpoint. This value does not include retries.|No Dimensions|
|d2c.telemetry.egress.fallback|Routing: messages delivered to fallback|Liczba|Łącznie|The number of times IoT Hub routing delivered messages to the endpoint associated with the fallback route.|No Dimensions|
|d2c.endpoints.egress.eventHubs|Routing: messages delivered to Event Hub|Liczba|Łącznie|The number of times IoT Hub routing successfully delivered messages to Event Hub endpoints.|No Dimensions|
|d2c.endpoints.latency.eventHubs|Routing: message latency for Event Hub|Milliseconds|Średnia|The average latency (milliseconds) between message ingress to IoT Hub and message ingress into an Event Hub endpoint.|No Dimensions|
|d2c.endpoints.egress.serviceBusQueues|Routing: messages delivered to Service Bus Queue|Liczba|Łącznie|The number of times IoT Hub routing successfully delivered messages to Service Bus queue endpoints.|No Dimensions|
|d2c.endpoints.latency.serviceBusQueues|Routing: message latency for Service Bus Queue|Milliseconds|Średnia|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into a Service Bus queue endpoint.|No Dimensions|
|d2c.endpoints.egress.serviceBusTopics|Routing: messages delivered to Service Bus Topic|Liczba|Łącznie|The number of times IoT Hub routing successfully delivered messages to Service Bus topic endpoints.|No Dimensions|
|d2c.endpoints.latency.serviceBusTopics|Routing: message latency for Service Bus Topic|Milliseconds|Średnia|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into a Service Bus topic endpoint.|No Dimensions|
|d2c.endpoints.egress.builtIn.events|Routing: messages delivered to messages/events|Liczba|Łącznie|The number of times IoT Hub routing successfully delivered messages to the built-in endpoint (messages/events). This metric only starts working when routing is enabled (https://aka.ms/iotrouting) for the IoT hub.|No Dimensions|
|d2c.endpoints.latency.builtIn.events|Routing: message latency for messages/events|Milliseconds|Średnia|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into the built-in endpoint (messages/events). This metric only starts working when routing is enabled (https://aka.ms/iotrouting) for the IoT hub.|No Dimensions|
|d2c.endpoints.egress.storage|Routing: messages delivered to storage|Liczba|Łącznie|The number of times IoT Hub routing successfully delivered messages to storage endpoints.|No Dimensions|
|d2c.endpoints.latency.storage|Routing: message latency for storage|Milliseconds|Średnia|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into a storage endpoint.|No Dimensions|
|d2c.endpoints.egress.storage.bytes|Routing: data delivered to storage|Bytes|Łącznie|The amount of data (bytes) IoT Hub routing delivered to storage endpoints.|No Dimensions|
|d2c.endpoints.egress.storage.blobs|Routing: blobs delivered to storage|Liczba|Łącznie|The number of times IoT Hub routing delivered blobs to storage endpoints.|No Dimensions|
|EventGridDeliveries|Event Grid deliveries (preview)|Liczba|Łącznie|The number of IoT Hub events published to Event Grid. Use the Result dimension for the number of successful and failed requests. EventType dimension shows the type of event (https://aka.ms/ioteventgrid).|Result, EventType|
|EventGridLatency|The average latency (milliseconds) from when the Iot Hub event was generated to when the event was published to Event Grid. This number is an average between all event types. Use the EventType dimension to see latency of a specific type of event.|EventType|
|d2c.twin.read.success|Successful twin reads from devices|Liczba|Łącznie|The count of all successful device-initiated twin reads.|No Dimensions|
|d2c.twin.read.failure|Failed twin reads from devices|Liczba|Łącznie|The count of all failed device-initiated twin reads.|No Dimensions|
|d2c.twin.read.size|Response size of twin reads from devices|Bytes|Średnia|The average, min, and max of all successful device-initiated twin reads.|No Dimensions|
|d2c.twin.update.success|Successful twin updates from devices|Liczba|Łącznie|The count of all successful device-initiated twin updates.|No Dimensions|
|d2c.twin.update.failure|Failed twin updates from devices|Liczba|Łącznie|The count of all failed device-initiated twin updates.|No Dimensions|
|d2c.twin.update.size|Size of twin updates from devices|Bytes|Średnia|The average, min, and max size of all successful device-initiated twin updates.|No Dimensions|
|c2d.methods.success|Successful direct method invocations|Liczba|Łącznie|The count of all successful direct method calls.|No Dimensions|
|c2d.methods.failure|Failed direct method invocations|Liczba|Łącznie|The count of all failed direct method calls.|No Dimensions|
|c2d.methods.requestSize|Request size of direct method invocations|Bytes|Średnia|The average, min, and max of all successful direct method requests.|No Dimensions|
|c2d.methods.responseSize|Response size of direct method invocations|Bytes|Średnia|The average, min, and max of all successful direct method responses.|No Dimensions|
|c2d.twin.read.success|Successful twin reads from back end|Liczba|Łącznie|The count of all successful back-end-initiated twin reads.|No Dimensions|
|c2d.twin.read.failure|Failed twin reads from back end|Liczba|Łącznie|The count of all failed back-end-initiated twin reads.|No Dimensions|
|c2d.twin.read.size|Response size of twin reads from back end|Bytes|Średnia|The average, min, and max of all successful back-end-initiated twin reads.|No Dimensions|
|c2d.twin.update.success|Successful twin updates from back end|Liczba|Łącznie|The count of all successful back-end-initiated twin updates.|No Dimensions|
|c2d.twin.update.failure|Failed twin updates from back end|Liczba|Łącznie|The count of all failed back-end-initiated twin updates.|No Dimensions|
|c2d.twin.update.size|Size of twin updates from back end|Bytes|Średnia|The average, min, and max size of all successful back-end-initiated twin updates.|No Dimensions|
|twinQueries.success|Successful twin queries|Liczba|Łącznie|The count of all successful twin queries.|No Dimensions|
|twinQueries.failure|Failed twin queries|Liczba|Łącznie|The count of all failed twin queries.|No Dimensions|
|twinQueries.resultSize|Twin queries result size|Bytes|Średnia|The average, min, and max of the result size of all successful twin queries.|No Dimensions|
|jobs.createTwinUpdateJob.success|Successful creations of twin update jobs|Liczba|Łącznie|The count of all successful creation of twin update jobs.|No Dimensions|
|jobs.createTwinUpdateJob.failure|Failed creations of twin update jobs|Liczba|Łącznie|The count of all failed creation of twin update jobs.|No Dimensions|
|jobs.createDirectMethodJob.success|Successful creations of method invocation jobs|Liczba|Łącznie|The count of all successful creation of direct method invocation jobs.|No Dimensions|
|jobs.createDirectMethodJob.failure|Failed creations of method invocation jobs|Liczba|Łącznie|The count of all failed creation of direct method invocation jobs.|No Dimensions|
|jobs.listJobs.success|Successful calls to list jobs|Liczba|Łącznie|The count of all successful calls to list jobs.|No Dimensions|
|jobs.listJobs.failure|Failed calls to list jobs|Liczba|Łącznie|The count of all failed calls to list jobs.|No Dimensions|
|jobs.cancelJob.success|Successful job cancellations|Liczba|Łącznie|The count of all successful calls to cancel a job.|No Dimensions|
|jobs.cancelJob.failure|Failed job cancellations|Liczba|Łącznie|The count of all failed calls to cancel a job.|No Dimensions|
|jobs.queryJobs.success|Successful job queries|Liczba|Łącznie|The count of all successful calls to query jobs.|No Dimensions|
|jobs.queryJobs.failure|Failed job queries|Liczba|Łącznie|The count of all failed calls to query jobs.|No Dimensions|
|jobs.completed|Completed jobs|Liczba|Łącznie|The count of all completed jobs.|No Dimensions|
|jobs.failed|Failed jobs|Liczba|Łącznie|The count of all failed jobs.|No Dimensions|
|d2c.telemetry.ingress.sendThrottle|Number of throttling errors|Liczba|Łącznie|Number of throttling errors due to device throughput throttles|No Dimensions|
|dailyMessageQuotaUsed|Total number of messages used|Liczba|Średnia|Number of total messages used today. This is a cumulative value that is reset to zero at 00:00 UTC every day.|No Dimensions|
|deviceDataUsage|Total device data usage|Bytes|Łącznie|Bytes transferred to and from any devices connected to IotHub|No Dimensions|
|totalDeviceCount|Total devices (preview)|Liczba|Średnia|Number of devices registered to your IoT hub|No Dimensions|
|connectedDeviceCount|Connected devices (preview)|Liczba|Średnia|Number of devices connected to your IoT hub|No Dimensions|
|configurations|Configuration Metrics|Liczba|Łącznie|Metrics for Configuration Operations|No Dimensions|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|RegistrationAttempts|Registration attempts|Liczba|Łącznie|Number of device registrations attempted|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Devices assigned|Liczba|Łącznie|Number of devices assigned to an IoT hub|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Attestation attempts|Liczba|Łącznie|Number of device attestations attempted|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|AvailableStorage|Available Storage|Bytes|Łącznie|Total available storage reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|CassandraConnectionClosures|Cassandra Connection Closures|Liczba|Łącznie|Number of Cassandra connections that were closed, reported at a 1 minute granularity|Region, ClosureReason|
|CassandraRequestCharges|Cassandra Request Charges|Liczba|Łącznie|RUs consumed for Cassandra requests made|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Cassandra Requests|Liczba|Liczba|Number of Cassandra requests made|DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|DataUsage|Użycie danych|Bytes|Łącznie|Total data usage reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|DocumentCount|Document Count|Liczba|Łącznie|Total document count reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|DocumentQuota|Document Quota|Bytes|Łącznie|Total storage quota reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|IndexUsage|Index Usage|Bytes|Łącznie|Total index usage reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|MetadataRequests|Metadata Requests|Liczba|Liczba|Count of metadata requests. Cosmos DB maintains system metadata collection for each account, that allows you to enumerate collections, databases, etc., and their configurations, free of charge.|DatabaseName, CollectionName, Region, StatusCode, |
|MongoRequestCharge|Mongo Request Charge|Liczba|Łącznie|Mongo Request Units Consumed|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Mongo Requests|Liczba|Liczba|Number of Mongo Requests Made|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|ProvisionedThroughput|Aprowizowana przepływność|Liczba|Maksimum|Aprowizowana przepływność|DatabaseName, CollectionName|
|ReplicationLatency|P99 Replication Latency|MilliSeconds|Średnia|P99 Replication Latency across source and target regions for geo-enabled account|SourceRegion, TargetRegion|
|ServiceAvailability|Service Availability|Procent|Średnia|Account requests availability at one hour, day or month granularity|No Dimensions|
|TotalRequestUnits|Total Request Units|Liczba|Łącznie|Request Units consumed|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|TotalRequests|Łączna liczba żądań|Liczba|Liczba|Number of requests made|DatabaseName, CollectionName, Region, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events|Liczba|Łącznie|Total events published to this topic|No Dimensions|
|PublishFailCount|Publish Failed Events|Liczba|Łącznie|Total events failed to publish to this topic|ErrorType, Error|
|UnmatchedEventCount|Unmatched Events|Liczba|Łącznie|Total events not matching any of the event subscriptions for this topic|No Dimensions|
|PublishSuccessLatencyInMs|Publish Success Latency|Liczba|Łącznie|Publish success latency in milliseconds|No Dimensions|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|MatchedEventCount|Matched Events|Liczba|Łącznie|Total events matched to this event subscription|No Dimensions|
|DeliveryAttemptFailCount|Delivery Failed Events|Liczba|Łącznie|Total events failed to deliver to this event subscription|Error, ErrorType|
|DeliverySuccessCount|Delivered Events|Liczba|Łącznie|Total events delivered to this event subscription|No Dimensions|
|DestinationProcessingDurationInMs|Destination Processing Duration|Milliseconds|Średnia|Destination processing duration in milliseconds|No Dimensions|
|DroppedEventCount|Dropped Events|Liczba|Łącznie|Total dropped events matching to this event subscription|DropReason|
|DeadLetteredCount|Dead Lettered Events|Liczba|Łącznie|Total dead lettered events matching to this event subscription|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events|Liczba|Łącznie|Total events published to this topic|No Dimensions|
|PublishFailCount|Failed Events|Liczba|Łącznie|Total events failed to publish to this topic|ErrorType, Error|
|UnmatchedEventCount|Unmatched Events|Liczba|Łącznie|Total events not matching any of the event subscriptions for this topic|No Dimensions|
|PublishSuccessLatencyInMs|Publish Success Latency|Liczba|Łącznie|Publish success latency in milliseconds|No Dimensions|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|SuccessfulRequests|Successful Requests|Liczba|Łącznie|Successful Requests for Microsoft.EventHub.|EntityName, |
|ServerErrors|Server Errors.|Liczba|Łącznie|Server Errors for Microsoft.EventHub.|EntityName, |
|UserErrors|User Errors.|Liczba|Łącznie|User Errors for Microsoft.EventHub.|EntityName, |
|QuotaExceededErrors|Quota Exceeded Errors.|Liczba|Łącznie|Quota Exceeded Errors for Microsoft.EventHub.|EntityName, |
|ThrottledRequests|Throttled Requests.|Liczba|Łącznie|Throttled Requests for Microsoft.EventHub.|EntityName, |
|IncomingRequests|Incoming Requests|Liczba|Łącznie|Incoming Requests for Microsoft.EventHub.|EntityName|
|IncomingMessages|Incoming Messages|Liczba|Łącznie|Incoming Messages for Microsoft.EventHub.|EntityName|
|OutgoingMessages|Outgoing Messages|Liczba|Łącznie|Outgoing Messages for Microsoft.EventHub.|EntityName|
|IncomingBytes|Incoming Bytes.|Bytes|Łącznie|Incoming Bytes for Microsoft.EventHub.|EntityName|
|OutgoingBytes|Outgoing Bytes.|Bytes|Łącznie|Outgoing Bytes for Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Liczba|Średnia|Total Active Connections for Microsoft.EventHub.|No Dimensions|
|ConnectionsOpened|Connections Opened.|Liczba|Średnia|Connections Opened for Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Connections Closed.|Liczba|Średnia|Connections Closed for Microsoft.EventHub.|EntityName|
|CaptureBacklog|Capture Backlog.|Liczba|Łącznie|Capture Backlog for Microsoft.EventHub.|EntityName|
|CapturedMessages|Captured Messages.|Liczba|Łącznie|Captured Messages for Microsoft.EventHub.|EntityName|
|CapturedBytes|Captured Bytes.|Bytes|Łącznie|Captured Bytes for Microsoft.EventHub.|EntityName|
|Rozmiar|Rozmiar|Bytes|Średnia|Size of an EventHub in Bytes.|EntityName|
|INREQS|Incoming Requests (Deprecated)|Liczba|Łącznie|Total incoming send requests for a namespace (Deprecated)|No Dimensions|
|SUCCREQ|Successful Requests (Deprecated)|Liczba|Łącznie|Total successful requests for a namespace (Deprecated)|No Dimensions|
|FAILREQ|Failed Requests (Deprecated)|Liczba|Łącznie|Total failed requests for a namespace (Deprecated)|No Dimensions|
|SVRBSY|Server Busy Errors (Deprecated)|Liczba|Łącznie|Total server busy errors for a namespace (Deprecated)|No Dimensions|
|INTERR|Internal Server Errors (Deprecated)|Liczba|Łącznie|Total internal server errors for a namespace (Deprecated)|No Dimensions|
|MISCERR|Other Errors (Deprecated)|Liczba|Łącznie|Total failed requests for a namespace (Deprecated)|No Dimensions|
|INMSGS|Incoming Messages (Deprecated) (Deprecated)|Liczba|Łącznie|Total incoming messages for a namespace. This metric is deprecated. Please use Incoming Messages metric instead (Deprecated)|No Dimensions|
|EHINMSGS|Incoming Messages (Deprecated)|Liczba|Łącznie|Total incoming messages for a namespace (Deprecated)|No Dimensions|
|OUTMSGS|Outgoing Messages (Deprecated) (Deprecated)|Liczba|Łącznie|Total outgoing messages for a namespace. This metric is deprecated. Please use Outgoing Messages metric instead (Deprecated)|No Dimensions|
|EHOUTMSGS|Outgoing Messages (Deprecated)|Liczba|Łącznie|Total outgoing messages for a namespace (Deprecated)|No Dimensions|
|EHINMBS|Incoming bytes (Deprecated) (Deprecated)|Bytes|Łącznie|Event Hub incoming message throughput for a namespace. This metric is deprecated. Please use Incoming bytes metric instead (Deprecated)|No Dimensions|
|EHINBYTES|Incoming bytes (Deprecated)|Bytes|Łącznie|Event Hub incoming message throughput for a namespace (Deprecated)|No Dimensions|
|EHOUTMBS|Outgoing bytes (Deprecated) (Deprecated)|Bytes|Łącznie|Event Hub outgoing message throughput for a namespace. This metric is deprecated. Please use Outgoing bytes metric instead (Deprecated)|No Dimensions|
|EHOUTBYTES|Outgoing bytes (Deprecated)|Bytes|Łącznie|Event Hub outgoing message throughput for a namespace (Deprecated)|No Dimensions|
|EHABL|Archive backlog messages (Deprecated)|Liczba|Łącznie|Event Hub archive messages in backlog for a namespace (Deprecated)|No Dimensions|
|EHAMSGS|Archive messages (Deprecated)|Liczba|Łącznie|Event Hub archived messages in a namespace (Deprecated)|No Dimensions|
|EHAMBS|Archive message throughput (Deprecated)|Bytes|Łącznie|Event Hub archived message throughput in a namespace (Deprecated)|No Dimensions|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|SuccessfulRequests|Successful Requests (Preview)|Liczba|Łącznie|Successful Requests for Microsoft.EventHub. (Wersja zapoznawcza)|No Dimensions|
|ServerErrors|Server Errors. (Wersja zapoznawcza)|Liczba|Łącznie|Server Errors for Microsoft.EventHub. (Wersja zapoznawcza)|No Dimensions|
|UserErrors|User Errors. (Wersja zapoznawcza)|Liczba|Łącznie|User Errors for Microsoft.EventHub. (Wersja zapoznawcza)|No Dimensions|
|QuotaExceededErrors|Quota Exceeded Errors. (Wersja zapoznawcza)|Liczba|Łącznie|Quota Exceeded Errors for Microsoft.EventHub. (Wersja zapoznawcza)|No Dimensions|
|ThrottledRequests|Throttled Requests. (Wersja zapoznawcza)|Liczba|Łącznie|Throttled Requests for Microsoft.EventHub. (Wersja zapoznawcza)|No Dimensions|
|IncomingRequests|Incoming Requests (Preview)|Liczba|Łącznie|Incoming Requests for Microsoft.EventHub. (Wersja zapoznawcza)|No Dimensions|
|IncomingMessages|Incoming Messages (Preview)|Liczba|Łącznie|Incoming Messages for Microsoft.EventHub. (Wersja zapoznawcza)|No Dimensions|
|OutgoingMessages|Outgoing Messages (Preview)|Liczba|Łącznie|Outgoing Messages for Microsoft.EventHub. (Wersja zapoznawcza)|No Dimensions|
|IncomingBytes|Incoming Bytes. (Wersja zapoznawcza)|Bytes|Łącznie|Incoming Bytes for Microsoft.EventHub. (Wersja zapoznawcza)|No Dimensions|
|OutgoingBytes|Outgoing Bytes. (Wersja zapoznawcza)|Bytes|Łącznie|Outgoing Bytes for Microsoft.EventHub. (Wersja zapoznawcza)|No Dimensions|
|ActiveConnections|ActiveConnections (Preview)|Liczba|Średnia|Total Active Connections for Microsoft.EventHub. (Wersja zapoznawcza)|No Dimensions|
|ConnectionsOpened|Connections Opened. (Wersja zapoznawcza)|Liczba|Średnia|Connections Opened for Microsoft.EventHub. (Wersja zapoznawcza)|No Dimensions|
|ConnectionsClosed|Connections Closed. (Wersja zapoznawcza)|Liczba|Średnia|Connections Closed for Microsoft.EventHub. (Wersja zapoznawcza)|No Dimensions|
|CaptureBacklog|Capture Backlog. (Wersja zapoznawcza)|Liczba|Łącznie|Capture Backlog for Microsoft.EventHub. (Wersja zapoznawcza)|No Dimensions|
|CapturedMessages|Captured Messages. (Wersja zapoznawcza)|Liczba|Łącznie|Captured Messages for Microsoft.EventHub. (Wersja zapoznawcza)|No Dimensions|
|CapturedBytes|Captured Bytes. (Wersja zapoznawcza)|Bytes|Łącznie|Captured Bytes for Microsoft.EventHub. (Wersja zapoznawcza)|No Dimensions|
|Procesor CPU|CPU (Preview)|Procent|Maksimum|CPU utilization for the Event Hub Cluster as a percentage|Rola|
|AvailableMemory|Available Memory (Preview)|Liczba|Maksimum|Available memory for the Event Hub Cluster in bytes|Rola|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|GatewayRequests|Gateway Requests|Liczba|Łącznie|Number of gateway requests|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Categorized Gateway Requests|Liczba|Łącznie|Number of gateway requests by categories (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Number of Active Workers|Liczba|Maksimum|Number of Active Workers|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|ObservedMetricValue|Observed Metric Value|Liczba|Średnia|The value computed by autoscale when executed|MetricTriggerSource|
|MetricThreshold|Metric Threshold|Liczba|Średnia|The configured autoscale threshold when autoscale ran.|MetricTriggerRule|
|ObservedCapacity|Observed Capacity|Liczba|Średnia|The capacity reported to autoscale when it executed.|No Dimensions|
|ScaleActionsInitiated|Scale Actions Initiated|Liczba|Łącznie|The direction of the scale operation.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Public Preview)

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Dostępność|Procent|Średnia|Percentage of successfully completed availability tests|availabilityResult/name, availabilityResult/location|
|availabilityResults/count|Testy dostępności|Liczba|Liczba|Count of availability tests|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|Availability test duration|MilliSeconds|Średnia|Availability test duration|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|Page load network connect time|MilliSeconds|Średnia|Time between user request and network connection. Includes DNS lookup and transport connection.|No Dimensions|
|browserTimings/processingDuration|Client processing time|MilliSeconds|Średnia|Time between receiving the last byte of a document until the DOM is loaded. Async requests may still be processing.|No Dimensions|
|browserTimings/receiveDuration|Receiving response time|MilliSeconds|Średnia|Time between the first and last bytes, or until disconnection.|No Dimensions|
|browserTimings/sendDuration|Send request time|MilliSeconds|Średnia|Time between network connection and receiving the first byte.|No Dimensions|
|browserTimings/totalDuration|Browser page load time|MilliSeconds|Średnia|Time from user request until DOM, stylesheets, scripts and images are loaded.|No Dimensions|
|dependencies/count|Dependency calls|Liczba|Liczba|Count of calls made by the application to external resources.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/duration|Dependency duration|MilliSeconds|Średnia|Duration of calls made by the application to external resources.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/failed|Dependency call failures|Liczba|Liczba|Count of failed dependency calls made by the application to external resources.|dependency/type, dependency/performanceBucket, operation/synthetic, cloud/roleInstance, cloud/roleName|
|pageViews/count|Page views|Liczba|Liczba|Count of page views.|operation/synthetic|
|pageViews/duration|Page view load time|MilliSeconds|Średnia|Page view load time|operation/synthetic|
|performanceCounters/requestExecutionTime|HTTP request execution time|MilliSeconds|Średnia|Execution time of the most recent request.|cloud/roleInstance|
|performanceCounters/requestsInQueue|HTTP requests in application queue|Liczba|Średnia|Length of the application request queue.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|HTTP request rate|CountPerSecond|Średnia|Rate of all requests to the application per second from ASP.NET.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Exception rate|CountPerSecond|Średnia|Count of handled and unhandled exceptions reported to windows, including .NET exceptions and unmanaged exceptions that are converted into .NET exceptions.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Process IO rate|BytesPerSecond|Średnia|Total bytes per second read and written to files, network and devices.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Process CPU|Procent|Średnia|The percentage of elapsed time that all process threads used the processor to execute instructions. This can vary between 0 to 100. This metric indicates the performance of w3wp process alone.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Processor time|Procent|Średnia|The percentage of time that the processor spends in non-idle threads.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Available memory|Bytes|Średnia|Physical memory immediately available for allocation to a process or for system use.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Process private bytes|Bytes|Średnia|Memory exclusively assigned to the monitored application's processes.|cloud/roleInstance|
|requests/duration|Server response time|MilliSeconds|Średnia|Time between receiving an HTTP request and finishing sending the response.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/count|Server requests|Liczba|Liczba|Count of HTTP requests completed.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|Żądania zakończone niepowodzeniem|Liczba|Liczba|Count of HTTP requests marked as failed. In most cases these are requests with a response code >= 400 and not equal to 401.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|requests/rate|Server request rate|CountPerSecond|Średnia|Rate of server requests per second|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|exceptions/count|Wyjątki|Liczba|Liczba|Combined count of all uncaught exceptions.|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/browser|Wyjątki przeglądarki|Liczba|Liczba|Count of uncaught exceptions thrown in the browser.|No Dimensions|
|exceptions/server|Server exceptions|Liczba|Liczba|Count of uncaught exceptions thrown in the server application.|cloud/roleName, cloud/roleInstance|
|traces/count|Traces|Liczba|Liczba|Trace document count|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|ServiceApiHit|Total Service Api Hits|Liczba|Liczba|Number of total service api hits|ActivityType, ActivityName|
|ServiceApiLatency|Overall Service Api Latency|Milliseconds|Średnia|Overall latency of service api requests|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Total Service Api Results|Liczba|Liczba|Number of total service api results|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|CacheUtilization|Cache Utilization|Procent|Średnia|Utilization level in the cluster scope|Brak|
|QueryDuration|Query Duration|Milliseconds|Średnia|Queries’ duration in seconds|Query Status|
|IngestionUtilization|Ingestion Utilization|Procent|Średnia|Ratio of used ingestion slots in the cluster|Brak|
|KeepAlive|Keep Alive|Liczba|Średnia|Sanity check indicates the cluster responds to queries|Brak|
|IngestionVolumeInMB|Ingestion Volume (In MB)|Liczba|Łącznie|Overall volume of ingested data to the cluster (in MB)|Database (Baza danych)|
|IngestionLatencyInSeconds|Ingestion Latency (In seconds)|Sekundy|Średnia|Ingestion time from the source (e.g. message is in EventHub) to the cluster in seconds|Brak|
|EventProcessedForEventHubs|Events Processed (for Event Hubs)|Liczba|Łącznie|Number of events processed by the cluster when ingesting from Event Hub|Brak|
|IngestionResult|Ingestion Result|Liczba|Liczba|Number of ingestion operations|Stan|
|Procesor CPU|Procesor CPU|Procent|Średnia|CPU utilization level|Brak|
| ContinuousExportNumOfRecordsExported | Number of records exported in continuous export | Liczba | Łącznie | Number of records exported for every storage artifact written during the export operation  | Brak |
| ExportUtilization | Export Utilization | Procent | Maksimum | Export utilization | Brak |
| ContinuousExportPendingCount | Continuous Export Pending Count | Liczba | Maksimum | The number of pending continuous export jobs ready for execution | Brak |
| ContinuousExportMaxLatenessMinutes | Continuous Export Max Lateness Minutes | Liczba | Maksimum | The max time in minutes of all continuous exports which are pending and ready for execution | Brak |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|Użycie|Użycie|Liczba|Liczba|Count of API calls|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Liczba|Łącznie|Number of workflow runs started.|No Dimensions|
|RunsCompleted|Runs Completed|Liczba|Łącznie|Number of workflow runs completed.|No Dimensions|
|RunsSucceeded|Runs Succeeded|Liczba|Łącznie|Number of workflow runs succeeded.|No Dimensions|
|RunsFailed|Runs Failed|Liczba|Łącznie|Number of workflow runs failed.|No Dimensions|
|RunsCancelled|Runs Canceled|Liczba|Łącznie|Number of workflow runs canceled.|No Dimensions|
|RunLatency|Run Latency|Sekundy|Średnia|Latency of completed workflow runs.|No Dimensions|
|RunSuccessLatency|Run Success Latency|Sekundy|Średnia|Latency of succeeded workflow runs.|No Dimensions|
|RunThrottledEvents|Run Throttled Events|Liczba|Łącznie|Number of workflow action or trigger throttled events.|No Dimensions|
|RunFailurePercentage|Run Failure Percentage|Procent|Łącznie|Percentage of workflow runs failed.|No Dimensions|
|ActionsStarted|Actions Started |Liczba|Łącznie|Number of workflow actions started.|No Dimensions|
|ActionsCompleted|Actions Completed |Liczba|Łącznie|Number of workflow actions completed.|No Dimensions|
|ActionsSucceeded|Actions Succeeded |Liczba|Łącznie|Number of workflow actions succeeded.|No Dimensions|
|ActionsFailed|Actions Failed|Liczba|Łącznie|Number of workflow actions failed.|No Dimensions|
|ActionsSkipped|Actions Skipped |Liczba|Łącznie|Number of workflow actions skipped.|No Dimensions|
|ActionLatency|Action Latency |Sekundy|Średnia|Latency of completed workflow actions.|No Dimensions|
|ActionSuccessLatency|Action Success Latency |Sekundy|Średnia|Latency of succeeded workflow actions.|No Dimensions|
|ActionThrottledEvents|Action Throttled Events|Liczba|Łącznie|Number of workflow action throttled events..|No Dimensions|
|TriggersStarted|Triggers Started |Liczba|Łącznie|Number of workflow triggers started.|No Dimensions|
|TriggersCompleted|Triggers Completed |Liczba|Łącznie|Number of workflow triggers completed.|No Dimensions|
|TriggersSucceeded|Triggers Succeeded |Liczba|Łącznie|Number of workflow triggers succeeded.|No Dimensions|
|TriggersFailed|Triggers Failed |Liczba|Łącznie|Number of workflow triggers failed.|No Dimensions|
|TriggersSkipped|Triggers Skipped|Liczba|Łącznie|Number of workflow triggers skipped.|No Dimensions|
|TriggersFired|Triggers Fired |Liczba|Łącznie|Number of workflow triggers fired.|No Dimensions|
|TriggerLatency|Trigger Latency |Sekundy|Średnia|Latency of completed workflow triggers.|No Dimensions|
|TriggerFireLatency|Trigger Fire Latency |Sekundy|Średnia|Latency of fired workflow triggers.|No Dimensions|
|TriggerSuccessLatency|Trigger Success Latency |Sekundy|Średnia|Latency of succeeded workflow triggers.|No Dimensions|
|TriggerThrottledEvents|Trigger Throttled Events|Liczba|Łącznie|Number of workflow trigger throttled events.|No Dimensions|
|BillableActionExecutions|Billable Action Executions|Liczba|Łącznie|Number of workflow action executions getting billed.|No Dimensions|
|BillableTriggerExecutions|Billable Trigger Executions|Liczba|Łącznie|Number of workflow trigger executions getting billed.|No Dimensions|
|TotalBillableExecutions|Total Billable Executions|Liczba|Łącznie|Number of workflow executions getting billed.|No Dimensions|
|BillingUsageNativeOperation|Billing Usage for Native Operation Executions|Liczba|Łącznie|Number of native operation executions getting billed.|No Dimensions|
|BillingUsageStandardConnector|Billing Usage for Standard Connector Executions|Liczba|Łącznie|Number of standard connector executions getting billed.|No Dimensions|
|BillingUsageStorageConsumption|Billing Usage for Storage Consumption Executions|Liczba|Łącznie|Number of storage consumption executions getting billed.|No Dimensions|
|BillingUsageNativeOperation|Billing Usage for Native Operation Executions|Liczba|Łącznie|Number of native operation executions getting billed.|No Dimensions|
|BillingUsageStandardConnector|Billing Usage for Standard Connector Executions|Liczba|Łącznie|Number of standard connector executions getting billed.|No Dimensions|
|BillingUsageStorageConsumption|Billing Usage for Storage Consumption Executions|Liczba|Łącznie|Number of storage consumption executions getting billed.|No Dimensions|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Liczba|Łącznie|Number of workflow runs started.|No Dimensions|
|RunsCompleted|Runs Completed|Liczba|Łącznie|Number of workflow runs completed.|No Dimensions|
|RunsSucceeded|Runs Succeeded|Liczba|Łącznie|Number of workflow runs succeeded.|No Dimensions|
|RunsFailed|Runs Failed|Liczba|Łącznie|Number of workflow runs failed.|No Dimensions|
|RunsCancelled|Runs Canceled|Liczba|Łącznie|Number of workflow runs canceled.|No Dimensions|
|RunLatency|Run Latency|Sekundy|Średnia|Latency of completed workflow runs.|No Dimensions|
|RunSuccessLatency|Run Success Latency|Sekundy|Średnia|Latency of succeeded workflow runs.|No Dimensions|
|RunThrottledEvents|Run Throttled Events|Liczba|Łącznie|Number of workflow action or trigger throttled events.|No Dimensions|
|RunStartThrottledEvents|Run Start Throttled Events|Liczba|Łącznie|Number of workflow run start throttled events.|No Dimensions|
|RunFailurePercentage|Run Failure Percentage|Procent|Łącznie|Percentage of workflow runs failed.|No Dimensions|
|ActionsStarted|Actions Started |Liczba|Łącznie|Number of workflow actions started.|No Dimensions|
|ActionsCompleted|Actions Completed |Liczba|Łącznie|Number of workflow actions completed.|No Dimensions|
|ActionsSucceeded|Actions Succeeded |Liczba|Łącznie|Number of workflow actions succeeded.|No Dimensions|
|ActionsFailed|Actions Failed |Liczba|Łącznie|Number of workflow actions failed.|No Dimensions|
|ActionsSkipped|Actions Skipped |Liczba|Łącznie|Number of workflow actions skipped.|No Dimensions|
|ActionLatency|Action Latency |Sekundy|Średnia|Latency of completed workflow actions.|No Dimensions|
|ActionSuccessLatency|Action Success Latency |Sekundy|Średnia|Latency of succeeded workflow actions.|No Dimensions|
|ActionThrottledEvents|Action Throttled Events|Liczba|Łącznie|Number of workflow action throttled events..|No Dimensions|
|TriggersStarted|Triggers Started |Liczba|Łącznie|Number of workflow triggers started.|No Dimensions|
|TriggersCompleted|Triggers Completed |Liczba|Łącznie|Number of workflow triggers completed.|No Dimensions|
|TriggersSucceeded|Triggers Succeeded |Liczba|Łącznie|Number of workflow triggers succeeded.|No Dimensions|
|TriggersFailed|Triggers Failed |Liczba|Łącznie|Number of workflow triggers failed.|No Dimensions|
|TriggersSkipped|Triggers Skipped|Liczba|Łącznie|Number of workflow triggers skipped.|No Dimensions|
|TriggersFired|Triggers Fired |Liczba|Łącznie|Number of workflow triggers fired.|No Dimensions|
|TriggerLatency|Trigger Latency |Sekundy|Średnia|Latency of completed workflow triggers.|No Dimensions|
|TriggerFireLatency|Trigger Fire Latency |Sekundy|Średnia|Latency of fired workflow triggers.|No Dimensions|
|TriggerSuccessLatency|Trigger Success Latency |Sekundy|Średnia|Latency of succeeded workflow triggers.|No Dimensions|
|TriggerThrottledEvents|Trigger Throttled Events|Liczba|Łącznie|Number of workflow trigger throttled events.|No Dimensions|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Workflow Processor Usage for Integration Service Environment|Procent|Średnia|Workflow processor usage for integration service environment.|No Dimensions|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Workflow Memory Usage for Integration Service Environment|Procent|Średnia|Workflow memory usage for integration service environment.|No Dimensions|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Connector Processor Usage for Integration Service Environment|Procent|Średnia|Connector processor usage for integration service environment.|No Dimensions|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Connector Memory Usage for Integration Service Environment|Procent|Średnia|Connector memory usage for integration service environment.|No Dimensions|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|Completed Runs|Completed Runs|Liczba|Łącznie|Number of runs completed successfully for this workspace|Scenariusz|
|Started Runs|Started Runs|Liczba|Łącznie|Number of runs started for this workspace|Scenariusz|
|Failed Runs|Failed Runs|Liczba|Łącznie|Number of runs failed for this workspace|Scenariusz|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|Użycie|Użycie|Liczba|Liczba|Count of API calls|ApiCategory, ApiName, ResultType, ResponseCode|
|Dostępność|Dostępność|Procent|Średnia|Availability of the APIs|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|AverageOtherLatency|Average other latency|ms/op|Średnia|Average other latency (that is not read or write) in milliseconds per operation|No Dimensions|
|AverageReadLatency|Average read latency|ms/op|Średnia|Average read latency in milliseconds per operation|No Dimensions|
|AverageTotalLatency|Average total latency|ms/op|Średnia|Average total latency in milliseconds per operation|No Dimensions|
|AverageWriteLatency|Average write latency|ms/op|Średnia|Average write latency in milliseconds per operation|No Dimensions|
|FilesystemOtherOps|Filesystem other ops|ops|Średnia|Number of filesystem other operations (that is not read or write)|No Dimensions|
|FilesystemReadOps|Filesystem read ops|ops|Średnia|Number of filesystem read operations|No Dimensions|
|FilesystemTotalOps|Filesystem total ops|ops|Średnia|Sum of all filesystem operations|No Dimensions|
|FilesystemWriteOps|Filesystem write ops|ops|Średnia|Number of filesystem write operations|No Dimensions|
|IoBytesPerOtherOps|Io bytes per other ops|bytes/op|Średnia|Number of In/out bytes per other operations (that is not read or write)|No Dimensions|
|IoBytesPerReadOps|Io bytes per read ops|bytes/op|Średnia|Number of In/out bytes per read operation|No Dimensions|
|IoBytesPerTotalOps|Io bytes per op across all operations|bytes/op|Średnia|Sum of all In/out bytes operation|No Dimensions|
|IoBytesPerWriteOps|Io bytes per write ops|bytes/op|Średnia|Number of In/out bytes per write operation|No Dimensions|
|OtherIops|Other iops|operations/second|Średnia|Other In/out operation per second|No Dimensions|
|OtherThroughput|Other throughput|MBps|Średnia|Other throughput (that is not read or write) in megabytes per second|No Dimensions|
|ReadIops|Read iops|operations/second|Średnia|Read In/out operations per second|No Dimensions|
|ReadThroughput|Read throughput|MBps|Średnia|Read throughput in megabytes per second|No Dimensions|
|TotalIops|Total iops|operations/second|Średnia|Sum of all In/out operations per second|No Dimensions|
|TotalThroughput|Total throughput|MBps|Średnia|Sum of all throughput in megabytes per second|No Dimensions|
|VolumeAllocatedSize|Volume allocated size|bytes|Średnia|Allocated size of the volume (Not the actual used bytes)|No Dimensions|
|VolumeLogicalSize|Volume logical size|bytes|Średnia|Logical size of the volume (used bytes)|No Dimensions|
|VolumeSnapshotSize|Volume snapshot size|bytes|Średnia|Size of all snapshots in volume|No Dimensions|
|WriteIops|Write iops|operations/second|Średnia|Write In/out operations per second|No Dimensions|
|WriteThroughput|Write throughput|MBps|Średnia|Write throughput in megabytes per second|No Dimensions|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Volume pool allocated size|bytes|Średnia|Allocated size of the pool (Not the actual used bytes)|No Dimensions|
|VolumePoolAllocatedUsed|Volume pool allocated used|bytes|Średnia|Allocated used size of the pool|No Dimensions|
|VolumePoolTotalLogicalSize|Volume pool total logical size|bytes|Średnia|Sum of the logical size of all the volumes belonging to the pool|No Dimensions|
|VolumePoolTotalSnapshotSize|Volume pool total snapshot size|bytes|Średnia|Sum of all snapshots in pool|No Dimensions|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|BytesSentRate|Bytes Sent|Liczba|Łącznie|Number of bytes the Network Interface sent|No Dimensions|
|BytesReceivedRate|Bytes Received|Liczba|Łącznie|Number of bytes the Network Interface received|No Dimensions|
|PacketsSentRate|Packets Sent|Liczba|Łącznie|Number of packets the Network Interface sent|No Dimensions|
|PacketsReceivedRate|Packets Received|Liczba|Łącznie|Number of packets the Network Interface received|No Dimensions|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|VipAvailability|Data Path Availability|Liczba|Średnia|Average Load Balancer data path availability per time duration|FrontendIPAddress, FrontendPort|
|DipAvailability|Health Probe Status|Liczba|Średnia|Average Load Balancer health probe status per time duration|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Byte Count|Liczba|Łącznie|Total number of Bytes transmitted within time period|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Packet Count|Liczba|Łącznie|Total number of Packets transmitted within time period|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|SYN Count|Liczba|Łącznie|Total number of SYN Packets transmitted within time period|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|SNAT Connection Count|Liczba|Łącznie|Total number of new SNAT connections created within time period|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Allocated SNAT Ports (Preview)|Liczba|Łącznie|Total number of SNAT ports allocated within time period|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Used SNAT Ports (Preview)|Liczba|Łącznie|Total number of SNAT ports used within time period|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|QueryVolume|Query Volume|Liczba|Łącznie|Number of queries served for a DNS zone|No Dimensions|
|RecordSetCount|Record Set Count|Liczba|Maksimum|Number of Record Sets in a DNS zone|No Dimensions|
|RecordSetCapacityUtilization|Record Set Capacity Utilization|Procent|Maksimum|Percent of Record Set capacity utilized by a DNS zone|No Dimensions|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|PacketsInDDoS|Inbound packets DDoS|CountPerSecond|Maksimum|Inbound packets DDoS|No Dimensions|
|PacketsDroppedDDoS|Inbound packets dropped DDoS|CountPerSecond|Maksimum|Inbound packets dropped DDoS|No Dimensions|
|PacketsForwardedDDoS|Inbound packets forwarded DDoS|CountPerSecond|Maksimum|Inbound packets forwarded DDoS|No Dimensions|
|TCPPacketsInDDoS|Inbound TCP packets DDoS|CountPerSecond|Maksimum|Inbound TCP packets DDoS|No Dimensions|
|TCPPacketsDroppedDDoS|Inbound TCP packets dropped DDoS|CountPerSecond|Maksimum|Inbound TCP packets dropped DDoS|No Dimensions|
|TCPPacketsForwardedDDoS|Inbound TCP packets forwarded DDoS|CountPerSecond|Maksimum|Inbound TCP packets forwarded DDoS|No Dimensions|
|UDPPacketsInDDoS|Inbound UDP packets DDoS|CountPerSecond|Maksimum|Inbound UDP packets DDoS|No Dimensions|
|UDPPacketsDroppedDDoS|Inbound UDP packets dropped DDoS|CountPerSecond|Maksimum|Inbound UDP packets dropped DDoS|No Dimensions|
|UDPPacketsForwardedDDoS|Inbound UDP packets forwarded DDoS|CountPerSecond|Maksimum|Inbound UDP packets forwarded DDoS|No Dimensions|
|BytesInDDoS|Inbound bytes DDoS|BytesPerSecond|Maksimum|Inbound bytes DDoS|No Dimensions|
|BytesDroppedDDoS|Inbound bytes dropped DDoS|BytesPerSecond|Maksimum|Inbound bytes dropped DDoS|No Dimensions|
|BytesForwardedDDoS|Inbound bytes forwarded DDoS|BytesPerSecond|Maksimum|Inbound bytes forwarded DDoS|No Dimensions|
|TCPBytesInDDoS|Inbound TCP bytes DDoS|BytesPerSecond|Maksimum|Inbound TCP bytes DDoS|No Dimensions|
|TCPBytesDroppedDDoS|Inbound TCP bytes dropped DDoS|BytesPerSecond|Maksimum|Inbound TCP bytes dropped DDoS|No Dimensions|
|TCPBytesForwardedDDoS|Inbound TCP bytes forwarded DDoS|BytesPerSecond|Maksimum|Inbound TCP bytes forwarded DDoS|No Dimensions|
|UDPBytesInDDoS|Inbound UDP bytes DDoS|BytesPerSecond|Maksimum|Inbound UDP bytes DDoS|No Dimensions|
|UDPBytesDroppedDDoS|Inbound UDP bytes dropped DDoS|BytesPerSecond|Maksimum|Inbound UDP bytes dropped DDoS|No Dimensions|
|UDPBytesForwardedDDoS|Inbound UDP bytes forwarded DDoS|BytesPerSecond|Maksimum|Inbound UDP bytes forwarded DDoS|No Dimensions|
|IfUnderDDoSAttack|Under DDoS attack or not|Liczba|Maksimum|Under DDoS attack or not|No Dimensions|
|DDoSTriggerTCPPackets|Inbound TCP packets to trigger DDoS mitigation|CountPerSecond|Maksimum|Inbound TCP packets to trigger DDoS mitigation|No Dimensions|
|DDoSTriggerUDPPackets|Inbound UDP packets to trigger DDoS mitigation|CountPerSecond|Maksimum|Inbound UDP packets to trigger DDoS mitigation|No Dimensions|
|DDoSTriggerSYNPackets|Inbound SYN packets to trigger DDoS mitigation|CountPerSecond|Maksimum|Inbound SYN packets to trigger DDoS mitigation|No Dimensions|
|VipAvailability|Data Path Availability|Liczba|Średnia|Average IP Address availability per time duration|Port|
|ByteCount|Byte Count|Liczba|Łącznie|Total number of Bytes transmitted within time period|Port, Direction|
|PacketCount|Packet Count|Liczba|Łącznie|Total number of Packets transmitted within time period|Port, Direction|
|SynCount|SYN Count|Liczba|Łącznie|Total number of SYN Packets transmitted within time period|Port, Direction|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|ApplicationRuleHit|Application rules hit count|Liczba|Łącznie|Number of times Application rules were hit|Status, Reason, Protocol|
|NetworkRuleHit|Network rules hit count|Liczba|Łącznie|Number of times Network rules were hit|Status, Reason, Protocol|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|Przepływność|Przepływność|BytesPerSecond|Łącznie|Number of bytes per second the Application Gateway has served|No Dimensions|
|UnhealthyHostCount|Unhealthy Host Count|Liczba|Średnia|Number of unhealthy backend hosts|BackendSettingsPool|
|HealthyHostCount|Healthy Host Count|Liczba|Średnia|Number of healthy backend hosts|BackendSettingsPool|
|TotalRequests|Łączna liczba żądań|Liczba|Łącznie|Count of successful requests that Application Gateway has served|BackendSettingsPool|
|FailedRequests|Żądania zakończone niepowodzeniem|Liczba|Łącznie|Count of failed requests that Application Gateway has served|BackendSettingsPool|
|ResponseStatus|Response Status|Liczba|Łącznie|Http response status returned by Application Gateway|HttpStatusGroup|
|CurrentConnections|Current Connections|Liczba|Łącznie|Count of current connections established with Application Gateway|No Dimensions|
|CapacityUnits|Current Capacity Units|Liczba|Średnia|Capacity Units consumed|No Dimensions|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|AverageBandwidth|Gateway S2S Bandwidth|BytesPerSecond|Średnia|Average site-to-site bandwidth of a gateway in bytes per second|No Dimensions|
|P2SBandwidth|Gateway P2S Bandwidth|BytesPerSecond|Średnia|Average point-to-site bandwidth of a gateway in bytes per second|No Dimensions|
|P2SConnectionCount|P2S Connection Count|Liczba|Maksimum|Point-to-site connection count of a gateway|Protocol (Protokół)|
|TunnelAverageBandwidth|Tunnel Bandwidth|BytesPerSecond|Średnia|Average bandwidth of a tunnel in bytes per second|ConnectionName, RemoteIP|
|TunnelEgressBytes|Tunnel Egress Bytes|Bytes|Łącznie|Outgoing bytes of a tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Tunnel Ingress Bytes|Bytes|Łącznie|Incoming bytes of a tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Tunnel Egress Packets|Liczba|Łącznie|Outgoing packet count of a tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Tunnel Ingress Packets|Liczba|Łącznie|Incoming packet count of a tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Tunnel Egress TS Mismatch Packet Drop|Liczba|Łącznie|Outgoing packet drop count from traffic selector mismatch of a tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Tunnel Ingress TS Mismatch Packet Drop|Liczba|Łącznie|Incoming packet drop count from traffic selector mismatch of a tunnel|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Średnia|Bits ingressing Azure per second|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Średnia|Bits egressing Azure per second|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Średnia|Bits ingressing Azure per second|No Dimensions|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Średnia|Bits egressing Azure per second|No Dimensions|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Średnia|Bits ingressing Azure per second|No Dimensions|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Średnia|Bits egressing Azure per second|No Dimensions|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|QpsByEndpoint|Queries by Endpoint Returned|Liczba|Łącznie|Number of times a Traffic Manager endpoint was returned in the given time frame|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Endpoint Status by Endpoint|Liczba|Maksimum|1 if an endpoint's probe status is "Enabled", 0 otherwise.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Probes Failed|Procent|Średnia|% of connectivity monitoring probes failed|No Dimensions|
|AverageRoundtripMs|Avg. Round-trip Time (ms)|MilliSeconds|Średnia|Average network round-trip time (ms) for connectivity monitoring probes sent between source and destination|No Dimensions|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|RequestCount|Liczba żądań|Liczba|Łącznie|The number of client requests served by the HTTP/S proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Request Size|Bytes|Łącznie|The number of bytes sent as requests from clients to the HTTP/S proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Response Size|Bytes|Łącznie|The number of bytes sent as responses from HTTP/S proxy to clients|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Backend Request Count|Liczba|Łącznie|The number of requests sent from the HTTP/S proxy to backends|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Backend Request Latency|MilliSeconds|Średnia|The time calculated from when the request was sent by the HTTP/S proxy to the backend until the HTTP/S proxy received the last response byte from the backend|Backend|
|TotalLatency|Total Latency|MilliSeconds|Średnia|The time calculated from when the client request was received by the HTTP/S proxy until the client acknowledged the last response byte from the HTTP/S proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Backend Health Percentage|Procent|Średnia|The percentage of successful health probes from the HTTP/S proxy to backends|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Web Application Firewall Request Count|Liczba|Łącznie|The number of client requests processed by the Web Application Firewall|PolicyName, RuleName, Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|registration.all|Registration Operations|Liczba|Łącznie|The count of all successful registration operations (creations updates queries and deletions). |No Dimensions|
|registration.create|Registration Create Operations|Liczba|Łącznie|The count of all successful registration creations.|No Dimensions|
|registration.update|Registration Update Operations|Liczba|Łącznie|The count of all successful registration updates.|No Dimensions|
|registration.get|Registration Read Operations|Liczba|Łącznie|The count of all successful registration queries.|No Dimensions|
|registration.delete|Registration Delete Operations|Liczba|Łącznie|The count of all successful registration deletions.|No Dimensions|
|incoming|Incoming Messages|Liczba|Łącznie|The count of all successful send API calls. |No Dimensions|
|incoming.scheduled|Scheduled Push Notifications Sent|Liczba|Łącznie|Scheduled Push Notifications Canceled|No Dimensions|
|incoming.scheduled.cancel|Scheduled Push Notifications Canceled|Liczba|Łącznie|Scheduled Push Notifications Canceled|No Dimensions|
|scheduled.pending|Pending Scheduled Notifications|Liczba|Łącznie|Pending Scheduled Notifications|No Dimensions|
|installation.all|Installation Management Operations|Liczba|Łącznie|Installation Management Operations|No Dimensions|
|installation.get|Get Installation Operations|Liczba|Łącznie|Get Installation Operations|No Dimensions|
|installation.upsert|Create or Update Installation Operations|Liczba|Łącznie|Create or Update Installation Operations|No Dimensions|
|installation.patch|Patch Installation Operations|Liczba|Łącznie|Patch Installation Operations|No Dimensions|
|installation.delete|Delete Installation Operations|Liczba|Łącznie|Delete Installation Operations|No Dimensions|
|outgoing.allpns.success|Successful notifications|Liczba|Łącznie|The count of all successful notifications.|No Dimensions|
|outgoing.allpns.invalidpayload|Payload Errors|Liczba|Łącznie|The count of pushes that failed because the PNS returned a bad payload error.|No Dimensions|
|outgoing.allpns.pnserror|External Notification System Errors|Liczba|Łącznie|The count of pushes that failed because there was a problem communicating with the PNS (excludes authentication problems).|No Dimensions|
|outgoing.allpns.channelerror|Channel Errors|Liczba|Łącznie|The count of pushes that failed because the channel was invalid not associated with the correct app throttled or expired.|No Dimensions|
|outgoing.allpns.badorexpiredchannel|Bad or Expired Channel Errors|Liczba|Łącznie|The count of pushes that failed because the channel/token/registrationId in the registration was expired or invalid.|No Dimensions|
|outgoing.wns.success|WNS Successful Notifications|Liczba|Łącznie|The count of all successful notifications.|No Dimensions|
|outgoing.wns.invalidcredentials|WNS Authorization Errors (Invalid Credentials)|Liczba|Łącznie|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked. (Windows Live does not recognize the credentials).|No Dimensions|
|outgoing.wns.badchannel|WNS Bad Channel Error|Liczba|Łącznie|The count of pushes that failed because the ChannelURI in the registration was not recognized (WNS status: 404 not found).|No Dimensions|
|outgoing.wns.expiredchannel|WNS Expired Channel Error|Liczba|Łącznie|The count of pushes that failed because the ChannelURI is expired (WNS status: 410 Gone).|No Dimensions|
|outgoing.wns.throttled|WNS Throttled Notifications|Liczba|Łącznie|The count of pushes that failed because WNS is throttling this app (WNS status: 406 Not Acceptable).|No Dimensions|
|outgoing.wns.tokenproviderunreachable|WNS Authorization Errors (Unreachable)|Liczba|Łącznie|Windows Live is not reachable.|No Dimensions|
|outgoing.wns.invalidtoken|WNS Authorization Errors (Invalid Token)|Liczba|Łącznie|The token provided to WNS is not valid (WNS status: 401 Unauthorized).|No Dimensions|
|outgoing.wns.wrongtoken|WNS Authorization Errors (Wrong Token)|Liczba|Łącznie|The token provided to WNS is valid but for another application (WNS status: 403 Forbidden). This can happen if the ChannelURI in the registration is associated with another app. Check that the client app is associated with the same app whose credentials are in the notification hub.|No Dimensions|
|outgoing.wns.invalidnotificationformat|WNS Invalid Notification Format|Liczba|Łącznie|The format of the notification is invalid (WNS status: 400). Note that WNS does not reject all invalid payloads.|No Dimensions|
|outgoing.wns.invalidnotificationsize|WNS Invalid Notification Size Error|Liczba|Łącznie|The notification payload is too large (WNS status: 413).|No Dimensions|
|outgoing.wns.channelthrottled|WNS Channel Throttled|Liczba|Łącznie|The notification was dropped because the ChannelURI in the registration is throttled (WNS response header: X-WNS-NotificationStatus:channelThrottled).|No Dimensions|
|outgoing.wns.channeldisconnected|WNS Channel Disconnected|Liczba|Łącznie|The notification was dropped because the ChannelURI in the registration is throttled (WNS response header: X-WNS-DeviceConnectionStatus: disconnected).|No Dimensions|
|outgoing.wns.dropped|WNS Dropped Notifications|Liczba|Łącznie|The notification was dropped because the ChannelURI in the registration is throttled (X-WNS-NotificationStatus: dropped but not X-WNS-DeviceConnectionStatus: disconnected).|No Dimensions|
|outgoing.wns.pnserror|WNS Errors|Liczba|Łącznie|Notification not delivered because of errors communicating with WNS.|No Dimensions|
|outgoing.wns.authenticationerror|WNS Authentication Errors|Liczba|Łącznie|Notification not delivered because of errors communicating with Windows Live invalid credentials or wrong token.|No Dimensions|
|outgoing.apns.success|APNS Successful Notifications|Liczba|Łącznie|The count of all successful notifications.|No Dimensions|
|outgoing.apns.invalidcredentials|APNS Authorization Errors|Liczba|Łącznie|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|outgoing.apns.badchannel|APNS Bad Channel Error|Liczba|Łącznie|The count of pushes that failed because the token is invalid (APNS binary protocol status code: 8. APNS HTTP protocol status code: 400 with "BadDeviceToken").|No Dimensions|
|outgoing.apns.expiredchannel|APNS Expired Channel Error|Liczba|Łącznie|The count of token that were invalidated by the APNS feedback channel.|No Dimensions|
|outgoing.apns.invalidnotificationsize|APNS Invalid Notification Size Error|Liczba|Łącznie|The count of pushes that failed because the payload was too large (APNS binary protocol status code: 7).|No Dimensions|
|outgoing.apns.pnserror|APNS Errors|Liczba|Łącznie|The count of pushes that failed because of errors communicating with APNS.|No Dimensions|
|outgoing.gcm.success|GCM Successful Notifications|Liczba|Łącznie|The count of all successful notifications.|No Dimensions|
|outgoing.gcm.invalidcredentials|GCM Authorization Errors (Invalid Credentials)|Liczba|Łącznie|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|outgoing.gcm.badchannel|GCM Bad Channel Error|Liczba|Łącznie|The count of pushes that failed because the registrationId in the registration was not recognized (GCM result: Invalid Registration).|No Dimensions|
|outgoing.gcm.expiredchannel|GCM Expired Channel Error|Liczba|Łącznie|The count of pushes that failed because the registrationId in the registration was expired (GCM result: NotRegistered).|No Dimensions|
|outgoing.gcm.throttled|GCM Throttled Notifications|Liczba|Łącznie|The count of pushes that failed because GCM throttled this app (GCM status code: 501-599 or result:Unavailable).|No Dimensions|
|outgoing.gcm.invalidnotificationformat|GCM Invalid Notification Format|Liczba|Łącznie|The count of pushes that failed because the payload was not formatted correctly (GCM result: InvalidDataKey or InvalidTtl).|No Dimensions|
|outgoing.gcm.invalidnotificationsize|GCM Invalid Notification Size Error|Liczba|Łącznie|The count of pushes that failed because the payload was too large (GCM result: MessageTooBig).|No Dimensions|
|outgoing.gcm.wrongchannel|GCM Wrong Channel Error|Liczba|Łącznie|The count of pushes that failed because the registrationId in the registration is not associated to the current app (GCM result: InvalidPackageName).|No Dimensions|
|outgoing.gcm.pnserror|GCM Errors|Liczba|Łącznie|The count of pushes that failed because of errors communicating with GCM.|No Dimensions|
|outgoing.gcm.authenticationerror|GCM Authentication Errors|Liczba|Łącznie|The count of pushes that failed because the PNS did not accept the provided credentials the credentials are blocked or the SenderId is not correctly configured in the app (GCM result: MismatchedSenderId).|No Dimensions|
|outgoing.mpns.success|MPNS Successful Notifications|Liczba|Łącznie|The count of all successful notifications.|No Dimensions|
|outgoing.mpns.invalidcredentials|MPNS Invalid Credentials|Liczba|Łącznie|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|outgoing.mpns.badchannel|MPNS Bad Channel Error|Liczba|Łącznie|The count of pushes that failed because the ChannelURI in the registration was not recognized (MPNS status: 404 not found).|No Dimensions|
|outgoing.mpns.throttled|MPNS Throttled Notifications|Liczba|Łącznie|The count of pushes that failed because MPNS is throttling this app (WNS MPNS: 406 Not Acceptable).|No Dimensions|
|outgoing.mpns.invalidnotificationformat|MPNS Invalid Notification Format|Liczba|Łącznie|The count of pushes that failed because the payload of the notification was too large.|No Dimensions|
|outgoing.mpns.channeldisconnected|MPNS Channel Disconnected|Liczba|Łącznie|The count of pushes that failed because the ChannelURI in the registration was disconnected (MPNS status: 412 not found).|No Dimensions|
|outgoing.mpns.dropped|MPNS Dropped Notifications|Liczba|Łącznie|The count of pushes that were dropped by MPNS (MPNS response header: X-NotificationStatus: QueueFull or Suppressed).|No Dimensions|
|outgoing.mpns.pnserror|MPNS Errors|Liczba|Łącznie|The count of pushes that failed because of errors communicating with MPNS.|No Dimensions|
|outgoing.mpns.authenticationerror|MPNS Authentication Errors|Liczba|Łącznie|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|notificationhub.pushes|All Outgoing Notifications|Liczba|Łącznie|All outgoing notifications of the notification hub|No Dimensions|
|incoming.all.requests|All Incoming Requests|Liczba|Łącznie|Total incoming requests for a notification hub|No Dimensions|
|incoming.all.failedrequests|All Incoming Failed Requests|Liczba|Łącznie|Total incoming failed requests for a notification hub|No Dimensions|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|Average_% Free Inodes|% Free Inodes|Liczba|Średnia|Average_% Free Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% Free Space|Liczba|Średnia|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Inodes|% Used Inodes|Liczba|Średnia|Average_% Used Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Space|% Used Space|Liczba|Średnia|Average_% Used Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|Disk Read Bytes/sec|Liczba|Średnia|Average_Disk Read Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Disk Reads/sec|Liczba|Średnia|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Liczba|Średnia|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|Disk Write Bytes/sec|Liczba|Średnia|Average_Disk Write Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Disk Writes/sec|Liczba|Średnia|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Free Megabytes|Liczba|Średnia|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|Logical Disk Bytes/sec|Liczba|Średnia|Average_Logical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Memory|% Available Memory|Liczba|Średnia|Average_% Available Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|% Available Swap Space|Liczba|Średnia|Average_% Available Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|% Used Memory|Liczba|Średnia|Average_% Used Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|% Used Swap Space|Liczba|Średnia|Average_% Used Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|Available MBytes Memory|Liczba|Średnia|Average_Available MBytes Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|Available MBytes Swap|Liczba|Średnia|Average_Available MBytes Swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|Page Reads/sec|Liczba|Średnia|Average_Page Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|Page Writes/sec|Liczba|Średnia|Average_Page Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Pages/sec|Liczba|Średnia|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Used MBytes Swap Space|Liczba|Średnia|Average_Used MBytes Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|Used Memory MBytes|Liczba|Średnia|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|Total Bytes Transmitted|Liczba|Średnia|Average_Total Bytes Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|Total Bytes Received|Liczba|Średnia|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|Total Bytes|Liczba|Średnia|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|Total Packets Transmitted|Liczba|Średnia|Average_Total Packets Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Received|Total Packets Received|Liczba|Średnia|Average_Total Packets Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|Total Rx Errors|Liczba|Średnia|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx Errors|Total Tx Errors|Liczba|Średnia|Average_Total Tx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|Total Collisions|Liczba|Średnia|Average_Total Collisions|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Read|Avg. Disk sec/Read|Liczba|Średnia|Average_Avg. Disk sec/Read|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Transfer|Avg. Disk sec/Transfer|Liczba|Średnia|Average_Avg. Disk sec/Transfer|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Write|Avg. Disk sec/Write|Liczba|Średnia|Average_Avg. Disk sec/Write|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|Physical Disk Bytes/sec|Liczba|Średnia|Average_Physical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Pct Privileged Time|Liczba|Średnia|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|Pct User Time|Liczba|Średnia|Average_Pct User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory kBytes|Used Memory kBytes|Liczba|Średnia|Average_Used Memory kBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|Virtual Shared Memory|Liczba|Średnia|Average_Virtual Shared Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|% DPC Time|Liczba|Średnia|Average_% DPC Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|% Idle Time|Liczba|Średnia|Average_% Idle Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|% Interrupt Time|Liczba|Średnia|Average_% Interrupt Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|% IO Wait Time|Liczba|Średnia|Average_% IO Wait Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|% Nice Time|Liczba|Średnia|Average_% Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|% Privileged Time|Liczba|Średnia|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% Processor Time|Liczba|Średnia|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|% User Time|Liczba|Średnia|Average_% User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|Free Physical Memory|Liczba|Średnia|Average_Free Physical Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|Free Space in Paging Files|Liczba|Średnia|Average_Free Space in Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|Free Virtual Memory|Liczba|Średnia|Average_Free Virtual Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Procesy|Liczba|Średnia|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|Size Stored In Paging Files|Liczba|Średnia|Average_Size Stored In Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Uptime|Liczba|Średnia|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Użytkownicy|Liczba|Średnia|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Read|Avg. Disk sec/Read|Liczba|Średnia|Average_Avg. Disk sec/Read|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Write|Avg. Disk sec/Write|Liczba|Średnia|Average_Avg. Disk sec/Write|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Current Disk Queue Length|Liczba|Średnia|Average_Current Disk Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Disk Reads/sec|Liczba|Średnia|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Liczba|Średnia|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Disk Writes/sec|Liczba|Średnia|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Free Megabytes|Liczba|Średnia|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% Free Space|Liczba|Średnia|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|Available MBytes|Liczba|Średnia|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|% Committed Bytes In Use|Liczba|Średnia|Average_% Committed Bytes In Use|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Bytes Received/sec|Liczba|Średnia|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Bytes Sent/sec|Liczba|Średnia|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Bytes Total/sec|Liczba|Średnia|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% Processor Time|Liczba|Średnia|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|Processor Queue Length|Liczba|Średnia|Average_Processor Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Heartbeat|Heartbeat|Liczba|Łącznie|Heartbeat|Computer, OSType, Version, SourceComputerId|
|Aktualizacja|Aktualizacja|Liczba|Średnia|Aktualizacja|Computer, Product, Classification, UpdateState, Optional, Approved|
|Wydarzenie|Wydarzenie|Liczba|Średnia|Wydarzenie|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|QueryDuration|Query Duration|Milliseconds|Średnia|DAX Query duration in last interval|No Dimensions|
|QueryPoolJobQueueLength|Threads: Query pool job queue length|Liczba|Średnia|Number of jobs in the queue of the query thread pool.|No Dimensions|
|qpu_high_utilization_metric|QPU High Utilization|Liczba|Łącznie|QPU High Utilization In Last Minute, 1 For High QPU Utilization, Otherwise 0|No Dimensions|
|memory_metric|Pamięć|Bytes|Średnia|Memory. Range 0-3 GB for A1, 0-5 GB for A2, 0-10 GB for A3, 0-25 GB for A4, 0-50 GB for A5 and 0-100 GB for A6|No Dimensions|
|memory_thrashing_metric|Memory Thrashing|Procent|Średnia|Average memory thrashing.|No Dimensions|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Liczba|Łącznie|Successful ListenerConnections for Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Liczba|Łącznie|ClientError on ListenerConnections for Microsoft.Relay.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Liczba|Łącznie|ServerError on ListenerConnections for Microsoft.Relay.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Liczba|Łącznie|Successful SenderConnections for Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Liczba|Łącznie|ClientError on SenderConnections for Microsoft.Relay.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Liczba|Łącznie|ServerError on SenderConnections for Microsoft.Relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Liczba|Łącznie|Total ListenerConnections for Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Liczba|Łącznie|Total SenderConnections requests for Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Liczba|Łącznie|Total ActiveConnections for Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Liczba|Łącznie|Total ActiveListeners for Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Liczba|Łącznie|Total BytesTransferred for Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Liczba|Łącznie|Total ListenerDisconnects for Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Liczba|Łącznie|Total SenderDisconnects for Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|SearchLatency|Search Latency|Sekundy|Średnia|Average search latency for the search service|No Dimensions|
|SearchQueriesPerSecond|Search queries per second|CountPerSecond|Średnia|Search queries per second for the search service|No Dimensions|
|ThrottledSearchQueriesPercentage|Throttled search queries percentage|Procent|Średnia|Percentage of search queries that were throttled for the search service|No Dimensions|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|SuccessfulRequests|Successful Requests (Preview)|Liczba|Łącznie|Total successful requests for a namespace (Preview)|EntityName|
|ServerErrors|Server Errors. (Wersja zapoznawcza)|Liczba|Łącznie|Server Errors for Microsoft.ServiceBus. (Wersja zapoznawcza)|EntityName|
|UserErrors|User Errors. (Wersja zapoznawcza)|Liczba|Łącznie|User Errors for Microsoft.ServiceBus. (Wersja zapoznawcza)|EntityName|
|ThrottledRequests|Throttled Requests. (Wersja zapoznawcza)|Liczba|Łącznie|Throttled Requests for Microsoft.ServiceBus. (Wersja zapoznawcza)|EntityName|
|IncomingRequests|Incoming Requests (Preview)|Liczba|Łącznie|Incoming Requests for Microsoft.ServiceBus. (Wersja zapoznawcza)|EntityName|
|IncomingMessages|Incoming Messages (Preview)|Liczba|Łącznie|Incoming Messages for Microsoft.ServiceBus. (Wersja zapoznawcza)|EntityName|
|OutgoingMessages|Outgoing Messages (Preview)|Liczba|Łącznie|Outgoing Messages for Microsoft.ServiceBus. (Wersja zapoznawcza)|EntityName|
|ActiveConnections|ActiveConnections (Preview)|Liczba|Łącznie|Total Active Connections for Microsoft.ServiceBus. (Wersja zapoznawcza)|No Dimensions|
|Rozmiar|Size (Preview)|Bytes|Średnia|Size of an Queue/Topic in Bytes. (Wersja zapoznawcza)|EntityName|
|Komunikaty|Count of messages in a Queue/Topic. (Wersja zapoznawcza)|Liczba|Średnia|Count of messages in a Queue/Topic. (Wersja zapoznawcza)|EntityName|
|ActiveMessages|Count of active messages in a Queue/Topic. (Wersja zapoznawcza)|Liczba|Średnia|Count of active messages in a Queue/Topic. (Wersja zapoznawcza)|EntityName|
|DeadletteredMessages|Count of dead-lettered messages in a Queue/Topic. (Wersja zapoznawcza)|Liczba|Średnia|Count of dead-lettered messages in a Queue/Topic. (Wersja zapoznawcza)|EntityName|
|ScheduledMessages|Count of scheduled messages in a Queue/Topic. (Wersja zapoznawcza)|Liczba|Średnia|Count of scheduled messages in a Queue/Topic. (Wersja zapoznawcza)|EntityName|
|CPUXNS|CPU usage per namespace|Procent|Maksimum|Service bus premium namespace CPU usage metric|No Dimensions|
|WSXNS|Memory size usage per namespace|Procent|Maksimum|Service bus premium namespace memory usage metric|No Dimensions|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Liczba|Średnia|Cpu allocated to this container in millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Bytes|Średnia|Memory allocated to this container in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Liczba|Średnia|Actual CPU usage in millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Bytes|Średnia|Actual memory usage in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Procent|Średnia|Utilization of CPU for this container as percentage of AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Procent|Średnia|Utilization of CPU for this container as percentage of AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Liczba|Średnia|Status of Service Fabric Mesh application|ApplicationName, Status|
|ServiceStatus|ServiceStatus|Liczba|Średnia|Health Status of a service in Service Fabric Mesh application|ApplicationName, Status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Liczba|Średnia|Health Status of a service replica in Service Fabric Mesh application|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Liczba|Średnia|Status of the container in Service Fabric Mesh application|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|RestartCount|RestartCount|Liczba|Średnia|Restart count of a container in Service Fabric Mesh application|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|ConnectionCount|Connection Count|Liczba|Maksimum|The amount of user connection.|Punkt końcowy|
|MessageCount|Message Count|Liczba|Łącznie|The total amount of messages.|No Dimensions|
|InboundTraffic|Ruch przychodzący|Bytes|Łącznie|The inbound traffic of service|No Dimensions|
|OutboundTraffic|Ruch wychodzący|Bytes|Łącznie|The outbound traffic of service|No Dimensions|
|UserErrors|User Errors|Procent|Maksimum|The percentage of user errors|No Dimensions|
|SystemErrors|System Errors|Procent|Maksimum|The percentage of system errors|No Dimensions|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|allocated_data_storage|Data space allocated|Bytes|Średnia|Data space allocated. Not applicable to data warehouses.|No Dimensions|
|app_cpu_billed|App CPU billed|Liczba|Łącznie|App CPU billed. Applies to serverless databases.|No Dimensions|
|app_cpu_percent|App CPU percentage|Procent|Średnia|App CPU percentage. Applies to serverless databases.|No Dimensions|
|app_memory_percent|App memory used percentage|Procent|Średnia|App memory used percentage. Applies to serverless databases.|No Dimensions|
|blocked_by_firewall|Blocked by Firewall|Liczba|Łącznie|Blocked by Firewall|No Dimensions|
|cache_hit_percent|Cache hit percentage|Procent|Maksimum|Cache hit percentage. Applies only to data warehouses.|No Dimensions|
|cache_used_percent|Cache used percentage|Procent|Maksimum|Cache used percentage. Applies only to data warehouses.|No Dimensions|
|connection_failed|Połączenia zakończone niepowodzeniem|Liczba|Łącznie|Połączenia zakończone niepowodzeniem|No Dimensions|
|connection_successful|Successful Connections|Liczba|Łącznie|Successful Connections|No Dimensions|
|cpu_percent|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|No Dimensions|
|cpu_limit|CPU limit|Liczba|Średnia|CPU limit. Applies to vCore-based databases.|No Dimensions|
|cpu_used|CPU used|Liczba|Średnia|CPU used. Applies to vCore-based databases.|No Dimensions|
|deadlock|Deadlocks|Liczba|Łącznie|Deadlocks. Not applicable to data warehouses.|No Dimensions|
|diff_backup_size_bytes|Differential backup storage size|Bytes|Maksimum|Cumulative differential backup storage size. Applies to General Purpose and Business Critical databases. Does not currently apply to Manage instance databases.|No Dimensions|
|dtu_limit|DTU Limit|Liczba|Średnia|DTU Limit. Applies to DTU-based databases.|No Dimensions|
|dtu_consumption_percent|Procent użycia jednostek DTU|Procent|Średnia|DTU percentage. Applies to DTU-based databases.|No Dimensions|
|dtu_used|DTU used|Liczba|Średnia|DTU used. Applies to DTU-based databases.|No Dimensions|
|dw_cpu_percent|DW node level CPU percentage|Procent|Średnia|DW node level CPU percentage|DwLogicalNodeId|
|dw_physical_data_read_percent|DW node level Data IO percentage|Procent|Średnia|DW node level Data IO percentage|DwLogicalNodeId|
|dwu_consumption_percent|DWU percentage|Procent|Maksimum|DWU percentage. Applies only to data warehouses.|No Dimensions|
|dwu_limit|DWU limit|Liczba|Maksimum|DWU limit. Applies only to data warehouses.|No Dimensions|
|dwu_used|DWU used|Liczba|Maksimum|DWU used. Applies only to data warehouses.|No Dimensions|
|full_backup_size_bytes|Full backup storage size|Bytes|Maksimum|Cumulative full backup storage size. Applies to General Purpose and Business Critical databases. Does not currently apply to Manage instance databases.|No Dimensions|
|local_tempdb_usage_percent|Local tempdb percentage|Procent|Średnia|Local tempdb percentage. Applies only to data warehouses.|No Dimensions|
|log_backup_size_bytes|Log backup storage size|Bytes|Maksimum|Cumulative log backup storage size. Applies to General Purpose and Business Critical databases. Does not currently apply to Manage instance databases.|No Dimensions|
|log_write_percent|Log IO percentage|Procent|Średnia|Log IO percentage. Not applicable to data warehouses.|No Dimensions|
|physical_data_read_percent|Procent użycia operacji we/wy na danych|Procent|Średnia|Procent użycia operacji we/wy na danych|No Dimensions|
|sessions_percent|Sessions percentage|Procent|Średnia|Sessions percentage. Not applicable to data warehouses.|No Dimensions|
|sqlserver_process_core_percent|SQL Server process core percent|Procent|Maksimum|This metric is a placeholder and not populated at this time.|No Dimensions|
|sqlserver_process_memory_percent|SQL Server process memory percent|Procent|Maksimum|This metric is a placeholder and not populated at this time.|No Dimensions|
|magazyn|Data space used|Bytes|Maksimum|Total database size. Not applicable to data warehouses.|No Dimensions|
|storage_percent|Data space used percent|Procent|Maksimum|Database size percentage. Not applicable to data warehouses or hyperscale databases.|No Dimensions|
|tempdb_data_size|Tempdb Data File Size Kilobytes|Liczba|Maksimum|Tempdb Data File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_size|Tempdb Log File Size Kilobytes|Liczba|Maksimum|Tempdb Log File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_used_percent|Tempdb Percent Log Used|Procent|Maksimum|Tempdb Percent Log Used. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|workers_percent|Workers percentage|Procent|Średnia|Workers percentage. Not applicable to data warehouses.|No Dimensions|
|xtp_storage_percent|In-Memory OLTP storage percent|Procent|Średnia|In-Memory OLTP storage percent. Not applicable to data warehouses.|No Dimensions|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|allocated_data_storage|Data space allocated|Bytes|Średnia|Data space allocated|No Dimensions|
|allocated_data_storage_percent|Data space allocated percent|Procent|Maksimum|Data space allocated percent|No Dimensions|
|cpu_limit|CPU limit|Liczba|Średnia|CPU limit. Applies to vCore-based elastic pools.|No Dimensions|
|cpu_percent|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|No Dimensions|
|cpu_used|CPU used|Liczba|Średnia|CPU used. Applies to vCore-based elastic pools.|No Dimensions|
|dtu_consumption_percent|Procent użycia jednostek DTU|Procent|Średnia|DTU percentage. Applies to DTU-based elastic pools.|No Dimensions|
|eDTU_limit|eDTU limit|Liczba|Średnia|eDTU limit. Applies to DTU-based elastic pools.|No Dimensions|
|eDTU_used|eDTU used|Liczba|Średnia|eDTU used. Applies to DTU-based elastic pools.|No Dimensions|
|log_write_percent|Log IO percentage|Procent|Średnia|Log IO percentage|No Dimensions|
|physical_data_read_percent|Procent użycia operacji we/wy na danych|Procent|Średnia|Procent użycia operacji we/wy na danych|No Dimensions|
|sessions_percent|Sessions percentage|Procent|Średnia|Sessions percentage|No Dimensions|
|storage_limit|Data max size|Bytes|Średnia|Storage limit|No Dimensions|
|storage_percent|Data space used percent||Procent|Średnia|Storage percentage|No Dimensions|
|storage_used|Data space used|Bytes|Średnia|Użyty magazyn|No Dimensions|
|sqlserver_process_core_percent|SQL Server process core percent|Procent|Maksimum|This metric is a placeholder and not populated at this time.|No Dimensions|
|sqlserver_process_memory_percent|SQL Server process memory percent|Procent|Maksimum|This metric is a placeholder and not populated at this time.|No Dimensions|
|tempdb_data_size|Tempdb Data File Size Kilobytes|Liczba|Maksimum|Tempdb Data File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_size|Tempdb Log File Size Kilobytes|Liczba|Maksimum|Tempdb Log File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_used_percent|Tempdb Percent Log Used|Procent|Maksimum|Tempdb Percent Log Used. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|workers_percent|Workers percentage|Procent|Średnia|Workers percentage|No Dimensions|
|xtp_storage_percent|In-Memory OLTP storage percent|Procent|Średnia|In-Memory OLTP storage percent|No Dimensions|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|avg_cpu_percent|Average CPU percentage|Procent|Średnia|Average CPU percentage|No Dimensions|
|io_bytes_read|IO bytes read|Bytes|Średnia|IO bytes read|No Dimensions|
|io_requests|IO requests count|Liczba|Średnia|IO requests count|No Dimensions|
|io_bytes_written|IO bytes written|Bytes|Średnia|IO bytes written|No Dimensions|
|reserved_storage_mb|Storage space reserved|Liczba|Średnia|Storage space reserved|No Dimensions|
|storage_space_used_mb|Storage space used|Liczba|Średnia|Storage space used|No Dimensions|
|virtual_core_count|Virtual core count|Liczba|Średnia|Virtual core count|No Dimensions|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|UsedCapacity|Used capacity|Bytes|Średnia|Account used capacity|No Dimensions|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Zdarzenia związane z transferem danych przychodzących|Zdarzenia związane z transferem danych przychodzących|Bytes|Łącznie|The amount of ingress data, in bytes. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|GeoType, ApiName, Authentication|
|Ruch wychodzący|Ruch wychodzący|Bytes|Łącznie|The amount of egress data, in bytes. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Średnia|The average latency used by Azure Storage to process a successful request, in milliseconds. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Średnia|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|GeoType, ApiName, Authentication|
|Dostępność|Dostępność|Procent|Średnia|The percentage of availability for the storage service or the specified API operation. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|BlobCapacity|Blob Capacity|Bytes|Średnia|The amount of storage used by the storage account’s Blob service in bytes.|BlobType, Tier|
|BlobCount|Blob Count|Liczba|Łącznie|The number of Blob in the storage account’s Blob service.|BlobType|       |BlobCount|Blob Count|Liczba|Średnia|The number of Blob in the storage account’s Blob service.|BlobType, Tier|
|ContainerCount|Blob Container Count|Liczba|Średnia|The number of containers in the storage account’s Blob service.|No Dimensions|
|IndexCapacity|Index Capacity|Bytes|Średnia|The amount of storage used by ADLS Gen2 (Hierarchical) Index in bytes.|No Dimensions|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Zdarzenia związane z transferem danych przychodzących|Zdarzenia związane z transferem danych przychodzących|Bytes|Łącznie|The amount of ingress data, in bytes. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|GeoType, ApiName, Authentication|
|Ruch wychodzący|Ruch wychodzący|Bytes|Łącznie|The amount of egress data, in bytes. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Średnia|The average latency used by Azure Storage to process a successful request, in milliseconds. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Średnia|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|GeoType, ApiName, Authentication|
|Dostępność|Dostępność|Procent|Średnia|The percentage of availability for the storage service or the specified API operation. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|FileCapacity|File Capacity|Bytes|Średnia|The amount of storage used by the storage account’s File service in bytes.|No Dimensions|
|FileCount|File Count|Liczba|Średnia|The number of file in the storage account’s File service.|No Dimensions|
|FileShareCount|File Share Count|Liczba|Średnia|The number of file shares in the storage account’s File service.|No Dimensions|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Zdarzenia związane z transferem danych przychodzących|Zdarzenia związane z transferem danych przychodzących|Bytes|Łącznie|The amount of ingress data, in bytes. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|GeoType, ApiName, Authentication|
|Ruch wychodzący|Ruch wychodzący|Bytes|Łącznie|The amount of egress data, in bytes. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Średnia|The average latency used by Azure Storage to process a successful request, in milliseconds. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Średnia|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|GeoType, ApiName, Authentication|
|Dostępność|Dostępność|Procent|Średnia|The percentage of availability for the storage service or the specified API operation. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|QueueCapacity|Queue Capacity|Bytes|Średnia|The amount of storage used by the storage account’s Queue service in bytes.|No Dimensions|
|QueueCount|Queue Count|Liczba|Średnia|The number of queue in the storage account’s Queue service.|No Dimensions|
|QueueMessageCount|Queue Message Count|Liczba|Średnia|The approximate number of queue messages in the storage account’s Queue service.|No Dimensions|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Zdarzenia związane z transferem danych przychodzących|Zdarzenia związane z transferem danych przychodzących|Bytes|Łącznie|The amount of ingress data, in bytes. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|GeoType, ApiName, Authentication|
|Ruch wychodzący|Ruch wychodzący|Bytes|Łącznie|The amount of egress data, in bytes. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Średnia|The average latency used by Azure Storage to process a successful request, in milliseconds. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Średnia|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|GeoType, ApiName, Authentication|
|Dostępność|Dostępność|Procent|Średnia|The percentage of availability for the storage service or the specified API operation. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|TableCapacity|Table Capacity|Bytes|Średnia|The amount of storage used by the storage account’s Table service in bytes.|No Dimensions|
|TableCount|Table Count|Liczba|Średnia|The number of table in the storage account’s Table service.|No Dimensions|
|TableEntityCount|Table Entity Count|Liczba|Średnia|The number of table entities in the storage account’s Table service.|No Dimensions|
|Transakcje|Transakcje|Liczba|Łącznie|Liczba żądań wysyłanych do usługi magazynu lub określonej operacji interfejsu API. Liczba ta obejmuje żądania zakończone powodzeniem i zakończone niepowodzeniem, a także żądania, które wygenerowały błędy. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Zdarzenia związane z transferem danych przychodzących|Zdarzenia związane z transferem danych przychodzących|Bytes|Łącznie|The amount of ingress data, in bytes. Ta liczba obejmuje dane przychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane przychodzące w obrębie platformy Azure.|GeoType, ApiName, Authentication|
|Ruch wychodzący|Ruch wychodzący|Bytes|Łącznie|The amount of egress data, in bytes. Ta liczba obejmuje dane wychodzące z klienta zewnętrznego do usługi Azure Storage, a także dane wychodzące w obrębie platformy Azure. W rezultacie liczba ta nie odzwierciedla danych wychodzących uwzględnianych na rozliczeniu.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Średnia|The average latency used by Azure Storage to process a successful request, in milliseconds. Ta wartość nie obejmuje opóźnienia sieci uwzględnionego w wartości AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Średnia|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Ta wartość obejmuje wymagany czas przetwarzania w usłudze Azure Storage do odczytania żądania, wysłania odpowiedzi i odebrania potwierdzenia dla odpowiedzi.|GeoType, ApiName, Authentication|
|Dostępność|Dostępność|Procent|Średnia|The percentage of availability for the storage service or the specified API operation. Dostępność jest obliczana przez podzielenie wartości TotalBillableRequests przez liczbę żądań mających zastosowanie, łącznie z tymi, które wygenerowały nieoczekiwane błędy. Wszystkie nieoczekiwane błędy powodują obniżenie poziomu dostępności usługi magazynu lub określonej operacji interfejsu API.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Sync Session Result|Liczba|Średnia|Metric that logs a value of 1 each time the Server Endpoint successfully completes a Sync Session with the Cloud Endpoint|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Files Synced|Liczba|Łącznie|Count of Files synced|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Files not syncing|Liczba|Łącznie|Count of files failed to sync|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Bytes synced|Bytes|Łącznie|Total file size transferred for Sync Sessions|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Server Online Status|Liczba|Maksimum|Metric that logs a value of 1 each time the registered server successfully records a heartbeat with the Cloud Endpoint|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Cloud tiering recall|Bytes|Łącznie|Total size of data recalled by the server|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|ResourceUtilization|SU % Utilization|Procent|Maksimum|SU % Utilization|LogicalName, PartitionId|
|InputEvents|Input Events|Liczba|Łącznie|Input Events|LogicalName, PartitionId|
|InputEventBytes|Input Event Bytes|Bytes|Łącznie|Input Event Bytes|LogicalName, PartitionId|
|LateInputEvents|Late Input Events|Liczba|Łącznie|Late Input Events|LogicalName, PartitionId|
|OutputEvents|Output Events|Liczba|Łącznie|Output Events|LogicalName, PartitionId|
|ConversionErrors|Data Conversion Errors|Liczba|Łącznie|Data Conversion Errors|LogicalName, PartitionId|
|Błędy|Runtime Errors|Liczba|Łącznie|Runtime Errors|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Out of order Events|Liczba|Łącznie|Out of order Events|LogicalName, PartitionId|
|AMLCalloutRequests|Function Requests|Liczba|Łącznie|Function Requests|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Failed Function Requests|Liczba|Łącznie|Failed Function Requests|LogicalName, PartitionId|
|AMLCalloutInputEvents|Function Events|Liczba|Łącznie|Function Events|LogicalName, PartitionId|
|DeserializationError|Input Deserialization Errors|Liczba|Łącznie|Input Deserialization Errors|LogicalName, PartitionId|
|EarlyInputEvents|Early Input Events|Liczba|Łącznie|Early Input Events|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Watermark Delay|Sekundy|Maksimum|Watermark Delay|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Backlogged Input Events|Liczba|Maksimum|Backlogged Input Events|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Input Sources Received|Liczba|Łącznie|Input Sources Received|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages|Liczba|Łącznie|Count of messages read from all Event hub or IoT hub event sources|No Dimensions|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages|Liczba|Łącznie|Count of invalid messages read from all Event hub or IoT hub event sources|No Dimensions|
|IngressReceivedBytes|Ingress Received Bytes|Bytes|Łącznie|Count of bytes read from all event sources|No Dimensions|
|IngressStoredBytes|Ingress Stored Bytes|Bytes|Łącznie|Total size of events successfully processed and available for query|No Dimensions|
|IngressStoredEvents|Ingress Stored Events|Liczba|Łącznie|Count of flattened events successfully processed and available for query|No Dimensions|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag|Sekundy|Maksimum|Difference between the time that the message is enqueued in the event source and the time it is processed in Ingress|No Dimensions|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag|Liczba|Średnia|Difference between the sequence number of last enqueued message in the event source partition and sequence number of message being processed in Ingress|No Dimensions|
|WarmStorageMaxProperties|Warm Storage Max Properties|Liczba|Maksimum|Maximum number of properties used allowed by the environment for S1/S2 SKU and maximum number of properties allowed by Warm Store for PAYG SKU|No Dimensions|
|WarmStorageUsedProperties|Warm Storage Used Properties |Liczba|Maksimum|Number of properties used by the environment for S1/S2 SKU and number of properties used by Warm Store for PAYG SKU|No Dimensions|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages|Liczba|Łącznie|Count of messages read from the event source|No Dimensions|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages|Liczba|Łącznie|Count of invalid messages read from the event source|No Dimensions|
|IngressReceivedBytes|Ingress Received Bytes|Bytes|Łącznie|Count of bytes read from the event source|No Dimensions|
|IngressStoredBytes|Ingress Stored Bytes|Bytes|Łącznie|Total size of events successfully processed and available for query|No Dimensions|
|IngressStoredEvents|Ingress Stored Events|Liczba|Łącznie|Count of flattened events successfully processed and available for query|No Dimensions|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag|Sekundy|Maksimum|Difference between the time that the message is enqueued in the event source and the time it is processed in Ingress|No Dimensions|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag|Liczba|Średnia|Difference between the sequence number of last enqueued message in the event source partition and sequence number of message being processed in Ingress|No Dimensions|
|WarmStorageMaxProperties|Warm Storage Max Properties|Liczba|Maksimum|Maximum number of properties used allowed by the environment for S1/S2 SKU and maximum number of properties allowed by Warm Store for PAYG SKU|No Dimensions|
|WarmStorageUsedProperties|Warm Storage Used Properties |Liczba|Maksimum|Number of properties used by the environment for S1/S2 SKU and number of properties used by Warm Store for PAYG SKU|No Dimensions|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|BytesPerSecond|Średnia|Average disk throughput due to read operations over the sample period.|No Dimensions|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|BytesPerSecond|Średnia|Average disk throughput due to write operations over the sample period.|No Dimensions|
|Disk Read Bytes|Disk Read Bytes|Bytes|Łącznie|Total disk throughput due to read operations over the sample period.|No Dimensions|
|Disk Write Bytes|Disk Write Bytes|Bytes|Łącznie|Total disk throughput due to write operations over the sample period.|No Dimensions|
|DiskReadOperations|Disk Read Operations|Liczba|Łącznie|The number of IO read operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|DiskWriteOperations|Disk Write Operations|Liczba|Łącznie|The number of IO write operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Średnia|The average number of IO read operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Średnia|The average number of IO write operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|DiskReadLatency|Disk Read Latency|Milliseconds|Średnia|Total read latency. The sum of the device and kernel read latencies.|No Dimensions|
|DiskWriteLatency|Disk Write Latency|Milliseconds|Średnia|Total write latency. The sum of the device and kernel write latencies.|No Dimensions|
|NetworkInBytesPerSecond|Network In Bytes/Sec|BytesPerSecond|Średnia|Average network throughput for received traffic.|No Dimensions|
|NetworkOutBytesPerSecond|Network Out Bytes/Sec|BytesPerSecond|Średnia|Average network throughput for transmitted traffic.|No Dimensions|
|Sieć — wejście|Sieć — wejście|Bytes|Łącznie|Total network throughput for received traffic.|No Dimensions|
|Sieć — wyjście|Sieć — wyjście|Bytes|Łącznie|Total network throughput for transmitted traffic.|No Dimensions|
|MemoryUsed|Memory Used|Bytes|Średnia|The amount of machine memory that is in use by the VM.|No Dimensions|
|MemoryGranted|Memory Granted|Bytes|Średnia|The amount of memory that was granted to the VM by the host. Memory is not granted to the host until it is touched one time and granted memory may be swapped out or ballooned away if the VMkernel needs the memory.|No Dimensions|
|MemoryActive|Memory Active|Bytes|Średnia|The amount of memory used by the VM in the past small window of time. This is the "true" number of how much memory the VM currently has need of. Additional, unused memory may be swapped out or ballooned with no impact to the guest's performance.|No Dimensions|
|Procentowe użycie procesora CPU|Procentowe użycie procesora CPU|Procent|Średnia|The CPU utilization. This value is reported with 100% representing all processor cores on the system. As an example, a 2-way VM using 50% of a four-core system is completely using two cores.|No Dimensions|
|PercentageCpuReady|Percentage CPU Ready|Milliseconds|Łącznie|Ready time is the time spend waiting for CPU(s) to become available in the past update interval.|No Dimensions|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuPercentage|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage|Memory Percentage|Procent|Średnia|Memory Percentage|Wystąpienie|
|DiskQueueLength|Disk Queue Length|Liczba|Średnia|Disk Queue Length|Wystąpienie|
|HttpQueueLength|Http Queue Length|Liczba|Średnia|Http Queue Length|Wystąpienie|
|BytesReceived|Data In|Bytes|Łącznie|Data In|Wystąpienie|
|BytesSent|Data Out|Bytes|Łącznie|Data Out|Wystąpienie|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (excluding functions)

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuTime|CPU Time|Sekundy|Łącznie|CPU Time|Wystąpienie|
|Żądania|Żądania|Liczba|Łącznie|Żądania|Wystąpienie|
|BytesReceived|Data In|Bytes|Łącznie|Data In|Wystąpienie|
|BytesSent|Data Out|Bytes|Łącznie|Data Out|Wystąpienie|
|Http101|Http 101|Liczba|Łącznie|Http 101|Wystąpienie|
|Http2xx|Http 2xx|Liczba|Łącznie|Http 2xx|Wystąpienie|
|Http3xx|Http 3xx|Liczba|Łącznie|Http 3xx|Wystąpienie|
|Http401|Http 401|Liczba|Łącznie|Http 401|Wystąpienie|
|Http403|Http 403|Liczba|Łącznie|Http 403|Wystąpienie|
|Http404|Http 404|Liczba|Łącznie|Http 404|Wystąpienie|
|Http406|Http 406|Liczba|Łącznie|Http 406|Wystąpienie|
|Http4xx|Http 4xx|Liczba|Łącznie|Http 4xx|Wystąpienie|
|Http5xx|Http Server Errors|Liczba|Łącznie|Http Server Errors|Wystąpienie|
|MemoryWorkingSet|Memory working set|Bytes|Średnia|Memory working set|Wystąpienie|
|AverageMemoryWorkingSet|Average memory working set|Bytes|Średnia|Average memory working set|Wystąpienie|
|AverageResponseTime|Average Response Time|Sekundy|Średnia|Average Response Time|Wystąpienie|
|AppConnections|Połączenia|Liczba|Średnia|Połączenia|Wystąpienie|
|Handles|Handle Count|Liczba|Średnia|Handle Count|Wystąpienie|
|Threads|Thread Count|Liczba|Średnia|Thread Count|Wystąpienie|
|PrivateBytes|Private Bytes|Bytes|Średnia|Private Bytes|Wystąpienie|
|IoReadBytesPerSecond|IO Read Bytes Per Second|BytesPerSecond|Łącznie|IO Read Bytes Per Second|Wystąpienie|
|IoWriteBytesPerSecond|IO Write Bytes Per Second|BytesPerSecond|Łącznie|IO Write Bytes Per Second|Wystąpienie|
|IoOtherBytesPerSecond|IO Other Bytes Per Second|BytesPerSecond|Łącznie|IO Other Bytes Per Second|Wystąpienie|
|IoReadOperationsPerSecond|IO Read Operations Per Second|BytesPerSecond|Łącznie|IO Read Operations Per Second|Wystąpienie|
|IoWriteOperationsPerSecond|IO Write Operations Per Second|BytesPerSecond|Łącznie|IO Write Operations Per Second|Wystąpienie|
|IoOtherOperationsPerSecond|IO Other Operations Per Second|BytesPerSecond|Łącznie|IO Other Operations Per Second|Wystąpienie|
|RequestsInApplicationQueue|Requests In Application Queue|Liczba|Średnia|Requests In Application Queue|Wystąpienie|
|CurrentAssemblies|Current Assemblies|Liczba|Średnia|Current Assemblies|Wystąpienie|
|TotalAppDomains|Total App Domains|Liczba|Średnia|Total App Domains|Wystąpienie|
|TotalAppDomainsUnloaded|Total App Domains Unloaded|Liczba|Średnia|Total App Domains Unloaded|Wystąpienie|
|Gen0Collections|Gen 0 Garbage Collections|Liczba|Łącznie|Gen 0 Garbage Collections|Wystąpienie|
|Gen1Collections|Gen 1 Garbage Collections|Liczba|Łącznie|Gen 1 Garbage Collections|Wystąpienie|
|Gen2Collections|Gen 2 Garbage Collections|Liczba|Łącznie|Gen 2 Garbage Collections|Wystąpienie|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (functions)

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|BytesReceived|Data In|Bytes|Łącznie|Data In|Wystąpienie|
|BytesSent|Data Out|Bytes|Łącznie|Data Out|Wystąpienie|
|Http5xx|Http Server Errors|Liczba|Łącznie|Http Server Errors|Wystąpienie|
|MemoryWorkingSet|Memory working set|Bytes|Średnia|Memory working set|Wystąpienie|
|AverageMemoryWorkingSet|Average memory working set|Bytes|Średnia|Average memory working set|Wystąpienie|
|FunctionExecutionUnits|Function Execution Units|MB / Milliseconds|Łącznie|[Function Execution Units](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Wystąpienie|
|FunctionExecutionCount|Function Execution Count|Liczba|Łącznie|Function Execution Count|Wystąpienie|
|PrivateBytes|Private Bytes|Bytes|Średnia|Private Bytes|Wystąpienie|
|IoReadBytesPerSecond|IO Read Bytes Per Second|BytesPerSecond|Łącznie|IO Read Bytes Per Second|Wystąpienie|
|IoWriteBytesPerSecond|IO Write Bytes Per Second|BytesPerSecond|Łącznie|IO Write Bytes Per Second|Wystąpienie|
|IoOtherBytesPerSecond|IO Other Bytes Per Second|BytesPerSecond|Łącznie|IO Other Bytes Per Second|Wystąpienie|
|IoReadOperationsPerSecond|IO Read Operations Per Second|BytesPerSecond|Łącznie|IO Read Operations Per Second|Wystąpienie|
|IoWriteOperationsPerSecond|IO Write Operations Per Second|BytesPerSecond|Łącznie|IO Write Operations Per Second|Wystąpienie|
|IoOtherOperationsPerSecond|IO Other Operations Per Second|BytesPerSecond|Łącznie|IO Other Operations Per Second|Wystąpienie|
|RequestsInApplicationQueue|Requests In Application Queue|Liczba|Średnia|Requests In Application Queue|Wystąpienie|
|CurrentAssemblies|Current Assemblies|Liczba|Średnia|Current Assemblies|Wystąpienie|
|TotalAppDomains|Total App Domains|Liczba|Średnia|Total App Domains|Wystąpienie|
|TotalAppDomainsUnloaded|Total App Domains Unloaded|Liczba|Średnia|Total App Domains Unloaded|Wystąpienie|
|Gen0Collections|Gen 0 Garbage Collections|Liczba|Łącznie|Gen 0 Garbage Collections|Wystąpienie|
|Gen1Collections|Gen 1 Garbage Collections|Liczba|Łącznie|Gen 1 Garbage Collections|Wystąpienie|
|Gen2Collections|Gen 2 Garbage Collections|Liczba|Łącznie|Gen 2 Garbage Collections|Wystąpienie|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|CpuTime|CPU Time|Sekundy|Łącznie|CPU Time|Wystąpienie|
|Żądania|Żądania|Liczba|Łącznie|Żądania|Wystąpienie|
|BytesReceived|Data In|Bytes|Łącznie|Data In|Wystąpienie|
|BytesSent|Data Out|Bytes|Łącznie|Data Out|Wystąpienie|
|Http101|Http 101|Liczba|Łącznie|Http 101|Wystąpienie|
|Http2xx|Http 2xx|Liczba|Łącznie|Http 2xx|Wystąpienie|
|Http3xx|Http 3xx|Liczba|Łącznie|Http 3xx|Wystąpienie|
|Http401|Http 401|Liczba|Łącznie|Http 401|Wystąpienie|
|Http403|Http 403|Liczba|Łącznie|Http 403|Wystąpienie|
|Http404|Http 404|Liczba|Łącznie|Http 404|Wystąpienie|
|Http406|Http 406|Liczba|Łącznie|Http 406|Wystąpienie|
|Http4xx|Http 4xx|Liczba|Łącznie|Http 4xx|Wystąpienie|
|Http5xx|Http Server Errors|Liczba|Łącznie|Http Server Errors|Wystąpienie|
|MemoryWorkingSet|Memory working set|Bytes|Średnia|Memory working set|Wystąpienie|
|AverageMemoryWorkingSet|Average memory working set|Bytes|Średnia|Average memory working set|Wystąpienie|
|AverageResponseTime|Average Response Time|Sekundy|Średnia|Average Response Time|Wystąpienie|
|FunctionExecutionUnits|Function Execution Units|Liczba|Łącznie|Function Execution Units|Wystąpienie|
|FunctionExecutionCount|Function Execution Count|Liczba|Łącznie|Function Execution Count|Wystąpienie|
|AppConnections|Połączenia|Liczba|Średnia|Połączenia|Wystąpienie|
|Handles|Handle Count|Liczba|Średnia|Handle Count|Wystąpienie|
|Threads|Thread Count|Liczba|Średnia|Thread Count|Wystąpienie|
|PrivateBytes|Private Bytes|Bytes|Średnia|Private Bytes|Wystąpienie|
|IoReadBytesPerSecond|IO Read Bytes Per Second|BytesPerSecond|Łącznie|IO Read Bytes Per Second|Wystąpienie|
|IoWriteBytesPerSecond|IO Write Bytes Per Second|BytesPerSecond|Łącznie|IO Write Bytes Per Second|Wystąpienie|
|IoOtherBytesPerSecond|IO Other Bytes Per Second|BytesPerSecond|Łącznie|IO Other Bytes Per Second|Wystąpienie|
|IoReadOperationsPerSecond|IO Read Operations Per Second|BytesPerSecond|Łącznie|IO Read Operations Per Second|Wystąpienie|
|IoWriteOperationsPerSecond|IO Write Operations Per Second|BytesPerSecond|Łącznie|IO Write Operations Per Second|Wystąpienie|
|IoOtherOperationsPerSecond|IO Other Operations Per Second|BytesPerSecond|Łącznie|IO Other Operations Per Second|Wystąpienie|
|RequestsInApplicationQueue|Requests In Application Queue|Liczba|Średnia|Requests In Application Queue|Wystąpienie|
|CurrentAssemblies|Current Assemblies|Liczba|Średnia|Current Assemblies|Wystąpienie|
|TotalAppDomains|Total App Domains|Liczba|Średnia|Total App Domains|Wystąpienie|
|TotalAppDomainsUnloaded|Total App Domains Unloaded|Liczba|Średnia|Total App Domains Unloaded|Wystąpienie|
|Gen0Collections|Gen 0 Garbage Collections|Liczba|Łącznie|Gen 0 Garbage Collections|Wystąpienie|
|Gen1Collections|Gen 1 Garbage Collections|Liczba|Łącznie|Gen 1 Garbage Collections|Wystąpienie|
|Gen2Collections|Gen 2 Garbage Collections|Liczba|Łącznie|Gen 2 Garbage Collections|Wystąpienie|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|Żądania|Żądania|Liczba|Łącznie|Żądania|Wystąpienie|
|BytesReceived|Data In|Bytes|Łącznie|Data In|Wystąpienie|
|BytesSent|Data Out|Bytes|Łącznie|Data Out|Wystąpienie|
|Http101|Http 101|Liczba|Łącznie|Http 101|Wystąpienie|
|Http2xx|Http 2xx|Liczba|Łącznie|Http 2xx|Wystąpienie|
|Http3xx|Http 3xx|Liczba|Łącznie|Http 3xx|Wystąpienie|
|Http401|Http 401|Liczba|Łącznie|Http 401|Wystąpienie|
|Http403|Http 403|Liczba|Łącznie|Http 403|Wystąpienie|
|Http404|Http 404|Liczba|Łącznie|Http 404|Wystąpienie|
|Http406|Http 406|Liczba|Łącznie|Http 406|Wystąpienie|
|Http4xx|Http 4xx|Liczba|Łącznie|Http 4xx|Wystąpienie|
|Http5xx|Http Server Errors|Liczba|Łącznie|Http Server Errors|Wystąpienie|
|AverageResponseTime|Average Response Time|Sekundy|Średnia|Average Response Time|Wystąpienie|
|CpuPercentage|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage|Memory Percentage|Procent|Średnia|Memory Percentage|Wystąpienie|
|DiskQueueLength|Disk Queue Length|Liczba|Średnia|Disk Queue Length|Wystąpienie|
|HttpQueueLength|Http Queue Length|Liczba|Średnia|Http Queue Length|Wystąpienie|
|ActiveRequests|Active Requests|Liczba|Łącznie|Active Requests|Wystąpienie|
|TotalFrontEnds|Total Front Ends|Liczba|Średnia|Total Front Ends|No Dimensions|
|SmallAppServicePlanInstances|Small App Service Plan Workers|Liczba|Średnia|Small App Service Plan Workers|No Dimensions|
|MediumAppServicePlanInstances|Medium App Service Plan Workers|Liczba|Średnia|Medium App Service Plan Workers|No Dimensions|
|LargeAppServicePlanInstances|Large App Service Plan Workers|Liczba|Średnia|Large App Service Plan Workers|No Dimensions|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metryka|Metric Display Name|Jednostka|Aggregation Type|Opis|Wymiary|
|---|---|---|---|---|---|
|WorkersTotal|Total Workers|Liczba|Średnia|Total Workers|No Dimensions|
|WorkersAvailable|Available Workers|Liczba|Średnia|Available Workers|No Dimensions|
|WorkersUsed|Used Workers|Liczba|Średnia|Used Workers|No Dimensions|
|CpuPercentage|Procent użycia procesora CPU|Procent|Średnia|Procent użycia procesora CPU|Wystąpienie|
|MemoryPercentage|Memory Percentage|Procent|Średnia|Memory Percentage|Wystąpienie|

## <a name="next-steps"></a>Następne kroki
* [Read about metrics in Azure Monitor](data-platform.md)
* [Create alerts on metrics](alerts-overview.md)
* [Export metrics to storage, Event Hub, or Log Analytics](resource-logs-overview.md)
