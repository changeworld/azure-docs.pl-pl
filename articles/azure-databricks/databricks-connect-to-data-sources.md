---
title: Nawiązywanie połączenia różnych źródeł danych z usługi Azure Databricks | Dokumentacja firmy Microsoft
description: Dowiedz się, jak połączyć się z różnych źródeł danych z Azure Databricks.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: nitinme
ms.openlocfilehash: 865313a7c6eabd847529b88ff5fff0b7db438fa5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2018
ms.locfileid: "30174277"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Nawiązywać połączenia ze źródłami danych z Azure Databricks

Ten artykuł zawiera linki do wszystkich źródeł danych na platformie Azure, które mogą być połączone z Azure Databricks. Wykonaj w przykładach zawartych w poniższych linków, aby wyodrębnić dane ze źródeł danych Azure (na przykład magazyn obiektów Blob Azure, Azure Event Hubs, itp.) do klastra Azure Databricks i uruchamianie zadań analityczne na nich. 

## <a name="prerequisites"></a>Wymagania wstępne

* Należy obszarem roboczym Azure Databricks oraz klaster Spark. Postępuj zgodnie z instrukcjami w [wprowadzenie do usługi Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Źródła danych dla Azure Databricks

Poniższa lista zawiera źródła danych w Azure, której można używać z Azure Databricks. Aby uzyskać pełną listę źródeł danych, które mogą być używane z Azure Databricks, zobacz [źródła danych dla usługi Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html).

- [Baza danych SQL Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/sql-databases.html)

    Ten link zawiera DataFrame API łączenie z bazami danych SQL przy użyciu JDBC i sterowanie równoległości odczyty za pośrednictwem interfejsu JDBC. Ten temat zawiera szczegółowe przykłady przy użyciu interfejsu API programu Scala o skrócie Python i przykłady Spark SQL na końcu.
- [Azure Data Lake Store](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html)

    Ten link zawiera przykłady dotyczące sposobu używania nazwy głównej usługi Azure Active Directory do uwierzytelniania w usłudze Data Lake Store. Zawiera również instrukcje dotyczące sposobu dostępu do danych w usłudze Data Lake Store z Azure Databricks.

- [Azure Blob Storage](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html)

    To łącze przykłady bezpośredni dostęp do magazynu obiektów Blob Azure z Azure Databricks przy użyciu klucza dostępu lub sygnatury dostępu Współdzielonego dla danego kontenera. Łącze są także informacje o tym, jak uzyskać dostępu do magazynu obiektów Blob Azure z Azure Databricks przy użyciu interfejsu API RDD.

- [Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

    Ten link zawiera instrukcje dotyczące sposobu używania [łącznika usługi Azure Spark DB rozwiązania Cosmos](https://github.com/Azure/azure-cosmosdb-spark) z Databricks Azure w celu dostępu do danych w usłudze Azure DB rozwiązania Cosmos.

- [Azure Event Hubs](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/eventhubs-connector.html)

    Ten link zawiera instrukcje dotyczące sposobu używania [łącznika usługi Azure Spark centra zdarzeń](https://github.com/Azure/azure-event-hubs-spark) z Databricks Azure w celu dostępu do danych w usłudze Azure Event Hubs.

- [Azure SQL Data Warehouse](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/sql-data-warehouse.html)

    Ten link zawiera instrukcje dotyczące sposobu korzystania z łącznika usługi Azure SQL Data Warehouse do łączenia z Azure Databricks.
    

## <a name="next-steps"></a>Kolejne kroki

Aby uzyskać informacje dotyczące źródła, z których możesz zaimportować dane Azure Databricks, zobacz [źródła danych dla usługi Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#).


