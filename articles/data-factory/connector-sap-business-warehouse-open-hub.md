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
ms.date: 03/08/2019
ms.author: jingwang
ms.openlocfilehash: 6fb989632d3165ac5e54e540aae4385fc2258c85
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/28/2019
ms.locfileid: "66256910"
---
# <a name="copy-data-from-sap-business-warehouse-via-open-hub-using-azure-data-factory"></a>Kopiowanie danych z rozwiązania SAP Business Warehouse, za pośrednictwem Centrum Otwórz za pomocą usługi Azure Data Factory

W tym artykule opisano sposób używania działania kopiowania w usłudze Azure Data Factory do kopiowania danych z SAP Business Warehouse (BW) za pośrednictwem Centrum Otwórz. Opiera się na [omówienie działania kopiowania](copy-activity-overview.md) artykułu, który przedstawia ogólne omówienie działania kopiowania.

## <a name="supported-capabilities"></a>Obsługiwane funkcje

Możesz skopiować dane z rozwiązania SAP Business Warehouse, za pośrednictwem Centrum Otwórz, do dowolnego obsługiwanego magazynu danych ujścia. Aby uzyskać listę magazynów danych, obsługiwane przez działanie kopiowania jako źródła/ujścia, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats) tabeli.

W szczególności ten łącznik SAP Business Warehouse Otwórz Centrum obsługuje:

- SAP Business Warehouse **7.01 lub nowszy (w ostatnich SAP pomocy technicznej pakietu stosie wydana po 2015 roku) w wersji**.
- Kopiowanie danych za pomocą funkcji Otwórz docelowy Centrum lokalnej tabeli, która poniżej może być DSO InfoCube, MultiProvider, źródła danych, itp.
- Kopiowanie danych przy użyciu uwierzytelniania podstawowego.
- Łączenie z serwerem aplikacji.

## <a name="sap-bw-open-hub-integration"></a>Integracja programu SAP BW Open Centrum 

[Otwórz usługę Centrum SAP BW](https://wiki.scn.sap.com/wiki/display/BI/Overview+of+Open+Hub+Service) jest wydajny sposób wyodrębniania danych z systemu SAP BW. Na poniższym diagramie przedstawiono jedną z typowych przepływów, klienci mają w ich systemie SAP, w których przepływy danych przypadków z SAP ECC -> PSA -> DSO -> modułu.

SAP BW Open koncentrator docelowy (OHD) definiowania miejsca docelowego, do którego jest przekazywane danych SAP. Wszystkie obiekty obsługiwane przez proces transferu danych SAP (DTP) może służyć jako źródeł danych otwórz Centrum, na przykład DSO InfoCube, źródła danych, itp. Otwórz Centrum docelowego — w którym są przechowywane dane z przekazywaniem — może być tabel bazy danych (lokalnym lub zdalnym) i plików prostych. Ten łącznik SAP BW Open Centrum Obsługa kopiowanie danych z lokalnej tabeli OHD w BW. W przypadku, gdy używane są inne typy, możesz połączyć bezpośrednio do bazy danych lub systemu plików przy użyciu innych łączników.

![Oprogramowanie SAP BW Open Centrum](./media/connector-sap-business-warehouse-open-hub/sap-bw-open-hub.png)

## <a name="delta-extraction-flow"></a>Przepływ wyodrębniania delta

ADF Otwórz Centrum łącznika systemu SAP BW oferuje dwie właściwości opcjonalne: `excludeLastRequest` i `baseRequestId` który może służyć do obsługi zmian obciążenia z Centrum Otwórz. 

- **excludeLastRequestId**: Określa, czy wykluczyć rekordy ostatniego żądania. Wartość domyślna to true. 
- **baseRequestId**: Identyfikator żądania dla ładowania różnicowych. Po ustawieniu, będą pobierane tylko dane z identyfikatorem requestId jest większa niż wartość tej właściwości. 

Ogólne wyodrębnianie z SAP InfoProviders do platformy Azure Data Factory (ADF) składa się z 2 kroki: 

1. **Oprogramowanie SAP BW dane transferu procesu (DTP)** tego kroku kopiuje dane z SAP BW InfoProvider do tabeli programu SAP BW Open Centrum 

1. **Kopiowanie danych w usłudze ADF** w tym kroku tabeli Otwórz Centrum jest odczytywany przez łącznik usługi ADF 

![Przepływ wyodrębniania delta](media/connector-sap-business-warehouse-open-hub/delta-extraction-flow.png)

W pierwszym kroku DTP jest wykonywany. Każdego wykonania utworzy nowy identyfikator żądania SAP. Identyfikator żądania są przechowywane w tabeli Centrum Otwórz, a następnie jest używany do identyfikowania danych różnicowych przez łącznik usługi ADF. Dwa kroki uruchamiane asynchronicznie: DTP jest wyzwalany przez oprogramowanie SAP, a kopiowanie danych usługi ADF jest wyzwalane za pomocą usługi ADF. 

Domyślnie usługi ADF nie odczytuje najnowszą różnicową z tabeli Otwórz Centrum (opcja "Wyklucz ostatnie żądanie" to true). Niniejszym użytkownik udziela firmie dane w usłudze ADF nie jest 100% aktualny z danymi w tabeli Centrum Otwórz (ostatnie delta Brak). W zamian tej procedury zapewnia żadnych wierszy giną spowodowane przez asynchroniczne wyodrębniania. Działa ona poprawnie, nawet wtedy, gdy ADF odczytuje tabeli Centrum Otwórz DTP jest nadal zapisu w tej samej tabeli. 

Zazwyczaj identyfikator maksymalny skopiowany żądania są przechowywane w ostatnim uruchomieniu przez usługę ADF w przejściowy magazyn danych (np. usługi Azure Blob w powyżej diagram). W związku z tym tego samego żądania nie jest odczytywany po raz drugi przez usługi ADF w kolejnych przebiegu. Ponadto należy zauważyć, że dane nie są automatycznie usuwane z tabeli Otwórz Centrum.

Dla odpowiednich zmian jej obsługa jest nie może mieć identyfikatory z różnych DTPs żądania w tej samej tabeli Otwórz Centrum. W związku z tym należy nie utworzyć więcej niż jeden DTP dla każdego otwartego koncentratora docelowy (OHD). Jeśli zachodzi pełne i różnicowe wyodrębniania z tego samego InfoProvider, należy utworzyć dwa OHDs dla tego samego InfoProvider. 

## <a name="prerequisites"></a>Wymagania wstępne

Aby użyć tego łącznika SAP Business Warehouse Otwórz Centrum, należy:

- Skonfiguruj własne środowisko IR za pomocą wersji 3.13 lub nowszej. Zobacz [własne środowisko IR](create-self-hosted-integration-runtime.md) artykuł, aby uzyskać szczegółowe informacje.

- Pobierz **64-bitowych [SAP .NET łącznika 3.0](https://support.sap.com/en/product/connectors/msnet.html)**  z witryny sieci Web firmy SAP i zainstaluj go na maszynie własne środowisko IR (). Podczas instalowania w oknie kroki opcjonalne ustawienia upewnij się, możesz wybrać **zainstalować zestawy GAC** opcji, jak pokazano na poniższej ilustracji. 

    ![Instalowanie łącznika systemu SAP platformy .NET](./media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

- Użytkownik SAP on używany w łączniku BW fabryki danych musi mieć następujące uprawnienia: 

    - Autoryzacja RFC i system SAP BW. 
    - Uprawnienia do działania "Execute" obiekt autoryzacji "S_SDSAUTH".

- Tworzenie typu miejsca docelowego SAP Otwórz Centrum jako **tabeli bazy danych** z zaznaczoną opcją "Techniczne klucz".  Zalecane jest również pozostawiają dane usuwania z tabeli jako niesprawdzone, ale nie jest wymagana. Wykorzystaj DTP (bezpośrednio wykonania lub integrowanie istniejących łańcuch procesów) jako proponowany danych z obiektu źródłowego (na przykład moduł), aby użytkownik wybrał Centrum Otwórz tabelę docelową.

## <a name="getting-started"></a>Wprowadzenie

> [!TIP]
>
> Przewodnik dotyczący użycia łącznika SAP BW Open Centrum, można zobaczyć [ładowanie danych z usługi SAP Business Warehouse (BW) przy użyciu usługi Azure Data Factory](load-sap-bw-data.md).

[!INCLUDE [data-factory-v2-connector-get-started](../../includes/data-factory-v2-connector-get-started.md)]

Poniższe sekcje zawierają szczegółowe informacje dotyczące właściwości, które są używane do definiowania jednostek usługi fabryka danych określonej do łącznika SAP Business Warehouse Otwórz Centrum.

## <a name="linked-service-properties"></a>Właściwości usługi połączonej

Następujące właściwości są obsługiwane w przypadku usługi SAP Business Warehouse Otwórz Centrum połączone:

| Właściwość | Opis | Wymagane |
|:--- |:--- |:--- |
| type | Właściwość type musi być równa: **SapOpenHub** | Yes |
| serwer | Nazwa serwera, na którym znajduje się wystąpienie systemu SAP BW. | Yes |
| systemNumber | Numer systemu systemu SAP BW.<br/>Dozwolone wartości: liczba dziesiętna dwucyfrowy reprezentowane jako ciąg. | Yes |
| clientId | Identyfikator klienta klienta w SAP W systemie.<br/>Dozwolone wartości: liczba dziesiętna trzy cyfry, reprezentowane jako ciąg. | Yes |
| language | Język, który używa systemu SAP. | Nie (wartość domyślna to **EN**)|
| userName | Nazwa użytkownika, który ma dostęp do serwera SAP. | Yes |
| password | Hasło użytkownika. Oznacz to pole jako SecureString, aby bezpiecznie przechowywać w usłudze Data Factory lub [odwołanie wpisu tajnego przechowywanych w usłudze Azure Key Vault](store-credentials-in-key-vault.md). | Yes |
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

Aby uzyskać pełną listę sekcje i właściwości dostępne Definiowanie zestawów danych, zobacz [zestawów danych](concepts-datasets-linked-services.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez zestaw danych SAP BW Open koncentratora.

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
            "referenceName": "<SAP BW Open Hub linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "openHubDestinationName": "<open hub destination name>"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Właściwości działania kopiowania

Aby uzyskać pełną listę sekcje i właściwości dostępne do definiowania działań zobacz [potoki](concepts-pipelines-activities.md) artykułu. Ta sekcja zawiera listę właściwości obsługiwanych przez źródło SAP BW Open koncentratora.

### <a name="sap-bw-open-hub-as-source"></a>Oprogramowanie SAP BW Open Centrum jako źródło

Aby skopiować dane z Centrum Otwórz programu SAP BW, należy ustawić typ źródła w działaniu kopiowania, aby **SapOpenHubSource**. Nie ma żadnych dodatkowych właściwości specyficzne dla typu, które są potrzebne w działaniu kopiowania **źródła** sekcji.

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

Podczas kopiowania danych z Centrum Otwórz programu SAP BW, następujące mapowania są używane do typów danych tymczasowych usługi Azure Data Factory z typów danych SAP BW. Zobacz [schemat i dane mapowanie typu](copy-activity-schema-and-type-mapping.md) Aby poznać sposób działania kopiowania mapowania typ schematu i danych źródła do ujścia.

| Typ ABAP SAP | Typ danych tymczasowych fabryki danych |
|:--- |:--- |
| C (ciąg) | String |
| Czy mogę (liczba całkowita) | Int32 |
| F (zmiennoprzecinkowego) | Double |
| D (Data) | String |
| T (czas) | String |
| P (BCD spakowane, waluty, Decimal, ilość) | Decimal |
| N (Numc) | String |
| X (binarnych i Raw) | String |

## <a name="next-steps"></a>Kolejne kroki
Aby uzyskać listę magazynów danych obsługiwanych jako źródła i ujścia działania kopiowania w usłudze Azure Data Factory, zobacz [obsługiwane magazyny danych](copy-activity-overview.md#supported-data-stores-and-formats).
