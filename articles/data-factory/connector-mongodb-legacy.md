---
title: Kopiowanie danych z bazy danych MongoDB przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z Mongo DB do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/20/2018
ms.author: jingwang
ms.openlocfilehash: 86dcd39ad7b9f1e207e9254ec72698db3998bbd6
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "61400478"
---
# <a name="copy-data-from-mongodb-using-azure-data-factory"></a>Kopiowanie danych z bazy danych MongoDB przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](v1/data-factory-on-premises-mongodb-connector.md)
> * [Bieżąca wersja](connector-mongodb.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory, aby skopiować dane z bazy danych MongoDB. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

>[!IMPORTANT]
>Usługi ADF w wersji nowy łącznik bazy danych MongoDB, która zapewnia lepsze natywnej bazy danych MongoDB obsługują porównywania tej implementacji na podstawie ODBC, zapoznaj się [łącznika MongoDB](connector-mongodb.md) artykuł na temat szczegółów. Tej starszej wersji łącznika MongoDB jest przechowywana obsługiwane jako — jest na potrzeby zgodności z poprzednimi wersjami, natomiast w przypadku dowolnego nowego obciążenia, użyj nowego łącznika.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z bazy danych MongoDB, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności obsługuje ten łącznik bazy danych MongoDB:

- Bazy danych MongoDB **wersji 2.4, 2.6, 3.0, 3.2, 3.4 i 3.6**.
- Kopiowanie danych przy użyciu **podstawowe** lub **anonimowe** uwierzytelniania.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skopiować dane z bazy danych MongoDB, który nie jest dostępny publicznie, musisz skonfigurować środowiskiem Integration Runtime. Zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md) artykuł, aby poznać szczegóły. Infrastruktura Integration Runtime zapewnia wbudowane sterownik bazy danych MongoDB, dlatego nie trzeba ręcznie zainstalować dowolnego sterownika podczas kopiowania danych z bazy danych MongoDB.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika usługi MongoDB.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane dla bazy danych MongoDB, połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type |Właściwość type musi być równa: **MongoDb** |Yes |
| server |Adres IP lub hosta nazwę serwera bazy danych MongoDB. |Yes |
| port |Port TCP używany przez serwer bazy danych MongoDB do nasłuchiwania połączeń klientów. |Nie (wartość domyślna to 27017) |
| databaseName |Nazwa bazy danych MongoDB, który chcesz uzyskać dostęp. |Yes |
| authenticationType | Typ uwierzytelniania używany do łączenia z bazą danych MongoDB.<br/>Dozwolone wartości to: **Podstawowe**, i **anonimowe**. |Yes |
| username |Konto użytkownika, aby dostęp do bazy danych MongoDB. |Tak (jeśli jest używane uwierzytelnianie podstawowe). |
| password |Hasło użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). |Tak (jeśli jest używane uwierzytelnianie podstawowe). |
| authSource |Nazwa bazy danych MongoDB, który chcesz użyć, aby sprawdzić swoje poświadczenia dla uwierzytelniania. |Nie. W przypadku uwierzytelniania podstawowego domyślna ma używać konta administratora i baza danych określona za pomocą właściwości databaseName. |
| enableSsl | Określa, czy połączenia z serwerem są szyfrowane przy użyciu protokołu SSL. Wartość domyślna to false.  | Nie |
| allowSelfSignedServerCert | Określa, czy zezwalać na certyfikaty z podpisem własnym z serwera. Wartość domyślna to false.  | Nie |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Używając środowiskiem Integration Runtime lub Azure Integration Runtime (Jeśli magazyn danych jest publicznie dostępny). Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

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

Aby uzyskać pełną listę sekcje i właściwości, które są dostępne do definiowania zestawów danych, zobacz [zestawy danych i połączone usługi](concepts-datasets-linked-services.md). Następujące właściwości są obsługiwane dla zestawu danych z bazy danych MongoDB:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa: **MongoDbCollection** | Yes |
| collectionName |Nazwa kolekcji w bazie danych MongoDB. |Yes |

**Przykład:**

```json
{
    "name": "MongoDbDataset",
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

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło bazy danych MongoDB.

### <a name="mongodb-as-source"></a>Bazy danych MongoDB jako źródło

Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Musi być równa wartości właściwości type źródło działania kopiowania: **MongoDbSource** | Yes |
| query |Niestandardowe zapytania SQL 92 umożliwia odczytywanie danych. Na przykład: Wybierz * z MyTable. |Nie (Jeśli określono parametr "collectionName" w zestawie danych) |

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
> Gdy Określ zapytanie SQL, należy zwrócić uwagę na format daty/godziny. Na przykład: `SELECT * FROM Account WHERE LastModifiedDate >= '2018-06-01' AND LastModifiedDate < '2018-06-02'` lub użyj parametru `SELECT * FROM Account WHERE LastModifiedDate >= '@{formatDateTime(pipeline().parameters.StartTime,'yyyy-MM-dd HH:mm:ss')}' AND LastModifiedDate < '@{formatDateTime(pipeline().parameters.EndTime,'yyyy-MM-dd HH:mm:ss')}'`

## <a name="schema-by-data-factory"></a>Schemat przez usługę Data Factory

Usługa Azure Data Factory wnioskuje schemat z kolekcji usługi MongoDB przy użyciu **najnowszych 100 dokumentów** w kolekcji. Jeśli te 100 dokumentach nie zawierają pełnego schematu, niektóre kolumny mogą ignorowane podczas operacji kopiowania.

## <a name="data-type-mapping-for-mongodb"></a>Typ danych mapowania dla bazy danych MongoDB

Podczas kopiowania danych z bazy danych MongoDB, następujące mapowania są używane z typów danych bazy danych MongoDB do typów danych tymczasowych usługi Azure Data Factory. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

| Typ danych bazy danych MongoDB | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| Binary |Byte[] |
| Boolean |Boolean |
| Date |DateTime |
| NumberDouble |Double |
| NumberInt |Int32 |
| NumberLong |Int64 |
| ObjectID |String |
| Ciąg |String |
| UUID |Guid |
| Object |Renormalized do spłaszczenia kolumn z "_" jako separatora zagnieżdżonych |

> [!NOTE]
> Aby dowiedzieć się więcej o obsłudze tablic przy użyciu wirtualnego tabel, zobacz [obsługę złożonych typów, przy użyciu tabele wirtualne](#support-for-complex-types-using-virtual-tables) sekcji.
>
> Obecnie nie są obsługiwane następujące typy danych bazy danych MongoDB: Klucz DBPointer, JavaScript, maksymalna liczba na minutę, wyrażeń regularnych, symboli, Timestamp, niezdefiniowane.

## <a name="support-for-complex-types-using-virtual-tables"></a>Obsługa złożonych typów, przy użyciu tabele wirtualne

Usługa Azure Data Factory używa wbudowanego sterownika ODBC, aby nawiązać połączenie i kopiowanie danych z bazy danych MongoDB. W przypadku typów złożonych takich jak tablicami i obiektami z różnymi typami wszystkich dokumentów sterownik ponownie normalizuje dane na odpowiednie tabele wirtualne. W szczególności jeśli tabela zawiera takich kolumn, sterownik generuje następujące tabele wirtualne:

* A **tabeli podstawowej**, który zawiera te same dane jako tabelę rzeczywistych, z wyjątkiem kolumn typu złożonego. Tabela podstawowa używa tej samej nazwie jako rzeczywistych tabeli, który reprezentuje.
* A **tabeli wirtualnej** dla każdej kolumny na typ złożony, który rozwija zagnieżdżone dane. Tabele wirtualne są nazywane przy użyciu nazwy tabeli rzeczywistych, separator "_" i nazwę tablicy lub obiektu.

Tabele wirtualne odnoszą się do danych w tabeli rzeczywistych Włączanie sterownik dostępu do danych nieznormalizowany. Wykonywanie zapytań i przyłączanie tabel wirtualnego, ma dostęp do zawartości, tablic bazy danych MongoDB.

### <a name="example"></a>Przykład

Na przykład w tym miejscu ExampleTable jest tabeli bazy danych MongoDB, która zawiera jedną kolumnę z tablicy obiektów w każdej komórce — faktury i jedną kolumnę z tablicą typów skalarnych — klasyfikacji.

| _identyfikator | Nazwa klienta | Faktury | Poziom usług | Klasyfikacje |
| --- | --- | --- | --- | --- |
| 1111 |ABC |[{invoice_id: elementu "123": "tostera", price: "456" rabat w wysokości: "0,2"}, {invoice_id: element "124": "piec", price: "1235", discount: "0.2"}] |Srebrny |[5,6] |
| 2222 |XYZ |[{invoice_id: element "135": "lodówko", price: "12543", discount: "0.0"}] |Złoty |[1,2] |

Sterownik wygeneruje wiele tabel wirtualnego do reprezentowania jednej tabeli. Pierwsza tabela wirtualnego jest tabela bazowa o nazwie "ExampleTable", jak pokazano w przykładzie. Tabela podstawowa zawiera wszystkie dane z oryginalnej tabeli, ale dane z tablic została pominięta i jest rozwinięty w tabelach wirtualnych.

| _identyfikator | Nazwa klienta | Poziom usług |
| --- | --- | --- |
| 1111 |ABC |Srebrny |
| 2222 |XYZ |Złoty |

W poniższych tabelach przedstawiono wirtualnych tabel, które reprezentują oryginalnego tablic w przykładzie. Te tabele zawierają następujące czynności:

* Odwołanie z powrotem do oryginalnego kolumny klucza podstawowego odpowiadającą wierszowi tablicy oryginalnej (za pośrednictwem kolumnie _identyfikator)
* Wskazanie pozycji danych w oryginalnym tablicy
* Rozwinięty danych dla każdego elementu w tablicy

**Tabela "ExampleTable_Invoices":**

| _identyfikator | ExampleTable_Invoices_dim1_idx | invoice_id | Element | price | Rabat |
| --- | --- | --- | --- | --- | --- |
| 1111 |0 |123 |tostera |456 |0.2 |
| 1111 |1 |124 |Piec |1235 |0.2 |
| 2222 |0 |135 |lodówko |12543 |0.0 |

**Tabela "ExampleTable_Ratings":**

| _identyfikator | ExampleTable_Ratings_dim1_idx | ExampleTable_Ratings |
| --- | --- | --- |
| 1111 |0 |5 |
| 1111 |1 |6 |
| 2222 |0 |1 |
| 2222 |1 |2 |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
