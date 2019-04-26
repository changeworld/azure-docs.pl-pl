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
ms.date: 02/01/2019
ms.author: jingwang
ms.openlocfilehash: 3fa7612b9e4cd8a714e60879229bd0d39349494f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60405940"
---
# <a name="copy-data-from-and-to-oracle-by-using-azure-data-factory"></a>Kopiowanie danych z i do oprogramowania Oracle przy użyciu usługi Azure Data Factory
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1](v1/data-factory-onprem-oracle-connector.md)
> * [Bieżąca wersja](connector-oracle.md)

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z i do bazy danych Oracle. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z bazy danych Oracle do dowolnego obsługiwanego magazynu danych ujścia. Możesz także skopiować dane z dowolnego obsługiwanego źródłowego magazynu danych do bazy danych Oracle. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła lub ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik Oracle obsługuje następujące wersje bazy danych Oracle. Obsługuje również uwierzytelnianie Basic lub identyfikator OID:

- R1 Oracle 12c (12.1)
- Oracle 11g R1, R2 (11.1, 11.2)
- Oracle 10g R1, R2 (10.1, 10.2)
- Oracle 9i R1, R2 (9.0.1, 9.2)
- Oracle 8i R3 (8.1.7)

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

1.  Aby użyć **Triple-DES szyfrowania (3DES) i Advanced Encryption Standard (AES)**, po stronie serwera Oracle, przejdź do bazy danych Oracle zaawansowane zabezpieczenia (OAS) i konfigurowanie ustawień szyfrowania, zobacz szczegóły dotyczące [tutaj](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). Łącznik ADF Oracle automatycznie negocjuje metodę szyfrowania, użyj skonfigurowanych w OAS, podczas ustanawiania połączenia Oracle.

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

Aby skopiować dane z bazy danych Oracle, należy ustawić typ źródła w działaniu kopiowania, aby **OracleSource**. Następujące właściwości są obsługiwane w działaniu kopiowania **źródła** sekcji.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość typu źródła działania kopiowania musi być równa **OracleSource**. | Yes |
| oracleReaderQuery | Umożliwia odczytywanie danych niestandardowe zapytania SQL. Może to być na przykład `"SELECT * FROM MyTable"`. | Nie |

Jeśli nie określisz "oracleReaderQuery" kolumny zdefiniowane w sekcji "strukturę" zestawu danych są używane do konstruowania zapytania (`select column1, column2 from mytable`) do uruchamiania w bazie danych Oracle. Definicja zestawu danych nie ma "strukturę", wszystkie kolumny są wybierane z tabeli.

**Przykład:**

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

## <a name="data-type-mapping-for-oracle"></a>Mapowanie na oprogramowanie Oracle, typ danych

Podczas kopiowania danych z i do oprogramowania Oracle, następujące mapowania są używane z typów danych programu Oracle do typów danych tymczasowych fabryki danych. Aby uzyskać informacje dotyczące sposobu działania kopiowania mapowania typ schematu i danych źródła do ujścia, zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md).

| Typ danych Oracle | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| BPLIK |Byte[] |
| BLOB |Byte[]<br/>(obsługiwane tylko w bazie danych Oracle 10g lub nowszy) |
| CHAR |String |
| CLOB |String |
| DATE |DateTime |
| FLOAT |Decimal, ciąg (jeśli dokładności > 28) |
| INTEGER |Decimal, ciąg (jeśli dokładności > 28) |
| DŁUGI |String |
| DŁUGI NIEPRZETWORZONE |Byte[] |
| NCHAR |String |
| NCLOB |String |
| NUMER |Decimal, ciąg (jeśli dokładności > 28) |
| NVARCHAR2 |String |
| RAW |Byte[] |
| ROWID |String |
| SYGNATURA CZASOWA |DateTime |
| SYGNATURA CZASOWA PRZY UŻYCIU LOKALNEJ STREFY CZASOWEJ |String |
| SYGNATURA CZASOWA ZE STREFĄ CZASOWĄ |String |
| LICZBA CAŁKOWITA BEZ ZNAKU |Liczba |
| VARCHAR2 |String |
| XML |String |

> [!NOTE]
> Po drugie INTERWAŁU roku do miesiąca i INTERWAŁU dnia na typy danych nie są obsługiwane.


## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md##supported-data-stores-and-formats).
