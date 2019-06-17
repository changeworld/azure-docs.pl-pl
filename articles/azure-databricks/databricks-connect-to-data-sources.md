---
title: 'Nawiązać połączenie z różnych źródeł danych z usługi Azure Databricks '
description: Dowiedz się, jak połączyć się z różnych źródeł danych z usługi Azure Databricks.
services: azure-databricks
author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/21/2018
ms.author: mamccrea
ms.openlocfilehash: f2b7136ec21416e31c2af658974577023a4494de
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60240349"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Łączenie ze źródłami danych z usługi Azure Databricks

Ten artykuł zawiera linki do wszystkich źródeł danych na platformie Azure, które mogą być połączone z usługi Azure Databricks. Postępuj zgodnie z przykładami w poniższych linków, aby wyodrębnić dane ze źródeł danych na platformie Azure (np. usługi Azure Blob Storage, Azure Event Hubs itp.) do klastra usługi Azure Databricks, a w nich wykonywane zadania analityczne. 

## <a name="prerequisites"></a>Wymagania wstępne

* Musisz mieć obszar roboczy usługi Azure Databricks oraz klaster Spark. Postępuj zgodnie z instrukcjami w artykule [Rozpoczynanie pracy z usługą Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Źródła danych dla usługi Azure Databricks

Poniższa lista zawiera źródła danych na platformie Azure, można użyć z usługą Azure Databricks. Aby uzyskać pełną listę źródeł danych, które mogą być używane z usługi Azure Databricks, zobacz [źródeł danych dla usługi Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html).

- [Usługa Azure SQL database](https://docs.azuredatabricks.net/spark/latest/data-sources/sql-databases.html)

    Ten link zapewnia interfejs API ramkę danych do łączenia się z bazy danych SQL przy użyciu sterownika JDBC i sposobie kontrolowania równoległości operacji odczytu za pomocą interfejsu JDBC. Ten temat zawiera szczegółowe przykłady, za pomocą interfejsu API języka Scala, za pomocą języka Python skróconej i przykłady Spark SQL na końcu.
- [Azure Data Lake Store](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake-gen2.html)

    Ten link zawiera przykłady dotyczące korzystania z jednostki usługi Azure Active Directory do uwierzytelniania w usłudze Data Lake Store. Zawiera również instrukcje dotyczące sposobu dostępu do danych w Data Lake Store z usługi Azure Databricks.

- [Azure Blob Storage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html)

    Ten link zawiera przykłady dotyczące sposobu uzyskania bezpośredniego dostępu do usługi Azure Blob Storage z usługi Azure Databricks przy użyciu sygnatury dostępu Współdzielonego lub klucza dostępu dla danego kontenera. Link także informacje o tym, jak uzyskać dostęp do usługi Azure Blob Storage z usługi Azure Databricks przy użyciu interfejsu API RDD.

- [Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

    Ten link zawiera instrukcje dotyczące sposobu używania [łącznika usługi Azure Cosmos DB Spark](https://github.com/Azure/azure-cosmosdb-spark) z usługi Azure Databricks w celu dostępu do danych w usłudze Azure Cosmos DB.

- [Azure Event Hubs](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/eventhubs-connector.html)

    Ten link zawiera instrukcje dotyczące sposobu używania [łącznika usługi Azure Event Hubs Spark](https://github.com/Azure/azure-event-hubs-spark) z usługi Azure Databricks w celu dostępu do danych w usłudze Azure Event Hubs.

- [Azure SQL Data Warehouse](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/sql-data-warehouse.html)

    Ten link zawiera instrukcje dotyczące sposobu korzystania z łącznika usługi Azure SQL Data Warehouse, aby połączyć się z usługi Azure Databricks.
    

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat źródeł, z których możesz zaimportować dane do usługi Azure Databricks, zobacz [źródeł danych dla usługi Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#).


