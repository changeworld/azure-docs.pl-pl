---
title: Kopiowanie danych do i z oprogramowania Oracle przy użyciu usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z obsługiwanego źródłowego magazynów z bazą danych Oracle lub Oracle do ujścia obsługiwanych magazynów za pomocą usługi Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: jingwang
ms.openlocfilehash: 04f623a889a87c325b1f53e3b39656ca4b703961
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509236"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Kopiowanie danych z i do oprogramowania Oracle przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](v1/data-factory-onprem-oracle-connector.md)
> * [Bieżąca wersja](connector-oracle.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do bazy danych Oracle. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z bazy danych Oracle do dowolnego obsługiwanego magazynu danych ujścia. Możesz także skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do bazy danych Oracle. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła lub ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik Oracle obsługuje:

- Następujące wersje bazy danych Oracle:
  - R1 Oracle 12c (12.1)
  - Oracle 11g R1, R2 (11.1, 11.2)
  - Oracle 10g R1, R2 (10.1, 10.2)
  - Oracle 9i R1, R2 (9.0.1, 9.2)
  - Oracle 8i R3 (8.1.7)
- Kopiowanie danych przy użyciu **podstawowe** lub **OID** uwierzytelnienia.
- Równoległe kopia ze źródła programu Oracle. Zobacz [równoległe kopiowanie danych z bazy danych Oracle](#parallel-copy-from-oracle) sekcja ze szczegółowymi informacjami.

> [!Note]
> Serwer proxy Oracle nie jest obsługiwana.

## <a name="prerequisites"></a>Wymagania wstępne

Aby skopiować dane z i do bazy danych Oracle, która nie jest dostępny publicznie, musisz skonfigurować środowiskiem Integration Runtime. Aby uzyskać więcej informacji na temat środowiska integration runtime, zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md). Infrastruktura integration runtime zapewnia wbudowane Sterownik Oracle. W związku z tym nie trzeba ręcznie zainstalować sterownik podczas kopiowania danych z i do bazy danych Oracle.

## <a name="get-started"></a>Rozpoczęcie pracy

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika programu Oracle.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku usługi połączonej bazy danych Oracle.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **Oracle**. | Yes |
| connectionString | Określa informacje wymagane do nawiązania połączenia z wystąpieniem bazy danych Oracle. <br/>Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory. Można również wprowadzić hasło w usłudze Azure Key Vault i ściągania `password` konfiguracji poza parametry połączenia. Zobacz poniższe przykłady i [Store poświadczeń w usłudze Azure Key Vault](store-credentials-in-key-vault.md) artykułu z bardziej szczegółowymi informacjami. <br><br>**Obsługiwany typ połączenia**: Możesz użyć **identyfikator SID programu Oracle** lub **nazwa usługi Oracle** Aby zidentyfikować bazy danych:<br>— Jeśli używasz identyfikatora SID: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>— Jeśli używasz nazwa usługi: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;` | Yes |
| connectVia | [Środowiska integration runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Używając środowiskiem Integration Runtime lub Azure Integration Runtime (Jeśli magazyn danych jest publicznie dostępny). Jeśli nie zostanie określony, używa domyślnego środowiska Azure Integration Runtime. |Nie |

>[!TIP]
>Jeśli napotkasz błąd powiedzenie "ORA 01025: Parametr zaległej płatności za przedmiot poza zakresem"i usługi Oracle jest w wersji 8i, Dodaj `WireProtocolMode=1` parametry połączenia i spróbuj ponownie.

**Aby włączyć szyfrowanie na połączenie z oprogramowaniem Oracle**, masz dwie opcje:

1.  Aby użyć **Triple-DES szyfrowania (3DES) i Advanced Encryption Standard (AES)** , po stronie serwera Oracle, przejdź do bazy danych Oracle zaawansowane zabezpieczenia (OAS) i konfigurowanie ustawień szyfrowania, zobacz szczegóły dotyczące [tutaj](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Łącznik ADF Oracle automatycznie negocjuje metodę szyfrowania, użyj skonfigurowanych w OAS, podczas ustanawiania połączenia Oracle.

2.  Aby użyć **SSL**, wykonaj poniższe kroki:

    1.  Pobieranie informacji o certyfikacie SSL. Pobierz informacje o certyfikacie algorytmem DER Twojego certyfikatu SSL i Zapisz dane wyjściowe (---Begin certyfikatu Zakończenia certyfikatu---) jako plik tekstowy.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Przykład:** wyodrębniać informacje z certyfikatu z DERcert.cer; następnie zapisz dane wyjściowe do cert.txt

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  Tworzenie magazynu kluczy lub truststore. Następujące polecenie tworzy plik truststore z lub bez hasła w formacie PKCS 12.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Przykład:** tworzy plik truststore PKCS12 o nazwie MyTrustStoreFile przy użyciu hasła

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Umieść plik truststore na maszynie własne środowisko IR, np. na C:\MyTrustStoreFile.
    4.  W usłudze ADF, należy skonfigurować parametry połączenia bazy danych Oracle przy użyciu `EncryptionMethod=1` i odpowiadających im `TrustStore` / `TrustStorePassword`wartość, np. `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Przykład:**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Przykład: przechowywanie haseł w usłudze Azure Key Vault**

```json
{
    "name": "OracleLinkedService",
    "properties": {
        "type": "Oracle",
        "typeProperties": {
            "connectionString": {
                "type": "SecureString",
                "value": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;"
            },
            "password": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych programu Oracle.

Aby skopiować dane z i do oprogramowania Oracle, należy ustawić właściwość typu zestawu danych na **OracleTable**. Następujące właściwości są obsługiwane.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu elementu dataset musi być równa **OracleTable**. | Yes |
| tableName |Nazwa tabeli w bazie danych Oracle, których połączona usługa przywołuje. | Yes |

**Przykład:**

```json
{
    "name": "OracleDataset",
    "properties":
    {
        "type": "OracleTable",
        "linkedServiceName": {
            "referenceName": "<Oracle linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "tableName": "MyTable"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez Oracle źródła i ujścia.

### <a name="oracle-as-a-source-type"></a>Oracle jako typ źródła

> [!TIP]
>
> Dowiedz się więcej z [równoległe kopiowanie danych z bazy danych Oracle](#parallel-copy-from-oracle) sekcję dotyczącą sposobu ładowania danych z bazy danych Oracle, efektywnie są używane partycjonowanie danych.

Aby skopiować dane z bazy danych Oracle, należy ustawić typ źródła w działaniu kopiowania, aby **OracleSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być równa **OracleSource**. | Yes |
| oracleReaderQuery | Umożliwia odczytywanie danych niestandardowe zapytania SQL. Może to być na przykład `"SELECT * FROM MyTable"`.<br>Po włączeniu ładowania podzielonym na partycje, musisz dołączyć odpowiednie parametry wbudowanej partycji w zapytaniu. Zobacz przykłady w [równoległe kopiowanie danych z bazy danych Oracle](#parallel-copy-from-oracle) sekcji. | Nie |
| partitionOptions | Określa dane, partycjonowanie opcje używane do ładowania danych z bazy danych Oracle. <br>Zezwalaj na wartości to: **Brak** (ustawienie domyślne), **PhysicalPartitionsOfTable** i **DynamicRange**.<br>Po włączeniu opcji partycji (nie "None"), należy również skonfigurować **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** ustawienie dla działania kopiowania, np. jako 4, która określa stopień równoległego do współbieżnego ładowania danych z bazy danych Oracle Baza danych. | Nie |
| partitionSettings | Określ grupę ustawień partycjonowanie danych. <br>Są stosowane, gdy opcja partycji nie jest `None`. | Nie |
| partitionNames | Lista partycji fizycznych, które trzeba skopiować. <br>Są stosowane, gdy opcja partycji jest `PhysicalPartitionsOfTable`. Użycie zapytania można pobrać źródła danych, podłączyć `?AdfTabularPartitionName` w klauzuli WHERE. Zobacz przykład w [równoległe kopiowanie danych z bazy danych Oracle](#parallel-copy-from-oracle) sekcji. | Nie |
| partitionColumnName | Określ nazwę kolumny źródłowej **typu Liczba całkowita** który będzie używany przez zakres partycjonowanie związanym z kopiowaniem równoległych. Jeśli nie zostanie określony, klucz podstawowy tabeli będzie automatycznie wykryte i jest używana jako kolumna partycji. <br>Są stosowane, gdy opcja partycji jest `DynamicRange`. Użycie zapytania można pobrać źródła danych, podłączyć `?AdfRangePartitionColumnName` w klauzuli WHERE. Zobacz przykład w [równoległe kopiowanie danych z bazy danych Oracle](#parallel-copy-from-oracle) sekcji. | Nie |
| partitionUpperBound | Maksymalna wartość kolumny partycji w celu skopiowania danych. <br>Są stosowane, gdy opcja partycji jest `DynamicRange`. Użycie zapytania można pobrać źródła danych, podłączyć `?AdfRangePartitionUpbound` w klauzuli WHERE. Zobacz przykład w [równoległe kopiowanie danych z bazy danych Oracle](#parallel-copy-from-oracle) sekcji. | Nie |
| PartitionLowerBound | Minimalna wartość kolumny partycji w celu skopiowania danych. <br>Są stosowane, gdy opcja partycji jest `DynamicRange`. Użycie zapytania można pobrać źródła danych, podłączyć `?AdfRangePartitionLowbound` w klauzuli WHERE. Zobacz przykład w [równoległe kopiowanie danych z bazy danych Oracle](#parallel-copy-from-oracle) sekcji. | Nie |

**Przykład: kopiowanie danych przy użyciu podstawowego zapytania bez partycji**

```json
"activities":[
    {
        "name": "CopyFromOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Oracle input dataset name>",
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
                "type": "OracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

Zobacz więcej przykładów w [równoległe kopiowanie danych z bazy danych Oracle](#parallel-copy-from-oracle) sekcji.

### <a name="oracle-as-a-sink-type"></a>Oracle jako typ ujścia

Aby skopiować dane do bazy danych Oracle, należy ustawić typ ujścia w działaniu kopiowania, aby **elementu OracleSink**. Następujące właściwości są obsługiwane w działaniu kopiowania **ujścia** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type ujścia działania kopiowania musi być równa **elementu OracleSink**. | Yes |
| writeBatchSize | Wstawia dane do tabeli SQL, gdy writeBatchSize osiągnie rozmiar buforu.<br/>Dozwolone wartości to liczby całkowitej (liczba wierszy). |Nie (wartość domyślna to 10 000) |
| writeBatchTimeout | Czas na ukończenie przed upływem limitu czasu operacji wstawiania wsadowego oczekiwania.<br/>Dozwolone wartości to przedziału czasu. Przykładem jest 00:30:00 (30 minut). | Nie |
| preCopyScript | Określ zapytanie SQL, działanie kopiowania do wykonania przed zapisaniem danych do bazy danych Oracle, w każdym przebiegu. Ta właściwość służy do czyszczenia załadowanych danych. | Nie |

**Przykład:**

```json
"activities":[
    {
        "name": "CopyToOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Oracle output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "<source type>"
            },
            "sink": {
                "type": "OracleSink"
            }
        }
    }
]
```

## <a name="parallel-copy-from-oracle"></a>Równoległe kopiowanie danych z bazy danych Oracle

Łącznik Oracle fabryki danych zawiera wbudowane danych partycji w celu skopiowania danych z bazy danych Oracle równolegle z wysoką wydajność. Można znaleźć opcje partycjonowania danych w działaniu kopiowania -> źródła Oracle:

![Opcje partycji](./media/connector-oracle/connector-oracle-partition-options.png)

Po włączeniu kopii podzielonym na partycje usługi data factory uruchamia zapytania równoległe względem źródła danych programu Oracle do ładowania danych przez partycje. Równoległe stopień jest skonfigurowana i kontrolowany za pośrednictwem **[ `parallelCopies` ](copy-activity-performance.md#parallel-copy)** ustawienie dla działania kopiowania. Na przykład jeśli ustawisz `parallelCopies` cztery, jednocześnie generuje usługi data factory i uruchomienia cztery kwerend na podstawie określonej partycji opcji i ustawień, każdy fragment podczas pobierania danych z bazy danych programu Oracle.

Sugerowane są umożliwiające równoległych kopii danych, partycjonowanie, szczególnie w przypadku, gdy ładowanie dużej ilości danych z bazy danych Oracle. Poniżej przedstawiono sugerowany konfiguracje w różnych scenariuszach:

| Scenariusz                                                     | Sugerowane ustawienia                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Pełne ładowanie z dużych tabel z partycji fizycznych          | **Opcja partycji**: Fizyczne partycje tabeli. <br><br/>W czasie wykonywania data Factory automatycznie Wykryj partycje fizyczne i kopiowanie danych przez partycje. |
| Pełne ładowanie z dużych tabeli bez partycje fizyczne za pomocą kolumna liczb całkowitych do partycjonowania danych | **Opcje partycji**: Partycja zakresu dynamicznego.<br>**Kolumna partycji**: Określ kolumny, używany do partycjonowania danych. Jeśli nie zostanie określony, podstawowej kolumny klucza jest używana. |
| Ładowanie dużej ilości danych przy użyciu niestandardowych zapytań poniżej z partycji fizycznych | **Opcja partycji**: Fizyczne partycje tabeli.<br>**Zapytanie**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**Nazwa partycji**: Określ nazwy partycji w celu skopiowania danych z. Jeśli nie zostanie określony, ADF automatycznie wykryje partycje fizyczne w tabeli, które określiłeś w zestawie danych Oracle.<br><br>W czasie wykonywania i Zastąp fabryki danych `?AdfTabularPartitionName` z nazwą rzeczywistego partycji i Wyślij do programu Oracle. |
| Ładowanie dużej ilości danych przy użyciu zapytanie niestandardowe poniżej bez partycji fizycznych, podczas z kolumna liczb całkowitych do partycjonowania danych | **Opcje partycji**: Partycja zakresu dynamicznego.<br>**Zapytanie**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Kolumna partycji**: Określ kolumny, używany do partycjonowania danych. Można podzielić wobec kolumny z typem danych integer.<br>**Partycja górną granicę** i **partycji dolna granica**: Określ, jeśli chcesz filtrować wobec kolumny partycji w celu pobrania tylko dane między dolny i górny zakres.<br><br>W czasie wykonywania i Zastąp fabryki danych `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound`, i `?AdfRangePartitionLowbound` o nazwie rzeczywiste kolumny i wartości zakresów dla każdej partycji i wysyłać do bazy danych Oracle. <br>Na przykład jeśli Twoje kolumny partycji "ID" ustawiony za pomocą dolnej granicy jako 1 a górną granicę jako 80, za pomocą zestawu równoległego kopii jako 4, usługi ADF pobierania danych przez 4 partycje o identyfikatorze między [1,20], [21, 40], [41, 60] i [61, 80]. |

**Przykład: zapytanie o partycję fizyczną**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**Przykład: zapytanie z dynamicznego zakresu partycji**

```json
"source": {
    "type": "OracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="data-type-mapping-for-oracle"></a>Mapowanie na oprogramowanie Oracle, typ danych

Podczas kopiowania danych z i do oprogramowania Oracle, następujące mapowania są używane z typów danych programu Oracle do typów danych tymczasowych fabryki danych. Aby uzyskać informacje dotyczące sposobu działania kopiowania mapowania typ schematu i danych źródła do ujścia, zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md).

| Typ danych Oracle | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| BFILE |Byte[] |
| BLOB |Byte[]<br/>(obsługiwane tylko w bazie danych Oracle 10g lub nowszy) |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLOAT |Decimal, String (jeśli dokładności > 28) |
| INTEGER |Decimal, String (jeśli dokładności > 28) |
| LONG |String |
| LONG RAW |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMBER |Decimal, String (jeśli dokładności > 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| TIMESTAMP |DateTime |
| TIMESTAMP WITH LOCAL TIME ZONE |String |
| TIMESTAMP WITH TIME ZONE |String |
| UNSIGNED INTEGER |Number |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> Po drugie INTERWAŁU roku do miesiąca i INTERWAŁU dnia na typy danych nie są obsługiwane.


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
