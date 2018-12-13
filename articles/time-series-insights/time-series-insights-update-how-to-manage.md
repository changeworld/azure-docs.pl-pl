---
title: Zarządzanie platformą Azure (wersja zapoznawcza) dla serii czasu — jak aprowizować i zarządzania platformy Azure serii czasu z wersji zapoznawczej. | Microsoft Docs
description: Zrozumienie, jak aprowizować i zarządzać nimi Azure czas Series Insights w wersji zapoznawczej.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: 1834a59ff675f109267a406e3c2b03411e3bb50b
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/12/2018
ms.locfileid: "53273520"
---
# <a name="how-to-provision-and-manage-azure-time-series-insights-preview"></a>Jak aprowizować i zarządzać nimi Azure czas Series Insights w wersji zapoznawczej

W tym dokumencie opisano sposób tworzenia i zarządzania nimi w środowisku Azure czas Series Insights w wersji zapoznawczej za pomocą [witryny Azure portal](https://portal.azure.com/).

## <a name="overview"></a>Przegląd

Poniżej przedstawiono krótkie omówienie sposobu aprowizowanie środowiska Azure czas Series Insights w wersji zapoznawczej:

* Środowiska czasu Series Insights w wersji zapoznawczej platformy Azure są **PAYG** środowisk.
  * W ramach procesu tworzenia będzie konieczne jest zapewnienie serii czasu identyfikatora. Identyfikatory serii czasu może być maksymalnie trzy klucze. Dowiedz się więcej o wybieraniu Identyfikatora serii czasu, czytając [jak wybrać identyfikator serii czasu](./time-series-insights-update-how-to-id.md).
  * Podczas aprowizacji środowiska Azure czas Series Insights w wersji zapoznawczej, jest utworzenie dwóch zasobów platformy Azure:

    * Środowisko Azure czas Series Insights w wersji zapoznawczej  
    * Konto usługi Azure Storage ogólnego przeznaczenia w wersji 1
  
    Dowiedz się, [jak Planowanie środowiska](./time-series-insights-update-plan.md).

    >[!IMPORTANT]
    > Dla klientów korzystających z kont w wersji 2 nie należy włączać właściwości zimnego/archiwizowanie na koncie magazynu, który będzie używany.

* Każde środowisko Azure czas Series Insights w wersji zapoznawczej można (opcjonalnie) skojarzone z źródłem zdarzeń. Odczyt [sposób dodawania źródła zdarzeń Centrum](./time-series-insights-how-to-add-an-event-source-eventhub.md) i [jak dodawanie źródła usługi IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) Aby uzyskać więcej informacji.
  * W tym kroku należy podać właściwość Identyfikatora sygnatury czasowej i grupy unikatowych odbiorców. Daje to gwarancję, że środowisko ma dostęp do odpowiedniego zdarzenia.

* Po ukończeniu inicjowania obsługi administracyjnej użytkownik może modyfikować zasady dostępu i inne atrybuty środowiska do zestawu wymagań biznesowych.

## <a name="new-environment-creation"></a>Tworzenie nowego środowiska

W poniższych krokach opisano sposób tworzenia środowiska Azure czas Series Insights w wersji zapoznawczej:

1. Wybierz **PAYG** przycisku w obszarze **jednostki SKU** menu. Podaj nazwę środowiska, określić grupę, do której subskrypcję i grupę zasobów do użycia. Następnie wybierz dla środowiska hostowane w obsługiwanej lokalizacji.

1. Wprowadź identyfikator serii czasu

    >[!NOTE]
    > * Identyfikator serii czasu jest rozróżniana wielkość liter i niezmienne (nie można zmienić po jego ustawieniu).
    > * Identyfikatory serii czasu mogą być klucze do 3.
    > * Dowiedz się więcej o wybranie Identyfikatora serii czasu, czytając [jak wybrać identyfikator serii czasu](./time-series-insights-update-how-to-id.md).

1. Utwórz konto usługi Azure Storage, wybierając nazwę konta usługi Azure Storage i wyznaczanie wybór replikacji. Wykonując to automatycznie tworzy konto usługi Azure Storage ogólnego przeznaczenia w wersji 1. Zostanie utworzony w tym samym regionie, co środowiska Azure czas Series Insights w wersji zapoznawczej, które wcześniej wybrano.

    ![Utwórz wystąpienie usługi Azure Time Series Insights.][1]

1. Opcjonalnie można dodać źródła zdarzenia.

   * Time Series Insights obsługuje [usługi Azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) i [usługi Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) jako opcje. Gdy źródło pojedynczego zdarzenia można dodawać tylko w czasie tworzenia środowiska, źródła zdarzeń dodatkowe można dodać później. Zaleca się, można utworzyć grupy konsumentów unikatowy, aby upewnić się, że wszystkie zdarzenia są widoczne dla swojego wystąpienia usługi Azure czas Series Insights w wersji zapoznawczej. Można wybrać istniejącą grupę odbiorców lub Utwórz nową grupę odbiorców podczas dodawania źródła zdarzeń.

   * Należy także określić odpowiednie właściwości sygnatury czasowej. Domyślnie usługa Azure Time Series Insights używa czas umieszczonych w kolejce komunikatu dla każdego źródła zdarzeń.

     > [!TIP]
     > Czas umieszczonych w kolejce komunikatów może nie być najlepiej skonfigurowane ustawienie do użycia w partii zdarzeń lub danych historycznych, przekazywanie scenariuszy. Upewnij się, że zdecydować, czy użycie lub nie właściwość sygnatury czasowej w takich przypadkach.

    ![Dodawanie źródła zdarzeń do Twojego wystąpienia usługi.][2]

1. Przejrzyj i utwórz

    ![Dodawanie źródła zdarzeń do Twojego wystąpienia usługi.][3]

Upewnij się, że środowisko został aprowizowany przy użyciu odpowiednich ustawień.

## <a name="management"></a>Zarządzanie

Masz możliwość zarządzania środowiskiem Azure czas Series Insights w wersji zapoznawczej w witrynie Azure portal. Oto najważniejsze różnice w zarządzaniu **PAYG** Azure czas Series Insights w wersji zapoznawczej środowiska, w przeciwieństwie do środowiska wersji S1 lub S2 przy użyciu witryny Azure Portal:

* Witryna Azure portal *Przegląd* bloku pozostaje niezmieniony w usłudze Azure Time Series Insights, z wyjątkiem sytuacji, w następujący sposób:
  * Pojemność zostanie usunięta, ponieważ takie podejście nie jest ważna dla **PAYG** środowisk.
  * Dodano właściwość ID serii czasu. Ustala, jak Twoje dane są podzielone na partycje.
  * Zestawy danych referencyjnych są usuwane.
  * Wyświetlany adres URL kieruje użytkownika do [Azure czas Series Insights w wersji zapoznawczej Eksplorator](./time-series-insights-update-explorer.md).
  * Znajduje się nazwa konta magazynu platformy Azure.

* Azure Portal *Konfiguruj* bloku został usunięty w wersji zapoznawczej Azure czas serii Insights ponieważ **PAYG** środowiska nie są konfigurowalne.

* Azure Portal *odwołania* blok danych została usunięta w wersji zapoznawczej Azure czas serii szczegółowych informacji, ponieważ dane referencyjne nie jest składnikiem **PAYG** środowisk.

![Sprawdź wystąpienie usługi.][4]

## <a name="next-steps"></a>Kolejne kroki

Odczyt [jak Planowanie środowiska](./time-series-insights-update-plan.md).

Odczyt [sposób dodawania źródła zdarzeń Centrum](./time-series-insights-how-to-add-an-event-source-eventhub.md).

Odczyt [jak dodawanie źródła usługi IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md).

<!-- Images -->
[1]: media/v2-update-manage/manage_one.png
[2]: media/v2-update-manage/manage_two.png
[3]: media/v2-update-manage/manage_three.png
[4]: media/v2-update-manage/manage_four.png
