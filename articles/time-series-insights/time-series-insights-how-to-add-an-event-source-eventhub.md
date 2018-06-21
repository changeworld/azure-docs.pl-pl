---
title: Jak dodać źródła zdarzenia Centrum zdarzeń do szczegółowych danych serii czasu Azure | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób dodawania źródła zdarzenia podłączoną do Centrum zdarzeń w danym środowisku Insights serii czasu.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: edett
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/21/2017
ms.openlocfilehash: 780a7cb3035dbe19c45b5fe9c6dfae54fccafd03
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/20/2018
ms.locfileid: "36293652"
---
# <a name="how-to-add-an-event-hub-event-source-to-time-series-insights-environment"></a>Jak dodać źródła zdarzenia Centrum zdarzeń do środowiska Insights serii czasu

W tym artykule opisano, jak przy użyciu portalu Azure Dodaj źródło zdarzenia, które odczytuje dane z Centrum zdarzeń do środowiska Insights serii czasu.

## <a name="prerequisites"></a>Wymagania wstępne
- Utwórz środowisko Insights serii czasu. Aby uzyskać więcej informacji, zobacz [utworzyć środowiska Azure czas serii Insights](time-series-insights-get-started.md) 
- Tworzenie centrum zdarzeń. Aby uzyskać więcej informacji dotyczących usługi Event Hubs, zobacz [tworzenie przestrzeni nazw usługi Event Hubs i Centrum zdarzeń za pomocą portalu Azure](../event-hubs/event-hubs-create.md)
- Zdarzenia aktywne wiadomości wysyłane podczas musi Centrum zdarzeń. Aby uzyskać więcej informacji, zobacz [wysyłania zdarzeń do usługi Azure Event Hubs przy użyciu programu .NET Framework](../event-hubs/event-hubs-dotnet-framework-getstarted-send.md).
- Utwórz dedykowanej grupy klientów w Centrum zdarzeń środowiska szczegółowe informacje o czasie serii, aby korzystać z. Każdego źródła zdarzeń Insights serii czasu musi mieć własną dedykowanej grupy klientów, które nie są współużytkowane z innym klientom. Jeśli wielu czytników korzystanie ze zdarzeń z tej samej grupy konsumentów, wszystkich czytelników prawdopodobnie błędy. Należy pamiętać, że jest również maksymalnie 20 grup odbiorców, na Centrum zdarzeń. Aby uzyskać więcej informacji, zobacz [Event Hubs Programming Guide](../event-hubs/event-hubs-programming-guide.md).

## <a name="add-a-new-event-source"></a>Dodaj nowe źródło zdarzeń
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Znajdź istniejące środowisko Insights serii czasu. Kliknij przycisk **wszystkie zasoby** w menu po lewej stronie portalu Azure. Wybierz środowisko usługi Time Series Insights.

3. W obszarze **topologia środowiska** kliknij pozycję **źródła zdarzeń**.

   ![Zdarzenie źródeł + Dodaj](media/time-series-insights-how-to-add-an-event-source-eventhub/1-event-sources.png)

4. Kliknij pozycję **+ Dodaj**.

5. Podaj **nazwa źródła zdarzenia** unikatowe dla tego środowiska Insights serii czasu, takich jak **strumienia zdarzeń**.

   ![Wypełnij pierwsze trzy parametry w formularzu.](media/time-series-insights-how-to-add-an-event-source-eventhub/2-import-option.png)

6. Wybierz **źródła** jako **Centrum zdarzeń**.

7. Wybierz odpowiednie **opcji importowania**. 
   - Wybierz **Użyj Centrum zdarzeń z dostępnych subskrypcji** gdy masz już istniejącą Centrum zdarzeń na jednym z Twoich subskrypcji. Jest to najprostszy podejście.
   - Wybierz **ustawień Centrum zdarzeń Podaj ręcznie** Jeśli Centrum zdarzeń jest zewnętrzne względem Twojej subskrypcji, lub chcesz wybrać opcje zaawansowane. 

8. Jeśli wybrano **Użyj Centrum zdarzeń z dostępnych subskrypcji** opcji w poniższej tabeli opisano każdy wymaganej właściwości:

   ![Szczegóły Centrum subskrypcji i zdarzeń](media/time-series-insights-how-to-add-an-event-source-eventhub/3-new-event-source.png)

   | Właściwość | Opis |
   | --- | --- |
   | Identyfikator subskrypcji | Wybierz subskrypcję, w której utworzono to Centrum zdarzeń.
   | Przestrzeń nazw magistrali usług | Wybierz obszar nazw usługi Service Bus, który zawiera Centrum zdarzeń.
   | Nazwa Centrum zdarzeń | Wybierz nazwę Centrum zdarzeń.
   | Nazwa zasad Centrum zdarzeń | Wybierz zasady dostępu współdzielonego, można utworzyć na karcie Konfigurowanie Centrum zdarzeń. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia ustawić i klucze dostępu. Zasady dostępu współdzielonego dla źródła zdarzeń *musi* ma **odczytu** uprawnienia.
   | Klucz zasad Centrum zdarzeń | Wartość klucza może być wstępnie.
   | Grupy konsumentów Centrum zdarzeń | Grupy odbiorców, które mają być odczytywane zdarzenia z Centrum zdarzeń. Zdecydowanie zaleca się używania dedykowanej grupy klientów dla źródła zdarzeń. |
   | Format serializacji zdarzeń | Kod JSON jest dostępne tylko serializacji w chwili obecnej. Komunikaty zdarzeń musi być w następującym formacie, lub żadne dane nie mogą być odczytywane. |
   | Nazwa właściwości sygnatury czasowej | Aby określić tę wartość, należy zrozumieć format komunikatu z danymi wiadomości wysyłane do Centrum zdarzeń. Ta wartość jest **nazwa** właściwości określonego zdarzenia w danych wiadomości, który ma być używany jako znacznik czasu zdarzeń. Wartość jest rozróżniana wielkość liter. Jeśli pole pozostanie puste, **czasu umieścić w kolejce zdarzenia** w zdarzeniu źródła jest używany jako znacznik czasu zdarzeń. |


9. Jeśli wybrano **ustawień Centrum zdarzeń Podaj ręcznie** opcji w poniższej tabeli opisano każdy wymaganej właściwości:

   | Właściwość | Opis |
   | --- | --- |
   | Identyfikator subskrypcji | Subskrypcja, w której utworzono to Centrum zdarzeń.
   | Grupa zasobów | Grupy zasobów, w której utworzono to Centrum zdarzeń.
   | Przestrzeń nazw magistrali usług | Przestrzeń nazw magistrali usług to kontener dla zestawu jednostek do obsługi komunikatów. Podczas tworzenia nowego Centrum zdarzeń utworzonego również przestrzeni nazw usługi Service Bus.
   | Nazwa Centrum zdarzeń | Nazwa Centrum zdarzeń. Podczas tworzenia Centrum zdarzeń należy też nadać mu nazwę.
   | Nazwa zasad Centrum zdarzeń | Zasady dostępu współdzielonego, można utworzyć na karcie Konfigurowanie Centrum zdarzeń. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia ustawić i klucze dostępu. Zasady dostępu współdzielonego dla źródła zdarzeń *musi* ma **odczytu** uprawnienia.
   | Klucz zasad Centrum zdarzeń | Klucz dostępu współużytkowanego używany do uwierzytelniania dostępu do przestrzeni nazw usługi Service Bus. Wpisz tutaj klucz podstawowy lub pomocniczy.
   | Grupy konsumentów Centrum zdarzeń | Grupy odbiorców, które mają być odczytywane zdarzenia z Centrum zdarzeń. Zdecydowanie zaleca się używania dedykowanej grupy klientów dla źródła zdarzeń.
   | Format serializacji zdarzeń | Kod JSON jest dostępne tylko serializacji w chwili obecnej. Komunikaty zdarzeń musi być w następującym formacie, lub żadne dane nie mogą być odczytywane. |
   | Nazwa właściwości sygnatury czasowej | Aby określić tę wartość, należy zrozumieć format komunikatu z danymi wiadomości wysyłane do Centrum zdarzeń. Ta wartość jest **nazwa** właściwości określonego zdarzenia w danych wiadomości, który ma być używany jako znacznik czasu zdarzeń. Wartość jest rozróżniana wielkość liter. Jeśli pole pozostanie puste, **czasu umieścić w kolejce zdarzenia** w zdarzeniu źródła jest używany jako znacznik czasu zdarzeń. |


10. Wybierz **Utwórz** można dodać nowego źródła zdarzenia.
   
   ![Kliknięcie pozycji Utwórz](media/time-series-insights-how-to-add-an-event-source-eventhub/4-create-button.png)

   Po utworzeniu źródła zdarzeń usługa Time Series Insights automatycznie rozpocznie przesyłanie strumieni danych do środowiska.


### <a name="add-a-consumer-group-to-your-event-hub"></a>Dodaj grupy odbiorców do Centrum zdarzeń
Grupy konsumentów są używane przez aplikacje w celu pobierania danych z usługi Azure Event Hubs. Podaj dedykowanej grupy klientów, na potrzeby używania przez tego czasu serii Insights środowiska, niezawodnie odczytać danych z Centrum zdarzeń.

Aby dodać nowe grupy odbiorców w Centrum zdarzeń, wykonaj następujące kroki:
1. W portalu Azure zlokalizuj i Otwórz Centrum zdarzeń.

2. W obszarze **jednostek** nagłówek, wybierz **grupy konsumentów**.

   ![Centrum zdarzeń — Dodawanie grupy odbiorców](media/time-series-insights-how-to-add-an-event-source-eventhub/5-event-hub-consumer-group.png)

3. Wybierz **+ grupy odbiorców** Aby dodać nowe grupy odbiorców. 

4. Na **grupy konsumentów** Podaj nowy unikatowy **nazwa**.  Podczas tworzenia nowego źródła zdarzeń w środowisku czasu serii wgląd, należy użyć tej samej nazwie.

5. Wybierz **Utwórz** do tworzenia nowej grupy odbiorców.

## <a name="next-steps"></a>Kolejne kroki
- [Definiowanie zasad dostępu danych](time-series-insights-data-access.md) do zabezpieczania danych.
- [Wysyłanie zdarzeń](time-series-insights-send-events.md) w źródle zdarzeń.
- Dostęp do środowiska w [Eksplorator czasu serii Insights](https://insights.timeseries.azure.com).
