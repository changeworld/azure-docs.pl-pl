---
title: Pozyskiwania przykładowych danych do Eksploratora danych platformy Azure
description: Dowiedz się, jak pozyskiwania (ładowania) dane próbki związane z pogodą do Usługi Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 08/12/2019
ms.openlocfilehash: 3ece5a9d225e48654a0a3a96c3b7b78327565841
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74975180"
---
# <a name="quickstart-ingest-sample-data-into-azure-data-explorer"></a>Szybki start: pozyskiwania przykładowych danych do Eksploratora danych platformy Azure

W tym artykule pokazano, jak pozyskiwania (ładowania) przykładowe dane do bazy danych usługi Azure Data Explorer. Istnieje [kilka sposobów pozyskiwania danych;](ingest-data-overview.md) w tym artykule koncentruje się na podstawowe podejście, które jest odpowiednie do celów testowych.

> [!NOTE]
> Te dane są już dostępne po zakończeniu [pozyskiwania danych przy użyciu biblioteki Usługi Azure Data Explorer w języku Python.](python-ingest-data.md)

## <a name="prerequisites"></a>Wymagania wstępne

[Klaster testowy i baza danych](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Pozyskiwanie danych

Przykładowy zestaw danych **StormEvents** zawiera dane związane z pogodą z [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

1. Zaloguj się [https://dataexplorer.azure.com](https://dataexplorer.azure.com)do .

1. W lewym górnym rogu aplikacji wybierz pozycję **Dodaj klaster**.

1. W oknie dialogowym **Dodawanie klastra** wprowadź `https://<ClusterName>.<Region>.kusto.windows.net/`adres URL klastra w formularzu , a następnie wybierz pozycję **Dodaj**.

1. Wklej w poniższym poleceniu i wybierz pozycję **Uruchom,** aby utworzyć tabelę StormEvents.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)
    ```
1. Wklej w poniższym poleceniu i wybierz pozycję **Uruchom,** aby pozyskiwania danych w tabeli StormEvents.

    ```Kusto
    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. Po zakończeniu pozyskiwania wklej w poniższej kwerendzie zaznacz zapytanie w oknie i wybierz pozycję **Uruchom**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    Kwerenda zwraca następujące wyniki z pochłoniętych danych przykładowych.

    ![Wyniki zapytania](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>Następne kroki

* [Pozyskiwania danych usługi Azure Data Explorer,](ingest-data-overview.md) aby dowiedzieć się więcej o metodach pozyskiwania.
* [Szybki start: zapytanie o dane w Eksploratorze danych platformy Azure](web-query-data.md) Interfejs użytkownika sieci Web.
* [Pisanie zapytań](write-queries.md) za pomocą języka zapytania Kusto.
