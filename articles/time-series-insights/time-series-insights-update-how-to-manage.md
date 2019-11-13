---
title: Udostępnianie środowiska w wersji zapoznawczej i zarządzanie nim — Azure Time Series | Microsoft Docs
description: Dowiedz się, jak inicjować obsługę środowiska Azure Time Series Insights w wersji zapoznawczej i zarządzać nim.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/23/2019
ms.custom: seodec18
ms.openlocfilehash: f66925c20f6767286eb98a7f5fab180845da4c33
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014798"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Udostępnianie wersji zapoznawczej Azure Time Series Insights i zarządzanie nią

W tym artykule opisano sposób tworzenia środowiska Azure Time Series Insights w wersji zapoznawczej i zarządzania nim przy użyciu [Azure Portal](https://portal.azure.com/).

## <a name="overview"></a>Omówienie

Środowiska Azure Time Series Insights w wersji zapoznawczej to środowiska z opcją płatność zgodnie z rzeczywistym użyciem.

Po udostępnieniu środowiska Azure Time Series Insights w wersji zapoznawczej można utworzyć następujące zasoby platformy Azure:

* Środowisko wersji zapoznawczej Azure Time Series Insights  
* Konto usługi Azure Storage ogólnego przeznaczenia w wersji 1
* Opcjonalny magazyn ciepły dla szybszych i nieograniczonych zapytań
  
Dowiedz się [, jak zaplanować środowisko](./time-series-insights-update-plan.md).

Skojarz każde środowisko Azure Time Series Insights w wersji zapoznawczej ze źródłem zdarzenia. Aby uzyskać więcej informacji, przeczytaj temat [Dodawanie źródła centrum zdarzeń](./time-series-insights-how-to-add-an-event-source-eventhub.md) i [Dodawanie źródła Centrum IoT](./time-series-insights-how-to-add-an-event-source-iothub.md). W tym kroku podajesz Właściwość identyfikatora znacznika czasu i unikatową grupę odbiorców. Dzięki temu środowisko będzie miało dostęp do odpowiednich zdarzeń.

> [!NOTE]
> Poprzedni krok jest opcjonalny w przepływie pracy aprowizacji podczas tworzenia środowiska w wersji zapoznawczej szeregów czasowych. Należy jednak dołączyć Źródło zdarzenia do środowiska, aby dane mogły rozpocząć przepływ w tym środowisku.

Po zakończeniu aprowizacji można zmodyfikować zasady dostępu i inne atrybuty środowiska zgodnie z wymaganiami firmy.

## <a name="create-the-environment"></a>Tworzenie środowiska

Aby utworzyć środowisko Azure Time Series Insights w wersji zapoznawczej:

1. W menu **SKU** wybierz przycisk **PAYG** . Podaj nazwę środowiska i wybierz grupę subskrypcji i grupę zasobów do użycia. Następnie wybierz obsługiwaną lokalizację do hostowania środowiska.

   [![utworzyć wystąpienia Azure Time Series Insights.](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

1. Wprowadź identyfikator szeregów czasowych.

    >[!NOTE]
    > * Identyfikator szeregów czasowych uwzględnia wielkość liter i jest niezmienny. (Nie można jej zmienić po ustawieniu).
    > * Identyfikatory szeregów czasowych mogą składać się z maksymalnie trzech kluczy.
    > * Aby uzyskać więcej informacji na temat wybierania identyfikatora szeregów czasowych, przeczytaj temat [Wybieranie identyfikatora szeregów czasowych](./time-series-insights-update-how-to-id.md).

1. Utwórz konto usługi Azure Storage, wybierając nazwę konta magazynu i wyznaczając wybór replikacji. Spowoduje to automatyczne utworzenie konta ogólnego przeznaczenia w usłudze Azure Storage. Konto jest tworzone w tym samym regionie co wcześniej wybrane środowisko Azure Time Series Insights w wersji zapoznawczej.

    [![utworzyć konto usługi Azure Storage dla swojego wystąpienia](media/v2-update-manage/manage-five.png)](media/v2-update-manage/manage-five.png#lightbox)

1. **(Opcjonalnie)** Włącz sklep ciepły dla Twojego środowiska, jeśli chcesz szybciej i nieograniczoną liczbę zapytań dotyczących najnowszych danych w środowisku. Możesz również utworzyć lub usunąć sklep ciepły za pomocą opcji **konfiguracji magazynu** w okienku nawigacji po lewej stronie po utworzeniu środowiska Time Series Insights w wersji zapoznawczej.

1. **(Opcjonalnie)** Możesz teraz dodać Źródło zdarzenia. Alternatywnie możesz poczekać na zainicjowanie wystąpienia.

   * Time Series Insights obsługuje [platformę azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) i [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) jako opcje źródła zdarzeń. Chociaż można dodać tylko jedno źródło zdarzenia podczas tworzenia środowiska, można później dodać kolejne Źródło zdarzenia. Po dodaniu źródła zdarzenia możesz wybrać istniejącą grupę odbiorców lub utworzyć nową grupę odbiorców. Najlepiej utworzyć unikatową grupę odbiorców, aby upewnić się, że wszystkie zdarzenia są widoczne dla środowiska Azure Time Series Insights w wersji zapoznawczej.

   * Wybierz odpowiednią właściwość sygnatury czasowej. Domyślnie Azure Time Series Insights używa czasu w kolejce dla każdego źródła zdarzenia.

     > [!TIP]
     > Czas w kolejce może nie być najlepszym skonfigurowanym ustawieniem do użycia w scenariuszach zdarzeń wsadowych lub w historycznych scenariuszach przekazywania danych. W takich przypadkach należy zweryfikować decyzję o użyciu lub nie używać właściwości timestamp.

     [Karta źródła zdarzeń ![](media/v2-update-manage/manage-two.png)](media/v2-update-manage/manage-two.png#lightbox)

1. Upewnij się, że Twoje środowisko zostało udostępnione przy użyciu żądanych ustawień.

    [![przegląd + tworzenie](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

## <a name="manage-the-environment"></a>Zarządzanie środowiskiem

Możesz zarządzać środowiskiem Azure Time Series Insights w wersji zapoznawczej przy użyciu Azure Portal. Gdy zarządzasz za pomocą Azure Portal, zobaczysz kilka najważniejszych różnic między w środowisku z opcją płatność zgodnie z rzeczywistym użyciem Azure Time Series Insights wersji zapoznawczej i ogólnie dostępnymi środowiskami S1 lub S2:

* Blok **przegląd** Azure Portal jest niezmieniony w Azure Time Series Insights, z wyjątkiem następujących sposobów:
  * Pojemność jest usuwana, ponieważ nie dotyczy środowisk z opcją płatność zgodnie z rzeczywistym użyciem.
  * Zostanie dodana Właściwość identyfikatora szeregów czasowych. Określa sposób partycjonowania danych.
  * Zestawy danych referencyjnych są usuwane.
  * Wyświetlany adres URL kieruje użytkownika do [eksploratora Azure Time Series Insights w wersji zapoznawczej](./time-series-insights-update-explorer.md).
  * Nazwa konta usługi Azure Storage znajduje się na liście.

* Blok **konfigurowania** Azure Portal zostanie usunięty w wersji zapoznawczej programu Azure Time Series Insights, ponieważ nie można konfigurować środowisk PAYG. Można jednak użyć **konfiguracji magazynu** w celu skonfigurowania nowo wprowadzonego magazynu w postaci ciepłej.

* Blok **danych referencyjnych** Azure Portal jest usuwany w programie Azure Time Series Insights Preview, ponieważ dane referencyjne nie należą do środowisk z opcją płatność zgodnie z rzeczywistym użyciem.

[![Time Series Insights środowiska w wersji zapoznawczej w Azure Portal](media/v2-update-manage/manage-four.png)](media/v2-update-manage/manage-four.png#lightbox)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej na temat Time Series Insights ogólnie dostępnych środowisk i wersji zapoznawczych, odczytując [Plan środowiska](./time-series-insights-update-plan.md).

- Dowiedz się, jak [dodać Źródło centrum zdarzeń](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Skonfiguruj [Źródło Centrum IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).
