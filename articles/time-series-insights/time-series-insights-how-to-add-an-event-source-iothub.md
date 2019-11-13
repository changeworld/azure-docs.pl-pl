---
title: Jak dodać źródło zdarzeń centrum IoT Hub — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak dodać źródło zdarzeń centrum IoT Hub do środowiska Time Series Insightsowego.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/09/2019
ms.custom: seodec18
ms.openlocfilehash: e564bb6be22b3cee07fca2acd8a4d3ef91698111
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74006894"
---
# <a name="add-an-iot-hub-event-source-to-your-time-series-insights-environment"></a>Dodawanie źródła zdarzeń centrum IoT Hub do środowiska Time Series Insights

W tym artykule opisano, jak za pomocą Azure Portal dodać Źródło zdarzenia, które odczytuje dane z usługi Azure IoT Hub do środowiska Azure Time Series Insights.

> [!NOTE]
> Instrukcje zawarte w tym artykule mają zastosowanie do Azure Time Series Insights GA i Time Series Insights środowiska w wersji zapoznawczej.

## <a name="prerequisites"></a>Wymagania wstępne

* Utwórz [środowisko Azure Time Series Insights](time-series-insights-update-create-environment.md).
* Utwórz [Centrum IoT Hub przy użyciu Azure Portal](../iot-hub/iot-hub-create-through-portal.md).
* Usługa IoT Hub musi mieć aktywne zdarzenia komunikatów wysyłane w usłudze.
* Utwórz dedykowaną grupę odbiorców w centrum IoT Hub, aby można było używać środowiska Time Series Insights. Każdego źródła zdarzeń usługi Time Series Insights musi mieć swój własny dedykowanej grupy klientów, które nie zostały udostępnione innych odbiorców. Jeśli wielu elementów odczytujących korzystanie ze zdarzeń z tej samej grupy konsumentów, wszystkich czytelników prawdopodobnie błędy. Aby uzyskać szczegółowe informacje, zobacz [Przewodnik dla deweloperów platformy Azure IoT Hub](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Dodawanie grupy odbiorców do centrum IoT Hub

Aplikacje używają grup konsumenckich do ściągania danych z usługi Azure IoT Hub. Aby niezawodnie odczytywać dane z usługi IoT Hub, podaj dedykowaną grupę konsumentów, która jest używana tylko przez to środowisko Time Series Insights.

Aby dodać nową grupę odbiorców do centrum IoT:

1. W [Azure Portal](https://portal.azure.com)Znajdź i Otwórz swoje centrum IoT Hub.

1. W obszarze **Ustawienia**wybierz pozycję **wbudowane punkty końcowe**, a następnie wybierz punkt końcowy **zdarzeń** .

   [![na stronie punkty końcowe kompilacji wybierz przycisk zdarzenia](media/time-series-insights-how-to-add-an-event-source-iothub/1-iot-hub.png)](media/time-series-insights-how-to-add-an-event-source-iothub/1-iot-hub.png#lightbox)

1. W obszarze **grupy odbiorców**wprowadź unikatową nazwę grupy odbiorców. Użyj tej samej nazwy w środowisku Time Series Insights podczas tworzenia nowego źródła zdarzeń.

1. Wybierz pozycję **Zapisz**.

## <a name="add-a-new-event-source"></a>Dodaj nowe źródło zdarzeń

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W menu po lewej stronie wybierz pozycję **Wszystkie zasoby**. Wybierz środowisko usługi Time Series Insights.

1. W obszarze **topologii środowiska**, wybierz opcję **źródła zdarzeń**, a następnie wybierz pozycję **Dodaj**.

   [![wybierz źródła zdarzeń, a następnie wybierz przycisk Dodaj](media/time-series-insights-how-to-add-an-event-source-iothub/2-add-event-source.png)](media/time-series-insights-how-to-add-an-event-source-iothub/2-add-event-source.png#lightbox)

1. W okienku **Nowy Źródło zdarzenia** w polu **Nazwa źródła zdarzenia**wprowadź nazwę, która jest unikatowa dla tego środowiska Time Series Insights. Na przykład wprowadź **strumień zdarzeń**.

1. W obszarze **Źródło**wybierz pozycję **IoT Hub**.

1. Wybierz wartość **opcji importowania**:

   * Jeśli masz już Centrum IoT Hub w jednej z subskrypcji, wybierz opcję **użyj IoT Hub z dostępnych subskrypcji**. Ta opcja jest to najłatwiejsza metoda.
   
     [![Wybieranie opcji w okienku Nowy Źródło zdarzenia](media/time-series-insights-how-to-add-an-event-source-iothub/3-select-an-import-option.png)](media/time-series-insights-how-to-add-an-event-source-iothub/3-select-an-import-option.png#lightbox)

    * W poniższej tabeli opisano właściwości, które są wymagane dla opcji **użyj IoT Hub z dostępnych subskrypcji** :

       [![nowe okienko źródła zdarzeń — właściwości do ustawienia w opcji Użyj IoT Hub z dostępnych subskrypcji](media/time-series-insights-how-to-add-an-event-source-iothub/4-create-button.png)](media/time-series-insights-how-to-add-an-event-source-iothub/4-create-button.png#lightbox)

       | Właściwość | Opis |
       | --- | --- |
       | Subskrypcja | Subskrypcja, do której należy wymagane Centrum IoT. |
       | Nazwa Centrum IoT Hub | Nazwa wybranego Centrum IoT Hub. |
       | Nazwa zasad Centrum IoT | Wybierz zasady dostępu współdzielonego. Zasady dostępu współdzielonego można znaleźć na karcie Ustawienia Centrum IoT. Każda zasada dostępu współdzielonego ma określoną nazwę, uprawnienia oraz klucze dostępu. Zasady dostępu współdzielonego dla źródła zdarzeń *muszą* mieć uprawnienia do **połączenia z usługą** . |
       | Klucz zasad Centrum IoT | Klucz jest wstępnie wypełniony. |

    * Jeśli Centrum IoT znajduje się poza subskrypcjami lub chcesz wybrać opcje zaawansowane, wybierz opcję **podaj IoT Hub ustawienia ręcznie**.

      W poniższej tabeli opisano wymagane właściwości **Ustawienia podaj IoT Hub ręcznie**:

       | Właściwość | Opis |
       | --- | --- |
       | Identyfikator subskrypcji | Subskrypcja, do której należy wymagane Centrum IoT. |
       | Grupa zasobów | Nazwa grupy zasobów, w której utworzono Centrum IoT Hub. |
       | Nazwa Centrum IoT Hub | Nazwa Centrum IoT. Po utworzeniu usługi IoT Hub wprowadzono nazwę Centrum IoT. |
       | Nazwa zasad Centrum IoT | Zasady dostępu współdzielonego. Zasady dostępu współdzielonego można utworzyć na karcie Ustawienia Centrum IoT. Każda zasada dostępu współdzielonego ma określoną nazwę, uprawnienia oraz klucze dostępu. Zasady dostępu współdzielonego dla źródła zdarzeń *muszą* mieć uprawnienia do **połączenia z usługą** . |
       | Klucz zasad Centrum IoT | Współużytkowany klucz dostępu używany do uwierzytelniania dostępu do przestrzeni nazw Azure Service Bus. Wprowadź tutaj klucz podstawowy lub pomocniczy. |

    * Obie opcje udostępniają następujące opcje konfiguracji:

       | Właściwość | Opis |
       | --- | --- |
       | Grupa konsumentów Centrum IoT Hub | Grupa konsumentów, która odczytuje zdarzenia z Centrum IoT Hub. Firma Microsoft zdecydowanie zaleca się Użyj dedykowanej grupy klientów dla źródła zdarzenia. |
       | Format serializacji zdarzeń | Obecnie JSON jest format serializacji jedyną dostępną. Komunikaty zdarzeń muszą mieć ten format lub nie można odczytać danych. |
       | Nazwa właściwości sygnatury czasowej | Aby określić tę wartość, należy zrozumieć format wiadomości przesyłanych do centrum IoT Hub. Ta wartość jest **nazwa** właściwości określone zdarzenie w danych wiadomości, które chcesz użyć jako sygnatura czasowa zdarzenia. Wartość jest rozróżniana wielkość liter. Jeśli pole pozostanie puste, **czas umieścić w kolejce zdarzenia** w zdarzeniu źródłowego jest używana jako sygnatura czasowa zdarzenia. |


1. Dodaj dedykowaną nazwę grupy konsumentów Time Series Insights, która została dodana do centrum IoT Hub.

1. Wybierz pozycję **Utwórz**.

1. Po utworzeniu źródła zdarzeń Time Series Insights automatycznie rozpoczyna przesyłanie strumieniowe danych do środowiska.

## <a name="next-steps"></a>Następne kroki

* [Zdefiniuj zasady dostępu do danych](time-series-insights-data-access.md) do zabezpieczania danych.

* [Wysyłanie zdarzeń](time-series-insights-send-events.md) do źródła zdarzenia.

* Dostęp do środowiska w [Eksploratora usługi Time Series Insights](https://insights.timeseries.azure.com).
