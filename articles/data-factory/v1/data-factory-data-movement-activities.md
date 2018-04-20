---
title: Przenoszenie danych za pomocą działania kopiowania | Dokumentacja firmy Microsoft
description: 'Więcej informacji na temat przenoszenia danych w potokach fabryki danych: migracji danych między magazynami chmury, a także między magazynem lokalnym i magazynu w chmurze. Użyj działania kopiowania.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 67543a20-b7d5-4d19-8b5e-af4c1fd7bc75
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/05/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 09b35483b5ace1523c88576cfe37dfc313b93036
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2018
---
# <a name="move-data-by-using-copy-activity"></a>Przenoszenie danych za pomocą działania kopiowania
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Wersja 1 — ogólnie dostępna](data-factory-data-movement-activities.md)
> * [Wersja 2 — wersja zapoznawcza](../copy-activity-overview.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory, która jest ogólnie dostępna (GA). Jeśli używasz wersji 2 usługi fabryka danych, która jest w wersji zapoznawczej, zobacz [działanie kopiowania w wersji 2](../copy-activity-overview.md).

## <a name="overview"></a>Przegląd
W fabryce danych Azure, umożliwia działanie kopiowania kopiowanie danych między lokalnymi i w chmurze magazynów danych. Po skopiowaniu danych można go dalej transformacji i analizy. Działanie kopiowania umożliwia także publikowanie transformacji i wyniki analizy do analizy biznesowej (BI) i użycie aplikacji.

![Rola działanie kopiowania](media/data-factory-data-movement-activities/copy-activity.png)

Działanie kopiowania jest obsługiwany przez usługę bezpieczny, niezawodnych, skalowalnych i [ogólnie dostępna usługa](#global). Ten artykuł zawiera szczegółowe informacje na przenoszenie danych w fabryce danych i działanie kopiowania.

Po pierwsze Zobaczmy, jak migracja danych odbywa się między dwoma magazyny danych chmury oraz między z lokalnym magazynem danych i magazynu danych w chmurze.

> [!NOTE]
> Aby dowiedzieć się więcej o działaniach ogólnie rzecz biorąc, zobacz [potoki zrozumienia i działania](data-factory-create-pipelines.md).
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Kopiowanie danych między dwa magazyny danych w chmurze
Jeśli źródłowy i odbiorczy magazyny danych znajdują się w chmurze, działanie kopiowania przechodzi przez następujące etapy, aby skopiować dane ze źródła do ujścia. Usługa obsługującego działanie kopiowania:

1. Odczytuje dane z magazynu danych źródłowych.
2. Przeprowadza serializacji/deserializacji, kompresji i dekompresji, w mapowaniu kolumn, a konwersja typu. Tak, aby te operacje konfiguracji wejściowy zestaw danych, wyjściowy zestaw danych i działanie kopiowania.
3. Zapisuje dane w magazynie danych docelowym.

Usługa automatycznie wybiera optymalną region do wykonania przepływu danych. Ten region jest zwykle najbliżej ujścia magazynu danych.

![Kopiowania w chmurze na chmurze](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopiowanie danych między z lokalnym magazynem danych i magazynu danych w chmurze
Bezpieczne przenoszenie danych między z lokalnym magazynem danych i magazynu danych w chmurze, należy zainstalować bramę zarządzania danymi na komputerze lokalnym. Brama zarządzania danymi jest agentem, który umożliwia hybrydowego przenoszenia danych i przetwarzania. Można zainstalować go na tym samym komputerze jako magazyn danych sam lub na osobnym komputerze mający dostęp do magazynu danych.

W tym scenariuszu brama zarządzania danymi wykonuje serializacji/deserializacji, kompresji i dekompresji, w mapowaniu kolumn, a konwersja typu. Nie przepływu danych, za pośrednictwem usługi fabryka danych Azure. Zamiast tego brama zarządzania danymi bezpośrednio zapisuje dane do magazynu docelowego.

![Kopiuj na lokalnym — do chmury](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Zobacz [przenoszenie danych między lokalnymi i w chmurze magazyny danych](data-factory-move-data-between-onprem-and-cloud.md) wprowadzenie i wskazówki. Zobacz [brama zarządzania danymi](data-factory-data-management-gateway.md) szczegółowe informacje dotyczące tego agenta.

Można również przenosić dane z/na obsługiwane magazyny danych, hostowanych na maszynach wirtualnych Azure IaaS (VM) przy użyciu bramy zarządzania danymi. W takim przypadku można zainstalować brama zarządzania danymi na tej samej maszyny Wirtualnej, Magazyn danych sam lub na oddzielnym maszyny Wirtualnej mający dostęp do magazynu danych.

## <a name="supported-data-stores-and-formats"></a>Magazyny danych obsługiwane i formaty
Działanie kopiowania w usłudze Data Factory kopiuje dane z magazynu danych źródła do magazynu danych ujścia. Usługa Data Factory obsługuje następujące magazyny danych. Dane z dowolnego źródła można zapisać do dowolnego ujścia. Kliknij magazyn danych, aby dowiedzieć się, jak kopiować dane do i z tego magazynu.

> [!NOTE] 
> Jeśli musisz przenieść dane do/z magazynu danych nieobsługiwanego przez działanie kopiowania, użyj **niestandardowego działania** w usłudze Data Factory z własną logiką do obsługi kopiowania/przenoszenia danych. Aby uzyskać szczegółowe informacje na temat tworzenia i używania niestandardowego działania, zobacz artykuł [Use custom activities in an Azure Data Factory pipeline](data-factory-use-custom-activities.md) (Korzystanie z niestandardowych działań w potoku usługi Azure Data Factory).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Magazyny danych oznaczone znakiem * mogą być konfigurowane lokalnie lub w usłudze Azure IaaS i wymagają zainstalowania [bramy zarządzania danymi](data-factory-data-management-gateway.md) na maszynie lokalnej lub w usłudze Azure IaaS.

### <a name="supported-file-formats"></a>Obsługiwane formaty plików
Można użyć do działania kopiowania **skopiuj pliki jako — jest** między magazyny dwóch danych opartych na plikach, możesz pominąć [format sekcji](data-factory-create-datasets.md) w definicji zestawu danych wejściowych i wyjściowych. Dane są kopiowane wydajnie bez żadnych serializacji/deserializacji.

Działanie kopiowania również odczytuje i zapisuje do plików w określonym formacie: **tekst JSON, Avro, ORC i Parquet**i koder-dekoder kompresji **GZip, Deflate, BZip2 i ZipDeflate** są obsługiwane. Zobacz [obsługiwane formaty plików i kompresji](data-factory-supported-file-and-compression-formats.md) ze szczegółami.

Na przykład można wykonywać następujące działania kopiowania:

* Kopiowanie danych w lokalnym programie SQL Server i zapisać w formacie ORC Azure Data Lake Store.
* Skopiuj pliki w formacie tekstowym (CSV) z lokalnego systemu plików i zapisać w formacie Avro obiektów Blob platformy Azure.
* Skopiuj pliki zip z lokalnego systemu plików, a następnie dekompresja ziemi do usługi Azure Data Lake Store.
* Kopiowanie danych w formacie tekstowym skompresowane (CSV) GZip z obiektów Blob platformy Azure i zapisu do bazy danych SQL Azure.

## <a name="global"></a>Przenoszenie danych ogólnie dostępna
Fabryka danych Azure jest dostępna tylko w regionach zachodnie stany USA, wschodnie stany USA i Europa Północna, Europa. Jednak usługa obsługującego działanie kopiowania jest dostępna globalnie w następujących regionach i lokalizacji geograficznych. Ogólnie dostępna topologia zapewnia przepływ danych wydajne, pozwalający na uniknięcie zwykle przeskoków między regionu. Zobacz [usług według regionu](https://azure.microsoft.com/regions/#services) dostępność fabryki danych i przenoszenie danych w regionie.

### <a name="copy-data-between-cloud-data-stores"></a>Kopiowanie danych między magazyny danych w chmurze
W przypadku zarówno źródłowy i odbiorczy magazyny danych w chmurze, fabryki danych użyje wdrożenia usługi w regionie zbliżony do ujścia w tej samej lokalizacji geograficznej, aby przenieść dane. Mapę można znaleźć w poniższej tabeli:

| Geograficzne docelowego magazyny danych | Region docelowy magazynu danych | Region używane do przenoszenia danych |
|:--- |:--- |:--- |
| Stany Zjednoczone | Wschodnie stany USA | Wschodnie stany USA |
| &nbsp; | Wschodnie stany USA 2 | Wschodnie stany USA 2 |
| &nbsp; | Środkowe stany USA | Środkowe stany USA |
| &nbsp; | Środkowo-północne stany USA | Środkowo-północne stany USA |
| &nbsp; | Środkowo-południowe stany USA | Środkowo-południowe stany USA |
| &nbsp; | Środkowo-zachodnie stany USA | Środkowo-zachodnie stany USA |
| &nbsp; | Zachodnie stany USA | Zachodnie stany USA |
| &nbsp; | Zachodnie stany USA 2 | Zachodnie stany USA 2 |
| Kanada | Kanada Wschodnia | Kanada Środkowa |
| &nbsp; | Kanada Środkowa | Kanada Środkowa |
| Brazylia | Brazylia Południowa | Brazylia Południowa |
| Europa | Europa Północna | Europa Północna |
| &nbsp; | Europa Zachodnia | Europa Zachodnia |
| Wielka Brytania | Zachodnie Zjednoczone Królestwo | Południowe Zjednoczone Królestwo |
| &nbsp; | Południowe Zjednoczone Królestwo | Południowe Zjednoczone Królestwo |
| Azja i Pacyfik | Azja Południowo-Wschodnia | Azja Południowo-Wschodnia |
| &nbsp; | Azja Wschodnia | Azja Południowo-Wschodnia |
| Australia | Australia Wschodnia | Australia Wschodnia |
| &nbsp; | Australia Południowo-Wschodnia | Australia Południowo-Wschodnia |
| Indie | Indie Środkowe | Indie Środkowe |
| &nbsp; | Indie Zachodnie | Indie Środkowe |
| &nbsp; | Indie Południowe | Indie Środkowe |
| Japonia | Japonia Wschodnia | Japonia Wschodnia |
| &nbsp; | Japonia Zachodnia | Japonia Wschodnia |
| Korea | Korea Środkowa | Korea Środkowa |
| &nbsp; | Korea Południowa | Korea Środkowa |

Można również jawnie wskazać region usługi fabryka danych ma być używany do wykonania kopii, określając `executionLocation` właściwości w działanie kopiowania `typeProperties`. Obsługiwane wartości dla tej właściwości są wymienione w powyżej **Region używane do przenoszenia danych** kolumny. Należy pamiętać, że dane przechodzi przez tego regionu przez sieć podczas kopiowania. Na przykład do kopiowania między Azure są przechowywane w Korei, można określić `"executionLocation": "Japan East"` do Rozsyłanie za pomocą region Japonii (zobacz [przykładowe JSON](#by-using-json-scripts) jako odwołanie).

> [!NOTE]
> Jeśli region docelowy magazyn danych nie jest w powyższej listy lub niewykrywalnym, domyślnie aktywność kopiowania nie powiedzie się zamiast pośrednictwa alternatywnych regionu, chyba że `executionLocation` jest określona. Lista obsługiwanych regionów będą rozszerzane wraz z upływem czasu.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopiowanie danych między z lokalnym magazynem danych i magazynu danych w chmurze
Gdy dane są kopiowane między lokalnymi (lub maszyn wirtualnych Azure/IaaS) i w chmurze magazynów, [brama zarządzania danymi](data-factory-data-management-gateway.md) wykonuje przenoszenia danych na lokalnym komputerze lub maszynie wirtualnej. Nie przepływ danych za pośrednictwem usługi w chmurze, jeśli nie używasz [przemieszczane kopiowania](data-factory-copy-activity-performance.md#staged-copy) możliwości. W takim przypadku dane przechodzi przez przemieszczania magazynu obiektów Blob Azure zanim zostaną zapisane w magazynie danych obiektu sink.

## <a name="create-a-pipeline-with-copy-activity"></a>Utworzyć potok z działanie kopiowania
Można utworzyć potoku o działanie kopiowania na kilka sposobów:

### <a name="by-using-the-copy-wizard"></a>Za pomocą Kreatora kopiowania
Kreator kopiowania fabryki danych pomaga utworzyć potok z działania kopiowania. Tego potoku umożliwia kopiowanie danych z obsługiwanych źródeł do miejsc docelowych *bez pisania JSON* definicje połączone usługi, zestawy danych i potoki. Zobacz [kreatora kopiowania fabryki danych](data-factory-copy-wizard.md) szczegółowe informacje na temat kreatora.  

### <a name="by-using-json-scripts"></a>Za pomocą skryptów JSON
Edytor fabryki danych w portalu Azure, programu Visual Studio lub Azure PowerShell służy do tworzenia definicji JSON potoku (za pomocą działania kopiowania). Następnie można wdrożyć, aby utworzyć potok w fabryce danych. Zobacz [samouczek: użyj kopii działania w potoku fabryki danych Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) samouczek z instrukcjami krok po kroku.    

Właściwości JSON (takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasad) są dostępne dla wszystkich typów działań. Właściwości, które są dostępne w `typeProperties` sekcji działania zależne od każdego typu działania.

Dla działania kopiowania `typeProperties` sekcji może być różna w zależności od typów źródeł i sink. Kliknij źródło/ujście w [obsługiwanych źródeł i wychwytywanie](#supported-data-stores-and-formats) sekcji, aby dowiedzieć się więcej na temat typu właściwości, które obsługuje działanie kopiowania dla tego magazynu danych.

Oto przykład definicji JSON:

```json
{
  "name": "ADFTutorialPipeline",
  "properties": {
    "description": "Copy data from Azure blob to Azure SQL table",
    "activities": [
      {
        "name": "CopyFromBlobToSQL",
        "type": "Copy",
        "inputs": [
          {
            "name": "InputBlobTable"
          }
        ],
        "outputs": [
          {
            "name": "OutputSQLTable"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "SqlSink"
          },
          "executionLocation": "Japan East"          
        },
        "Policy": {
          "concurrency": 1,
          "executionPriorityOrder": "NewestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
    ],
    "start": "2016-07-12T00:00:00Z",
    "end": "2016-07-13T00:00:00Z"
  }
}
```
Określa harmonogram, który jest zdefiniowany w zestawie danych wyjściowych, gdy działanie zostało uruchomione (na przykład: **codzienne**, częstotliwość jako **dzień**i interwał jako **1**). Działanie kopiuje dane z zestawem danych wejściowych (**źródła**) do zestawu danych wyjściowych (**zbiornika**).

Można określić więcej niż jeden wejściowy zestaw danych do działania kopiowania. Są one używane do Sprawdź zależności, przed rozpoczęciem wykonywania działania. Jednak tylko dane z pierwszego zestawu danych jest kopiowana do docelowego zestawu danych. Aby uzyskać więcej informacji, zobacz [planowania i wykonywania](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Zobacz [wydajności działania kopiowania i dostrajania przewodnik](data-factory-copy-activity-performance.md), która opisuje kluczowych czynników wpływających na wydajność przepływu danych (działanie kopiowania) w fabryce danych Azure. Również zawiera listę obserwowana wydajność podczas testowania wewnętrznego oraz opisano różne sposoby optymalizacji wydajności działania kopiowania.

## <a name="fault-tolerance"></a>Odporność na uszkodzenia
Domyślnie działanie kopiowania będzie zatrzymać kopiowanie danych i przywrócenie błąd wystąpi podczas niezgodne dane między źródłowy i odbiorczy; Mimo że możesz jawnie skonfigurować, aby pominąć i niezgodny wierszy i kopię tylko dziennika tych danych zgodne, aby wykonać kopię zakończyło się pomyślnie. Zobacz [działanie kopiowania odporność na uszkodzenia](data-factory-copy-activity-fault-tolerance.md) na więcej szczegółów.

## <a name="security-considerations"></a>Zagadnienia związane z zabezpieczeniami
Zobacz [zagadnienia dotyczące zabezpieczeń](data-factory-data-movement-security-considerations.md), która opisuje infrastrukturę zabezpieczeń, która umożliwia Zabezpieczanie danych usługi przenoszenia danych z fabryki danych Azure.

## <a name="scheduling-and-sequential-copy"></a>Kopiowanie planowania i sekwencyjnych
Zobacz [planowania i wykonywania](data-factory-scheduling-and-execution.md) Aby uzyskać szczegółowe informacje na temat planowania i wykonywania działania w fabryce danych. Istnieje możliwość uruchamiane po kolei wiele operacji kopiowania w sposób sekwencyjnych/uporządkowane. Zobacz [skopiuj sekwencyjnie](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) sekcji.

## <a name="type-conversions"></a>Konwersje typów
Magazyny danych różnych ma inny typ macierzysty systemów. Działanie kopiowania wykonuje automatyczne konwersje z typów źródła do zbiornika typy z następujących rozwinięcie:

1. Konwertowanie typów natywnych źródła na typ architektury .NET.
2. Konwertować z typu .NET do typu natywnego ujścia.

Mapowanie system typu macierzystego typu .NET dla magazynu danych znajduje się w artykule magazynu odpowiednich danych. (Kliknij łącze w [obsługiwane magazyny danych](#supported-data-stores) tabeli). Te mapowania umożliwia określanie typów odpowiednie podczas tworzenia tabel, dzięki czemu prawo konwersje wykonuje działanie kopiowania.

## <a name="next-steps"></a>Kolejne kroki
* Więcej informacji o działaniu kopiowania zobacz [skopiowanie danych z magazynu obiektów Blob platformy Azure do usługi Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Aby dowiedzieć się więcej na temat przenoszenia danych z lokalnego magazynu danych w magazynie danych w chmurze, zobacz [przenoszenia danych z lokalnymi do chmury magazyny danych](data-factory-move-data-between-onprem-and-cloud.md).
