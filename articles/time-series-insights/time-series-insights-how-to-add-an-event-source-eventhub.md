---
title: Dodawanie Event Hubs źródła zdarzeń — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak dodać źródło zdarzeń Event Hubs platformy Azure do środowiska Time Series Insights.
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
ms.openlocfilehash: 421a4635a80c5a7a45fb14bf900c205a06789279
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2019
ms.locfileid: "74012616"
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

1. W [Azure Portal](https://portal.azure.com)zlokalizuj i Otwórz centrum zdarzeń z przestrzeni nazw centrum zdarzeń.

    [![otworzyć przestrzeni nazw centrum zdarzeń](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-hub-namespace.png#lightbox)

1. W obszarze **jednostek**, wybierz opcję **grupy konsumentów**, a następnie wybierz pozycję **grupy odbiorców**.

   [![centrum zdarzeń — Dodaj grupę odbiorców](media/time-series-insights-how-to-add-an-event-source-eventhub/2-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/2-event-hub-consumer-group.png#lightbox)

1. Na **grupy konsumentów** wpisz nową unikatową wartość dla **nazwa**.  Podczas tworzenia nowego źródła zdarzeń w środowisku usługi Time Series Insights, należy użyć tej samej nazwy.

1. Wybierz pozycję **Utwórz**.

## <a name="add-a-new-event-source"></a>Dodaj nowe źródło zdarzeń

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Znajdź istniejącego środowiska usługi Time Series Insights. W menu po lewej stronie wybierz **wszystkie zasoby**, a następnie wybierz środowisko usługi Time Series Insights.

1. W obszarze **topologii środowiska**, wybierz opcję **źródła zdarzeń**, a następnie wybierz pozycję **Dodaj**.

   [![w obszarze źródła zdarzeń wybierz przycisk Dodaj](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png#lightbox)

1. Wprowadź wartość w **nazwy źródła zdarzeń** który jest unikatowy dla tego środowiska usługi Time Series Insights, takich jak **strumienia zdarzeń**.

1. Aby uzyskać **źródła**, wybierz opcję **Centrum zdarzeń**.

1. Wybierz odpowiednie wartości dla **opcji importowania**:

   * Jeśli masz już Centrum zdarzeń w jednej z Twoich subskrypcji wybierz **Użyj Centrum zdarzeń z dostępnych subskrypcji**. Ta opcja jest to najłatwiejsza metoda.

     [![wybrać opcji importowania źródła zdarzeń](media/time-series-insights-how-to-add-an-event-source-eventhub/4-select-an-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/4-select-an-option.png#lightbox)

    *  W poniższej tabeli opisano wymaganych właściwości dla **Użyj Centrum zdarzeń z dostępnych subskrypcji** opcji:

       [![szczegóły subskrypcji i centrum zdarzeń](media/time-series-insights-how-to-add-an-event-source-eventhub/5-create-button.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/5-create-button.png#lightbox)

       | Właściwość | Opis |
       | --- | --- |
       | Subskrypcja | Subskrypcja, do której należy wymagane wystąpienie centrum zdarzeń i przestrzeń nazw. |
       | Przestrzeń nazw centrum zdarzeń | Przestrzeń nazw centrum zdarzeń, do którego należy wystąpienie centrum zdarzeń. |
       | Nazwa centrum zdarzeń | Nazwa żądanego wystąpienia centrum zdarzeń. |
       | Wartość zasad centrum zdarzeń | Wybierz odpowiednie zasady dostępu współdzielonego. Zasady dostępu współdzielonego można utworzyć na karcie **Konfiguracja** centrum zdarzeń. Każda zasada dostępu współdzielonego ma określoną nazwę, uprawnienia oraz klucze dostępu. Zasady dostępu współdzielonego dla źródła zdarzenia *musi* mają **odczytu** uprawnienia. |
       | Klucz zasad centrum zdarzeń | Wstępnie wypełnione z wybranej wartości zasad centrum zdarzeń. |

    * Jeśli Centrum zdarzeń jest zewnętrzne w stosunku do subskrypcji, lub jeśli chcesz wybrać opcje zaawansowane, wybierz **ustawienia Centrum zdarzeń zapewnia ręcznie**.

       W poniższej tabeli opisano wymaganych właściwości dla **ustawienia Centrum zdarzeń zapewnia ręcznie** opcji:
 
       | Właściwość | Opis |
       | --- | --- |
       | Identyfikator subskrypcji | Subskrypcja, do której należy wymagane wystąpienie centrum zdarzeń i przestrzeń nazw. |
       | Grupa zasobów | Grupa zasobów, do której należy wymagane wystąpienie centrum zdarzeń i przestrzeń nazw. |
       | Przestrzeń nazw centrum zdarzeń | Przestrzeń nazw centrum zdarzeń, do którego należy wystąpienie centrum zdarzeń. |
       | Nazwa centrum zdarzeń | Nazwa żądanego wystąpienia centrum zdarzeń. |
       | Wartość zasad centrum zdarzeń | Wybierz odpowiednie zasady dostępu współdzielonego. Zasady dostępu współdzielonego można utworzyć na karcie **Konfiguracja** centrum zdarzeń. Każda zasada dostępu współdzielonego ma określoną nazwę, uprawnienia oraz klucze dostępu. Zasady dostępu współdzielonego dla źródła zdarzenia *musi* mają **odczytu** uprawnienia. |
       | Klucz zasad centrum zdarzeń | Klucz dostępu współdzielonego, który jest używany do uwierzytelniania dostępu do przestrzeni nazw usługi Service Bus. Wprowadź tutaj klucz podstawowy lub pomocniczy. |

    * Obie opcje udostępniają następujące opcje konfiguracji:

       | Właściwość | Opis |
       | --- | --- |
       | Grupa konsumentów centrum zdarzeń | Grupa odbiorców odczytuje zdarzenia z Centrum zdarzeń. Firma Microsoft zdecydowanie zaleca się Użyj dedykowanej grupy klientów dla źródła zdarzenia. |
       | Format serializacji zdarzeń | Obecnie JSON jest format serializacji jedyną dostępną. Komunikaty zdarzeń muszą mieć ten format lub nie można odczytać danych. |
       | Nazwa właściwości sygnatury czasowej | Aby określić tę wartość, należy zrozumieć format wiadomości danych komunikatów, które są wysyłane do Centrum zdarzeń. Ta wartość jest **nazwa** właściwości określone zdarzenie w danych wiadomości, które chcesz użyć jako sygnatura czasowa zdarzenia. Wartość jest rozróżniana wielkość liter. Jeśli pole pozostanie puste, **czas umieścić w kolejce zdarzenia** w zdarzeniu źródłowego jest używana jako sygnatura czasowa zdarzenia. |

1. Dodanie dedykowane usługi Time Series Insights konsumenta grupy nazwy dodanego do Centrum zdarzeń.

1. Wybierz pozycję **Utwórz**.

   Po utworzeniu źródła zdarzeń Time Series Insights automatycznie rozpoczyna przesyłanie strumieniowe danych do środowiska.

## <a name="next-steps"></a>Następne kroki

* [Zdefiniuj zasady dostępu do danych](time-series-insights-data-access.md) do zabezpieczania danych.

* [Wysyłanie zdarzeń](time-series-insights-send-events.md) do źródła zdarzenia.

* Dostęp do środowiska w [Eksploratora usługi Time Series Insights](https://insights.timeseries.azure.com).
