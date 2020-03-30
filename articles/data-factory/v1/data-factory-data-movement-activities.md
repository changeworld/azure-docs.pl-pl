---
title: Przenoszenie danych przy użyciu działania kopiowania
description: 'Dowiedz się więcej o przenoszeniu danych w potokach usługi Data Factory: migracja danych między magazynami w chmurze oraz między magazynem lokalnym a magazynem w chmurze. Użyj działania kopiowania.'
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.assetid: 67543a20-b7d5-4d19-8b5e-af4c1fd7bc75
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/05/2017
ms.author: jingwang
robots: noindex
ms.openlocfilehash: fbaa8c3544b35978786404619879f59ab91a6979
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79281888"
---
# <a name="move-data-by-using-copy-activity"></a>Przenoszenie danych przy użyciu działania kopiowania
> [!div class="op_single_selector" title1="Wybierz wersję używanej usługi Data Factory:"]
> * [Wersja 1](data-factory-data-movement-activities.md)
> * [Wersja 2 (bieżąca wersja)](../copy-activity-overview.md)

> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [Kopiowanie aktywności w wersji 2](../copy-activity-overview.md).

## <a name="overview"></a>Omówienie
W usłudze Azure Data Factory można użyć funkcji Kopiuj aktywność do kopiowania danych między magazynami danych w chmurze i w chmurze. Po skopiowaniu danych można je dalej przekształcać i analizować. Za pomocą funkcji Kopiuj działanie można również opublikować wyniki transformacji i analizy dla analizy biznesowej (BI) i zużycia aplikacji.

![Rola działania kopiowania](media/data-factory-data-movement-activities/copy-activity.png)

Działanie kopiowania jest obsługiwane przez bezpieczną, niezawodną, skalowalną i [globalnie dostępną usługę.](#global) Ten artykuł zawiera szczegółowe informacje na temat przenoszenia danych w fabryce danych i działania kopiowania.

Najpierw zobaczmy, jak następuje migracja danych między dwoma magazynami danych w chmurze oraz między lokalnym magazynem danych a magazynem danych w chmurze.

> [!NOTE]
> Aby dowiedzieć się więcej o działaniach w ogóle, zobacz [Opis potoków i działań](data-factory-create-pipelines.md).
>
>

### <a name="copy-data-between-two-cloud-data-stores"></a>Kopiowanie danych między dwoma magazynami danych w chmurze
Gdy magazyny danych źródłowych i ujścia znajdują się w chmurze, aktywność kopiowania przechodzi przez następujące etapy, aby skopiować dane ze źródła do ujścia. Usługa, która zasila działanie kopiowania:

1. Odczytuje dane z magazynu danych źródłowych.
2. Wykonuje serializację/deserializację, kompresję/dekompresję, mapowanie kolumn i konwersję typów. Wykonuje te operacje na podstawie konfiguracji wejściowego zestawu danych, wyjściowego zestawu danych i działania kopiowania.
3. Zapisuje dane do docelowego magazynu danych.

Usługa automatycznie wybiera optymalny region do wykonania przenoszenia danych. Ten region jest zwykle najbliżej magazynu danych ujścia.

![Kopiowanie z chmury do chmury](./media/data-factory-data-movement-activities/cloud-to-cloud.png)

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopiowanie danych między lokalnym magazynem danych a magazynem danych w chmurze
Aby bezpiecznie przenosić dane między lokalnym magazynem danych a magazynem danych w chmurze, zainstaluj bramę zarządzania danymi na komputerze lokalnym. Brama zarządzania danymi jest agentem, który umożliwia hybrydowe przenoszenie i przetwarzanie danych. Można go zainstalować na tym samym komputerze co sam magazyn danych lub na oddzielnym komputerze, który ma dostęp do magazynu danych.

W tym scenariuszu brama zarządzania danymi wykonuje serializacji/deserializacji, kompresji/dekompresji, mapowania kolumn i konwersji typu. Dane nie przepływają za pośrednictwem usługi Azure Data Factory. Zamiast tego brama zarządzania danymi bezpośrednio zapisuje dane do magazynu docelowego.

![Kopiowanie lokalnie do chmury](./media/data-factory-data-movement-activities/onprem-to-cloud.png)

Zobacz [Przenoszenie danych między lokalnymi i chmurowymi magazynami danych,](data-factory-move-data-between-onprem-and-cloud.md) aby uzyskać informacje na temat wprowadzenia i instruktażu. Zobacz [Brama zarządzania danymi, aby](data-factory-data-management-gateway.md) uzyskać szczegółowe informacje na temat tego agenta.

Można również przenieść dane z/do obsługiwanych magazynów danych, które są hostowane na maszynach wirtualnych usługi Azure IaaS przy użyciu bramy zarządzania danymi. W takim przypadku można zainstalować bramę zarządzania danymi na tej samej maszynie wirtualnej co sam magazyn danych lub na osobnej maszynie wirtualnej, która ma dostęp do magazynu danych.

## <a name="supported-data-stores-and-formats"></a>Obsługiwane magazyny i formaty danych
Działanie kopiowania w usłudze Data Factory kopiuje dane z magazynu danych źródła do magazynu danych ujścia. Usługa Data Factory obsługuje następujące magazyny danych. Dane z dowolnego źródła można zapisać do dowolnego ujścia. Kliknij magazyn danych, aby dowiedzieć się, jak kopiować dane do i z tego magazynu.

> [!NOTE] 
> Jeśli musisz przenieść dane do/z magazynu danych nieobsługiwanego przez działanie kopiowania, użyj **niestandardowego działania** w usłudze Data Factory z własną logiką do obsługi kopiowania/przenoszenia danych. Aby uzyskać szczegółowe informacje na temat tworzenia i używania niestandardowego działania, zobacz artykuł [Use custom activities in an Azure Data Factory pipeline](data-factory-use-custom-activities.md) (Korzystanie z niestandardowych działań w potoku usługi Azure Data Factory).

[!INCLUDE [data-factory-supported-data-stores](../../../includes/data-factory-supported-data-stores.md)]

> [!NOTE]
> Magazyny danych oznaczone znakiem * mogą być konfigurowane lokalnie lub w usłudze Azure IaaS i wymagają zainstalowania [bramy zarządzania danymi](data-factory-data-management-gateway.md) na maszynie lokalnej lub w usłudze Azure IaaS.

### <a name="supported-file-formats"></a>Obsługiwane formaty plików
Za pomocą funkcji Kopiuj aktywność można **kopiować pliki w stanie** przebywania między dwoma magazynami danych opartymi na plikach, można pominąć [sekcję formatu](data-factory-create-datasets.md) zarówno w definicjach wejściowych, jak i wyjściowych zestawów danych. Dane są kopiowane wydajnie bez serializacji/deserializacji.

Działanie kopiowania odczytuje również z plików i zapisuje je w określonych formatach: **Tekst, JSON, Avro, ORC i Parkiet**oraz kodek kompresji **GZip, Deflate, BZip2 i ZipDeflate.** Zobacz [Obsługiwane formaty plików i kompresji ze szczegółami.](data-factory-supported-file-and-compression-formats.md)

Na przykład można wykonać następujące działania kopiowania:

* Kopiowanie danych w lokalnym programie SQL Server i zapisywanie w usłudze Azure Data Lake Store w formacie ORC.
* Skopiuj pliki w formacie CSV z lokalnego systemu plików i zapisuj do obiektu Blob platformy Azure w formacie Avro.
* Skopiuj spakowane pliki z lokalnego systemu plików i rozpakować, a następnie wyląduj w magazynie usługi Azure Data Lake Store.
* Kopiowanie danych w formacie csv (Skompresowany tekst GZip) z usługi Azure Blob i zapis do usługi Azure SQL Database.

## <a name="globally-available-data-movement"></a><a name="global"></a>Globalnie dostępne przenoszenie danych
Usługa Azure Data Factory jest dostępna tylko w zachodnich stanach USA, wschodnich stanach USA i europie północnej. Jednak usługa, która zasila działanie kopiowania jest dostępna globalnie w następujących regionach i regionach geograficznych. Globalnie dostępna topologia zapewnia efektywne przenoszenie danych, które zwykle pozwala uniknąć przeskoków między regionami. Aby uzyskać informacje o dostępności fabryki danych i przenoszenia danych w regionie, zobacz [Usługi według regionów.](https://azure.microsoft.com/regions/#services)

### <a name="copy-data-between-cloud-data-stores"></a>Kopiowanie danych między magazynami danych w chmurze
Gdy magazyny danych źródłowych i magazynów ujścia znajdują się w chmurze, usługa Data Factory używa wdrożenia usługi w regionie, który znajduje się najbliżej ujścia w tej samej lokalizacji geograficznej, aby przenieść dane. Mapę można znaleźć w poniższej tabeli:

| Geografia docelowych magazynów danych | Region docelowego magazynu danych | Region używany do przenoszenia danych |
|:--- |:--- |:--- |
| Stany Zjednoczone | Wschodnie stany USA | Wschodnie stany USA |
| &nbsp; | Wschodnie stany USA 2 | Wschodnie stany USA 2 |
| &nbsp; | Środkowe stany USA | Środkowe stany USA |
| &nbsp; | Północno-środkowe stany USA | Północno-środkowe stany USA |
| &nbsp; | Południowo-środkowe stany USA | Południowo-środkowe stany USA |
| &nbsp; | Zachodnio-środkowe stany USA | Zachodnio-środkowe stany USA |
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

Alternatywnie można jawnie wskazać region usługi Data Factory, który ma być `executionLocation` używany do `typeProperties`wykonywania kopii, określając właściwość w obszarze Działanie kopiowania . Obsługiwane wartości dla tej właściwości są wymienione w powyższym regionie używane dla kolumny **przenoszenia danych.** Zanotuj, że dane przechodzą przez ten region za pośrednictwem sieci podczas kopiowania. Na przykład, aby skopiować między magazynami `"executionLocation": "Japan East"` platformy Azure w Korei, można określić, aby trasy przez region Japonii (zobacz [przykładowy JSON](#by-using-json-scripts) jako odwołanie).

> [!NOTE]
> Jeśli region docelowego magazynu danych nie znajduje się na poprzedniej liście lub niewykrywalny, domyślnie działanie `executionLocation` kopiowania kończy się niepowodzeniem zamiast przechodzić przez region alternatywny, chyba że zostanie określony. Lista obsługiwanych regionów zostanie rozszerzona w czasie.
>

### <a name="copy-data-between-an-on-premises-data-store-and-a-cloud-data-store"></a>Kopiowanie danych między lokalnym magazynem danych a magazynem danych w chmurze
Gdy dane są kopiowane między lokalnymi (lub maszynami wirtualnymi platformy Azure/IaaS) i magazynami w chmurze, [brama zarządzania danymi](data-factory-data-management-gateway.md) wykonuje przenoszenie danych na komputerze lokalnym lub maszynie wirtualnej. Dane nie przepływa przez usługę w chmurze, chyba że używasz możliwości [kopiowania etapowego.](data-factory-copy-activity-performance.md#staged-copy) W takim przypadku dane przepływa przez przemieszczania magazynu obiektów Blob platformy Azure, zanim zostanie zapisany w magazynie danych ujścia.

## <a name="create-a-pipeline-with-copy-activity"></a>Tworzenie potoku z działaniem kopiowania
Potok z funkcją Kopiowania można utworzyć na kilka sposobów:

### <a name="by-using-the-copy-wizard"></a>Za pomocą Kreatora kopiowania
Kreator kopiowania fabrycznego danych pomaga utworzyć potok z działaniem kopiowania. Ten potok umożliwia kopiowanie danych z obsługiwanych źródeł do miejsc docelowych bez pisania definicji *JSON* dla połączonych usług, zestawów danych i potoków. Szczegółowe informacje na temat kreatora można znaleźć w [Kreatorze kopiowania](data-factory-copy-wizard.md) w fabryce danych.  

### <a name="by-using-json-scripts"></a>Za pomocą skryptów JSON
Edytor fabryki danych w programie Visual Studio lub azure PowerShell służy do tworzenia definicji JSON dla potoku (przy użyciu działania kopiowania). Następnie można go wdrożyć, aby utworzyć potok w fabryce danych. Zobacz [samouczek: Użyj działania kopiowania w potoku usługi Azure Data Factory](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) dla samouczka z instrukcjami krok po kroku.    

Właściwości JSON (takie jak nazwa, opis, tabele danych wejściowych i wyjściowych oraz zasady) są dostępne dla wszystkich typów działań. Właściwości, które są `typeProperties` dostępne w sekcji działania różnią się w zależności od typu działania.

W przypadku działania `typeProperties` kopiowania sekcja różni się w zależności od typów źródeł i pochłaniacza. Kliknij źródło/ujście w sekcji [Obsługiwane źródła i pochłaniacze,](#supported-data-stores-and-formats) aby dowiedzieć się więcej o właściwościach typu obsługiwanych przez działanie kopiowania dla tego magazynu danych.

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
Harmonogram zdefiniowany w zestawie danych wyjściowych określa, kiedy działanie jest uruchamiane (na przykład: **dzienna**, częstotliwość jako **dzień**i interwał jako **1**). Działanie kopiuje dane z wejściowego zestawu danych **(źródła)** do wyjściowego zestawu danych **(sink).**

Można określić więcej niż jeden zestaw danych wejściowych do działania kopiowania. Są one używane do weryfikacji zależności przed uruchomieniem działania. Jednak tylko dane z pierwszego zestawu danych są kopiowane do docelowego zestawu danych. Aby uzyskać więcej informacji, zobacz [Planowanie i wykonywanie](data-factory-scheduling-and-execution.md).  

## <a name="performance-and-tuning"></a>Wydajności i dostosowywanie
Zobacz [przewodnik wydajności i dostrajania działania kopiowania,](data-factory-copy-activity-performance.md)który opisuje kluczowe czynniki wpływające na wydajność przenoszenia danych (działanie kopiowania) w usłudze Azure Data Factory. Zawiera również listę obserwowanych wydajności podczas testowania wewnętrznego i omówiono różne sposoby optymalizacji wydajności działania kopiowania.

## <a name="fault-tolerance"></a>Odporność na uszkodzenia
Domyślnie działanie kopiowania zatrzyma kopiowanie danych i zwracanie błędów w przypadku wystąpienia niezgodnych danych między źródłem a ujściem; podczas gdy można jawnie skonfigurować pominąć i rejestrować niezgodne wiersze i tylko skopiować te zgodne dane, aby kopia powiodła się. Zobacz odporność na [uszkodzenia działania kopiowania](data-factory-copy-activity-fault-tolerance.md) na więcej szczegółów.

## <a name="security-considerations"></a>Zagadnienia dotyczące bezpieczeństwa
Zobacz [zagadnienia dotyczące zabezpieczeń](data-factory-data-movement-security-considerations.md), który opisuje infrastrukturę zabezpieczeń, że usługi przenoszenia danych w usłudze Azure Data Factory używać do zabezpieczania danych.

## <a name="scheduling-and-sequential-copy"></a>Planowanie i kopiowanie sekwencyjne
Zobacz [Planowanie i wykonywanie](data-factory-scheduling-and-execution.md) szczegółowe informacje na temat sposobu planowania i wykonywania działa w fabryce danych. Istnieje możliwość uruchamiania wielu operacji kopiowania jeden po drugim w sposób sekwencyjny/uporządkowany. Zobacz sekcję [Kopiuj sekwencyjnie.](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline)

## <a name="type-conversions"></a>Konwersje typu
Różne magazyny danych mają różne systemy typu macierzystego. Działanie kopiowania wykonuje automatyczne konwersje typów z typów źródłowych do typów ujścia z następującym podejściem dwuetapowym:

1. Konwertuj z natywnych typów źródeł na typ .NET.
2. Konwertuj z typu .NET na typ ujścia macierzystego.

Mapowanie z systemu typu macierzystego do typu .NET dla magazynu danych znajduje się w odpowiednim artykule magazynu danych. (Kliknij określone łącze w tabeli Obsługiwane magazyny danych). Za pomocą tych mapowań można określić odpowiednie typy podczas tworzenia tabel, dzięki czemu działanie kopiowania wykonuje odpowiednie konwersje.

## <a name="next-steps"></a>Następne kroki
* Aby dowiedzieć się więcej o działaniu kopiowania, zobacz [Kopiowanie danych z magazynu obiektów Blob platformy Azure do bazy danych SQL Azure](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
* Aby dowiedzieć się więcej o przenoszeniu danych z lokalnego magazynu danych do magazynu danych w chmurze, zobacz [Przenoszenie danych z lokalnego do magazynu danych w chmurze.](data-factory-move-data-between-onprem-and-cloud.md)
