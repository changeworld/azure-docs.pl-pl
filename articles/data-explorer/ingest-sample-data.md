---
title: Pozyskiwanie danych przykładowych do Eksploratora danych usługi Azure
description: Dowiedz się więcej o sposobie pozyskiwania (załaduj) dotyczących pogody przykładowych danych do Eksploratora danych usługi Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: e80322cda671e2145cf3e65aa1457f1fa1827737
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2019
ms.locfileid: "59050632"
---
# <a name="ingest-sample-data-into-azure-data-explorer"></a>Pozyskiwanie danych przykładowych do Eksploratora danych usługi Azure

W tym artykule pokazano, jak pozyskiwania (załaduj) przykładowe dane w bazie danych programu Eksploratora danych usługi Azure. Istnieją [pozyskiwania danych na kilka sposobów](ingest-data-overview.md); ten artykuł koncentruje się na podejściu podstawowego, który jest odpowiedni do celów testowych.

> [!NOTE]
> Masz już dane Jeśli ukończono [Szybki Start: Pozyskiwanie danych przy użyciu biblioteki języka Python w Eksploratorze danych Azure](python-ingest-data.md).

## <a name="prerequisites"></a>Wymagania wstępne

[Klaster testowy i baza danych](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Pozyskiwanie danych

Przykładowy zestaw danych **StormEvents** zawiera dane dotyczące pogody pochodzące z organizacji [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

1. Zaloguj się do witryny [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. W lewym górnym rogu aplikacji wybierz pozycję **Dodaj klaster**.

1. W **klastra Dodaj** okna dialogowego wprowadź adres URL klastra w postaci `https://<ClusterName>.<Region>.kusto.windows.net/`, a następnie wybierz **Dodaj**.

1. Wklej poniższe polecenie, a następnie wybierz **Uruchom**.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)

    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. Po zakończeniu wprowadzania, wklej poniższe zapytanie, wybierz zapytanie w oknie, a następnie wybierz **Uruchom**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    Zapytanie zwraca następujące wyniki z dwóch przykładowych danych.

    ![Wyniki zapytania](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Szybki start: wykonywanie zapytań o dane w usłudze Azure Data Explorer](web-query-data.md)

> [!div class="nextstepaction"]
> [Pisanie zapytań](write-queries.md)

> [!div class="nextstepaction"]
> [Pozyskiwanie danych w usłudze Azure Eksplorator danych](ingest-data-overview.md)