---
title: Jak dodać źródła zdarzeń Centrum zdarzeń do usługi Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób dodawania źródła zdarzeń, który jest podłączony do Centrum zdarzeń do środowiska usługi Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/30/2018
ms.openlocfilehash: 93076a8b0d11e05b86c20868d4bc23f4826f107f
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52832449"
---
# <a name="how-to-add-an-event-hub-event-source-to-time-series-insights-environment"></a>Jak dodać źródła zdarzeń Centrum zdarzeń do środowiska usługi Time Series Insights

W tym artykule opisano sposób dodawania źródła zdarzeń, która odczytuje dane z Centrum zdarzeń do środowiska usługi Time Series Insights za pomocą witryny Azure portal.

> [!NOTE]
> Te instrukcje dotyczą zarówno GA Insights serii czasu i usługi Time Series Insights aktualizowanie środowisk prywatnej wersji zapoznawczej.

## <a name="prerequisites"></a>Wymagania wstępne

- Tworzenie środowiska usługi Time Series Insights. Aby uzyskać więcej informacji, zobacz [tworzenie środowiska usługi Azure Time Series Insights](./time-series-insights-update-create-environment.md).
- Tworzenie centrum zdarzeń. Aby uzyskać więcej informacji na temat usługi Event Hubs, zobacz [tworzenie przestrzeni nazw usługi Event Hubs i Centrum zdarzeń za pomocą witryny Azure portal](../event-hubs/event-hubs-create.md).
- Centrum zdarzeń musi mieć aktywne wiadomości zdarzenia są wysyłane. Aby uzyskać więcej informacji, zobacz [wysyłanie zdarzeń do usługi Azure Event Hubs przy użyciu programu .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Utwórz dedykowanej grupy klientów w Centrum zdarzeń dla środowiska Time Series Insights, aby korzystać z. Każdego źródła zdarzeń usługi Time Series Insights musi mieć swój własny dedykowanej grupy klientów, które nie są współużytkowane z innych klientów. Jeśli wielu elementów odczytujących korzystanie ze zdarzeń z tej samej grupy konsumentów, wszystkich czytelników prawdopodobnie błędy. Należy pamiętać, że istnieje również limit 20 grup odbiorców dla Centrum zdarzeń. Aby uzyskać więcej informacji, zobacz [Event Hubs Programming Guide](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Dodaj grupę odbiorców do Centrum zdarzeń

Grupy konsumentów są używane przez aplikacje do pobierania danych z usługi Azure Event Hubs. Podaj dedykowanej grupy klientów, do użytku przez to usługi Time Series Insights środowisko tylko niezawodne odczytu danych z Centrum zdarzeń.

Aby dodać nową grupę konsumentów Centrum zdarzeń, wykonaj następujące kroki:

1. W witrynie Azure portal zlokalizuj i Otwórz Centrum zdarzeń.

1. W obszarze **jednostek** nagłówka, wybierz **grupy konsumentów**.

   ![Centrum zdarzeń — dodać grupy konsumentów](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)

1. Wybierz **+ Grupa odbiorców** można dodać nowe grupy konsumentów. 

1. Na **grupy konsumentów** Podaj nowy unikatowy **nazwa**.  Podczas tworzenia nowego źródła zdarzeń w środowisku usługi Time Series Insights, należy użyć tej samej nazwie.

1. Wybierz **Utwórz** można utworzyć nowej grupy konsumentów.

## <a name="add-a-new-event-source"></a>Dodaj nowe źródło zdarzeń

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Znajdź istniejącego środowiska usługi Time Series Insights. Kliknij przycisk **wszystkie zasoby** w menu po lewej stronie witryny Azure portal. Wybierz środowisko usługi Time Series Insights.

1. W obszarze **topologii środowiska** nagłówek, kliknij przycisk **źródła zdarzeń**.

   ![Zdarzenie źródeł + Dodaj](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)

1. Kliknij pozycję **+ Dodaj**.

1. Podaj **nazwy źródła zdarzeń** unikatowe dla tego środowiska usługi Time Series Insights, takich jak **strumienia zdarzeń**.

   ![Należy wypełnić pierwsze trzy parametry w formularzu.](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)

1. Wybierz **źródła** jako **Centrum zdarzeń**.

1. Wybierz odpowiedni **opcji importowania**.
   - Wybierz **Użyj Centrum zdarzeń z dostępnych subskrypcji** Jeśli masz już istniejące Centrum zdarzeń w jednej z Twoich subskrypcji. Jest to najłatwiejsza metoda.
   - Wybierz **ustawienia Centrum zdarzeń zapewnia ręcznie** przypadku Centrum zdarzeń jest zewnętrzne w stosunku do subskrypcji lub chcesz wybrać opcje zaawansowane.

1. Jeśli wybrano **Użyj Centrum zdarzeń z dostępnych subskrypcji** opcji w poniższej tabeli opisano każdy wymaganej właściwości:

   ![Szczegółowe informacje dotyczące subskrypcji i zdarzeń Centrum](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)

   | Właściwość | Opis |
   | --- | --- |
   | Identyfikator subskrypcji | Wybierz subskrypcję, w której został utworzony tym Centrum zdarzeń.
   | Przestrzeń nazw magistrala usług | Wybierz przestrzeń nazw usługi Service Bus, zawierający Centrum zdarzeń.
   | Nazwa centrum zdarzeń | Wybierz nazwę Centrum zdarzeń.
   | Nazwa zasad Centrum zdarzeń | Wybierz zasady dostępu współdzielonego, można utworzyć na karcie Konfigurowanie Centrum zdarzeń. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia, ustaw i klucze dostępu. Zasady dostępu współdzielonego dla źródła zdarzenia *musi* mają **odczytu** uprawnienia.
   | Klucz zasad Centrum zdarzeń | Wartość klucza może być wstępnie wypełnione.
   | Grupa konsumentów Centrum zdarzeń | Grupy odbiorców na odczytywanie wydarzeń z Centrum zdarzeń. Zdecydowanie zaleca się Użyj dedykowanej grupy klientów dla źródła zdarzenia. |
   | Format serializacji zdarzeń | Kod JSON jest dostępne tylko serializacji w chwili obecnej. Komunikaty o zdarzeniach musi być w następującym formacie, lub żadne dane nie mogą być odczytywane. |
   | Nazwa właściwości sygnatury czasowej | Aby określić tę wartość, należy zrozumieć formacie wiadomości dane wiadomości wysyłane do Centrum zdarzeń. Ta wartość jest **nazwa** właściwości określone zdarzenie w danych wiadomości, które chcesz użyć jako sygnatura czasowa zdarzenia. Wartość jest rozróżniana wielkość liter. Jeśli pole pozostanie puste, **czas umieścić w kolejce zdarzenia** w zdarzeniu źródłowego jest używana jako sygnatura czasowa zdarzenia. |

1. Jeśli wybrano **ustawienia Centrum zdarzeń zapewnia ręcznie** opcji w poniższej tabeli opisano każdy wymaganej właściwości:

   | Właściwość | Opis |
   | --- | --- |
   | Identyfikator subskrypcji | Subskrypcja, w której został utworzony tym Centrum zdarzeń.
   | Grupa zasobów | Grupa zasobów, w której został utworzony tym Centrum zdarzeń.
   | Przestrzeń nazw magistrala usług | Przestrzeń nazw usługi Service Bus to kontener dla zestawu jednostek do obsługi komunikatów. Podczas tworzenia nowego Centrum zdarzeń utworzonego również przestrzeni nazw usługi Service Bus.
   | Nazwa centrum zdarzeń | Nazwa Centrum zdarzeń. Podczas tworzenia Centrum zdarzeń należy też nadać mu nazwę.
   | Nazwa zasad Centrum zdarzeń | Zasady dostępu współdzielonego można utworzyć na karcie Konfigurowanie Centrum zdarzeń. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia, ustaw i klucze dostępu. Zasady dostępu współdzielonego dla źródła zdarzenia *musi* mają **odczytu** uprawnienia.
   | Klucz zasad Centrum zdarzeń | Klucz dostępu współdzielonego, używany do uwierzytelniania dostępu do przestrzeni nazw usługi Service Bus. Wpisz tutaj klucz podstawowy lub pomocniczy.
   | Grupa konsumentów Centrum zdarzeń | Grupy odbiorców na odczytywanie wydarzeń z Centrum zdarzeń. Zdecydowanie zaleca się Użyj dedykowanej grupy klientów dla źródła zdarzenia.
   | Format serializacji zdarzeń | Kod JSON jest dostępne tylko serializacji w chwili obecnej. Komunikaty o zdarzeniach musi być w następującym formacie, lub żadne dane nie mogą być odczytywane. |
   | Nazwa właściwości sygnatury czasowej | Aby określić tę wartość, należy zrozumieć formacie wiadomości dane wiadomości wysyłane do Centrum zdarzeń. Ta wartość jest **nazwa** właściwości określone zdarzenie w danych wiadomości, które chcesz użyć jako sygnatura czasowa zdarzenia. Wartość jest rozróżniana wielkość liter. Jeśli pole pozostanie puste, **czas umieścić w kolejce zdarzenia** w zdarzeniu źródłowego jest używana jako sygnatura czasowa zdarzenia. |

1. Dodanie dedykowane TSI konsumenta grupy nazwy dodanego do Centrum zdarzeń.

1. Wybierz **Utwórz** można dodać nowe źródło zdarzeń.

   ![Kliknięcie pozycji Utwórz](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)

   Po utworzeniu źródła zdarzeń usługa Time Series Insights automatycznie rozpocznie przesyłanie strumieni danych do środowiska.

## <a name="next-steps"></a>Kolejne kroki

[Zdefiniuj zasady dostępu do danych](time-series-insights-data-access.md) do zabezpieczania danych.

[Wysyłanie zdarzeń](time-series-insights-send-events.md) do źródła zdarzenia.

Dostęp do środowiska w [Eksploratora usługi Time Series Insights](https://insights.timeseries.azure.com).
