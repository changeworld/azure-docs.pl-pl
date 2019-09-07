---
title: Inicjowanie obsługi wersji zapoznawczej usługi Azure Time Series i zarządzanie nią | Microsoft Docs
description: Informacje dotyczące aprowizacji i zarządzania Azure Time Series Insights w wersji zapoznawczej.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 09/04/2019
ms.custom: seodec18
ms.openlocfilehash: a84c587a2666982e862f0640b30d2d6bef3b9a42
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744650"
---
# <a name="provision-and-manage-azure-time-series-insights-preview"></a>Udostępnianie wersji zapoznawczej Azure Time Series Insights i zarządzanie nią

W tym artykule opisano sposób tworzenia środowiska Azure Time Series Insights w wersji zapoznawczej i zarządzania nim przy użyciu [Azure Portal](https://portal.azure.com/).

## <a name="overview"></a>Omówienie

Środowiska Azure Time Series Insights w wersji zapoznawczej to środowiska z opcją płatność zgodnie z rzeczywistym użyciem.

Po udostępnieniu środowiska Azure Time Series Insights w wersji zapoznawczej można utworzyć dwa zasoby platformy Azure:

* Środowisko wersji zapoznawczej Azure Time Series Insights  
* Konto usługi Azure Storage ogólnego przeznaczenia w wersji 1
  
Dowiedz się [, jak zaplanować środowisko](./time-series-insights-update-plan.md).

>[!IMPORTANT]
> Na potrzeby wersji zapoznawczej upewnij się, że używasz konta ogólnego przeznaczenia w wersji 1 (GPv1) usługi Azure Storage.

Opcjonalnie możesz skojarzyć każde środowisko Azure Time Series Insights w wersji zapoznawczej ze źródłem zdarzenia. Aby uzyskać więcej informacji, przeczytaj temat [Dodawanie źródła centrum zdarzeń](./time-series-insights-how-to-add-an-event-source-eventhub.md) i [Dodawanie źródła Centrum IoT](./time-series-insights-how-to-add-an-event-source-iothub.md). W tym kroku podajesz Właściwość identyfikatora znacznika czasu i unikatową grupę odbiorców. Dzięki temu środowisko będzie miało dostęp do odpowiednich zdarzeń.

Po zakończeniu aprowizacji można zmodyfikować zasady dostępu i inne atrybuty środowiska zgodnie z wymaganiami firmy.

## <a name="create-the-environment"></a>Tworzenie środowiska

W poniższych krokach opisano sposób tworzenia środowiska Azure Time Series Insights w wersji zapoznawczej:

1. Wybierz przycisk **PAYG** w menu **SKU** . Podaj nazwę środowiska, a następnie wybierz grupę subskrypcji i grupę zasobów do użycia. Następnie wybierz obsługiwaną lokalizację, w której ma być hostowane środowisko.

   [![Utwórz wystąpienie Azure Time Series Insights.](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

1. Wprowadź identyfikator szeregów czasowych.

    >[!NOTE]
    > * Identyfikator szeregów czasowych uwzględnia wielkość liter i jest niezmienny. (Nie można jej zmienić po ustawieniu).
    > * Identyfikatory szeregów czasowych mogą składać się z maksymalnie trzech kluczy.
    > * Aby uzyskać więcej informacji na temat wybierania identyfikatora szeregów czasowych, przeczytaj temat [Wybieranie identyfikatora szeregów czasowych](./time-series-insights-update-how-to-id.md).

1. Utwórz konto usługi Azure Storage, wybierając nazwę konta magazynu i wyznaczając wybór replikacji. Spowoduje to automatyczne utworzenie konta ogólnego przeznaczenia w usłudze Azure Storage. Zostanie on utworzony w tym samym regionie co wcześniej wybrane środowisko Azure Time Series Insights w wersji zapoznawczej.

    [![Utwórz konto usługi Azure Storage dla swojego wystąpienia](media/v2-update-manage/manage-five.png)](media/v2-update-manage/manage-five.png#lightbox)

1. Opcjonalnie możesz dodać Źródło zdarzenia.

   * Time Series Insights obsługuje [platformę azure IoT Hub](./time-series-insights-how-to-add-an-event-source-iothub.md) i [Azure Event Hubs](./time-series-insights-how-to-add-an-event-source-eventhub.md) jako opcje. Mimo że można dodać tylko jedno źródło zdarzenia w czasie tworzenia środowiska, można później dodać kolejne Źródło zdarzenia. Najlepiej utworzyć unikatową grupę odbiorców, aby upewnić się, że wszystkie zdarzenia są widoczne dla Twojego wystąpienia w wersji zapoznawczej Azure Time Series Insights. Podczas dodawania źródła zdarzeń można wybrać istniejącą grupę odbiorców lub utworzyć nową grupę odbiorców.

   * Należy również wybrać odpowiednią właściwość sygnatury czasowej. Domyślnie Azure Time Series Insights używa czasu komunikatu w kolejce dla każdego źródła zdarzenia.

     > [!TIP]
     > Czas w kolejce komunikatu może nie być najlepszym skonfigurowanym ustawieniem do użycia w przypadku zdarzeń wsadowych lub scenariuszy przekazywania danych historycznych. Upewnij się, że podjęto próbę zweryfikowania decyzji o użyciu lub nie używać właściwości timestamp w takich przypadkach.

     [![Karta źródło zdarzeń](media/v2-update-manage/manage-two.png)](media/v2-update-manage/manage-two.png#lightbox)

1. Upewnij się, że środowisko zostało udostępnione przy użyciu żądanych ustawień.

    [![Przejrzyj i Utwórz kartę](media/v2-update-manage/manage-three.png)](media/v2-update-manage/manage-three.png#lightbox)

## <a name="manage-the-environment"></a>Zarządzanie środowiskiem

Możesz zarządzać środowiskiem Azure Time Series Insights w wersji zapoznawczej przy użyciu Azure Portal. Poniżej przedstawiono główne różnice w zarządzaniu środowiskiem Azure Time Series Insights w wersji zapoznawczej PAYG, w przeciwieństwie do środowiska S1 lub S2, przy użyciu Azure Portal:

* Blok **przegląd** Azure Portal jest niezmieniony w Azure Time Series Insights, z wyjątkiem następujących sposobów:
  * Pojemność jest usuwana, ponieważ omawiane koncepcje nie odnosi się do środowisk PAYG.
  * Dodano Właściwość identyfikatora szeregów czasowych. Określa sposób partycjonowania danych.
  * Zestawy danych referencyjnych są usuwane.
  * Wyświetlany adres URL kieruje użytkownika do [eksploratora Azure Time Series Insights w wersji zapoznawczej](./time-series-insights-update-explorer.md).
  * Nazwa konta usługi Azure Storage znajduje się na liście.

* Blok **konfigurowania** Azure portal został usunięty w wersji zapoznawczej programu Azure Time Series Insights, ponieważ nie można konfigurować środowisk PAYG.

* Blok **danych referencyjnych** Azure portal został usunięty w programie Azure Time Series Insights Preview, ponieważ dane referencyjne nie są składnikiem środowisk PAYG.

[![Środowisko Time Series Insights w wersji zapoznawczej w Azure Portal](media/v2-update-manage/manage-four.png)](media/v2-update-manage/manage-four.png#lightbox)

## <a name="next-steps"></a>Następne kroki

- Odczytaj [Planowanie środowiska](./time-series-insights-update-plan.md).

- Dowiedz się, jak [dodać Źródło centrum zdarzeń](./time-series-insights-how-to-add-an-event-source-eventhub.md).

- Skonfiguruj [Źródło Centrum IoT](./time-series-insights-how-to-add-an-event-source-iothub.md).