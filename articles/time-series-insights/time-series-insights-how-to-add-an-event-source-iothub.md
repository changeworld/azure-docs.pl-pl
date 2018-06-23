---
title: Jak dodać źródła zdarzenia Centrum IoT Azure czas serii insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób dodawania źródła zdarzenia połączoną z Centrum IoT w danym środowisku Insights serii czasu
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: edett
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/21/2017
ms.openlocfilehash: 1cc8518e84bd9fe7a1f03a2f5d6ccdbac8fb78e3
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/23/2018
ms.locfileid: "36330598"
---
# <a name="how-to-add-an-iot-hub-event-source-to-time-series-insights-environment"></a>Jak dodać źródła zdarzenia Centrum IoT środowiska Insights serii czasu
W tym artykule opisano, jak przy użyciu portalu Azure Dodaj źródło zdarzenia, które odczytuje dane z Centrum IoT w środowisku Insights serii czasu.

## <a name="prerequisites"></a>Wymagania wstępne
- Utwórz środowisko Insights serii czasu. Aby uzyskać więcej informacji, zobacz [utworzyć środowiska Azure czas serii Insights](time-series-insights-get-started.md) 
- Tworzenie Centrum IoT. Aby uzyskać więcej informacji o centra IoT, zobacz [tworzenia Centrum IoT przy użyciu portalu Azure](../iot-hub/iot-hub-create-through-portal.md)
- Centrum IoT musi wysyłane podczas zdarzenia aktywne wiadomości.
- Utwórz dedykowanej grupy klientów w Centrum IoT dla środowiska szczegółowe informacje o czasie serii korzystać z. Każdego źródła zdarzeń Insights serii czasu musi mieć własną dedykowanej grupy klientów, które nie są współużytkowane z innym klientom. Jeśli wielu czytników korzystanie ze zdarzeń z tej samej grupy konsumentów, wszystkich czytelników prawdopodobnie błędy. Aby uzyskać więcej informacji, zobacz [Centrum IoT — przewodnik dewelopera](../iot-hub/iot-hub-devguide.md).

### <a name="add-a-consumer-group-to-your-iot-hub"></a>Dodaj grupy odbiorców do Centrum IoT
Grupy konsumentów są używane przez aplikacje w celu pobierania danych z usługą centra IoT Azure. Podaj dedykowanej grupy klientów, na potrzeby używania przez tego czasu serii Insights środowiska, niezawodnie odczytać danych z Centrum IoT.

Aby dodać nowe grupy odbiorców do Centrum IoT, wykonaj następujące kroki:
1. W portalu Azure zlokalizuj i Otwórz Centrum IoT.

2. W obszarze **wiadomości** nagłówek, wybierz **punkty końcowe**. 

   ![Dodaj grupy odbiorców](media/time-series-insights-how-to-add-an-event-source-iothub/5-add-consumer-group.png)

3. Wybierz **zdarzenia** punktu końcowego, a **właściwości** zostanie otwarta strona.

4. W obszarze **grupy konsumentów** nagłówek, podaj nową nazwę unikatową dla grupy odbiorców. Użyj tej samej nazwie w środowisku Insights serii czasu podczas tworzenia nowego źródła zdarzenia.

5. Wybierz **zapisać** Aby zapisać nową grupę odbiorców.

## <a name="add-a-new-event-source"></a>Dodaj nowe źródło zdarzeń
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2. Znajdź istniejące środowisko Insights serii czasu. Kliknij przycisk **wszystkie zasoby** w menu po lewej stronie portalu Azure. Wybierz środowisko usługi Time Series Insights.

3. W obszarze **topologia środowiska** kliknij pozycję **źródła zdarzeń**.
   ![Zdarzenie źródeł + Dodaj](media/time-series-insights-how-to-add-an-event-source-iothub/1-event-sources.png)

4. Kliknij pozycję **+ Dodaj**.

5. Podaj **nazwa źródła zdarzenia** unikatowe dla tego środowiska Insights serii czasu, takich jak **strumienia zdarzeń**.

   ![Wypełnij pierwsze trzy parametry w formularzu.](media/time-series-insights-how-to-add-an-event-source-iothub/2-import-option.png)

6. Wybierz **źródła** jako **Centrum IoT**.

7. Wybierz odpowiednie **opcji importowania**. 
   - Wybierz **Użyj Centrum IoT z dostępnych subskrypcji** gdy masz już istniejącą Centrum IoT na jednym z Twoich subskrypcji. Jest to najprostszy podejście.
   - Wybierz **ustawienia Centrum IoT Podaj ręcznie** po Centrum IoT to zewnętrznych do subskrypcji lub chcesz wybrać opcje zaawansowane. 

8. Jeśli wybrano **Użyj Centrum IoT z dostępnych subskrypcji** opcji w poniższej tabeli opisano każdy wymaganej właściwości:

   ![Szczegóły Centrum subskrypcji i zdarzeń](media/time-series-insights-how-to-add-an-event-source-iothub/3-new-event-source.png)

   | Właściwość | Opis |
   | --- | --- |
   | Identyfikator subskrypcji | Wybierz subskrypcję, w której utworzono to Centrum IoT.
   | Nazwa centrum IoT | Wybierz nazwę Centrum IoT.
   | Nazwa zasad Centrum IoT | Wybierz zasady dostępu współdzielonego, który można znaleźć na karcie Ustawienia Centrum IoT. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia ustawić i klucze dostępu. Zasady dostępu współdzielonego dla źródła zdarzeń *musi* ma **usługa połączyć** uprawnienia.
   | Klucz zasad Centrum IoT | Klucz jest wypełniana wstępnie.
   | Grupy odbiorców Centrum IoT | Grupy odbiorców, które mają być odczytywane zdarzenia z Centrum IoT. Zdecydowanie zaleca się używania dedykowanej grupy klientów dla źródła zdarzeń.
   | Format serializacji zdarzeń | Kod JSON jest dostępne tylko serializacji w chwili obecnej. Komunikaty zdarzeń musi być w następującym formacie, lub żadne dane nie mogą być odczytywane. |
   | Nazwa właściwości sygnatury czasowej | Aby określić tę wartość, należy zrozumieć format komunikatu z danymi wiadomości wysyłane do Centrum IoT. Ta wartość jest **nazwa** właściwości określonego zdarzenia w danych wiadomości, który ma być używany jako znacznik czasu zdarzeń. Wartość jest rozróżniana wielkość liter. Jeśli pole pozostanie puste, **czasu umieścić w kolejce zdarzenia** w zdarzeniu źródła jest używany jako znacznik czasu zdarzeń. |

9. Jeśli wybrano **ustawienia Centrum IoT Podaj ręcznie** opcji w poniższej tabeli opisano każdy wymaganej właściwości:

   | Właściwość | Opis |
   | --- | --- |
   | Identyfikator subskrypcji | Subskrypcja, w której ta utworzono Centrum IoT.
   | Grupa zasobów | Nazwa grupy zasobów, w której ta utworzono Centrum IoT.
   | Nazwa centrum IoT | Nazwa centrum IoT. Podczas tworzenia Centrum IoT należy też nadać mu nazwę.
   | Nazwa zasad Centrum IoT | Zasady dostępu współdzielonego, można utworzyć na karcie Ustawienia Centrum IoT. Wszystkie zasady dostępu współdzielonego ma nazwę uprawnienia ustawić i klucze dostępu. Zasady dostępu współdzielonego dla źródła zdarzeń *musi* ma **usługa połączyć** uprawnienia.
   | Klucz zasad Centrum IoT | Klucz dostępu współdzielonego, używany do uwierzytelniania dostępu do przestrzeni nazw usługi Service Bus. Wpisz tutaj klucz podstawowy lub pomocniczy.
   | Grupy odbiorców Centrum IoT | Grupy odbiorców, które mają być odczytywane zdarzenia z Centrum IoT. Zdecydowanie zaleca się używania dedykowanej grupy klientów dla źródła zdarzeń.
   | Format serializacji zdarzeń | Kod JSON jest dostępne tylko serializacji w chwili obecnej. Komunikaty zdarzeń musi być w następującym formacie, lub żadne dane nie mogą być odczytywane. |
   | Nazwa właściwości sygnatury czasowej | Aby określić tę wartość, należy zrozumieć format komunikatu z danymi wiadomości wysyłane do Centrum IoT. Ta wartość jest **nazwa** właściwości określonego zdarzenia w danych wiadomości, który ma być używany jako znacznik czasu zdarzeń. Wartość jest rozróżniana wielkość liter. Jeśli pole pozostanie puste, **czasu umieścić w kolejce zdarzenia** w zdarzeniu źródła jest używany jako znacznik czasu zdarzeń. |

10. Dodaj dedykowane TSI konsumenta nazwę grupy dodane do Centrum IoT.

11. Wybierz **Utwórz** można dodać nowego źródła zdarzenia.

   ![Kliknięcie pozycji Utwórz](media/time-series-insights-how-to-add-an-event-source-iothub/4-create-button.png)

   Po utworzeniu źródła zdarzeń usługa Time Series Insights automatycznie rozpocznie przesyłanie strumieni danych do środowiska.

## <a name="next-steps"></a>Kolejne kroki
- [Definiowanie zasad dostępu danych](time-series-insights-data-access.md) do zabezpieczania danych.
- [Wysyłanie zdarzeń](time-series-insights-send-events.md) w źródle zdarzeń.
- Dostęp do środowiska w [Eksplorator czasu serii Insights](https://insights.timeseries.azure.com).
