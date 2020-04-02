---
title: 'Łączenie się z różnymi źródłami danych z usługi Azure Databricks '
description: Dowiedz się, jak połączyć się z usługą Azure SQL Database, usługa Azure Data Lake Store, magazyn obiektów blob, usługa Cosmos DB, centra zdarzeń i usługa Azure SQL Data Warehouse z usługi Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: 79a821a4c8fe4cb2d048f0dcb0a6e091462a1779
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80548790"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Łączenie się ze źródłami danych z usługi Azure Databricks

Ten artykuł zawiera łącza do wszystkich różnych źródeł danych na platformie Azure, które mogą być połączone z usługi Azure Databricks. Postępuj zgodnie z przykładami w tych łączach, aby wyodrębnić dane ze źródeł danych platformy Azure (na przykład usługi Azure Blob Storage, usługi Azure Event Hubs itp.) do klastra usługi Azure Databricks i uruchomić zadania analityczne na nich. 

## <a name="prerequisites"></a>Wymagania wstępne

* Musisz mieć obszar roboczy usługi Azure Databricks i klaster platformy Spark. Postępuj zgodnie z instrukcjami w [wprowadzenie do usługi Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Źródła danych dla usługi Azure Databricks

Poniższa lista zawiera źródła danych na platformie Azure, których można używać z usługą Azure Databricks. Aby uzyskać pełną listę źródeł danych, które mogą być używane z usługą Azure Databricks, zobacz [Źródła danych dla usługi Azure Databricks](/azure/databricks/data/data-sources/index).

- [Baza danych SQL platformy Azure](/azure/databricks/data/data-sources/sql-databases)

    To łącze zawiera interfejs API DataFrame do łączenia się z bazami danych SQL przy użyciu JDBC i jak kontrolować równoległość odczytów za pośrednictwem interfejsu JDBC. W tym temacie przedstawiono szczegółowe przykłady przy użyciu scala interfejsu API, z skróconymi przykładami języka Python i Spark SQL na końcu.
- [Azure Data Lake Storage](/azure/databricks/data/data-sources/azure/azure-datalake-gen2)

    To łącze zawiera przykłady dotyczące sposobu uwierzytelniania za pomocą jednostki usługi Azure Active Directory w celu uwierzytelnienia za pomocą usługi Azure Data Lake Storage. Zawiera również instrukcje dotyczące uzyskiwania dostępu do danych w usłudze Azure Data Lake Storage z usługi Azure Databricks.

- [Magazyn obiektów blob platformy Azure](/azure/databricks/data/data-sources/azure/azure-storage)

    To łącze zawiera przykłady dotyczące bezpośredniego dostępu do usługi Azure Blob Storage z usługi Azure Databricks przy użyciu klucza dostępu lub sygnatury dostępu współdzielonego dla danego kontenera. Łącze zawiera również informacje na temat uzyskiwania dostępu do usługi Azure Blob Storage z usługi Azure Databricks przy użyciu interfejsu API RDD.

- [Azure Cosmos DB](/azure/databricks/data/data-sources/azure/cosmosdb-connector)

    Ta łącze zawiera instrukcje dotyczące korzystania z [łącznika platformy Azure Cosmos DB Spark](https://github.com/Azure/azure-cosmosdb-spark) z usługi Azure Databricks w celu uzyskania dostępu do danych w usłudze Azure Cosmos DB.

- [Azure Event Hubs](/azure/event-hubs/event-hubs-spark-connector)

    To łącze zawiera instrukcje dotyczące korzystania z [łącznika platformy Azure Event Hubs Spark](https://github.com/Azure/azure-event-hubs-spark) z usługi Azure Databricks w celu uzyskania dostępu do danych w usłudze Azure Event Hubs.

- [Magazyn danych SQL platformy Azure](/azure/synapse-analytics/sql-data-warehouse/)

    To łącze zawiera instrukcje dotyczące używania łącznika usługi Azure SQL Data Warehouse do łączenia się z usługi Azure Databricks.
    

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o źródłach, z których można importować dane do usługi Azure Databricks, zobacz [Źródła danych dla usługi Azure Databricks](/azure/databricks/data/data-sources/index).


