---
title: Diagnozowanie i rozwiązywanie problemów z wersji zapoznawczej Azure czas serii szczegółowych informacji | Dokumentacja firmy Microsoft
description: Dowiedz się, jak diagnozować i rozwiązywanie problemów z programem Azure czas Series Insights w wersji zapoznawczej.
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 04/30/2019
ms.custom: seodec18
ms.openlocfilehash: e4a63bfd4e82147fe3324e146f2aaff8889da87e
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472312"
---
# <a name="diagnose-and-troubleshoot"></a>Diagnozowanie i rozwiązywanie problemów

Ten artykuł zawiera podsumowanie kilka typowych problemów, które można napotkać podczas pracy ze środowiskiem Azure czas Series Insights w wersji zapoznawczej. Opisano również możliwe przyczyny i potencjalne rozwiązania dla każdego problemu.

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>Problem: Nie mogę znaleźć moje środowisko w programie explorer (wersja zapoznawcza)

Ten problem może wystąpić, jeśli nie masz uprawnień, aby uzyskiwać dostęp do środowiska usługi Time Series Insights. Użytkownicy muszą roli dostęp na poziomie czytelnika, aby wyświetlić swoje środowisko usługi Time Series Insights. Aby sprawdzić bieżące poziomy dostępu i udzielić dodatkowe prawa dostępu, można znaleźć w sekcji zasady dostępu do danych na zasób usługi Time Series Insights w [witryny Azure portal](https://portal.azure.com/).

  [![Środowisko](media/v2-update-diagnose-and-troubleshoot/environment.png)](media/v2-update-diagnose-and-troubleshoot/environment.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>Problem: żadne dane nie są widoczne w programie explorer (wersja zapoznawcza)

Istnieje kilka przyczyn typowych, dlaczego mogą nie być wyświetlane dane w [Azure czas Series Insights w wersji zapoznawczej Eksplorator](https://insights.timeseries.azure.com/preview).

- Źródło zdarzeń nie może być odbierania danych.

    Sprawdź, czy źródło zdarzenia, w którym jest Centrum zdarzeń lub usługi IoT hub, otrzymuje dane z tagami lub wystąpienia. Aby sprawdzić, przejdź do strony Przegląd zasobu w witrynie Azure portal.

    [![pulpit nawigacyjny analizy](media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png)](media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png#lightbox)

- Źródło danych zdarzeń nie jest w formacie JSON.

    Usługa Time Series Insights obsługuje tylko dane JSON. Aby uzyskać przykłady kodu JSON, zobacz [kształty JSON obsługiwany](./how-to-shape-query-json.md).

- Klucz źródła zdarzeń nie ma wymaganych uprawnień.

  * Dla usługi IoT hub, musisz podać klucz, który ma **połączenie z usługą** uprawnień.

    [![Konfiguracja](media/v2-update-diagnose-and-troubleshoot/configuration.png)](media/v2-update-diagnose-and-troubleshoot/configuration.png#lightbox)

  * Pokazany na wcześniejszej ilustracji, obie zasady **iothubowner** i **usługi** działa, ponieważ mają one **połączenie z usługą** uprawnień.
  * Centrum zdarzeń, musisz podać klucz, który ma **nasłuchiwania** uprawnień.
  
    [![Uprawnienia](media/v2-update-diagnose-and-troubleshoot/permissions.png)](media/v2-update-diagnose-and-troubleshoot/permissions.png#lightbox)

  * Pokazany na wcześniejszej ilustracji, oba **odczytu** i **zarządzanie** działania zasad, ponieważ mają one **nasłuchiwania** uprawnień.

- Grupy konsumentów pod warunkiem jest wyłącznie dla usługi Time Series Insights.

    Podczas rejestracji w usłudze IoT hub lub Centrum zdarzeń możesz określić grupy odbiorców, który służy do odczytywania danych. Nie udostępniaj danej grupie odbiorców. Jeśli grupa odbiorców jest udostępniony, podstawowe Centrum zdarzeń automatycznie Odłącza jeden czytników losowo. Podaj grupy odbiorców unikatowy dla usługi Time Series Insights do odczytu.

- Usługi Time Series ID określona właściwość w czasie inicjowania obsługi administracyjnej jest niepoprawny, brak lub wartość null.

    Ten problem może wystąpić, jeśli właściwość ID serii czasu jest nieprawidłowo skonfigurowany w czasie inicjowania obsługi administracyjnej w środowisku. Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące wybierania Identyfikatora serii czasu](./time-series-insights-update-how-to-id.md). W tej chwili nie można zaktualizować istniejącego środowiska usługi Time Series Insights, można użyć różnych serii czasu, identyfikator.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problem: dane pokazuje, ale niektóre nie istnieje

Wysyłanie danych bez identyfikatora serii czasu.

- Ten problem może wystąpić, gdy wysyła zdarzenia bez pola Identyfikator serii czasu w ładunku. Aby uzyskać więcej informacji, zobacz [kształty JSON obsługiwany](./how-to-shape-query-json.md).

- Ten problem może wystąpić, ponieważ w danym środowisku jest ograniczane.

    > [!NOTE]
    > W tej chwili usługi Time Series Insights obsługuje pozyskiwanie maksymalny stopień 6 MB/s.

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Problem: nie działa w nazwie właściwości sygnatury czasowej źródła zdarzeń

Upewnij się, że z nazwą i wartością są zgodne z następującymi zasadami:

* Nazwę właściwości sygnatury czasowej jest uwzględniana wielkość liter.
* Wartość właściwości sygnatury czasowej, która pochodzi ze źródła zdarzenia użytkownika jako ciąg JSON ma format `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`. Na przykład taki ciąg `“2008-04-12T12:53Z”`.

Najprostszym sposobem zapewnienia, że nazwę właściwości sygnatury czasowej są przechwytywane i działa poprawnie za pomocą Eksploratora czasu Series Insights w wersji zapoznawczej. W Eksploratorze czasu Series Insights w wersji zapoznawczej Użyj wykres, aby wybrać okres czasu, po podać nazwę właściwości sygnatury czasowej. Kliknij prawym przyciskiem myszy zaznaczenie, a następnie wybierz pozycję **Eksploruj zdarzenia** opcji. Pierwszy nagłówek kolumny jest nazwą właściwości sygnatury czasowej. Powinien on `($ts)` obok wyraz `Timestamp`, zamiast:

* `(abc)`, co oznacza, że usługa Time Series Insights odczytuje wartości danych jako ciągi.
* Ikonę kalendarza, co oznacza, że usługa Time Series Insights odczytuje wartości danych, jako wartość datetime.
* `#`, co oznacza, że usługa Time Series Insights odczytuje wartości danych jako liczba całkowita.

Jeśli właściwość sygnatury czasowej nie jest jawnie określona, zdarzeń usługi IoT hub lub Centrum zdarzeń czasu umieszczonych w kolejce jest używana jako domyślna sygnaturę czasową.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Problem: Nie można wyświetlić lub edytować Moje modelu szeregów czasowych

- Może uzyskiwać dostęp do czasu Series Insights S1 lub S2 środowiska.

   Modeli szeregów czasowych są obsługiwane tylko w środowiskach PAYG. Aby uzyskać więcej informacji na temat sposobu dostęp do środowiska S1/S2 Eksploratora czasu Series Insights w wersji zapoznawczej, zobacz [wizualizowanie danych w Eksploratorze](./time-series-insights-update-explorer.md).

   [![Access](media/v2-update-diagnose-and-troubleshoot/access.png)](media/v2-update-diagnose-and-troubleshoot/access.png#lightbox)

- Możesz nie mieć uprawnień do wyświetlania i edytowania modelu.

   Użytkownicy potrzebują dostępu do edytowania i wyświetlania ich modelu szeregów czasowych na poziomie współautora. Aby sprawdzić bieżące poziomy dostępu i udzielić dodatkowe prawa dostępu, można znaleźć w sekcji zasady dostępu do danych zasobu usługi Time Series Insights w witrynie Azure portal.

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>Problem: Moje wystąpienia w programie explorer (wersja zapoznawcza) brakuje elementu nadrzędnego

Ten problem może wystąpić, jeśli Twoje środowisko nie ma hierarchii modelu szeregów czasowych zdefiniowane. Aby uzyskać więcej informacji, zobacz [pracować z modeli szeregów czasowych](./time-series-insights-update-how-to-tsm.md).

  [![Modeli szeregów czasowych](media/v2-update-diagnose-and-troubleshoot/tsm.png)](media/v2-update-diagnose-and-troubleshoot/tsm.png#lightbox)

## <a name="next-steps"></a>Kolejne kroki

- Odczyt [pracować z modeli szeregów czasowych](./time-series-insights-update-how-to-tsm.md).

- Dowiedz się więcej o [obsługiwane kształty JSON](./how-to-shape-query-json.md).
