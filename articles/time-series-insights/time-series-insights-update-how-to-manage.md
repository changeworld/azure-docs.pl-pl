---
title: Aprowizuj środowisko w wersji Zapoznawczej i zarządzanie nim — usługi Azure Time Series | Dokumenty firmy Microsoft
description: Dowiedz się, jak aprowizować środowisko usługi Azure Time Series Insights Preview i zarządzać nimi.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 1ec0d9c7ecf16c60c32abdf08b358268f460edb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087220"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Aprowizuj usługę Azure Time Series Preview i zarządzanie nią

W tym artykule opisano sposób tworzenia środowiska usługi Azure Time Series Preview przy użyciu [witryny Azure portal](https://portal.azure.com/)i zarządzania nim.

## <a name="overview"></a>Omówienie

Środowiska usługi Azure Time Series Insights Preview są środowiskami *płatności zgodnie z rzeczywistymi* płatnościami (PAYG).

Podczas inicjowania obsługi administracyjnej środowiska usługi Azure Time Series Preview, należy utworzyć następujące zasoby platformy Azure:

* Środowisko usługi Azure Time Series Insights w wersji zapoznawczej  
* Konto usługi Azure Storage w wersji 1 ogólnego przeznaczenia
* Opcjonalny ciepły magazyn dla szybszych i nieograniczonych zapytań

> [!TIP]
> * Dowiedz [się, jak zaplanować środowisko](./time-series-insights-update-plan.md).
> * Przeczytaj o [dodaniu źródła centrum zdarzeń](./time-series-insights-how-to-add-an-event-source-eventhub.md) lub [dodaniu źródła centrum IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).

Poznasz następujące czynności:

1. **(Opcjonalnie)** Skojarz każde środowisko usługi Azure Time Series Insights Preview ze źródłem zdarzeń. Należy również podać timestamp ID właściwości i unikatową grupę odbiorców, aby upewnić się, że środowisko ma dostęp do odpowiednich zdarzeń.

   > [!NOTE]
   > Poprzedni krok jest opcjonalny podczas inicjowania obsługi administracyjnej środowiska. Jeśli pominiesz ten krok, należy dołączyć źródło zdarzeń do środowiska później, aby można było uzyskać dostęp do danych w środowisku.

1. Po zakończeniu inicjowania obsługi administracyjnej można zmodyfikować zasady dostępu i inne atrybuty środowiska, aby dostosować je do wymagań biznesowych.

## <a name="create-the-environment"></a>Tworzenie środowiska

Aby utworzyć środowisko usługi Azure Time Series Insights Preview:

1. Wybierz **PAYG** jako **warstwę**. Podaj nazwę środowiska i wybierz grupę subskrypcji i grupę zasobów do użycia. Następnie wybierz obsługiwaną lokalizację do hostowania środowiska.

   [![Utwórz wystąpienie usługi Azure Time Series Insights.](media/v2-update-manage/create-and-manage-configuration.png)](media/v2-update-manage/create-and-manage-configuration.png#lightbox)

1. Wprowadź identyfikator szeregów czasowych.

    > [!NOTE]
    > * W identyfikatorze szeregów czasowych *rozróżniana wielkość liter* i *niezmienna .* (Nie można go zmienić po ustawieniu).
    > * Identyfikatory szeregów czasowych mogą mieć maksymalnie *trzy* klucze.
    > * Dowiedz się więcej o [wyborze identyfikatora szeregów czasowych](time-series-insights-update-how-to-id.md)

1. Utwórz konto usługi Azure Storage, wybierając nazwę konta magazynu i wyznaczając wybór replikacji. W ten sposób automatycznie tworzy konto ogólnego przeznaczenia usługi Azure Storage w wersji 1. Konto jest tworzone w tym samym regionie co środowisko usługi Azure Time Series Insights Preview, które zostało wcześniej wybrane.

    [![Konfiguracja chłodni](media/v2-update-manage/create-and-manage-cold-store.png)](media/v2-update-manage/create-and-manage-cold-store.png#lightbox)

1. **(Opcjonalnie)** Włącz ciepły magazyn dla swojego środowiska, jeśli chcesz szybsze i nieograniczone zapytania dotyczące najnowszych danych w danym środowisku. Po utworzeniu środowiska podglądu usługi Time Series Insights można również utworzyć lub usunąć ciepły magazyn za pomocą opcji **Konfiguracja magazynu** w lewym okienku nawigacji.

    [![Konfiguracja ciepłej pamięci masowej](media/v2-update-manage/create-and-manage-warm-storage.png)](media/v2-update-manage/create-and-manage-warm-storage.png#lightbox)

1. **(Opcjonalnie)** Możesz teraz dodać źródło zdarzeń. Można również poczekać, aż po wystąpieniu została aprowizowana.

   * Usługa Time Series Insights obsługuje [usługi Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) i usługi Azure Event [Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) jako opcje źródła zdarzeń. Chociaż podczas tworzenia środowiska można dodać tylko jedno źródło zdarzeń, można dodać później inne źródło zdarzeń. 
   
     Można wybrać istniejącą grupę odbiorców lub utworzyć nową grupę odbiorców po dodaniu źródła zdarzeń. Najlepiej utworzyć unikatową grupę odbiorców, aby upewnić się, że wszystkie zdarzenia są widoczne dla środowiska usługi Azure Time Series Insights Preview.

   * Wybierz odpowiednią właściwość sygnatury czasowej. Domyślnie usługa Azure Time Series Insights używa czasu w kolejce wiadomości dla każdego źródła zdarzeń.

     > [!TIP]
     > Czas w kolejce wiadomości może nie być najlepiej skonfigurowanym ustawieniem do użycia w scenariuszach zdarzeń wsadowych lub scenariuszach przekazywania danych historycznych. W takich przypadkach należy zweryfikować decyzję o użyciu lub nie używać właściwości sygnatury czasowej.

     [![Karta Konfiguracja źródła zdarzeń](media/v2-update-manage/create-and-manage-event-source.png)](media/v2-update-manage/create-and-manage-event-source.png#lightbox)

1. Upewnij się, że środowisko zostało aprowied i skonfigurowane w odpowiedni sposób.

    [![Karta Recenzja + Tworzenie](media/v2-update-manage/create-and-manage-review-and-confirm.png)](media/v2-update-manage/create-and-manage-review-and-confirm.png#lightbox)

## <a name="manage-the-environment"></a>Zarządzanie środowiskiem

Możesz zarządzać środowiskiem usługi Azure Time Series Insights Preview przy użyciu witryny Azure portal. Istnieje kilka kluczowych różnic między środowiskiem payg Azure Time Series Insights Preview i ogólnie dostępnych środowisk S1 lub S2, o których należy pamiętać podczas zarządzania środowiskiem za pośrednictwem witryny Azure portal:

* W bloku **Przegląd usługi** Azure portal preview wprowadzono następujące zmiany:

  * Pojemność jest usuwana, ponieważ nie ma zastosowania do środowisk PAYG.
  * Zostanie dodana właściwość **Identyfikator szeregów czasowych.** Określa sposób partycjonowania danych.
  * Zestawy danych referencyjnych są usuwane.
  * Wyświetlany adres URL kieruje cię do [Eksploratora usługi Azure Time Series Insights Preview](./time-series-insights-update-explorer.md).
  * Nazwa konta usługi Azure Storage jest wyświetlana.

* Bloku **Konfiguruj witrynę** portalu Azure jest usuwany w usłudze Azure Time Series Insights Preview, ponieważ środowiska PAYG nie są konfigurowalne. Można jednak użyć **konfiguracji magazynu,** aby skonfigurować nowo wprowadzony magazyn ciepła.

* Blok **danych referencyjnych** portalu Azure jest usuwany w usłudze Azure Time Series Insights Preview, ponieważ dane referencyjne nie są częścią środowisk PAYG.

[![Środowisko usługi Time Series Insights Preview w witrynie Azure portal](media/v2-update-manage/create-and-manage-overview-confirm.png)](media/v2-update-manage/create-and-manage-overview-confirm.png#lightbox)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o ogólnie dostępnych środowiskach i środowiskach podglądu w usłudze Time Series Insights, czytając [zaplanuj swoje środowisko.](./time-series-insights-update-plan.md)

- Dowiedz się, jak [dodać źródło centrum zdarzeń](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Konfigurowanie [źródła centrum IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).
