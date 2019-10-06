---
title: Aktywność przepływu danych w Azure Data Factory | Microsoft Docs
description: Jak wykonywać przepływy danych z wnętrza potoku usługi Fabryka danych.
services: data-factory
documentationcenter: ''
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 10/07/2019
ms.author: makromer
ms.openlocfilehash: 7db410e97046b6d251eb73e754e40eab09a2ee64
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981799"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Aktywność przepływu danych w Azure Data Factory

Użyj działania przepływu danych do przekształcania i przenoszenia danych za pośrednictwem mapowania przepływów danych. Jeśli dopiero zaczynasz przepływy danych, zobacz [Mapowanie przepływu danych — omówienie](concepts-data-flow-overview.md)

[!INCLUDE [notes](../../includes/data-factory-data-flow-preview.md)]

## <a name="syntax"></a>Składnia

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Właściwości typu

Właściwość | Opis | Dozwolone wartości | Wymagane
-------- | ----------- | -------------- | --------
przepływu danych | Odwołanie do przepływu danych, który jest wykonywany | DataFlowReference | Tak
integrationRuntime | Środowisko obliczeniowe, w którym działa przepływ danych | IntegrationRuntimeReference | Tak
przemieszczanie. linkedService | Jeśli używasz źródła lub ujścia usługi SQL DW, konto magazynu używane na potrzeby przemieszczania podstawowego | LinkedServiceReference | Tylko wtedy, gdy przepływ danych odczytuje lub zapisuje dane do magazynu SQL
przemieszczanie. folderPath | Jeśli używasz źródła lub ujścia usługi SQL DW, ścieżka folderu w ramach konta usługi BLOB Storage używanego do przemieszczania podstawowego | String | Tylko wtedy, gdy przepływ danych odczytuje lub zapisuje dane do magazynu SQL

(media/data-flow/activity-data-flow.png "Przepływ danych") wykonywania ![przepływu danych]

### <a name="data-flow-integration-runtime"></a>Środowisko Integration Runtime

Wybierz Integration Runtime, które mają być używane do wykonywania działań przepływu danych. Domyślnie Data Factory będzie używać rozwiązania Azure Integration Runtime z czterema rdzeniami procesów roboczych i bez czasu wygaśnięcia (TTL). Ten IR ma typ obliczeń ogólnego przeznaczenia i działa w tym samym regionie, w którym znajduje się fabryka. Możesz tworzyć własne środowiska Azure Integration Runtime, które definiują określone regiony, typ obliczeń, liczniki rdzeni i czas wygaśnięcia dla wykonywania działania przepływu danych.

W przypadku wykonań potoku klaster jest klastrem zadań, co potrwa kilka minut, zanim uruchomienie zostanie rozpoczęte. Jeśli nie określono czasu wygaśnięcia, ten czas uruchamiania jest wymagany dla każdego uruchomienia potoku. W przypadku określenia czasu wygaśnięcia (TTL) w czasie określonym po ostatnim wykonaniu zostanie uaktywniona bezczynna Pula klastra, co spowoduje skrócenie czasu uruchomienia. Jeśli na przykład czas wygaśnięcia wynosi 60 minut i uruchomisz na nim przepływ danych raz na godzinę, Pula klastrów pozostanie aktywna. Aby uzyskać więcej informacji, zobacz [Azure Integration Runtime](concepts-integration-runtime.md).

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!NOTE]
> Integration Runtime wybór w działaniu przepływu danych dotyczy tylko *wyzwolonych wykonań* potoku. Debugowanie potoku za pomocą przepływów danych przebiega w klastrze określonym w sesji debugowania.

### <a name="polybase"></a>PolyBase

Jeśli używasz Azure SQL Data Warehouse jako ujścia lub źródło, musisz wybrać lokalizację przejściową dla obciążenia wsadowego z podstawową bazą danych. Baza danych umożliwia zbiorcze ładowanie wsadowe, zamiast ładować dane wiersz po wierszu. Podstawa znacznie zmniejsza czas ładowania do magazynu danych SQL.

## <a name="parameterizing-data-flows"></a>Parametryzacja przepływy danych

### <a name="parameterized-datasets"></a>Sparametryzowane zestawy danych

Jeśli przepływ danych używa sparametryzowane zestawy danych, ustaw wartości parametrów na karcie **Ustawienia** .

(media/data-flow/params.png "Parametry") parametrów ![przepływu danych]

### <a name="parameterized-data-flows"></a>Sparametryzowane przepływy danych

Jeśli przepływ danych jest sparametryzowane, ustaw wartości dynamiczne parametrów przepływu danych na karcie **Parametry** . Do przypisywania wartości parametrów dynamicznych lub literalnych można użyć języka wyrażeń potoku ADF (tylko dla typów ciągów) lub języka wyrażeń przepływu danych. Aby uzyskać więcej informacji, zobacz [parametry przepływu danych](parameters-data-flow.md).

Przykład przykładowego parametru ![przepływu danych](media/data-flow/parameter-example.png "")

## <a name="pipeline-debug-of-data-flow-activity"></a>Debugowanie potoku działania przepływu danych

Aby wykonać uruchomienie potoku debugowania z działaniem przepływu danych, należy przełączyć tryb debugowania przepływu danych za pomocą suwaka **debugowania przepływu** danych na górnym pasku. Tryb debugowania umożliwia uruchomienie przepływu danych na aktywnym klastrze Spark. Aby uzyskać więcej informacji, zobacz [tryb debugowania](concepts-data-flow-debug-mode.md).

Przycisk ![debugowania przycisku](media/data-flow/debugbutton.png "Debuguj")

Potok debugowania działa w odniesieniu do aktywnego klastra debugowania, a nie środowiska Integration Runtime określonego w ustawieniach działania przepływu danych. Podczas uruchamiania trybu debugowania można wybrać środowisko obliczeniowe debugowania.

## <a name="monitoring-the-data-flow-activity"></a>Monitorowanie działania przepływu danych

Działanie przepływu danych ma specjalne środowisko monitorowania, w którym można wyświetlać partycjonowanie, czas etapów i informacje o pomieszczeniu danych. Otwórz okienko monitorowanie za pomocą ikony okularów w obszarze **Akcje**. Aby uzyskać więcej informacji, zobacz [monitorowanie przepływów danych](concepts-data-flow-monitoring.md).

## <a name="next-steps"></a>Następne kroki

Zobacz aktywność przepływu sterowania obsługiwaną przez Data Factory: 

- [Działanie if Condition](control-flow-if-condition-activity.md)
- [Działanie wykonywania potoku](control-flow-execute-pipeline-activity.md)
- [Dla każdego działania](control-flow-for-each-activity.md)
- [Działanie pobierania metadanych](control-flow-get-metadata-activity.md)
- [Działanie Lookup](control-flow-lookup-activity.md)
- [Aktywność sieci Web](control-flow-web-activity.md)
- [Do działania do](control-flow-until-activity.md)
