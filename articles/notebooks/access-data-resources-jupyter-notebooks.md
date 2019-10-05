---
title: Dostęp do zasobów danych z notesów Jupyter na platformie Azure
description: Jak uzyskać dostęp do plików, interfejsów API REST, baz danych i różnych zasobów usługi Azure Storage z notesu Jupyter.
services: app-service
documentationcenter: ''
author: kraigb
manager: barbkess
ms.assetid: ee867303-a5e5-4686-b2da-8a0108247d18
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: e1284072db8ca8aa7a49bd3c4d52f54e5d09420a
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2019
ms.locfileid: "71970259"
---
# <a name="access-cloud-data-in-a-notebook"></a>Dostęp do danych w chmurze w notesie

Wykonanie interesującej pracy w notesie Jupyter wymaga danych. Dane, faktycznie, to Lifeblood notesów.

Można na pewno [zaimportować pliki danych do projektu](work-with-project-data-files.md), nawet używając poleceń takich jak `curl` z poziomu notesu, aby pobrać plik bezpośrednio. Jest to jednak konieczne, aby pracować z znacznie bardziej rozbudowanymi danymi, które są dostępne w źródłach niezwiązanych z plikami, takimi jak interfejsy API REST, relacyjne bazy danych i magazyn w chmurze, takie jak tabele platformy Azure.

W tym artykule krótko opisano te różne opcje. Ze względu na to, że dostęp do danych jest najlepiej widoczny w akcji, można znaleźć kod możliwy do uruchomienia w [Azure Notebooks próbkach — dostęp do danych](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

## <a name="rest-apis"></a>Interfejsy API REST

Ogólnie mówiąc, olbrzymia ilość danych dostępnych w Internecie jest dostępna nie za pośrednictwem plików, ale za pośrednictwem interfejsów API REST. Na szczęście, ponieważ komórka notesu może zawierać dowolny kod, który lubisz, można użyć kodu do wysyłania żądań i odbierania danych JSON. Następnie można przekonwertować ten kod JSON na dowolny format, który ma być używany, taki jak Pandas Dataframe.

Aby uzyskać dostęp do danych przy użyciu interfejsu API REST, należy użyć tego samego kodu w komórkach kodu notesu, które są używane w dowolnej innej aplikacji. Ogólna struktura przy użyciu biblioteki Requests jest następująca:

```python
import pandas
import requests

# New York City taxi data for 2014
data_url = 'https://data.cityofnewyork.us/resource/gkne-dk5s.json'

# General data request; include other API keys and credentials as needed in the data argument
response = requests.get(data_url, data={"limit": "20"})

if response.status_code == 200:
    dataframe_rest2 = pandas.DataFrame.from_records(response.json())
    print(dataframe_rest2)
```

## <a name="azure-sql-databases"></a>Bazy danych SQL Azure

Dostęp do SQL Server baz danych można uzyskać za pomocą bibliotek moduł pyodbc lub pymssql.

[Używanie języka Python do wykonywania zapytań w bazie danych Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) zawiera instrukcje dotyczące tworzenia bazy danych zawierającej dane AdventureWorks i pokazujące, jak wykonywać zapytania dotyczące tych danych. Ten sam kod jest przedstawiony w notesie przykładowym dla tego artykułu.

## <a name="azure-storage"></a>Azure Storage

Usługa Azure Storage oferuje kilka różnych typów magazynu nierelacyjnego, w zależności od typu danych i sposobu dostępu do niego:

- Table Storage: zapewnia niski koszt magazynowania magazynu o dużej pojemności dla danych tabelarycznych, na przykład zebranych dzienników czujników, dzienników diagnostycznych itd.
- BLOB Storage: zapewnia magazyn podobne do plików dla dowolnego typu danych.

W przykładowym notesie przedstawiono pracę z tabelami i obiektami BLOB, w tym używanie sygnatury dostępu współdzielonego w celu zezwalania na dostęp tylko do odczytu do obiektów BLOB.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB zapewnia w pełni indeksowany Magazyn NoSQL dla dokumentów JSON. Poniższe artykuły zawierają różne sposoby pracy z Cosmos DBami z poziomu języka Python:

- [Tworzenie aplikacji interfejsu API SQL przy użyciu języka Python](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Tworzenie aplikacji do kolby z interfejsem API Azure Cosmos DB dla MongoDB](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Tworzenie bazy danych grafu przy użyciu języka Python i interfejsu API Gremlin](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Tworzenie aplikacji Cassandra za pomocą języka Python i Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Tworzenie aplikacji interfejs API tabel przy użyciu języka Python i Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

Podczas pracy z Cosmos DB można użyć biblioteki [Azure-cosmosdb-Table](https://pypi.org/project/azure-cosmosdb-table/) .

## <a name="other-azure-databases"></a>Inne bazy danych platformy Azure

Platforma Azure udostępnia wiele innych typów baz danych, których można użyć. W poniższych artykułach przedstawiono wskazówki dotyczące uzyskiwania dostępu do tych baz danych z języka Python:

- [Azure Database for PostgreSQL: używanie języka Python do łączenia i wykonywania zapytań dotyczących danych](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Szybki Start: korzystanie z Azure Redis Cache w języku Python](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Azure Database for MySQL: używanie języka Python do łączenia i wykonywania zapytań dotyczących danych](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Kreator kopiowania dla Azure Data Factory](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Następne kroki

- [Instrukcje: współdziałanie z plikami danych projektu](work-with-project-data-files.md)
