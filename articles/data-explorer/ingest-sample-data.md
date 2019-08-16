---
title: Pozyskiwanie przykładowych danych do usługi Azure Eksplorator danych
description: Dowiedz się, jak pozyskiwanie (ładowanie) przykładowych danych związanych z Pogoda do usługi Azure Eksplorator danych.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 08/12/2019
ms.openlocfilehash: c803de599f6be98512b15e927c6d15f1c7d95ff1
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2019
ms.locfileid: "69515739"
---
# <a name="quickstart-ingest-sample-data-into-azure-data-explorer"></a>Szybki start: Pozyskiwanie przykładowych danych do usługi Azure Eksplorator danych

W tym artykule przedstawiono sposób pozyskiwania (ładowania) przykładowych danych do bazy danych Eksplorator danych platformy Azure. Istnieje [kilka sposobów](ingest-data-overview.md)pozyskiwania danych; Ten artykuł koncentruje się na podstawowym podejściu, które jest odpowiednie do celów testowych.

> [!NOTE]
> Te dane są już dostępne w przypadku ukończenia pozyskiwania [danych przy użyciu biblioteki języka Python platformy Azure Eksplorator danych](python-ingest-data.md).

## <a name="prerequisites"></a>Wymagania wstępne

[Klaster testowy i baza danych](create-cluster-database-portal.md)

## <a name="ingest-data"></a>Pozyskiwanie danych

Przykładowy zestaw danych **StormEvents** zawiera dane dotyczące pogody pochodzące z organizacji [National Centers for Environmental Information](https://www.ncdc.noaa.gov/stormevents/).

1. Zaloguj się do witryny [https://dataexplorer.azure.com](https://dataexplorer.azure.com).

1. W lewym górnym rogu aplikacji wybierz pozycję **Dodaj klaster**.

1. W oknie dialogowym **Dodawanie klastra** wprowadź adres URL klastra w formularzu `https://<ClusterName>.<Region>.kusto.windows.net/`, a następnie wybierz pozycję **Dodaj**.

1. Wklej w poniższym poleceniu i wybierz polecenie **Uruchom**.

    ```Kusto
    .create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)

    .ingest into table StormEvents h'https://kustosamplefiles.blob.core.windows.net/samplefiles/StormEvents.csv?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D' with (ignoreFirstRecord=true)
    ```

1. Po zakończeniu pozyskiwania Wklej w następującym zapytaniu, wybierz zapytanie w oknie i wybierz polecenie **Uruchom**.

    ```Kusto
    StormEvents
    | sort by StartTime desc
    | take 10
    ```
    Zapytanie zwraca następujące wyniki z pozyskiwanych danych przykładowych.

    ![Wyniki zapytania](media/ingest-sample-data/query-results.png)

## <a name="next-steps"></a>Następne kroki

* [Usługa Azure Eksplorator danych](ingest-data-overview.md) pozyskiwanie danych, aby dowiedzieć się więcej o metodach pozyskiwania.
* [Szybki start: Wykonywanie zapytań dotyczących danych w](web-query-data.md) interfejsie użytkownika sieci Web usługi Azure Eksplorator danych.
* [Zapisuj zapytania](write-queries.md) w języku zapytań Kusto.
