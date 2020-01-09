---
title: Dostęp do danych w notesach Jupyter Azure Notebooks — wersja zapoznawcza
description: Dowiedz się, jak uzyskać dostęp do plików, interfejsów API REST, baz danych i różnych zasobów usługi Azure Storage z notesu Jupyter.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 47d2f869021851c1451a66a84b1a70ec4ff4998f
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646351"
---
# <a name="access-cloud-data-in-a-notebook"></a>Dostęp do danych z chmury w notesie

Wykonując interesującej pracy w notesie Jupyter wymaga danych. Dane, są w rzeczywistości lifeblood notesów.

Możesz bez obaw [importowanie plików danych do projektu](work-with-project-data-files.md), nawet przy użyciu poleceń, takich jak `curl` z w ramach Notes, aby bezpośrednio pobrać plik. Istnieje prawdopodobieństwo, jednak, że musisz pracować z danymi znacznie bardziej rozległe, który jest dostępny z innego niż plik źródeł, takich jak interfejsy API REST, relacyjne bazy danych i Magazyn, takie jak tabele platformy Azure w chmurze.

W tym artykule krótko opisano te różne opcje. Ponieważ dostęp do danych, najlepiej jest widoczny w działaniu, kod możesz znaleźć możliwy do uruchomienia w [przykłady notesy platformy Azure — dostęp do danych](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

## <a name="rest-apis"></a>Interfejsy API REST

Ogólnie rzecz biorąc ogromnej ilości danych, które są dostępne z Internetu jest dostępny, nie za pomocą plików, ale także za pośrednictwem interfejsów API REST. Na szczęście komórki notesu może zawierać kod, niezależnie od chcesz, można użyć kodu do wysyłania żądań i odbierania danych JSON. Można następnie przekonwertować kod JSON do niezależnie od formatu, którego chcesz użyć, takie jak pandas dataframe.

Dostęp do danych za pomocą interfejsu API REST, należy użyć tego samego kodu w komórkach kod Notes, używanych w żadnej innej aplikacji. Ogólną strukturę przy użyciu biblioteki żądań jest następująca:

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

## <a name="azure-sql-databases"></a>Baz danych SQL Azure

Można uzyskać dostęp do baz danych programu SQL Server przy pomocy moduł pyodbc lub pymssql bibliotek.

[Zapytanie usługi Azure SQL database przy użyciu języka Python](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) zawiera instrukcje dotyczące tworzenia bazy danych AdventureWorks danymi i pokazuje, jak wykonywać zapytania dla tych danych. Ten sam kod jest wyświetlany w notesie próbki na potrzeby tego artykułu.

## <a name="azure-storage"></a>Azure Storage

Usługa Azure Storage oferuje kilka różnych typów magazynów nierelacyjnych, w zależności od typu danych i jak należy uzyskać do niego dostęp:

- Storage: TABLE to ekonomiczne, dużego magazynu na dane tabelaryczne, takich jak czujnik zebranych dzienników, dzienniki diagnostyczne i tak dalej.
- Magazynu obiektów blob: zapewnia podobne do pliku Magazyn danych dowolnego typu.

Przykładowy notes przedstawia pracę z tabel i obiektów blob, takich jak używać sygnatury dostępu współdzielonego, aby zezwolić na dostęp tylko do odczytu do obiektów blob.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Usługi Azure Cosmos DB udostępnia pełni indeksowanych magazyn danych NoSQL dla dokumentów JSON). Poniższe artykuły zawierają szereg różne sposoby pracy z usługą Cosmos DB za pomocą języka Python:

- [Aplikacja kompilacji przez interfejs API SQL za pomocą języka Python](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Tworzenie aplikacji do kolby z interfejsem API Azure Cosmos DB dla MongoDB](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Utwórz bazę danych grafów przy użyciu języka Python i interfejsu API języka Gremlin](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Tworzenie aplikacji bazy danych Cassandra z językiem Python i usługi Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Tworzenie aplikacji interfejsu API za pomocą języka Python i usługi Azure Cosmos DB tabeli](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

Podczas pracy z usługą Cosmos DB, możesz użyć [azure-cosmos DB table](https://pypi.org/project/azure-cosmosdb-table/) biblioteki.

## <a name="other-azure-databases"></a>Innych baz danych platformy Azure

System Azure oferuje wiele innych typów baz danych, które są dostępne. Poniższe artykuły zawierają wskazówki dotyczące uzyskiwania dostępu do tych baz danych za pomocą języka Python:

- [Usługa Azure Database for PostgreSQL: za pomocą języka Python do nawiązywania połączeń i wykonywanie zapytań dotyczących danych](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Szybki Start: Użyj usługi Azure Redis Cache za pomocą języka Python](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Usługa Azure Database for MySQL: za pomocą języka Python do nawiązywania połączeń i wykonywanie zapytań dotyczących danych](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Kreator kopiowania usługi fabryka danych Azure](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Następne kroki

- [Porady: Praca z plikami danych projektu](work-with-project-data-files.md)
