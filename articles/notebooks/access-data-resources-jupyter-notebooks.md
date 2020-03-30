---
title: Dostęp do danych w notesach jupyter — usługa Azure Notebooks Preview
description: Dowiedz się, jak uzyskać dostęp do plików, interfejsów API REST, baz danych i różnych zasobów usługi Azure Storage z notesu usługi Jupyter.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 47d2f869021851c1451a66a84b1a70ec4ff4998f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646351"
---
# <a name="access-cloud-data-in-a-notebook"></a>Dostęp do danych z chmury w notesie

Wykonywanie ciekawej pracy w notebooku Jupyter wymaga danych. Dane, rzeczywiście, jest ratą notebooków.

Z pewnością można [importować pliki danych do projektu,](work-with-project-data-files.md)nawet za pomocą poleceń, takich jak `curl` z notesu, aby pobrać plik bezpośrednio. Jest jednak prawdopodobne, że trzeba pracować z dużo bardziej obszerne dane, które są dostępne ze źródeł innych niż pliki, takich jak interfejsy API REST, relacyjne bazy danych i magazynu w chmurze, takich jak tabele platformy Azure.

W tym artykule pokrótce opisano te różne opcje. Ponieważ dostęp do danych jest najlepiej widoczny w akcji, można znaleźć kod do uruchomienia w [przykładach notesów platformy Azure — dostęp do danych](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="rest-apis"></a>Interfejsy API REST

Ogólnie rzecz biorąc, ogromna ilość danych dostępnych z Internetu jest dostępna nie za pośrednictwem plików, ale za pośrednictwem interfejsów API REST. Na szczęście, ponieważ komórka notesu może zawierać dowolny kod, można użyć kodu do wysyłania żądań i odbierania danych JSON. Następnie można przekonwertować ten JSON na dowolny format, którego chcesz użyć, na przykład ramkę danych pandas.

Aby uzyskać dostęp do danych przy użyciu interfejsu API REST, należy użyć tego samego kodu w komórkach kodu notesu, które są używane w dowolnej innej aplikacji. Ogólna struktura przy użyciu biblioteki żądań jest następująca:

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

## <a name="azure-sql-databases"></a>Bazy danych SQL platformy Azure

Dostęp do baz danych programu SQL Server można uzyskać za pomocą bibliotek pyodbc lub pymssql.

[Użyj języka Python do kwerendy bazy danych SQL platformy Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) zawiera instrukcje dotyczące tworzenia bazy danych zawierającej dane AdventureWorks i pokazuje, jak kwerendy tych danych. Ten sam kod jest wyświetlany w notesie przykładu dla tego artykułu.

## <a name="azure-storage"></a>Azure Storage

Usługa Azure Storage udostępnia kilka różnych typów magazynu nierelacyjnego, w zależności od typu posiadanych danych i sposobu uzyskiwania do nich dostępu:

- Magazyn tabel: zapewnia niedrogie, dużej ilości pamięci masowej dla danych tabelaryczne, takich jak zebrane dzienniki czujników, dzienniki diagnostyczne i tak dalej.
- Magazyn obiektów blob: zapewnia magazyn podobny do pliku dla dowolnego typu danych.

Przykładowy notes pokazuje pracę z tabelami i obiektami blob, w tym sposób używania podpisu dostępu współdzielonego w celu umożliwienia dostępu tylko do odczytu do obiektów blob.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Usługa Azure Cosmos DB udostępnia w pełni indeksowany magazyn NoSQL dla dokumentów JSON). Następujące artykuły zawierają wiele różnych sposobów pracy z usługą Cosmos DB z języka Python:

- [Tworzenie aplikacji interfejsu API SQL za pomocą języka Python](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Tworzenie aplikacji Flask za pomocą interfejsu API usługi Azure Cosmos DB dla mongodb](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Tworzenie bazy danych wykresów przy użyciu interfejsu Api języka Python i Gremlin](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [tworzenie aplikacji Cassandra przy użyciu języka Python i usługi Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [tworzenie aplikacji interfejsu API tabel przy użyciu języka Python i usługi Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

Podczas pracy z usługą Cosmos DB można użyć biblioteki [tabeli azure-cosmosdb.](https://pypi.org/project/azure-cosmosdb-table/)

## <a name="other-azure-databases"></a>Inne bazy danych platformy Azure

Platforma Azure udostępnia wiele innych typów baz danych, których można użyć. Poniższe artykuły zawierają wskazówki dotyczące uzyskiwania dostępu do tych baz danych z języka Python:

- [Usługa Azure Database for PostgreSQL: nawiązywanie połączeń z danymi i wykonywanie na nich zapytań za pomocą języka Python](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Szybki start: korzystanie z usługi Azure Redis Cache w połączeniu z językiem Python](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Usługa Azure Database for MySQL: nawiązywanie połączeń z danymi i wykonywanie na nich zapytań za pomocą języka Python](https://docs.microsoft.com/azure/mysql/connect-python)
- [Fabryka danych platformy Azure](https://azure.microsoft.com/services/data-factory/)
  - [Kreator kopiowania dla usługi Azure Data Factory](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Następne kroki

- [Jak: Praca z plikami danych projektu](work-with-project-data-files.md)
