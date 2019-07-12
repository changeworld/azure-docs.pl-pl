---
title: Przenoszenie danych za pomocą działania kopiowania | Dokumentacja firmy Microsoft
description: 'Więcej informacji na temat przenoszenia danych w potoków usługi Data Factory: migrację danych między magazynami w chmurze oraz między lokalnym magazynem i magazynem w chmurze. Użyj działania kopiowania.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.assetid: 67543a20-b7d5-4d19-8b5e-af4c1fd7bc75
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: bfb15e717e3cb726aba782d9a9506330d7ea39fe
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67839313"
---
# <a name="move-data-by-using-copy-activity"></a>Przenoszenie danych za pomocą działania kopiowania
> [!div class="op_single_selector" title1="Wybierz wersję usługi Data Factory, którego używasz:"]
> * [Wersja 1](data-factory-data-movement-activities.md)
> * [Wersja 2 (bieżąca wersja)](../copy-activity-overview.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącą wersję usługi Data Factory, zobacz [działania kopiowania w wersji 2](../copy-activity-overview.md).

## <a name="overview"></a>Omówienie
W usłudze Azure Data Factory umożliwia działanie kopiowania lokalnych i chmurze kopiować dane między magazynami danych. Po skopiowaniu danych można je dodatkowo przekształcane i analizowane. Działanie kopiowania umożliwia również publikować wyniki analizy do analizy biznesowej (BI) i użycie aplikacji i przekształcania.

![Rola działania kopiowania](media/data-factory-data-movement-activities/copy-activity.png)

Działanie kopiowania bazuje bezpieczne, niezawodne i skalowalne i [globalnie dostępną usługę,](#global). Ten artykuł zawiera szczegółowe informacje dotyczące przenoszenia danych w fabryce danych i działania kopiowania.

Po pierwsze Zobaczmy, jak migracja danych odbywa się między dwoma magazynami danych w chmurze oraz między lokalnym magazynem danych i magazynem danych w chmurze.

> [!NOTE]
> Aby dowiedzieć się więcej o działaniach ogólnie rzecz biorąc, zobacz [opis potoki i działania](data-factory-create-pipelines.md).
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Kopiowanie danych między dwoma magazynami danych w chmurze
W przypadku zarówno źródła i ujścia magazyny danych w chmurze, działanie kopiowania przechodzi przez następujące etapy w celu skopiowania danych ze źródła do ujścia. Usługa, która obsługuje działania kopiowania:

1. Odczytuje dane z magazynu danych źródłowych.
2. Wykonuje serializacji/deserializacji, kompresji i dekompresji, mapowania kolumn, a następnie wpisz konwersji. Robi te operacje na podstawie konfiguracji zestaw wejściowy, wyjściowy zestaw danych i działania kopiowania.
3. Zapisuje dane do docelowego magazynu danych.

Usługa automatycznie wybiera optymalną region do wykonania przenoszenia danych. Ten region to zwykle spowodowane jednym najbardziej zbliżony do magazynu danych ujścia.

![Kopia w chmurze do chmury](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopiowanie danych między lokalnym magazynem danych i magazynem danych w chmurze
Bezpieczne przenoszenie danych między lokalnym magazynem danych i magazynem danych w chmurze, należy zainstalować bramę zarządzania danymi na maszynie lokalnej. Brama zarządzania danymi to agent, który umożliwia hybrydowe przenoszenie danych i przetwarzania. Można zainstalować go na tym samym komputerze sam magazyn danych lub na osobnym komputerze, ma dostęp do magazynu danych.

W tym scenariuszu brama zarządzania danymi wykonuje serializacji/deserializacji, kompresji i dekompresji, mapowania kolumn i konwersja typu. Dane nie przepływać za pośrednictwem usługi Azure Data Factory. Zamiast tego bramy zarządzania danymi bezpośrednio zapisuje dane do magazynu docelowego.

![Kopia lokalna do-w chmurze](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Zobacz [przenoszenie danych między lokalizacją lokalną i chmurą magazyny danych](data-factory-move-data-between-onprem-and-cloud.md) wprowadzenie i wskazówki. Zobacz [bramy zarządzania danymi](data-factory-data-management-gateway.md) szczegółowe informacje dotyczące tego agenta.

Można również przenosić dane z i do obsługiwanych magazynów danych, które są hostowane na maszynach wirtualnych IaaS platformy Azure (maszyny wirtualne) przy użyciu bramy zarządzania danymi. W tym przypadku można zainstalować bramy zarządzania danymi na tej samej maszyny Wirtualnej, ponieważ sam magazyn danych lub osobne maszyny wirtualnej, ma dostęp do magazynu danych.

## <a name="supported-data-stores-and-formats"></a>Formaty i obsługiwane magazyny danych
Działanie kopiowania w usłudze Data Factory kopiuje dane z magazynu danych źródła do magazynu danych ujścia. Usługa Data Factory obsługuje następujące magazyny danych. Dane z dowolnego źródła można zapisać do dowolnego ujścia. Kliknij magazyn danych, aby dowiedzieć się, jak kopiować dane do i z tego magazynu.

> [!NOTE] 
> Jeśli musisz przenieść dane do/z magazynu danych nieobsługiwanego przez działanie kopiowania, użyj **niestandardowego działania** w usłudze Data Factory z własną logiką do obsługi kopiowania/przenoszenia danych. Aby uzyskać szczegółowe informacje na temat tworzenia i używania niestandardowego działania, zobacz artykuł [Use custom activities in an Azure Data Factory pipeline](data-factory-use-custom-activities.md) (Korzystanie z niestandardowych działań w potoku usługi Azure Data Factory).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Magazyny danych oznaczone znakiem * mogą być konfigurowane lokalnie lub w usłudze Azure IaaS i wymagają zainstalowania [bramy zarządzania danymi](data-factory-data-management-gateway.md) na maszynie lokalnej lub w usłudze Azure IaaS.

### <a name="supported-file-formats"></a>Obsługiwane formaty plików
Można użyć działania kopiowania do **skopiuj pliki — jest** między magazynami danych opartych na plikach dwóch, możesz pominąć [format sekcji](data-factory-create-datasets.md) w definicji zestawu danych wejściowych i wyjściowych. Dane są kopiowane efektywnie bez żadnych serializacji/deserializacji.

Działanie kopiowania również odczytuje i zapisuje w plikach w wskazanych formatów: **Tekst, JSON, Avro, ORC i Parquet**i kodera-dekodera kompresji **GZip, Deflate, BZip2 i ZipDeflate** są obsługiwane. Zobacz [obsługiwane formaty plików i kompresji](data-factory-supported-file-and-compression-formats.md) ze szczegółowymi informacjami.

Na przykład należy wykonać następujące działania kopiowania:

* Kopiowanie danych na lokalnym serwerze SQL i zapisywać do usługi Azure Data Lake Store w formacie ORC.
* Skopiuj pliki w formacie tekstowym (CSV) w systemie plików w środowisku lokalnym i zapisywać do obiektów Blob platformy Azure, w formacie Avro.
* Skopiuj pliki zip z systemu plików w środowisku lokalnym i następnie Dekompresuj ziemi do usługi Azure Data Lake Store.
* Kopiowanie danych w formacie tekstowym skompresowany (CSV) GZip z obiektów Blob platformy Azure i zapisu do usługi Azure SQL Database.

## <a name="global"></a>Przenoszenie danych dostępnie
Usługa Azure Data Factory jest dostępna tylko w regionach zachodnie stany USA, wschodnie stany USA i Europa Północna. Jednak usługa zapewniająca działania kopiowania jest dostępna globalnie w następujących regionów i obszarów geograficznych. Dostępnie topologii zapewnia efektywne przenoszenia, zazwyczaj pozwala uniknąć przeskoków między regionami. Zobacz [usługi według regionów](https://azure.microsoft.com/regions/#services) dostępność usługi Data Factory i przenoszenia danych w regionie.

### <a name="copy-data-between-cloud-data-stores"></a>Kopiowanie danych między magazynami danych w chmurze
W przypadku zarówno źródła i ujścia magazyny danych w chmurze, Data Factory przy użyciu wdrożenia usługi w regionie najbliższym ujścia w tej samej lokalizacji geograficznej, aby przenieść dane. Mapę można znaleźć w poniższej tabeli:

| Lokalizacja geograficzna magazynów danych docelowego | Region docelowego magazynu danych | Region, używane do przenoszenia danych |
|:--- |:--- |:--- |
| Stany Zjednoczone | East US | East US |
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
| Zjednoczone Królestwo | Zachodnie Zjednoczone Królestwo | Południowe Zjednoczone Królestwo |
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

Alternatywnie można jawnie wskazywały region usługi Data Factory, które ma być używany do wykonania kopii, określając `executionLocation` właściwości działania kopiowania `typeProperties`. Obsługiwane wartości dla tej właściwości są wymienione w powyżej **Region, używane do przenoszenia danych** kolumny. Należy zauważyć, że Twoje dane przechodzi przez ten region przewodowo podczas kopiowania. Na przykład, aby kopiowanie między platformy Azure są przechowywane w Korei, możesz określić `"executionLocation": "Japan East"` w przypadku kierowania za pośrednictwem regionie Japonia (zobacz [przykładowy JSON](#by-using-json-scripts) jako odwołanie).

> [!NOTE]
> Jeśli region docelowego magazynu danych nie ma na poprzedniej liście lub wykryć, domyślnie niepowodzenia działania kopiowania zamiast przechodzić przez inny region, chyba że `executionLocation` jest określony. Lista obsługiwanych regionów zostaną rozwinięte wraz z upływem czasu.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopiowanie danych między lokalnym magazynem danych i magazynem danych w chmurze
Gdy dane są kopiowane między lokalną (lub IaaS/maszyn wirtualnych platformy Azure) i w chmurze magazynów, [bramy zarządzania danymi](data-factory-data-management-gateway.md) wykonuje operację przenoszenia danych na komputerze lokalnym lub maszynie wirtualnej. Nie przepływu danych za pośrednictwem usługi w chmurze, chyba że używasz [kopiowania etapowego](data-factory-copy-activity-performance.md#staged-copy) możliwości. W tym przypadku dane przepływają przez przemieszczania usługi Azure Blob storage zanim zostaną zapisane do magazynu danych ujścia.

## <a name="create-a-pipeline-with-copy-activity"></a>Tworzenie potoku za pomocą działania kopiowania
Utworzysz potok za pomocą działania kopiowania na kilka sposobów:

### <a name="by-using-the-copy-wizard"></a>Za pomocą Kreatora kopiowania
Kreator kopiowania usługi Data Factory pomaga utworzyć potok za pomocą działania kopiowania. Ten potok umożliwia kopiowanie danych z obsługiwanych źródeł do miejsc docelowych *bez konieczności pisania JSON* definicje usługi połączone, zestawy danych i potoki. Zobacz [kreatora kopiowania usługi Data Factory](data-factory-copy-wizard.md) szczegółowe informacje na temat kreatora.  

### <a name="by-using-json-scripts"></a>Za pomocą skryptów JSON
Edytor usługi Data Factory w programie Visual Studio lub programu Azure PowerShell umożliwia tworzenie definicji JSON potok (za pomocą działania kopiowania). Można następnie wdrożyć go, aby utworzyć potok w fabryce danych. Zobacz [samouczka: Użyj działania kopiowania w potoku usługi Azure Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) samouczek z instrukcjami krok po kroku.    

Właściwości kodu JSON (takie jak nazwa, opis, dane wejściowe i wyjściowe tabel i zasad) są dostępne dla wszystkich typów działań. Właściwości, które są dostępne w `typeProperties` różnią się w sekcji działania za pomocą poszczególnych typów działań.

Działanie kopiowania `typeProperties` sekcji w zależności od typów źródeł i sink. Kliknij źródło/ujście w [obsługiwane źródłami i ujściami](#supported-data-stores-and-formats) sekcji, aby dowiedzieć się więcej na temat właściwości typu, obsługiwanych przez działanie kopiowania do tego magazynu danych.

Oto przykładowa definicja JSON:

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
Harmonogram, który jest zdefiniowany w wyjściowy zestaw danych określa podczas wykonywania działania (na przykład: **codzienne**, częstotliwości **dzień**i interwał jako **1**). Działanie kopiuje dane z wejściowego zestawu danych (**źródła**) do wyjściowego zestawu danych (**ujścia**).

Można określić więcej niż jeden wejściowy zestaw danych z działaniem kopiowania. Są one używane do sprawdzenia zależności, zanim działanie jest uruchamiane. Jednak tylko dane z pierwszego zestawu danych jest kopiowany do docelowego zestawu danych. Aby uzyskać więcej informacji, zobacz [planowanie i wykonywanie](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Zobacz [dostrajania przewodnik dotyczący wydajności działania kopiowania i](data-factory-copy-activity-performance.md), która opisuje kluczowe czynniki wpływające na wydajność przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory. Ponadto Wyświetla zaobserwowanego podczas testowania wewnętrznego i w tym artykule omówiono różne sposoby, aby zoptymalizować wydajność działania kopiowania.

## <a name="fault-tolerance"></a>Odporność na uszkodzenia
Domyślnie, działanie kopiowania zostanie zatrzymane, skopiowanie danych i zwrócenie błędu kiedy wystąpi niezgodne dane między źródła i ujścia; Mimo że jawnie możesz skonfigurować na tymczasowe pominięcie i rejestrowanie niezgodnych wierszy i tylko kopii tych danych zgodne, aby wykonać kopię zakończyło się pomyślnie. Zobacz [działania kopiowania odporności na uszkodzenia](data-factory-copy-activity-fault-tolerance.md) na więcej szczegółów.

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa
Zobacz [zagadnienia dotyczące zabezpieczeń](data-factory-data-movement-security-considerations.md), która opisuje infrastruktura zabezpieczeń, używanego przez usługi przenoszenia danych w usłudze Azure Data Factory do zabezpieczenia danych.

## <a name="scheduling-and-sequential-copy"></a>Kopiuj planowania i sekwencyjnych
Zobacz [planowanie i wykonywanie](data-factory-scheduling-and-execution.md) Aby uzyskać szczegółowe informacje dotyczące sposobu planowania i wykonywania działania w usłudze Data Factory. Istnieje możliwość uruchomić wiele operacji kopiowania, jedna po drugiej w sposób sekwencyjny/uporządkowane. Zobacz [skopiuj sekwencyjnie](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) sekcji.

## <a name="type-conversions"></a>Konwersje typów
Różnych magazynach danych ma inny typ natywny systemów. Działanie kopiowania wykonuje operację automatyczne konwersje z typów źródła do ujścia typy za pomocą następujących podejście dwuetapowe:

1. Przekonwertować na typ architektury .NET typy natywne źródła.
2. Przekonwertowanie z .NET do typu macierzystego ujścia.

Mapowanie z typu macierzystego systemu z typem platformy .NET dla magazynu danych jest w artykule magazynu odpowiednich danych. (Kliknij link określonych w tabeli obsługiwane magazyny danych). Te mapowania można użyć, aby określić odpowiednie typy podczas tworzenia tabel, tak aby działanie kopiowania wykonuje operację odpowiednie konwersje.

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej o działaniu kopiowania więcej, zobacz [kopiowanie danych z usługi Azure Blob storage do usługi Azure SQL Database](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Aby uzyskać informacje dotyczące przenoszenia danych z lokalnego magazynu danych do magazynu danych w chmurze, zobacz [przenieść dane z lokalnych magazynów danych w chmurze](data-factory-move-data-between-onprem-and-cloud.md).
