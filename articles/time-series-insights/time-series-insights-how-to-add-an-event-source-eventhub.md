---
title: Dodawanie źródła zdarzeń usługi Event Hubs do usługi Azure Time Series Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób dodawania źródła zdarzeń, który jest podłączony do usługi Azure Event Hubs do środowiska usługi Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 8403c8fbc4faf35e0ccd3c87347e88a46f0769ff
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/08/2019
ms.locfileid: "68854473"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Dodawanie źródła zdarzeń Centrum zdarzeń do środowiska usługi Time Series Insights

W tym artykule opisano sposób dodawania źródła zdarzeń, która odczytuje dane z usługi Azure Event Hubs do środowiska usługi Azure Time Series Insights za pomocą witryny Azure portal.

> [!NOTE]
> Kroki opisane w tym artykule dotyczą zarówno środowiska Time Series Insights w wersji zapoznawczej, jak i Time Series Insights.

## <a name="prerequisites"></a>Wymagania wstępne

- Utwórz środowisko Time Series Insights, zgodnie z opisem w artykule [Tworzenie środowiska Azure Time Series Insights](./time-series-insights-update-create-environment.md).
- Utwórz centrum zdarzeń. Zobacz [Tworzenie przestrzeni nazw Event Hubs i centrum zdarzeń przy użyciu Azure Portal](../event-hubs/event-hubs-create.md).
- Centrum zdarzeń musi mieć zdarzenia aktywne wiadomości wysyłane do niej. Dowiedz się [, jak wysyłać zdarzenia do usługi Azure Event Hubs przy użyciu .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Utwórz grupę odbiorców dedykowanych w Centrum zdarzeń, które korzystają ze środowiska usługi Time Series Insights. Każdego źródła zdarzeń usługi Time Series Insights musi mieć swój własny dedykowanej grupy klientów, które nie zostały udostępnione innych odbiorców. Jeśli wielu elementów odczytujących korzystanie ze zdarzeń z tej samej grupy konsumentów, wszystkich czytelników prawdopodobnie błędy. Obowiązuje limit 20 grup odbiorców dla Centrum zdarzeń. Aby uzyskać więcej informacji, zobacz [Podręcznik programowania usługi Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Dodaj grupę odbiorców do Centrum zdarzeń

Aplikacje używać grupy odbiorców do pobierania danych z usługi Azure Event Hubs. Aby niezawodnie odczytywać dane z centrum zdarzeń, należy udostępnić dedykowaną grupę użytkowników, która jest używana tylko przez to Time Series Insights środowisku.

Aby dodać nową grupę konsumentów Centrum zdarzeń:

1. W witrynie Azure portal zlokalizuj i Otwórz Centrum zdarzeń.

1. W obszarze **jednostek**, wybierz opcję **grupy konsumentów**, a następnie wybierz pozycję **grupy odbiorców**.

   [![Centrum zdarzeń — Dodaj grupę odbiorców](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png#lightbox)

1. Na **grupy konsumentów** wpisz nową unikatową wartość dla **nazwa**.  Podczas tworzenia nowego źródła zdarzeń w środowisku usługi Time Series Insights, należy użyć tej samej nazwy.

1. Wybierz pozycję **Utwórz**.

## <a name="add-a-new-event-source"></a>Dodaj nowe źródło zdarzeń

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Znajdź istniejącego środowiska usługi Time Series Insights. W menu po lewej stronie wybierz **wszystkie zasoby**, a następnie wybierz środowisko usługi Time Series Insights.

1. W obszarze **topologii środowiska**, wybierz opcję **źródła zdarzeń**, a następnie wybierz pozycję **Dodaj**.

   [![W obszarze źródła zdarzeń wybierz przycisk Dodaj.](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png#lightbox)

1. Wprowadź wartość w **nazwy źródła zdarzeń** który jest unikatowy dla tego środowiska usługi Time Series Insights, takich jak **strumienia zdarzeń**.

1. Aby uzyskać **źródła**, wybierz opcję **Centrum zdarzeń**.

1. Wybierz odpowiednie wartości dla **opcji importowania**:
   - Jeśli masz już Centrum zdarzeń w jednej z Twoich subskrypcji wybierz **Użyj Centrum zdarzeń z dostępnych subskrypcji**. Ta opcja jest to najłatwiejsza metoda.

       [![W okienku Nowy Źródło zdarzenia wprowadź wartości dla pierwszych trzech parametrów](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png#lightbox)


       [![Szczegóły subskrypcji i centrum zdarzeń](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png#lightbox)

     W poniższej tabeli opisano wymaganych właściwości dla **Użyj Centrum zdarzeń z dostępnych subskrypcji** opcji:

     | Właściwość | Opis |
     | --- | --- |
     | Identyfikator subskrypcji | Wybierz subskrypcję, w której został utworzony tym Centrum zdarzeń.
     | Przestrzeń nazw magistrali usług | Wybierz przestrzeń nazw usługi Azure Service Bus, która zawiera Centrum zdarzeń.
     | Nazwa centrum zdarzeń | Wybierz nazwę Centrum zdarzeń.
     | Nazwa zasad Centrum zdarzeń | Wybierz zasady dostępu współdzielonego. Możesz utworzyć zasady dostępu współdzielonego w Centrum zdarzeń **Konfiguruj** kartę. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia, ustaw i klucze dostępu. Zasady dostępu współdzielonego dla źródła zdarzenia *musi* mają **odczytu** uprawnienia.
     | Klucz zasad Centrum zdarzeń | Wartość klucza może być wstępnie wypełnione.
     | Grupa konsumentów Centrum zdarzeń | Grupa odbiorców odczytuje zdarzenia z Centrum zdarzeń. Firma Microsoft zdecydowanie zaleca się Użyj dedykowanej grupy klientów dla źródła zdarzenia. |
     | Format serializacji zdarzeń | Obecnie JSON jest format serializacji jedyną dostępną. Komunikaty zdarzeń muszą mieć ten format lub nie można odczytać danych. |
     | Nazwa właściwości sygnatury czasowej | Aby określić tę wartość, należy zrozumieć format wiadomości danych komunikatów, które są wysyłane do Centrum zdarzeń. Ta wartość jest **nazwa** właściwości określone zdarzenie w danych wiadomości, które chcesz użyć jako sygnatura czasowa zdarzenia. Wartość jest rozróżniana wielkość liter. Jeśli pole pozostanie puste, **czas umieścić w kolejce zdarzenia** w zdarzeniu źródłowego jest używana jako sygnatura czasowa zdarzenia. |

    - Jeśli Centrum zdarzeń jest zewnętrzne w stosunku do subskrypcji, lub jeśli chcesz wybrać opcje zaawansowane, wybierz **ustawienia Centrum zdarzeń zapewnia ręcznie**.

      W poniższej tabeli opisano wymaganych właściwości dla **ustawienia Centrum zdarzeń zapewnia ręcznie** opcji:
 
      | Właściwość | Opis |
      | --- | --- |
      | Identyfikator subskrypcji | Subskrypcja, w której został utworzony tym Centrum zdarzeń.
      | Grupa zasobów | Grupa zasobów, w której został utworzony tym Centrum zdarzeń.
      | Przestrzeń nazw magistrali usług | Przestrzeń nazw usługi Service Bus to kontener dla zestawu jednostek do obsługi komunikatów. Podczas tworzenia nowego Centrum zdarzeń utworzonego również przestrzeni nazw usługi Service Bus.
      | Nazwa centrum zdarzeń | Nazwa Centrum zdarzeń. Podczas tworzenia Centrum zdarzeń należy też nadać mu nazwę.
      | Nazwa zasad Centrum zdarzeń | Zasady dostępu współdzielonego. W tym Centrum zdarzeń można utworzyć zasady dostępu współdzielonego **Konfiguruj** kartę. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia, ustaw i klucze dostępu. Zasady dostępu współdzielonego dla źródła zdarzenia *musi* mają **odczytu** uprawnienia.
      | Klucz zasad Centrum zdarzeń | Klucz dostępu współdzielonego, który jest używany do uwierzytelniania dostępu do przestrzeni nazw usługi Service Bus. Wprowadź tutaj klucz podstawowy lub pomocniczy.
      | Grupa konsumentów Centrum zdarzeń | Grupa odbiorców odczytuje zdarzenia z Centrum zdarzeń. Firma Microsoft zdecydowanie zaleca się Użyj dedykowanej grupy klientów dla źródła zdarzenia.
      | Format serializacji zdarzeń | Obecnie JSON jest format serializacji jedyną dostępną. Komunikaty zdarzeń muszą mieć ten format lub nie można odczytać danych. |
      | Nazwa właściwości sygnatury czasowej | Aby określić tę wartość, należy zrozumieć format wiadomości danych komunikatów, które są wysyłane do Centrum zdarzeń. Ta wartość jest **nazwa** właściwości określone zdarzenie w danych wiadomości, które chcesz użyć jako sygnatura czasowa zdarzenia. Wartość jest rozróżniana wielkość liter. Jeśli pole pozostanie puste, **czas umieścić w kolejce zdarzenia** w zdarzeniu źródłowego jest używana jako sygnatura czasowa zdarzenia. |

1. Dodanie dedykowane usługi Time Series Insights konsumenta grupy nazwy dodanego do Centrum zdarzeń.

1. Wybierz pozycję **Utwórz**.

   [![Wybierz pozycję Utwórz](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png#lightbox)

   Po utworzeniu źródła zdarzeń usługi Time Series Insights automatycznie uruchamia, przesyłanie strumieniowe danych do danego środowiska.

## <a name="next-steps"></a>Kolejne kroki

* [Zdefiniuj zasady dostępu do danych](time-series-insights-data-access.md) do zabezpieczania danych.

* [Wysyłanie zdarzeń](time-series-insights-send-events.md) do źródła zdarzenia.

* Dostęp do środowiska w [Eksploratora usługi Time Series Insights](https://insights.timeseries.azure.com).
