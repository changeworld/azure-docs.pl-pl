---
title: Widoki systemowe
description: Łącza do dokumentacji widoków systemowych obsługiwanych w usłudze SQL Analytics.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 01/06/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: b3089cfbd0abb4bafe160b07fd75df877d61741b
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350265"
---
# <a name="system-views-supported-in-sql-analytics"></a>Widoki systemowe obsługiwane w usłudze SQL Analytics
Łącza do dokumentacji instrukcji T-SQL obsługiwanych w usłudze SQL Analytics.

## <a name="sql-analytics-catalog-views"></a>Widoki wykazu analizy SQL
* [sys.pdw_column_distribution_properties](https://msdn.microsoft.com/library/mt204022.aspx)
* [sys.pdw_distributions](https://msdn.microsoft.com/library/mt203892.aspx)
* [sys.pdw_index_mappings](https://msdn.microsoft.com/library/mt203912.aspx)
* [sys.pdw_loader_backup_run_details](https://msdn.microsoft.com/library/mt203877.aspx)
* [sys.pdw_loader_backup_runs](https://msdn.microsoft.com/library/mt203884.aspx)
* [sys.pdw_materialized_view_column_distribution_properties](/sql/relational-databases/system-catalog-views/sys-pdw-materialized-view-column-distribution-properties-transact-sql?view=azure-sqldw-latest) (Wersja zapoznawcza)
* [sys.pdw_materialized_view_distribution_properties](/sql/relational-databases/system-catalog-views/sys-pdw-materialized-view-distribution-properties-transact-sql?view=azure-sqldw-latest) (wersja zapoznawcza)
* [sys.pdw_materialized_view_mappings](/sql/relational-databases/system-catalog-views/sys-pdw-materialized-view-mappings-transact-sql?view=azure-sqldw-latest) (Wersja zapoznawcza)
* [sys.pdw_nodes_column_store_dictionaries](https://msdn.microsoft.com/library/mt203902.aspx)
* [sys.pdw_nodes_column_store_row_groups](https://msdn.microsoft.com/library/mt203880.aspx)
* [sys.pdw_nodes_column_store_segments](https://msdn.microsoft.com/library/mt203916.aspx)
* [sys.pdw_nodes_columns](https://msdn.microsoft.com/library/mt203881.aspx)
* [sys.pdw_nodes_indexes](https://msdn.microsoft.com/library/mt203885.aspx)
* [sys.pdw_nodes_partitions](https://msdn.microsoft.com/library/mt203908.aspx)
* [sys.pdw_nodes_pdw_physical_databases](https://msdn.microsoft.com/library/mt203897.aspx)
* [sys.pdw_nodes_tables](https://msdn.microsoft.com/library/mt203886.aspx)
* [sys.pdw_replicated_table_cache_state](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-pdw-replicated-table-cache-state-transact-sql)
* [sys.pdw_table_distribution_properties](https://msdn.microsoft.com/library/mt203896.aspx)
* [sys.pdw_table_mappings](https://msdn.microsoft.com/library/mt203876.aspx)
* [sys.workload_management_workload_classifier_details](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifier-details-transact-sql)
* [sys.workload_management_workload_classifiers](/sql/relational-databases/system-catalog-views/sys-workload-management-workload-classifiers-transact-sql)

## <a name="sql-data-warehouse-dynamic-management-views-dmvs"></a>Widoki dynamicznego zarządzania magazynem danych SQL (DMV)
* [sys.dm_pdw_dms_cores](https://msdn.microsoft.com/library/mt203911.aspx)
* [sys.dm_pdw_dms_external_work](https://msdn.microsoft.com/library/mt204024.aspx)
* [sys.dm_pdw_dms_workers](https://msdn.microsoft.com/library/mt203878.aspx)
* [sys.dm_pdw_errors](https://msdn.microsoft.com/library/mt203904.aspx)
* [sys.dm_pdw_exec_connections](https://msdn.microsoft.com/library/mt203882.aspx)
* [sys.dm_pdw_exec_requests](https://msdn.microsoft.com/library/mt203887.aspx)
* [sys.dm_pdw_exec_sessions](https://msdn.microsoft.com/library/mt203883.aspx)
* [sys.dm_pdw_hadoop_operations](https://msdn.microsoft.com/library/mt204032.aspx)
* [sys.dm_pdw_lock_waits](https://msdn.microsoft.com/library/mt203901.aspx)
* [sys.dm_pdw_nodes](https://msdn.microsoft.com/library/mt203907.aspx)
* [sys.dm_pdw_nodes_database_encryption_keys](https://msdn.microsoft.com/library/mt203922.aspx)
* [sys.dm_pdw_os_threads](https://msdn.microsoft.com/library/mt203917.aspx)
* [sys.dm_pdw_request_steps](https://msdn.microsoft.com/library/mt203913.aspx)
* [sys.dm_pdw_resource_waits](https://msdn.microsoft.com/library/mt203906.aspx)
* [sys.dm_pdw_sql_requests](https://msdn.microsoft.com/library/mt203889.aspx)
* [sys.dm_pdw_sys_info](https://msdn.microsoft.com/library/mt203900.aspx)
* [sys.dm_pdw_wait_stats](https://msdn.microsoft.com/library/mt203909.aspx)
* [sys.dm_pdw_waits](/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-waits-transact-sql)
* [sys.dm_workload_management_workload_groups_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-workload-management-workload-group-stats-transact-sql) (Wersja zapoznawcza)

## <a name="sql-server-dmvs-applicable-to-sql-analytics"></a>Dmv programu SQL Server mające zastosowanie do usługi SQL Analytics
Następujące dmvs mają zastosowanie do analizy SQL Analytics, ale muszą być wykonywane przez połączenie z **główną** bazą danych.

* [sys.database_service_objectives](https://msdn.microsoft.com/library/mt712619.aspx)
* [sys.dm_operation_status](https://msdn.microsoft.com/library/dn270022.aspx)
* [sys.fn_helpcollations()](https://msdn.microsoft.com/library/ms187963.aspx)

## <a name="sql-server-catalog-views"></a>Widoki wykazu programu SQL Server
* [sys.all_columns](https://msdn.microsoft.com/library/ms177522.aspx)
* [sys.all_objects](https://msdn.microsoft.com/library/ms178618.aspx)
* [sys.all_parameters](https://msdn.microsoft.com/library/ms190340.aspx)
* [sys.all_sql_modules](https://msdn.microsoft.com/library/ms184389.aspx)
* [sys.all_views](https://msdn.microsoft.com/library/ms189510.aspx)
* [sys.assemblies](https://msdn.microsoft.com/library/ms189790.aspx)
* [sys.assembly_modules](https://msdn.microsoft.com/library/ms180052.aspx)
* [sys.assembly_types](https://msdn.microsoft.com/library/ms178020.aspx)
* [sys.certificates](https://msdn.microsoft.com/library/ms189774.aspx)
* [Sys.check_constraints](https://msdn.microsoft.com/library/ms187388.aspx)
* [Sys.columns](https://msdn.microsoft.com/library/ms176106.aspx)
* [sys.computed_columns](https://msdn.microsoft.com/library/ms188744.aspx)
* [sys.credentials](https://msdn.microsoft.com/library/ms189745.aspx)
* [Sys.data_spaces](https://msdn.microsoft.com/library/ms190289.aspx)
* [sys.database_credentials](https://msdn.microsoft.com/library/mt270282.aspx)
* [Sys.database_files](https://msdn.microsoft.com/library/ms174397.aspx)
* [sys.database_permissions](https://msdn.microsoft.com/library/ms188367.aspx)
* [Sys.database_principals](https://msdn.microsoft.com/library/ms187328.aspx)
* [sys.database_query_store_options](/sql/relational-databases/system-catalog-views/sys-database-query-store-options-transact-sql?view=azure-sqldw-latest)
* [Sys.database_role_members](https://msdn.microsoft.com/library/ms189780.aspx)
* [Sys.databases](https://msdn.microsoft.com/library/ms178534.aspx)
* [sys.default_constraints](https://msdn.microsoft.com/library/ms173758.aspx)
* [sys.external_data_sources](https://msdn.microsoft.com/library/dn935019.aspx)
* [sys.external_file_formats](https://msdn.microsoft.com/library/dn935025.aspx)
* [sys.external_tables](https://msdn.microsoft.com/library/dn935029.aspx)
* [grupy plików sys.file](https://msdn.microsoft.com/library/ms187782.aspx)
* [sys.foreign_key_columns](https://msdn.microsoft.com/library/ms186306.aspx)
* [Sys.foreign_keys](https://msdn.microsoft.com/library/ms189807.aspx)
* [sys.identity_columns](https://msdn.microsoft.com/library/ms187334.aspx)
* [sys.index_columns](https://msdn.microsoft.com/library/ms175105.aspx)
* [Sys.indexes](https://msdn.microsoft.com/library/ms173760.aspx)
* [sys.key_constraints](https://msdn.microsoft.com/library/ms174321.aspx)
* [sys.numbered_procedures](https://msdn.microsoft.com/library/ms179865.aspx)
* [Sys.objects](https://msdn.microsoft.com/library/ms190324.aspx)
* [Sys.parameters](https://msdn.microsoft.com/library/ms176074.aspx)
* [sys.partition_functions](https://msdn.microsoft.com/library/ms187381.aspx)
* [sys.partition_parameters](https://msdn.microsoft.com/library/ms175054.aspx)
* [sys.partition_range_values](https://msdn.microsoft.com/library/ms187780.aspx)
* [sys.partition_schemes](https://msdn.microsoft.com/library/ms189752.aspx)
* [Sys.partitions](https://msdn.microsoft.com/library/ms175012.aspx)
* [Sys.procedures](https://msdn.microsoft.com/library/ms188737.aspx)
* [sys.query_context_settings](/sql/relational-databases/system-catalog-views/sys-query-context-settings-transact-sql?view=azure-sqldw-latest)
* [sys.query_store_plan](/sql/relational-databases/system-catalog-views/sys-query-store-plan-transact-sql?view=azure-sqldw-latest)
* [sys.query_store_query](/sql/relational-databases/system-catalog-views/sys-query-store-query-transact-sql?view=azure-sqldw-latest)
* [sys.query_store_query_text](/sql/relational-databases/system-catalog-views/sys-query-store-query-text-transact-sql?view=azure-sqldw-latest)
* [sys.query_store_runtime_stats](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-transact-sql?view=azure-sqldw-latest)
* [sys.query_store_runtime_stats_interval](/sql/relational-databases/system-catalog-views/sys-query-store-runtime-stats-interval-transact-sql?view=azure-sqldw-latest)
* [sys.schemas](https://msdn.microsoft.com/library/ms176011.aspx)
* [sys.securable_classes](https://msdn.microsoft.com/library/ms408301.aspx)
* [Sys.sql_expression_dependencies](https://msdn.microsoft.com/library/bb677315.aspx)
* [Sys.sql_modules](https://msdn.microsoft.com/library/ms175081.aspx)
* [Sys.stats](https://msdn.microsoft.com/library/ms177623.aspx)
* [sys.stats_columns](https://msdn.microsoft.com/library/ms187340.aspx)
* [Sys.symmetric_keys](https://msdn.microsoft.com/library/ms189446.aspx)
* [sys.synonimy](https://msdn.microsoft.com/library/ms189458.aspx)
* [zestawy sys.syscharsets](https://msdn.microsoft.com/library/ms190300.aspx)
* [syscolumns sys.syscolumns](https://msdn.microsoft.com/library/ms186816.aspx)
* [sys.sysdanabases](https://msdn.microsoft.com/library/ms179900.aspx)
* [Sys.syslanguages](https://msdn.microsoft.com/library/ms190303.aspx)
* [sys.sysobjects](https://msdn.microsoft.com/library/ms177596.aspx)
* [sys.sysreferences](https://msdn.microsoft.com/library/ms186900.aspx)
* [sys.system_columns](https://msdn.microsoft.com/library/ms178596.aspx)
* [Sys.system_objects](https://msdn.microsoft.com/library/ms173551.aspx)
* [sys.system_parameters](https://msdn.microsoft.com/library/ms174367.aspx)
* [sys.system_sql_modules](https://msdn.microsoft.com/library/ms188034.aspx)
* [sys.system_views](https://msdn.microsoft.com/library/ms187764.aspx)
* [Sys.systypes](https://msdn.microsoft.com/library/ms175109.aspx)
* [sys.sysusers](https://msdn.microsoft.com/library/ms179871.aspx)
* [Sys.tables](https://msdn.microsoft.com/library/ms187406.aspx)
* [Sys.types](https://msdn.microsoft.com/library/ms188021.aspx)
* [Sys.views](https://msdn.microsoft.com/library/ms190334.aspx)

## <a name="sql-server-dmvs-available-in-sql-analytics"></a>Dmv programu SQL Server dostępne w usłudze SQL Analytics
Usługa SQL Analytics udostępnia wiele widoków dynamicznego zarządzania programu SQL Server (DMV). Te widoki, gdy kwerendy w sql analytics, zgłaszają stan baz danych SQL uruchomionych w dystrybucjach.

Usługa SQL Analytics i parallel data warehouse (PDW) używają tych samych widoków systemowych. Każdy DMV ma kolumnę o nazwie pdw_node_id, który jest identyfikatorem węzła Compute. 

> [!NOTE]
> Aby użyć tych widoków, wstaw nazwę "pdw_nodes_", jak pokazano w poniższej tabeli:
> 
> 

| Nazwa DMV w usłudze SQL Analytics | Sql Server Transact-SQL artykuł|
|:--- |:--- |
| sys.dm_pdw_nodes_db_column_store_row_group_physical_stats | [sys.dm_db_column_store_row_group_physical_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-physical-stats-transact-sql)| 
| sys.dm_pdw_nodes_db_column_store_row_group_operational_stats | [sys.dm_db_column_store_row_group_operational_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-column-store-row-group-operational-stats-transact-sql)| 
| sys.dm_pdw_nodes_db_file_space_usage |[Sys.dm_db_file_space_usage](https://msdn.microsoft.com/library/ms174412.aspx) |
| sys.dm_pdw_nodes_db_index_usage_stats |[sys.dm_db_index_usage_stats](https://msdn.microsoft.com/library/ms188755.aspx) |
| sys.dm_pdw_nodes_db_partition_stats |[sys.dm_db_partition_stats](https://msdn.microsoft.com/library/ms187737.aspx) |
| sys.dm_pdw_nodes_db_session_space_usage |[sys.dm_db_session_space_usage](https://msdn.microsoft.com/library/ms187938.aspx) |
| sys.dm_pdw_nodes_db_task_space_usage |[sys.dm_db_task_space_usage](https://msdn.microsoft.com/library/ms190288.aspx) |
| sys.dm_pdw_nodes_exec_background_job_queue |[sys.dm_exec_background_job_queue](https://msdn.microsoft.com/library/ms173512.aspx) |
| sys.dm_pdw_nodes_exec_background_job_queue_stats |[sys.dm_exec_background_job_queue_stats](https://msdn.microsoft.com/library/ms176059.aspx) |
| sys.dm_pdw_nodes_exec_cached_plans |[Sys.dm_exec_cached_plans](https://msdn.microsoft.com/library/ms187404.aspx) |
| sys.dm_pdw_nodes_exec_connections |[sys.dm_exec_connections](https://msdn.microsoft.com/library/ms181509.aspx) |
| sys.dm_pdw_nodes_exec_procedure_stats |[Sys.dm_exec_procedure_stats](https://msdn.microsoft.com/library/cc280701.aspx) |
| sys.dm_pdw_nodes_exec_query_memory_grants |[Sys.dm_exec_query_memory_grants](https://msdn.microsoft.com/library/ms365393.aspx) |
| sys.dm_pdw_nodes_exec_query_optimizer_info |[sys.dm_exec_query_optimizer_info](https://msdn.microsoft.com/library/ms175002.aspx) |
| sys.dm_pdw_nodes_exec_query_resource_semaphores |[Sys.dm_exec_query_resource_semaphores](https://msdn.microsoft.com/library/ms366321.aspx) |
| sys.dm_pdw_nodes_exec_query_stats |[Sys.dm_exec_query_stats](https://msdn.microsoft.com/library/ms189741.aspx) |
| sys.dm_pdw_nodes_exec_requests |[Sys.dm_exec_requests](https://msdn.microsoft.com/library/ms177648.aspx) |
| sys.dm_pdw_nodes_exec_sessions |[sys.dm_exec_sessions](https://msdn.microsoft.com/library/ms176013.aspx) |
| sys.dm_pdw_nodes_io_pending_io_requests |[sys.dm_io_pending_io_requests](https://msdn.microsoft.com/library/ms188762.aspx) |
| sys.dm_pdw_nodes_io_virtual_file_stats |[Sys.dm_io_virtual_file_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-io-virtual-file-stats-transact-sql) |
| sys.dm_pdw_nodes_os_buffer_descriptors |[sys.dm_os_buffer_descriptors](https://msdn.microsoft.com/library/ms173442.aspx) |
| sys.dm_pdw_nodes_os_child_instances |[sys.dm_os_child_instances](https://msdn.microsoft.com/library/ms165698.aspx) |
| sys.dm_pdw_nodes_os_cluster_nodes |[sys.dm_os_cluster_nodes](https://msdn.microsoft.com/library/ms187341.aspx) |
| sys.dm_pdw_nodes_os_dispatcher_pools |[sys.dm_os_dispatcher_pools](https://msdn.microsoft.com/library/bb630336.aspx) |
| sys.dm_pdw_nodes_os_dispatchers |Dokumentacja transact-SQL jest niedostępna. |
| sys.dm_pdw_nodes_os_hosts |[sys.dm_os_hosts](https://msdn.microsoft.com/library/ms187800.aspx) |
| sys.dm_pdw_nodes_os_latch_stats |[Sys.dm_os_latch_stats](https://msdn.microsoft.com/library/ms175066.aspx) |
| sys.dm_pdw_nodes_os_memory_brokers |[sys.dm_os_memory_brokers](https://msdn.microsoft.com/library/bb522548.aspx) |
| sys.dm_pdw_nodes_os_memory_cache_clock_hands |[sys.dm_os_memory_cache_clock_hands](https://msdn.microsoft.com/library/ms173786.aspx) |
| sys.dm_pdw_nodes_os_memory_cache_counters |[sys.dm_os_memory_cache_counters](https://msdn.microsoft.com/library/ms188760.aspx) |
| sys.dm_pdw_nodes_os_memory_cache_entries |[sys.dm_os_memory_cache_entries](https://msdn.microsoft.com/library/ms189488.aspx) |
| sys.dm_pdw_nodes_os_memory_cache_hash_tables |[sys.dm_os_memory_cache_hash_tables](https://msdn.microsoft.com/library/ms182388.aspx) |
| sys.dm_pdw_nodes_os_memory_clerks |[Sys.dm_os_memory_clerks](https://msdn.microsoft.com/library/ms175019.aspx) |
| sys.dm_pdw_nodes_os_memory_node_access_stats |Dokumentacja transact-SQL jest niedostępna. |
| sys.dm_pdw_nodes_os_memory_nodes |[sys.dm_os_memory_nodes](https://msdn.microsoft.com/library/bb510622.aspx) |
| sys.dm_pdw_nodes_os_memory_objects |[Sys.dm_os_memory_objects](https://msdn.microsoft.com/library/ms179875.aspx) |
| sys.dm_pdw_nodes_os_memory_pools |[sys.dm_os_memory_pools](https://msdn.microsoft.com/library/ms175022.aspx) |
| sys.dm_pdw_nodes_os_nodes |[sys.dm_os_nodes](https://msdn.microsoft.com/library/bb510628.aspx) |
| sys.dm_pdw_nodes_os_performance_counters |[sys.dm_os_performance_counters](https://msdn.microsoft.com/library/ms187743.aspx) |
| sys.dm_pdw_nodes_os_process_memory |[sys.dm_os_process_memory](https://msdn.microsoft.com/library/bb510747.aspx) |
| sys.dm_pdw_nodes_os_schedulers |[sys.dm_os_schedulers](https://msdn.microsoft.com/library/ms177526.aspx) |
| sys.dm_pdw_nodes_os_spinlock_stats |Dokumentacja transact-SQL jest niedostępna. |
| sys.dm_pdw_nodes_os_sys_info |[Sys.dm_os_sys_info](https://msdn.microsoft.com/library/ms175048.aspx) |
| sys.dm_pdw_nodes_os_sys_memory |[sys.dm_os_memory_nodes](https://msdn.microsoft.com/library/bb510622.aspx) |
| sys.dm_pdw_nodes_os_tasks |[Sys.dm_os_tasks](https://msdn.microsoft.com/library/ms174963.aspx) |
| sys.dm_pdw_nodes_os_threads |[sys.dm_os_threads](https://msdn.microsoft.com/library/ms187818.aspx) |
| sys.dm_pdw_nodes_os_virtual_address_dump |[sys.dm_os_virtual_address_dump](https://msdn.microsoft.com/library/ms186294.aspx) |
| sys.dm_pdw_nodes_os_wait_stats |[Sys.dm_os_wait_stats](https://msdn.microsoft.com/library/ms179984.aspx) |
| sys.dm_pdw_nodes_os_waiting_tasks |[Sys.dm_os_waiting_tasks](https://msdn.microsoft.com/library/ms188743.aspx) |
| sys.dm_pdw_nodes_os_workers |[sys.dm_os_workers](https://msdn.microsoft.com/library/ms178626.aspx) |
| sys.dm_pdw_nodes_tran_active_snapshot_database_transactions |[Sys.dm_tran_active_snapshot_database_transactions](https://msdn.microsoft.com/library/ms180023.aspx) |
| sys.dm_pdw_nodes_tran_active_transactions |[sys.dm_tran_active_transactions](https://msdn.microsoft.com/library/ms174302.aspx) |
| sys.dm_pdw_nodes_tran_commit_table |[sys.dm_tran_commit_table](https://msdn.microsoft.com/library/cc645959.aspx) |
| sys.dm_pdw_nodes_tran_current_snapshot |[sys.dm_tran_current_snapshot](https://msdn.microsoft.com/library/ms184390.aspx) |
| sys.dm_pdw_nodes_tran_current_transaction |[sys.dm_tran_current_transaction](https://msdn.microsoft.com/library/ms186327.aspx) |
| sys.dm_pdw_nodes_tran_database_transactions |[sys.dm_tran_database_transactions](https://msdn.microsoft.com/library/ms186957.aspx) |
| sys.dm_pdw_nodes_tran_locks |[Sys.dm_tran_locks](https://msdn.microsoft.com/library/ms190345.aspx) |
| sys.dm_pdw_nodes_tran_session_transactions |[sys.dm_tran_session_transactions](https://msdn.microsoft.com/library/ms188739.aspx) |
| sys.dm_pdw_nodes_tran_top_version_generators |[sys.dm_tran_top_version_generators](https://msdn.microsoft.com/library/ms188778.aspx) |

## <a name="sql-server-2016-polybase-dmvs-available-in-sql-analytics"></a>Moduły DMV polybase programu SQL Server 2016 dostępne w usłudze SQL Analytics
Następujące dmvs mają zastosowanie do analizy SQL Analytics, ale muszą być wykonywane przez połączenie z **główną** bazą danych.

* [sys.dm_exec_compute_node_errors](https://msdn.microsoft.com/library/mt146380.aspx)
* [sys.dm_exec_compute_node_status](https://msdn.microsoft.com/library/mt146382.aspx)
* [sys.dm_exec_compute_nodes](https://msdn.microsoft.com/library/mt130700.aspx)
* [sys.dm_exec_distributed_request_steps](https://msdn.microsoft.com/library/mt130701.aspx)
* [sys.dm_exec_distributed_requests](https://msdn.microsoft.com/library/mt146385.aspx)
* [sys.dm_exec_distributed_sql_requests](https://msdn.microsoft.com/library/mt146390.aspx)
* [sys.dm_exec_dms_services](https://msdn.microsoft.com/library/mt146374.aspx)
* [sys.dm_exec_dms_workers](https://msdn.microsoft.com/library/mt146392.aspx)
* [sys.dm_exec_external_operations](https://msdn.microsoft.com/library/mt146391.aspx)
* [sys.dm_exec_external_work](https://msdn.microsoft.com/library/mt146375.aspx)

## <a name="sql-server-information_schema-views"></a>Widoki INFORMATION_SCHEMA programu SQL Server
* [Check_constraints](https://msdn.microsoft.com/library/ms189772.aspx)
* [Kolumny](https://msdn.microsoft.com/library/ms188348.aspx)
* [Parametry](https://msdn.microsoft.com/library/ms173796.aspx)
* [Procedur](https://msdn.microsoft.com/library/ms188757.aspx)
* [SCHEMATA](https://msdn.microsoft.com/library/ms182642.aspx)
* [Tabel](https://msdn.microsoft.com/library/ms186224.aspx)
* [VIEW_COLUMN_USAGE](https://msdn.microsoft.com/library/ms190492.aspx)
* [VIEW_TABLE_USAGE](https://msdn.microsoft.com/library/ms173869.aspx)
* [Widoki](https://msdn.microsoft.com/library/ms181381.aspx)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji referencyjnych, zobacz [Instrukcje T-SQL w usłudze SQL Analytics](sql-data-warehouse-reference-tsql-statements.md)i [elementy języka T-SQL w usłudze SQL Analytics](sql-data-warehouse-reference-tsql-language-elements.md).
