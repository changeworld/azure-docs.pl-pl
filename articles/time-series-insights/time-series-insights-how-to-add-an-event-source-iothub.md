---
title: Jak dodać źródła zdarzeń usługi IoT Hub do usługi Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób dodawania źródła zdarzeń, który jest podłączony do usługi IoT Hub dla środowiska usługi Time Series Insights
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/30/2018
ms.openlocfilehash: 3168fb6cf6e9b08fe4d2de921179129db241d153
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52847171"
---
# <a name="how-to-add-an-iot-hub-event-source-to-time-series-insights-environment"></a>Jak dodać źródła zdarzeń usługi IoT Hub dla środowiska usługi Time Series Insights

W tym artykule opisano sposób użycia witryny Azure portal w celu dodawania źródła zdarzeń, która odczytuje dane z usługi IoT Hub w środowisku usługi Azure Time Series Insights (TSI).

> [!NOTE]
> Te instrukcje dotyczą wersji ogólnie dostępnej usługi TSI i środowiskach w wersji zapoznawczej usługi TSI.

## <a name="prerequisites"></a>Wymagania wstępne

* Tworzenie środowiska usługi TSI. Aby uzyskać więcej informacji, zobacz [utworzyć środowisko Azure TSI](time-series-insights-update-create-environment.md).
* Tworzenie centrum IoT Hub. Aby uzyskać więcej informacji na temat centra IoT Hub, zobacz [Tworzenie Centrum IoT przy użyciu witryny Azure portal](../iot-hub/iot-hub-create-through-portal.md).
* Usługa IoT Hub musi zdarzenia w aktywnej wiadomości wysyłanych.
* Utwórz grupę odbiorców dedykowanej w usłudze IoT Hub dla środowiska usługi TSI do korzystania z. Każde źródło zdarzeń usługi TSI musi mieć swój własny dedykowanej grupy klientów, które nie są współużytkowane z innymi konsumentów. Jeśli wielu elementów odczytujących korzystanie ze zdarzeń z tej samej grupy konsumentów, wszystkich czytelników prawdopodobnie błędy. Aby uzyskać więcej informacji, zobacz [usługi IoT Hub — przewodnik dewelopera](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Dodaj grupę odbiorców do usługi IoT Hub

Grupy konsumentów są używane przez aplikacje do pobierania danych z usługi Azure IoT Hubs. Podaj dedykowanej grupy klientów, do użytku przez to usługa TSI Przesyła środowisko tylko niezawodne odczytu danych z usługi IoT Hub.

Aby dodać nową grupę odbiorców do Centrum IoT Hub, wykonaj następujące kroki:

1. W witrynie Azure portal zlokalizuj i Otwórz Centrum IoT Hub.
1. W obszarze **ustawienia** nagłówka, wybierz **wbudowanych punktach końcowych**.

   ![Usługa IoT Hub, jeden][1]

1. Wybierz **zdarzenia** punktu końcowego, a **właściwości** zostanie otwarta strona.

1. W obszarze **grupy konsumentów** nagłówka, podaj nową unikalną nazwę dla grupy odbiorców. Podczas tworzenia nowego źródła zdarzeń należy użyć tej samej nazwie w środowisku usługi TSI.

1. Wybierz **Zapisz** Aby zapisać nową grupę odbiorców.

## <a name="add-a-new-event-source"></a>Dodaj nowe źródło zdarzeń

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Znajdź istniejącego środowiska usługi TSI. Kliknij przycisk **wszystkie zasoby** w menu po lewej stronie witryny Azure portal. Wybierz środowisko usługi TSI.

1. W obszarze **topologii środowiska** nagłówek, kliknij przycisk **źródła zdarzeń**.

   ![Usługa IoT Hub dwóch][2]

1. Kliknij pozycję **+ Dodaj**.

1. Podaj **nazwy źródła zdarzeń** unikatowe dla tego środowiska TSI, takich jak **strumienia zdarzeń**.

   ![Usługa IoT Hub trzy][3]

1. Wybierz **źródła** jako **usługi IoT Hub**.

1. Wybierz odpowiedni **opcji importowania**.

   * Wybierz **korzystanie z usługi IoT Hub z dostępnych subskrypcji** Jeśli masz już istniejące Centrum IoT Hub w jednej z Twoich subskrypcji. Jest to najłatwiejsza metoda.
   * Wybierz **ustawienia Centrum IoT Hub zapewniają ręcznie** po usługi IoT Hub jest zewnętrzne w stosunku do subskrypcji lub chcesz wybrać opcje zaawansowane.

1. Jeśli wybrano **korzystanie z usługi IoT Hub z dostępnych subskrypcji** opcji w poniższej tabeli opisano każdy wymaganej właściwości:

   ![Usługa IoT Hub 4][4]

   | Właściwość | Opis |
   | --- | --- |
   | Identyfikator subskrypcji | Wybierz subskrypcję, w której został utworzony tego Centrum IoT Hub.
   | Nazwa centrum IoT Hub | Wybierz nazwę Centrum IoT Hub.
   | Nazwa zasad Centrum IoT Hub | Wybierz zasady dostępu współdzielonego, który można znaleźć na karcie Ustawienia Centrum IoT Hub. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia, ustaw i klucze dostępu. Zasady dostępu współdzielonego dla źródła zdarzenia *musi* mają **połączenie z usługą** uprawnienia.
   | Klucz zasad Centrum IoT Hub | Klucz jest wypełniana wstępnie.
   | Grupy konsumentów usługi IoT Hub | Grupy odbiorców na odczytywanie wydarzeń w usłudze IoT Hub. Zdecydowanie zaleca się Użyj dedykowanej grupy klientów dla źródła zdarzenia.
   | Format serializacji zdarzeń | Kod JSON jest dostępne tylko serializacji w chwili obecnej. Komunikaty o zdarzeniach musi być w następującym formacie, lub żadne dane nie mogą być odczytywane. |
   | Nazwa właściwości sygnatury czasowej | Aby określić tę wartość, należy zrozumieć formacie wiadomości dane wiadomości wysyłane do usługi IoT Hub. Ta wartość jest **nazwa** właściwości określone zdarzenie w danych wiadomości, które chcesz użyć jako sygnatura czasowa zdarzenia. Wartość jest rozróżniana wielkość liter. Jeśli pole pozostanie puste, **czas umieścić w kolejce zdarzenia** w zdarzeniu źródłowego jest używana jako sygnatura czasowa zdarzenia. |

1. Jeśli wybrano **ustawienia Centrum IoT Hub zapewniają ręcznie** opcji w poniższej tabeli opisano każdy wymaganej właściwości:

   | Właściwość | Opis |
   | --- | --- |
   | Identyfikator subskrypcji | Subskrypcja, w której ta utworzono Centrum IoT Hub.
   | Grupa zasobów | Nazwa grupy zasobów, w której ta utworzono Centrum IoT Hub.
   | Nazwa centrum IoT Hub | Nazwa centrum IoT Hub. Podczas tworzenia Centrum IoT Hub, należy też nadać mu nazwę.
   | Nazwa zasad Centrum IoT Hub | Zasady dostępu współdzielonego można utworzyć na karcie Ustawienia Centrum IoT Hub. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia, ustaw i klucze dostępu. Zasady dostępu współdzielonego dla źródła zdarzenia *musi* mają **połączenie z usługą** uprawnienia.
   | Klucz zasad Centrum IoT Hub | Klucz dostępu współdzielonego, używany do uwierzytelniania dostępu do przestrzeni nazw usługi Service Bus. Wpisz tutaj klucz podstawowy lub pomocniczy.
   | Grupy konsumentów usługi IoT Hub | Grupy odbiorców na odczytywanie wydarzeń w usłudze IoT Hub. Zdecydowanie zaleca się Użyj dedykowanej grupy klientów dla źródła zdarzenia.
   | Format serializacji zdarzeń | Kod JSON jest dostępne tylko serializacji w chwili obecnej. Komunikaty o zdarzeniach musi być w następującym formacie, lub żadne dane nie mogą być odczytywane. |
   | Nazwa właściwości sygnatury czasowej | Aby określić tę wartość, należy zrozumieć formacie wiadomości dane wiadomości wysyłane do usługi IoT Hub. Ta wartość jest **nazwa** właściwości określone zdarzenie w danych wiadomości, które chcesz użyć jako sygnatura czasowa zdarzenia. Wartość jest rozróżniana wielkość liter. Jeśli pole pozostanie puste, **czas umieścić w kolejce zdarzenia** w zdarzeniu źródłowego jest używana jako sygnatura czasowa zdarzenia. |

1. Dodanie dedykowane TSI konsumenta grupy nazwy dodanego do usługi IoT Hub.

1. Wybierz **Utwórz** można dodać nowe źródło zdarzeń.

   ![Usługa IoT Hub 5][5]

1. Po utworzeniu źródła zdarzeń usługa Time Series Insights automatycznie rozpocznie przesyłanie strumieni danych do środowiska.

## <a name="next-steps"></a>Kolejne kroki

* [Zdefiniuj zasady dostępu do danych](time-series-insights-data-access.md) do zabezpieczania danych.
* [Wysyłanie zdarzeń](time-series-insights-send-events.md) do źródła zdarzenia.
* Dostęp do środowiska w [Eksploratora usługi Time Series Insights](https://insights.timeseries.azure.com).

<!-- Images -->
[1]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_one.png
[2]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_two.png
[3]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_three.png
[4]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_four.png
[5]: media/time-series-insights-how-to-add-an-event-source-iothub/iothub_five.png