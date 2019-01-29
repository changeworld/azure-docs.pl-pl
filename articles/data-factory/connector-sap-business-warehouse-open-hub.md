---
title: Kopiowanie danych z rozwiązania SAP Business Warehouse, za pośrednictwem Centrum Otwórz za pomocą usługi Azure Data Factory | Dokumentacja firmy Microsoft
description: Dowiedz się, jak skopiować dane z SAP Business Warehouse (BW) za pośrednictwem Centrum Otwórz do magazynów danych ujścia obsługiwane za pomocą działania kopiowania w potoku usługi Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/28/2019
ms.author: jingwang
ms.openlocfilehash: d20ba372a23d2d4865bd9d6c5c004f955c896201
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2019
ms.locfileid: "55199187"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Kopiowanie danych z rozwiązania SAP Business Warehouse, za pośrednictwem Centrum Otwórz za pomocą usługi Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z SAP Business Warehouse (BW) za pośrednictwem Centrum Otwórz. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="sap-bw-open-hub-integration"></a>Integracja programu SAP BW Open Centrum 

[Otwórz usługę Centrum SAP BW](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) jest wydajny sposób wyodrębniania danych z systemu SAP BW. Na poniższym diagramie przedstawiono jedną z typowych przepływów, klienci mają w ich systemie SAP, w których przepływy danych przypadków z SAP ECC -> PSA -> DSO -> modułu.

SAP BW Open koncentrator docelowy (OHD) definiowania miejsca docelowego, do którego jest przekazywane danych SAP. Wszystkie obiekty obsługiwane przez proces transferu danych SAP (DTP) może służyć jako źródeł danych otwórz Centrum, na przykład DSO InfoCube, MultiProvider, źródła danych, itp. Otwórz Centrum docelowego — w którym są przechowywane dane z przekazywaniem — może być tabel bazy danych (lokalnym lub zdalnym) i plików prostych. Ten łącznik SAP BW Open Centrum Obsługa kopiowanie danych z lokalnej tabeli OHD w BW. W przypadku, gdy używane są inne typy, możesz połączyć bezpośrednio do bazy danych lub systemu plików przy użyciu innych łączników.

![Oprogramowanie SAP BW Open Centrum](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z rozwiązania SAP Business Warehouse, za pośrednictwem Centrum Otwórz, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik SAP Business Warehouse Otwórz Centrum obsługuje:

- SAP Business Warehouse **wersji 7.30 lub nowszy z SAPK 73013INPIBASIS obsługiwać zainstalowany pakiet**.
- Kopiowanie danych za pomocą funkcji Otwórz docelowy Centrum lokalnej tabeli, która poniżej może być DSO InfoCube, MultiProvider, źródła danych, itp.
- Kopiowanie danych przy użyciu uwierzytelniania podstawowego.
- Łączenie z serwerem aplikacji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika SAP Business Warehouse, należy:

- Skonfiguruj własne środowisko IR za pomocą wersji 3.13 lub nowszej. Zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md) artykuł, aby uzyskać szczegółowe informacje.

- Pobierz **64-bitowych [SAP .NET łącznika 3.0](https://support.sap.com/en/product/connectors/msnet.html)**  z witryny sieci Web firmy SAP i zainstaluj go na maszynie własne środowisko IR (). Podczas instalowania w oknie kroki opcjonalne ustawienia upewnij się, możesz wybrać **zainstalować zestawy GAC** opcji, jak pokazano na poniższej ilustracji. 

    ![Instalowanie łącznika systemu SAP platformy .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Użytkownik SAP on używany w łączniku BW fabryki danych musi mieć następujące uprawnienia: 

    - Autoryzacja RFC i system SAP BW. 
    - Uprawnienia do działania "Execute" obiekt autoryzacji "S_SDSAUTH".

- Typ miejsca docelowego Centrum Otwórz Craete SAP jako **tabeli bazy danych** z zaznaczoną opcją "Techniczne klucz".  Zalecane jest również pozostawiają dane usuwania z tabeli jako niesprawdzone, ale nie jest wymagana. Wykonaj DTP na ładowanie danych z obiektu źródłowego (na przykład moduł) została wybrana opcja Centrum Otwórz tabelę docelową.

## <a name="getting-started"></a>Wprowadzenie

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika SAP Business Warehouse.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane przez oprogramowanie SAP Business Warehouse (BW), połączone usługi:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **SapOpenHub** | Yes |
| serwer | Nazwa serwera, na którym znajduje się wystąpienie systemu SAP BW. | Yes |
| systemNumber | Numer systemu systemu SAP BW.<br/>Dozwolone wartości: liczba dziesiętna dwucyfrowy reprezentowane jako ciąg. | Yes |
| clientId | Identyfikator klienta klienta w SAP W systemie.<br/>Dozwolone wartości: liczba dziesiętna trzy cyfry, reprezentowane jako ciąg. | Yes |
| userName | Nazwa użytkownika, który ma dostęp do serwera SAP. | Yes |
| hasło | Hasło użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
| connectVia | [Środowiska Integration Runtime](concepts-integration-runtime.md) ma być używany do łączenia się z magazynem danych. Własne środowisko IR jest wymagany, zgodnie z opisem w [wymagania wstępne](#prerequisites). |Yes |

**Przykład:**

```json
{
    "name": "SapBwOpenHubLinkedService",
    "properties": {
        "type": "SapOpenHub",
        "typeProperties": {
            "server": "<server name>",
            "systemNumber": "<system number>",
            "clientId": "<client id>",
            "userName": "<SAP user>",
            "password": {
                "type": "SecureString",
                "value": "<Password for SAP user>"
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych usługi Salesforce.

Aby skopiować dane z i do Centrum Otwórz programu SAP BW, należy ustawić właściwość typu zestawu danych na **SapOpenHubTable**. Następujące właściwości są obsługiwane.

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa **SapOpenHubTable**.  | Yes |
| openHubDestinationName | Nazwa do skopiowania danych z Centrum Otwórz w lokalizacji docelowej. | Yes |
| excludeLastRequest | Określa, czy wykluczyć rekordy ostatniego żądania. | Nie (wartość domyślna to **true**) |
| baseRequestId | Identyfikator żądania dla ładowania różnicowych. Po ustawieniu, tylko dane z identyfikatorem requestId **większy niż** zostanie pobrana wartość tej właściwości.  | Nie |

>[!TIP]
>Jeśli Centrum Otwórz tabeli zawiera tylko dane generowane przez identyfikator pojedynczego żądania, na przykład zawsze pełnych obciążenia i zastąpić istniejące dane w tabeli lub uruchomisz wyłącznie DTP jeden raz dla testów, pamiętaj, aby Usuń zaznaczenie opcji "excludeLastRequest", aby można było skopiować do d Usługa ATA out.

**Przykład:**

```json
{
    "name": "SAPBWOpenHubDataset",
    "properties": {
        "type": "SapOpenHubTable",
        "linkedServiceName": {
            "referenceName": "<SAP BW linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwane przez system SAP BW źródło.

### <a name="sap-bw-open-hub-as-source"></a>Oprogramowanie SAP BW Open Centrum jako źródło

Aby skopiować dane z Centrum Otwórz programu SAP BW, należy ustawić typ źródła w działaniu kopiowania, aby **SapOpenHubSource**. Gdy nie ma żadnych dodatkowych właściwości specyficzne dla typu, które są potrzebne w działaniu kopiowania **źródła** sekcji.

**Przykład:**

```json
"activities":[
    {
        "name": "CopyFromSAPBWOpenHub",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<SAP BW Open Hub input dataset name>",
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
                "type": "SapOpenHubSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="data-type-mapping-for-sap-bw-open-hub"></a>Mapowanie typu danych dla Centrum Otwórz programu SAP BW

Podczas kopiowania danych z systemu SAP BW, następujące mapowania są używane do typów danych tymczasowych usługi Azure Data Factory z typów danych SAP BW. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

| Typ ABAP SAP | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| C (ciąg) | Ciąg |
| Czy mogę (liczba całkowita) | Int32 |
| F (zmiennoprzecinkowego) | Podwójne |
| D (Data) | Ciąg |
| T (czas) | Ciąg |
| P (BCD spakowane, waluty, Decimal, ilość) | Dziesiętny |
| N (Numc) | Ciąg |
| X (binarnych i Raw) | Ciąg |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
