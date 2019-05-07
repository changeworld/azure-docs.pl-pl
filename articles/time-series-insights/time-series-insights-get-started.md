---
title: Tworzenie środowiska usługi Azure Time Series Insights | Microsoft Docs
description: W tym artykule opisano sposób użycia witryny Azure portal do utworzenia nowego środowiska usługi Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 05/01/2019
ms.custom: seodec18
ms.openlocfilehash: d6e38465b46f387092ab457ebe6716a3dcff4768
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/06/2019
ms.locfileid: "65205860"
---
# <a name="create-a-new-time-series-insights-environment-in-the-azure-portal"></a>Tworzenie nowego środowiska usługi Time Series Insights w witrynie Azure Portal

W tym artykule opisano sposób tworzenia nowego środowiska usługi Time Series Insights przy użyciu witryny Azure portal.

Usługa Time Series Insights można rozpocząć wizualizowanie i wykonywania zapytań względem danych otrzymywanych przez Azure IoT Hub i Event Hubs w ciągu kilku minut, umożliwiając zapytania dużych ilości danych szeregów czasowych w ciągu kilku sekund.  On został zaprojektowany w skali Internetu rzeczy (IoT) i może obsługiwać terabajty danych.

## <a name="steps-to-create-the-environment"></a>Procedura tworzenia środowiska

Wykonaj następujące kroki, aby utworzyć środowisko:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Wybierz **+ nowy** przycisku.

1. Wybierz **Internet of Things** kategorii, a następnie wybierz **usługi Time Series Insights**.

   ![Tworzenie środowiska usługi Time Series Insights](media/time-series-insights-get-started/1-new-tsi.png)

1. Na **usługi Time Series Insights** wybierz opcję **Utwórz**.

1. Wypełnij wymagane parametry. W poniższej tabeli opisano każdy parametr:
   
   ![Tworzenie grupy zasobów usługi Time Series Insights](media/time-series-insights-get-started/2-create-tsi.png)
   
   Ustawienie|Sugerowana wartość|Opis
   ---|---|---
   Nazwa środowiska | Unikatowa nazwa | Ta nazwa reprezentuje środowiska w [Eksploratorze szeregów czasowych](https://insights.timeseries.azure.com)
   Subskrypcja | Twoja subskrypcja | Jeśli masz wiele subskrypcji, wybierz subskrypcję, która najlepiej zawiera źródło zdarzeń. Usługa Time Series Insights może automatycznie wykrywać zasoby usługi Azure IoT Hub i centrum zdarzeń istniejące w tej samej subskrypcji.
   Grupa zasobów | Utwórz nową lub użyj istniejącej | Grupa zasobów jest kolekcją używanych razem zasobów platformy Azure. Można wybrać istniejącą grupę zasobów, na przykład ten, który zawiera usługi IoT Hub lub Centrum zdarzeń. Lub utworzyć nowe konto, jeśli ten zasób nie jest powiązana z innych zasobów.
   Lokalizacja | Najbliżej źródła zdarzeń | Najlepiej wybrać tej samej lokalizacji centrum danych, zawierający dane źródłowe zdarzeń starań w celu uniknięcia dodana kosztów przepustowości między regionami i międzystrefowego a opóźnienie podczas przenoszenia danych z regionu.
   Warstwa cenowa | S1 | Wybierz potrzebną przepustowość. Najniższe koszty i pojemność starter wybierz pozycję S1.
   Pojemność | 1 | Pojemność jest mnożnik odnosi się do szybkości transferu danych przychodzących, pojemność i koszt związany z wybrana jednostka SKU.  Wydajność środowiska można zmienić po jego utworzeniu. W przypadku najniższe koszty wybrać o pojemności 1. 
  
1. Sprawdź **Przypnij do pulpitu nawigacyjnego** najlepiej łatwo uzyskać dostęp do Twojego środowiska usługi Time Series w przyszłości.

   ![Przypinanie usługi Time Series Insights do pulpitu nawigacyjnego](media/time-series-insights-get-started/3-pin-create.png)

1. Wybierz **Utwórz** aby rozpocząć proces inicjowania obsługi administracyjnej. Może upłynąć kilka minut.

1. Aby monitorować proces wdrażania, wybierz **powiadomienia** symbol (ikonę dzwonka).

   ![Obejrzyj powiadomienia](media/time-series-insights-get-started/4-notifications.png)

Po pomyślnym zakończeniu wdrożenia można wybrać **przejdź do zasobu** Aby skonfigurować inne właściwości, ustawienia zabezpieczeń z zasadami dostępu do danych, Dodaj źródła zdarzeń, a także innych działaniach.

## <a name="next-steps"></a>Kolejne kroki

* [Zdefiniuj zasady dostępu do danych](time-series-insights-data-access.md) na zabezpieczenie środowiska.

* [Dodawanie źródła zdarzeń Centrum zdarzeń](time-series-insights-how-to-add-an-event-source-eventhub.md) do środowiska usługi Azure Time Series Insights.

* [Wysyłanie zdarzeń](time-series-insights-send-events.md) do źródła zdarzenia.

* Wyświetlanie środowiska w [Eksploratora usługi Time Series Insights](https://insights.timeseries.azure.com).
