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
ms.date: 12/02/2019
ms.custom: seodec18
ms.openlocfilehash: c8f123871f1e87a18dadfa82ad6bb27d1c145dc4
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75863346"
---
# <a name="add-an-event-hub-event-source-to-your-time-series-insights-environment"></a>Dodawanie źródła zdarzeń Centrum zdarzeń do środowiska usługi Time Series Insights

W tym artykule opisano sposób dodawania źródła zdarzeń, która odczytuje dane z usługi Azure Event Hubs do środowiska usługi Azure Time Series Insights za pomocą witryny Azure portal.

> [!NOTE]
> Kroki opisane w tym artykule dotyczą zarówno środowiska Time Series Insights w wersji zapoznawczej, jak i Time Series Insights.

## <a name="prerequisites"></a>Wymagania wstępne

- Utwórz środowisko Time Series Insights, zgodnie z opisem w artykule [Tworzenie środowiska Azure Time Series Insights](./time-series-insights-update-create-environment.md).
- Utwórz centrum zdarzeń. Przeczytaj temat [Tworzenie przestrzeni nazw Event Hubs i centrum zdarzeń przy użyciu Azure Portal](../event-hubs/event-hubs-create.md).
- Centrum zdarzeń musi mieć zdarzenia aktywne wiadomości wysyłane do niej. Dowiedz się [, jak wysyłać zdarzenia do usługi Azure Event Hubs przy użyciu .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Utwórz grupę odbiorców dedykowanych w Centrum zdarzeń, które korzystają ze środowiska usługi Time Series Insights. Każdego źródła zdarzeń usługi Time Series Insights musi mieć swój własny dedykowanej grupy klientów, które nie zostały udostępnione innych odbiorców. Jeśli wielu czytników zużywa zdarzenia z tej samej grupy odbiorców, wszystkie czytelnicy mogą wykazywać błędy. Obowiązuje limit 20 grup odbiorców dla Centrum zdarzeń. Aby uzyskać szczegółowe informacje, Przeczytaj [Przewodnik programowania w Event Hubs](../event-hubs/event-hubs-programming-guide.md).

### <a name="add-a-consumer-group-to-your-event-hub"></a>Dodaj grupę odbiorców do Centrum zdarzeń

Aplikacje używać grupy odbiorców do pobierania danych z usługi Azure Event Hubs. Aby niezawodnie odczytywać dane z centrum zdarzeń, należy udostępnić dedykowaną grupę użytkowników, która jest używana tylko przez to Time Series Insights środowisku.

Aby dodać nową grupę konsumentów Centrum zdarzeń:

1. W [Azure Portal](https://portal.azure.com)zlokalizuj i Otwórz wystąpienie centrum zdarzeń w okienku **Przegląd** obszaru nazw centrum zdarzeń. Wybierz **jednostki > Event Hubs** lub Znajdź wystąpienie w polu **Nazwa**.

    [![otworzyć przestrzeni nazw centrum zdarzeń](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-connect-event-hub-namespace.png#lightbox)

1. W wystąpieniu centrum zdarzeń wybierz pozycję **jednostki > grupy konsumentów**. Następnie wybierz pozycję **+ Grupa konsumentów** , aby dodać nową grupę odbiorców. 

   [![centrum zdarzeń — Dodaj grupę odbiorców](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/add-event-hub-consumer-group.png#lightbox)

   W przeciwnym razie wybierz istniejącą grupę odbiorców i przejdź do następnej sekcji.

1. Na **grupy konsumentów** wpisz nową unikatową wartość dla **nazwa**.  Podczas tworzenia nowego źródła zdarzeń w środowisku usługi Time Series Insights, należy użyć tej samej nazwy.

1. Wybierz pozycję **Utwórz**.

## <a name="add-a-new-event-source"></a>Dodaj nowe źródło zdarzeń

1. Zaloguj się do [portalu Azure](https://portal.azure.com).

1. Znajdź istniejącego środowiska usługi Time Series Insights. W menu po lewej stronie wybierz **wszystkie zasoby**, a następnie wybierz środowisko usługi Time Series Insights.

1. Wybierz pozycję **źródła zdarzeń**, a następnie wybierz pozycję **Dodaj**.

   [![w obszarze źródła zdarzeń wybierz przycisk Dodaj](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-add-an-event-source.png#lightbox)

1. Wprowadź wartość dla **nazwy źródła zdarzenia** , która jest unikatowa dla tego środowiska Time Series Insights, na przykład `Contoso-TSI-GA-Event-Hub-ES`.

1. Aby uzyskać **źródła**, wybierz opcję **Centrum zdarzeń**.

1. Wybierz odpowiednie wartości dla **opcji importowania**:

   * Jeśli masz już Centrum zdarzeń w jednej z Twoich subskrypcji wybierz **Użyj Centrum zdarzeń z dostępnych subskrypcji**. Ta opcja jest to najłatwiejsza metoda.

     [![wybrać opcji importowania źródła zdarzeń](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-event-hub-select-import-option.png#lightbox)

    *  W poniższej tabeli opisano wymaganych właściwości dla **Użyj Centrum zdarzeń z dostępnych subskrypcji** opcji:

       [![szczegóły subskrypcji i centrum zdarzeń](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png)](media/time-series-insights-how-to-add-an-event-source-eventhub/tsi-configure-create-confirm.png#lightbox)

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
