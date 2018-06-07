---
title: Kopiowanie danych z bazy danych MongoDB przy użyciu fabryki danych Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z bazy danych Mongo do zbiornika obsługiwanych magazynów danych za pomocą działania kopiowania w potoku fabryki danych Azure.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/05/2018
ms.author: jingwang
ms.openlocfilehash: 24d641247ad9bb0b5e6199952cbde9cb56fcaea7
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809298"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Kopiowanie danych z bazy danych MongoDB przy użyciu fabryki danych Azure
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](v1/data-factory-on-premises-mongodb-connector.md)
> * [Wersja 2 — wersja zapoznawcza](connector-mongodb.md)

W tym artykule omówiono sposób użycia działanie kopiowania w fabryce danych Azure, aby skopiować dane z bazy danych MongoDB. Opiera się na [skopiuj omówienie działania](copy-activity-overview.md) artykułu, który przedstawia ogólny przegląd działanie kopiowania.

> [!NOTE]
> Ten artykuł dotyczy wersji 2 usługi Data Factory, która jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz wersji 1 usługi fabryka danych, która jest ogólnie dostępna (GA), zobacz [łącznika bazy danych MongoDB w wersji 1](v1/data-factory-on-premises-mongodb-connector.md).


## <a name="supported-capabilities"></a>Obsługiwane możliwości

Możesz skopiować dane z bazy danych MongoDB do żadnych obsługiwanych ujścia magazynu danych. Lista magazynów danych, które są obsługiwane jako źródła/wychwytywanie przez działanie kopiowania, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik bazy danych MongoDB obsługuje:

- Bazy danych MongoDB **wersji 2.4, 2.6, 3.0, 3.2 3.4 i 3,6**.
- Kopiowanie danych przy użyciu **podstawowe** lub **anonimowe** uwierzytelniania.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skopiować dane z bazy danych MongoDB, który nie jest dostępny publicznie, należy skonfigurować środowisko uruchomieniowe Self-hosted integracji. Zobacz [środowiska uruchomieniowego integracji Self-hosted](create-self-hosted-integration-runtime.md) artykuł, aby poznać szczegóły. Środowiska uruchomieniowego integracji oferuje wbudowane sterownik bazy danych MongoDB, dlatego nie trzeba ręcznie zainstalowania sterownika podczas kopiowania danych z bazy danych MongoDB.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje o właściwościach, które są używane do definiowania jednostek fabryki danych określonej do łącznika bazy danych MongoDB.

## <a name="linked-service-properties"></a>Połączona usługa właściwości

Obsługiwane są następujące właściwości dla bazy danych MongoDB połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type |Właściwość type musi mieć ustawioną: **bazy danych MongoDb** |Yes |
| serwer |Adres IP lub hosta nazwę serwera bazy danych MongoDB. |Yes |
| port |Port TCP używany przez serwer bazy danych MongoDB do nasłuchiwania dla połączeń klienta. |Nie (wartość domyślna to 27017) |
| databaseName |Nazwa bazy danych MongoDB, które chcesz uzyskać dostęp. |Yes |
| Typ authenticationType | Typ uwierzytelniania używany do łączenia z bazą danych MongoDB.<br/>Dozwolone wartości to: **podstawowe**, i **anonimowe**. |Yes |
| nazwa użytkownika |Konto użytkownika do bazy danych MongoDB. |Tak (jeśli jest używane uwierzytelnianie podstawowe). |
| hasło |Hasło dla użytkownika. Zaznacz to pole jako SecureString Zapisz w bezpiecznej lokalizacji w fabryce danych lub [odwołania klucz tajny przechowywane w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Tak (jeśli jest używane uwierzytelnianie podstawowe). |
| authSource |Nazwa bazy danych MongoDB, który ma być używany w celu sprawdzenia poświadczeń dla uwierzytelniania. |Nie. Dla uwierzytelniania podstawowego domyślnie używany jest konto administratora i baza danych określona za pomocą właściwości databaseName. |
| enableSsl | Określa, czy połączenia z serwerem są szyfrowane przy użyciu protokołu SSL. Wartość domyślna to false.  | Nie |
| allowSelfSignedServerCert | Określa, czy certyfikaty z podpisem własnym z serwera. Wartość domyślna to false.  | Nie |
| connectVia | [Integrację środowiska uruchomieniowego](concepts-integration-runtime.md) ma być używany do nawiązania połączenia z magazynem danych. (Jeśli w magazynie danych jest dostępny publicznie) można użyć środowiska uruchomieniowego integracji Self-hosted lub środowiska uruchomieniowego integracji Azure. Jeśli nie zostanie określony, używa domyślnej środowiska uruchomieniowego integracji Azure. |Nie |

**Przykład:**

```json
{
    "name": "MongoDBLinkedService",
    "properties": {
        "type": "MongoDb",
        "typeProperties": {
            "server": "<server name>",
            "databaseName": "<database name>",
            "authenticationType": "Basic",
            "username": "<username>",
            "password": {
                "type": "SecureString",
                "value": "<password>"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Właściwości zestawu danych

Aby uzyskać pełną listę właściwości dostępnych do definiowania zestawów danych i sekcje zobacz artykuł zestawów danych. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych MongoDB.

Aby skopiować dane z bazy danych MongoDB, ustaw właściwość Typ zestawu danych do **MongoDbCollection**. Obsługiwane są następujące właściwości:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type zestawu danych: **MongoDbCollection** | Yes |
| CollectionName |Nazwa kolekcji w bazie danych MongoDB. |Yes |

**Przykład:**

```json
{
     "name":  "MongoDbDataset",
    "properties": {
        "type": "MongoDbCollection",
        "linkedServiceName": {
            "referenceName": "<MongoDB linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "collectionName": "<Collection name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Pełną listę sekcje i właściwości dostępnych dla definiowania działań, zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwane przez źródło bazy danych MongoDB.

### <a name="mongodb-as-source"></a>Bazy danych MongoDB jako źródło

Aby skopiować dane z bazy danych MongoDB, należy ustawić typ źródła w przypadku działania kopiowania do **MongoDbSource**. Następujące właściwości są obsługiwane w przypadku działania kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi mieć ustawioną właściwość type źródła działania kopiowania: **MongoDbSource** | Yes |
| query |Użyj niestandardowych zapytania SQL 92 można odczytać danych. Na przykład: Wybierz * z MyTable. |Nie (Jeśli określono parametr "collectionName" w zestawie danych) |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromMongoDB",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<MongoDB input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "MongoDbSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

> [!TIP]
> Podczas Określ zapytanie SQL, należy zwrócić uwagę na format daty/godziny. Na przykład: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` lub użyj parametru `SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Schemat fabryka danych

Usługi fabryka danych Azure wnioskuje schemat z kolekcji bazy danych MongoDB przy użyciu **najnowszych dokumentów 100** w kolekcji. Jeśli te dokumenty 100 nie zawierają pełny schemat, niektóre kolumny można zignorować podczas operacji kopiowania.

## <a name="data-type-mapping-for-mongodb"></a>Typ danych mapowania bazy danych mongodb

Podczas kopiowania danych z bazy danych MongoDB, następujące mapowania są używane z typów danych MongoDB do typów danych tymczasowych fabryki danych Azure. Zobacz [schemat i dane typu mapowania](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typu źródłowego: schemat i dane sink.

| Typ danych MongoDB | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| Binarny |Byte[] |
| Wartość logiczna |Wartość logiczna |
| Date |DateTime |
| NumberDouble |O podwójnej precyzji |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |Ciąg |
| Ciąg |Ciąg |
| IDENTYFIKATOR UUID |Identyfikator GUID |
| Obiekt |Renormalized do spłaszczenia kolumn z "_" jako separatora zagnieżdżonych |

> [!NOTE]
> Aby dowiedzieć się więcej o obsługę tablic za pomocą tabele wirtualne, zapoznaj się [obsługę złożonych typów przy użyciu tabele wirtualne](#support-for-complex-types-using-virtual-tables) sekcji.
>
> Obecnie nie są obsługiwane następujące typy danych MongoDB: DBPointer, JavaScript, maksymalna liczba na minutę klucza, wyrażenie regularne, Symbol, Timestamp, niezdefiniowane.

## <a name="support-for-complex-types-using-virtual-tables"></a>Obsługę złożonych typów przy użyciu tabele wirtualne

Fabryka danych Azure używa wbudowanego sterownika ODBC do nawiązania połączenia, a następnie skopiować dane z bazy danych MongoDB. Typów złożonych takich jak macierze lub obiektów z różnymi typami wszystkich dokumentów sterownik ponownie normalizuje danych do odpowiednich tabel wirtualnego. W szczególności jeśli tabela zawiera takich kolumn, sterownik generuje następujące tabele wirtualne:

* A **tabeli podstawowej**, który zawiera te same dane jako prawdziwe tabeli z wyjątkiem kolumny typu złożonego. Tabela podstawowa używa tej samej nazwie jako prawdziwe tabeli, która reprezentuje.
* A **tabeli wirtualnej** dla każdej kolumny typu złożonego, która rozszerza zagnieżdżone dane. Tabele wirtualne są nazywane przy użyciu nazwy tabeli prawdziwe, separator "_" i nazwę tablicy lub obiektu.

Tabele wirtualne odwołują się do danych w tabeli prawdziwe, włączanie dostępu do danych nieznormalizowany sterownika. Ma dostęp do zawartości tablic bazy danych MongoDB, zapytań i przyłączanie tabel wirtualnego.

### <a name="example"></a>Przykład

Na przykład w tym miejscu ExampleTable jest tabeli bazy danych MongoDB, która zawiera jedną kolumnę z tablicę obiektów w każdej komórce — faktury i jedną kolumnę z tablicą typów skalarnych — klasyfikacje.

| _id | Nazwa klienta | Faktury | Poziom usług | Klasyfikacje |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id:"123", item:"toaster", price:"456", discount:"0.2"}, {invoice_id:"124", item:"oven", price: "1235", discount: "0.2"}] |Srebrny |[5,6] |
| 2222 |XYZ |[{invoice_id:"135", item:"fridge", price: "12543", discount: "0.0"}] |Złoty |[1,2] |

Sterownik przetwarzający generuje wiele tabel wirtualnego do reprezentowania tej pojedynczej tabeli. Pierwszy tabeli wirtualnej jest tabela podstawowa o nazwie "ExampleTable" pokazano w przykładzie. Podstawowa tabela zawiera wszystkie dane z oryginalnej tabeli, ale dane z macierzami została pominięta i jest rozwinięta w tabelach wirtualnych.

| _id | Nazwa klienta | Poziom usług |
| --- | --- | --- |
| 1111 |ABC |Srebrny |
| 2222 |XYZ |Złoty |

W poniższych tabelach przedstawiono wirtualnego tabel, które reprezentują oryginalnego tablic w przykładzie. Te tabele zawierać następujące informacje:

* Odwołanie do oryginalnego kolumna klucza podstawowego odpowiadający wiersza oryginalnej tablicy (za pomocą kolumny _id)
* Wskazanie pozycji danych w oryginalnej tablicy
* Rozwinięte danych dla każdego elementu w tablicy

**Tabela "ExampleTable_Invoices":**

| _id | ExampleTable_Invoices_dim1_idx | invoice_id | Element | price | Rabat |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |tostera |456 |0.2 |
| 1111 |1 |124 |Piec |1235 |0.2 |
| 2222 |0 |135 |lodówko |12543 |0.0 |

**Tabela "ExampleTable_Ratings":**

| _id | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |


## <a name="next-steps"></a>Kolejne kroki
Lista magazynów danych obsługiwane jako źródła i wychwytywanie przez działanie kopiowania w fabryce danych Azure, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
