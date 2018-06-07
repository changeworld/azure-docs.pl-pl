---
title: Tworzenie środowiska usługi Azure Time Series Insights | Microsoft Docs
description: W tym artykule opisano, jak używać portalu Azure do tworzenia nowego środowiska Insights serii czasu.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: jhubbard
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/15/2017
ms.openlocfilehash: ef5c194aa462a83cd982adab0a818f0aa095ffa0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654440"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Tworzenie nowego środowiska usługi Time Series Insights w witrynie Azure Portal
W tym artykule opisano sposób tworzenia nowego środowiska Insights serii czasu przy użyciu portalu Azure.

Czas serii Insights umożliwia rozpocząć wizualizowanie i badanie danych otrzymywanych przez centra IoT Azure i usługi Event Hubs w minutach, umożliwiając sobie zapytania dużych ilości danych w serii. czas w sekundach.  Została zaprojektowana w skali Internetu rzeczy (IoT), a może obsłużyć terabajtów danych.

## <a name="steps-to-create-the-environment"></a>Procedura tworzenia środowiska
Wykonaj następujące kroki, aby utworzyć środowisko:

1.  Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

2.  Wybierz **+ nowy** przycisku.

3.  Wybierz **Internetu rzeczy** kategorii, a następnie wybierz **Insights serii czas**.

   ![Tworzenie środowiska usługi Time Series Insights](media/time-series-insights-get-started/1-new-tsi.png)

4.  Na **czasu serii Insights** wybierz pozycję **Utwórz**.

5. Wypełnij wymaganych parametrów. W poniższej tabeli opisano każdy parametr:
   
   ![Tworzenie grupy zasobów usługi Time Series Insights](media/time-series-insights-get-started/2-create-tsi.png)
   
   Ustawienie|Sugerowana wartość|Opis
   ---|---|---
   Nazwa środowiska | Unikatowa nazwa | Ta nazwa reprezentuje środowiska w [Eksploratorze serię czasu](https://insights.timeseries.azure.com)
   Subskrypcja | Twoja subskrypcja | Jeśli masz wiele subskrypcji, wybierz subskrypcję, która najlepiej zawiera źródła zdarzenia. Usługa Time Series Insights może automatycznie wykrywać zasoby usługi Azure IoT Hub i centrum zdarzeń istniejące w tej samej subskrypcji.
   Grupa zasobów | Utwórz nowy lub użyj istniejących | Grupa zasobów jest kolekcją używanych razem zasobów platformy Azure. Można wybrać istniejącą grupę zasobów, na przykład ten, który zawiera Centrum zdarzeń lub Centrum IoT. Lub utworzyć nowy, jeśli ten zasób nie jest powiązana z innych zasobów.
   Lokalizacja | Najbliższej źródła zdarzeń | Najlepiej wybrać tej samej lokalizacji centrum danych, zawierający dane źródłowe zdarzeń w celu uniknięcia dodany kosztów przepustowości między regionu i między strefy i dodany czas oczekiwania, podczas przenoszenia danych poza region.
   Warstwa cenowa | S1 | Wybierz przepustowość potrzebne. Najniższych kosztów i wydajności starter wybierz S1.
   Pojemność | 1 | Pojemność jest mnożnik dotyczy prędkości wejściowej, pojemności, kosztów związanych z wybranej jednostki SKU.  Wydajność środowiska można zmienić po jego utworzeniu. Wybierz o pojemności 1 najniższych kosztów. 
  
6. Sprawdź **Przypnij do pulpitu nawigacyjnego** najlepiej łatwego dostępu do środowiska serii czasu w przyszłości.

   ![Przypinanie usługi Time Series Insights do pulpitu nawigacyjnego](media/time-series-insights-get-started/3-pin-create.png)

7. Wybierz **Utwórz** aby rozpocząć proces inicjowania obsługi administracyjnej. Może upłynąć kilka minut.

8. Aby monitorować proces wdrażania, wybierz **powiadomienia** symbolu (ikonę dzwonka).

   ![Obejrzyj powiadomień](media/time-series-insights-get-started/4-notifications.png)

Jeśli wdrożenie zakończy się powodzeniem, można wybrać **przejdź do zasobu** można skonfigurować inne właściwości, ustawienia zabezpieczeń, używając danych zasad dostępu, dodać źródła zdarzeń, a także innych działaniach.

## <a name="next-steps"></a>Kolejne kroki
* [Definiowanie zasad dostępu danych](time-series-insights-data-access.md) do zabezpieczania środowiska.
* [Dodawanie źródła zdarzeń Centrum zdarzeń](time-series-insights-how-to-add-an-event-source-eventhub.md) do środowiska Azure czas serii Insights. 
* [Wysyłanie zdarzeń](time-series-insights-send-events.md) w źródle zdarzeń.
* Wyświetl środowiska w [Eksplorator czasu serii Insights](https://insights.timeseries.azure.com).
