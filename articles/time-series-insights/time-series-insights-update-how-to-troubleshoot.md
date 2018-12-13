---
title: Diagnozowanie i rozwiązywanie problemów z usługi Azure Time Series Insights (wersja zapoznawcza) | Dokumentacja firmy Microsoft
description: Zrozumienie sposobu diagnozowanie i rozwiązywanie problemów przy użyciu usługi Azure Time Series Insights (wersja zapoznawcza)
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 4fdb7cf0007af5f92794ebe5f616c1c8a28af0e4
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2018
ms.locfileid: "53099667"
---
# <a name="how-to-diagnose-and-troubleshoot"></a>Sposób diagnozowania i rozwiązywania problemów

Ten artykuł zawiera podsumowanie kilka typowych problemów, które można napotkać pracy ze środowiskiem usługi Azure Time Series Insights (TSI). Tego artykułu opisano możliwe przyczyny i potencjalne rozwiązania dla każdego.

## <a name="problem-i-cant-find-my-environment-in-the-time-series-insights-preview-explorer"></a>Problem: nie mogę znaleźć moje środowisko w Eksploratorze usługi Time Series Insights (wersja zapoznawcza)

Może to nastąpić, jeśli nie masz uprawnień, aby uzyskiwać dostęp do środowiska usługi TSI. Użytkownicy potrzebują "Czytelnik" dostępu na poziomie roli, aby wyświetlić swoje środowisko usługi TSI. Możesz sprawdzić bieżące poziomy dostępu i udostępnienia dodatkowych, odwiedzając sekcję zasady dostępu do danych na zasób TSI w [witryny Azure Portal](https://portal.azure.com/).

  ![environment][1]

## <a name="problem-no-data-is-seen-in-the-time-series-insights-preview-explorer"></a>Problem: Żadne dane nie są widoczne w Eksploratorze usługi Time Series Insights (wersja zapoznawcza)

Istnieje kilka przyczyn typowych, dlaczego mogą nie być wyświetlane dane w [Eksplorator usługi Azure TSI (wersja zapoznawcza)](https://insights.timeseries.azure.com/preview):

1. Źródło zdarzeń nie może odbierać dane.

    Sprawdź, czy źródło zdarzeń (Centrum zdarzeń lub usługi IoT Hub) otrzymuje dane ze znaczników / wystąpień. Aby to zrobić, przechodząc do strony Przegląd zasobu w witrynie Azure portal.

    ![pulpit nawigacyjny analizy][2]

1. Źródło danych zdarzeń nie jest w formacie JSON

    Usługa Azure TSI obsługuje tylko dane JSON. Aby uzyskać przykłady kodu JSON, zobacz [kształty JSON obsługiwany](./how-to-shape-query-json.md).

1. Klucz źródła zdarzeń nie ma wymaganych uprawnień

    * Dla usługi IoT Hub musisz podać klucz, który ma uprawnienia do połączenia z usługi.

    ![konfiguracja][3]

    * Jak pokazano na poprzedniej ilustracji albo zasady *iothubowner* i usługa będzie działać, ponieważ mają uprawnienie połączenie z usługą.
    * Centrum zdarzeń musisz podać klucz, który ma uprawnienia do nasłuchiwania.
  
    ![uprawnienia][4]

    * Pokazany na wcześniejszej ilustracji, albo zasady **odczytu** i **zarządzanie** będzie działać, ponieważ mają **nasłuchiwania** uprawnień.

1. Podane grupy odbiorców nie jest zarezerwowana TSI

    Podczas rejestracji w usłudze IoT Hub lub Centrum zdarzeń możesz określić grupy odbiorców, które mają być używane do odczytywania danych. Nie może być udostępniany danej grupie odbiorców. Jeśli grupa odbiorców jest udostępniony, podstawowe Centrum zdarzeń automatycznie Odłącza jeden czytników losowo. Podaj grupy odbiorców unikatowy dla TSI do odczytu.

1. Usługi Time Series ID określona właściwość w czasie inicjowania obsługi administracyjnej lub jest ona nieprawidłowa, o wartości null

    Może to nastąpić, jeśli **identyfikator serii czasu** właściwość jest nieprawidłowo skonfigurowany w czasie inicjowania obsługi administracyjnej w środowisku. Zobacz [najlepsze rozwiązania dotyczące wybierania Identyfikatora serii czasu](./time-series-insights-update-how-to-id.md). W tej chwili nie można zaktualizować istniejącego środowiska usługi Time Series Insights aktualizacji można użyć innego **identyfikator serii czasu**.

## <a name="problem-some-data-is-shown-but-some-is-missing"></a>Problem: Niektóre dane są wyświetlane, ale brakuje

1. Może wysyłać dane bez Identyfikatora serii czasu

    Ten błąd może wystąpić, gdy trzeba wysłać zdarzenia bez pola Identyfikator serii czasu w ładunku. Zobacz [kształty JSON obsługiwany](./how-to-shape-query-json.md) Aby uzyskać więcej informacji.

1. Może to nastąpić, ponieważ w danym środowisku jest ograniczane.

    > [!NOTE]
    > W tej chwili Azure TSI obsługuje pozyskiwanie maksymalny stopień 6 MB/s.

## <a name="problem-my-event-sources-timestamp-property-name-setting-doesnt-work"></a>Problem: Ustawienie nazwę właściwości sygnatury czasowej źródła zdarzeń nie działa.

Upewnij się, że z nazwą i wartością są zgodne z następującymi zasadami:

* **Sygnatura czasowa** nazwa właściwości jest rozróżniana wielkość liter.
* **Sygnatura czasowa** wartości właściwości, które pochodzą ze źródła zdarzenia jako ciąg JSON powinien mieć format `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`. Na przykład taki ciąg `“2008-04-12T12:53Z”`.

Najprostszym sposobem zapewnienia, że nazwę właściwości sygnatury czasowej są przechwytywane i działa poprawnie za pomocą Eksploratora usługi TSI. W Eksploratorze TSI Użyj wykres, aby wybrać okres czasu, po podać nazwę właściwości sygnatury czasowej. Kliknij prawym przyciskiem myszy zaznaczenie, a następnie wybierz **Eksploruj zdarzenia** opcji. Pierwszy nagłówek kolumny powinna być Twoje **sygnatura czasowa** powinny mieć nazwy właściwości i `($ts)` obok wyraz `Timestamp`, zamiast:

* `(abc)`, który będzie wskazywać TSI odczytuje wartości danych jako ciągi
* Ikonę kalendarza, co oznaczałoby, że usługa TSI Przesyła odczytuje wartości danych jako daty/godziny
* `#`, który będzie wskazywać TSI odczytuje wartości danych jako liczba całkowita

Jeśli **sygnatura czasowa** właściwość nie jest jawnie określona, firma Microsoft będzie korzystać z usługi IoT Hub lub Centrum zdarzeń event **czasu umieszczonych w kolejce** jako sygnatura czasowa domyślne.

## <a name="problem-i-cant-edit-or-view-my-time-series-model"></a>Problem: nie można edytować lub Wyświetl moje modelu szeregów czasowych

1. Możesz być może czas serii Insights S1 lub S2 środowiska

   Modeli szeregów czasowych są obsługiwane tylko w **PAYG** środowisk. Aby uzyskać więcej informacji na temat sposobu dostęp do środowiska S1/S2 Eksploratora czasu Series Insights w wersji zapoznawczej, zobacz [wizualizowanie danych w Eksploratorze](./time-series-insights-update-explorer.md).

   ![access][5]

1. Nie masz uprawnień do wyświetlania i edytowania modelu

   Użytkownicy potrzebują dostępu na poziomie "Współautor" do edytowania i wyświetlania ich modelu szeregów czasowych. Możesz sprawdzić bieżące poziomy dostępu i udostępnienia dodatkowych, odwiedzając sekcję zasady dostępu do danych zasobu usługi Time Series Insights w witrynie Azure Portal.

## <a name="problem-all-my-instances-in-time-series-insights-preview-explorer-dont-have-a-parent"></a>Problem: Moje wystąpienia w Eksploratorze usługi Time Series Insights (wersja zapoznawcza) nie ma elementu nadrzędnego

Może to nastąpić, jeśli Twoje środowisko nie ma **modelu szeregów czasowych** hierarchii zdefiniowane. Zobacz ten artykuł, aby uzyskać więcej informacji na [sposób pracy z modeli szeregów czasowych](./time-series-insights-update-how-to-tsm.md).

  ![TSM][6]

## <a name="next-steps"></a>Kolejne kroki

Odczyt [sposób pracy z modeli szeregów czasowych](./time-series-insights-update-how-to-tsm.md).

Odczyt [kształty JSON obsługiwany](./how-to-shape-query-json.md).

<!-- Images -->
[1]: media/v2-update-diagnose-and-troubleshoot/environment.png
[2]: media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png
[3]: media/v2-update-diagnose-and-troubleshoot/configuration.png
[4]: media/v2-update-diagnose-and-troubleshoot/permissions.png
[5]: media/v2-update-diagnose-and-troubleshoot/access.png
[6]: media/v2-update-diagnose-and-troubleshoot/tsm.png