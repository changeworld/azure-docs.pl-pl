---
title: Diagnozowanie środowiska w wersji Zapoznawczej i rozwiązywanie problemów z tym środowiskiem — usługa Azure Time Series Insights | Dokumenty firmy Microsoft
description: Dowiedz się, jak diagnozować środowisko usługi Azure Time Series Preview i rozwiązywać problemy z tym środowiskiem.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18
ms.openlocfilehash: 667dee6365f38ae058e91c61c24838d8912df26a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80152664"
---
# <a name="diagnose-and-troubleshoot-a-preview-environment"></a>Diagnozowanie i rozwiązywanie problemów ze środowiskiem w wersji zapoznawczej

W tym artykule podsumowano kilka typowych problemów, które mogą wystąpić podczas pracy ze środowiskiem usługi Azure Time Series Insights Preview. W artykule opisano również potencjalne przyczyny i rozwiązania dla każdego problemu.

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>Problem: nie mogę znaleźć środowiska w eksploratorze wersji zapoznawczej

Ten problem może wystąpić, jeśli nie masz uprawnień dostępu do środowiska usługi Time Series Insights. Użytkownicy potrzebują roli dostępu na poziomie czytnika, aby wyświetlić ich środowisko usługi Time Series Insights. Aby zweryfikować bieżące poziomy dostępu i udzielić dodatkowego dostępu, przejdź do sekcji **Zasady dostępu** do danych w zasobie Usługi Time Series Insights w [witrynie Azure portal](https://portal.azure.com/).

  [![Weryfikuj zasady dostępu do danych.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>Problem: W Eksploratorze podglądu nie są widoczne żadne dane

Istnieje kilka typowych powodów, dla których dane mogą nie być wyświetlane w [Eksploratorze usługi Azure Time Series Insights Preview](https://insights.timeseries.azure.com/preview).

- Źródło zdarzenia może nie odbierać danych.

    Sprawdź, czy źródło zdarzeń, które jest centrum zdarzeń lub centrum IoT hub, odbiera dane ze znaczników lub wystąpień. Aby zweryfikować, przejdź do strony przeglądu zasobu w witrynie Azure portal.

    [![Przejrzyj omówienie metryk pulpitu nawigacyjnego.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- Dane źródłowe zdarzenia nie są w formacie JSON.

    Usługa Time Series Insights obsługuje tylko dane JSON. W przypadku próbek JSON można przeczytać [w obszarze Obsługiwane kształty JSON](./how-to-shape-query-json.md).

- Klucz źródła zdarzenia nie ma wymaganego uprawnienia.

  * W przypadku centrum IoT hub należy podać klucz, który ma uprawnienia **do łączenia usługi.**

    [![Sprawdź uprawnienia centrum IoT.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    * Zarówno zasady **iothubowner** i **usługi** pracy, ponieważ mają one uprawnienia **do łączenia usługi.**

  * W przypadku centrum zdarzeń należy podać klucz, który ma **uprawnienie Nasłuchiwać.**
  
    [![Przejrzyj uprawnienia Centrum zdarzeń.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    * Zasady **odczytu** i **zarządzania** działają, ponieważ mają uprawnienia **Nasłuchiwania.**

- Twoja grupa odbiorców pod warunkiem, że nie jest wyłącznie do time series insights.

    Podczas rejestracji centrum IoT hub lub centrum zdarzeń należy określić grupę odbiorców, która jest używana do odczytywania danych. Ta grupa odbiorców musi być unikatowa dla środowiska. Jeśli grupa odbiorców jest współużytkowana, centrum zdarzeń podstawowej automatycznie rozłącza losowo jednego z czytników. Podaj unikatową grupę odbiorców dla usługi Time Series Insights do odczytu.

- Właściwość identyfikatora szeregów czasowych określona w czasie inicjowania obsługi administracyjnej jest niepoprawna, brakuje lub null.

    Ten problem może wystąpić, jeśli właściwość identyfikator szeregów czasowych jest niepoprawnie skonfigurowany w czasie inicjowania obsługi administracyjnej środowiska. Aby uzyskać więcej informacji, przeczytaj [najważniejsze wskazówki dotyczące wybierania identyfikatora szeregów czasowych](./time-series-insights-update-how-to-id.md). W tej chwili nie można zaktualizować istniejącego środowiska usługi Time Series Insights, aby użyć innego identyfikatora szeregów czasowych.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problem: Niektóre dane pokazują, ale brakuje niektórych

Być może wysyłasz dane bez identyfikatora szeregów czasowych.

- Ten problem może wystąpić podczas wysyłania zdarzeń bez pola Identyfikator szeregów czasowych w ładunku. Aby uzyskać więcej informacji, zobacz [Obsługiwane kształty JSON](./how-to-shape-query-json.md).
- Ten problem może wystąpić, ponieważ środowisko jest ograniczane.

    > [!NOTE]
    > W tej chwili aplikacja Time Series Insights obsługuje maksymalną szybkość pozyskiwania 6 Mb/s.

## <a name="problem-data-was-showing-but-now-ingestion-has-stopped"></a>Problem: Dane były wyświetlane, ale teraz połknienie zostało zatrzymane

- Być może klucz źródłowy zdarzenia został ponownie wygenerowany, a środowisko w wersji zapoznawczej wymaga nowego klucza źródłowego zdarzenia.

Ten problem występuje, gdy klucz podany podczas tworzenia źródła zdarzeń nie jest już prawidłowy. Zostaną wyświetleń danych telemetrycznych w centrum, ale nie przychodzące odebrane wiadomości w aplikacji Time Series Insights. Jeśli nie masz pewności, czy klucz został ponownie wygenerowany, możesz przeszukać dziennik aktywności centrum zdarzeń pod kątem "Utwórz lub zaktualizuj reguły autoryzacji obszaru nazw" lub wyszukaj "Utwórz lub zaktualizuj zasób IotHub" dla centrum IoT Hub. 

Aby zaktualizować środowisko usługi Time Series Insights Preview za pomocą nowego klucza, otwórz zasób centrum w witrynie Azure portal i skopiuj nowy klucz. Przejdź do zasobu TSI i kliknij źródła zdarzeń. 

   [![Klucz aktualizacji.](media/preview-troubleshoot/update-hub-key-step-1.png)](media/preview-troubleshoot/update-hub-key-step-1.png#lightbox)

Wybierz źródło zdarzeń, z których zatrzymano pozyskiwanie, wklej nowy klucz i kliknij przycisk Zapisz.

   [![Klucz aktualizacji.](media/preview-troubleshoot/update-hub-key-step-2.png)](media/preview-troubleshoot/update-hub-key-step-2.png#lightbox)

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Problem: Nazwa właściwości Sygnatury czasowej mojego źródła zdarzeń nie działa

Upewnij się, że nazwa i wartość są zgodne z następującymi regułami:

* W nazwie właściwości sygnatury czasowej jest rozróżniana wielkość liter.
* Wartość właściwości sygnatury czasowej, która pochodzi ze `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`źródła zdarzenia jako ciąg JSON ma format . Przykładem takiego ciągu `“2008-04-12T12:53Z”`jest .

Najprostszym sposobem zapewnienia, że nazwa właściwości sygnatury czasowej jest przechwytywany i działa poprawnie jest użycie Eksploratora podglądu usługi Time Series Insights. W eksploratorze Podgląd warstwy czasowej wglądu w dane czasowe użyj wykresu, aby wybrać okres po podasz nazwę właściwości sygnatury czasowej. Kliknij prawym przyciskiem myszy zaznaczenie i wybierz opcję **eksploruj wydarzenia.** Pierwszy nagłówek kolumny to nazwa właściwości sygnatury czasowej. Powinien mieć `($ts)` obok słowa `Timestamp`, a nie:

* `(abc)`, co oznacza, że usługa Time Series Insights odczytuje wartości danych jako ciągi.
* Ikona **kalendarza,** która wskazuje, że usługa Time Series Insights odczytuje wartość danych jako datetime.
* `#`, co oznacza, że usługa Time Series Insights odczytuje wartości danych jako całkowitą.

Jeśli właściwość sygnatury czasowej nie jest jawnie określona, centrum IoT lub centrum zdarzeń Enqueued Time jest używany jako domyślny sygnatura czasowa.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Problem: Nie mogę wyświetlić danych z mojego ciepłego magazynu w eksploratorze

- Być może ostatnio zainicjowano aprowisję w ciepłym magazynie, a dane nadal płyną.
- Być może został usunięty ciepły magazyn, w którym to przypadku utracisz dane.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Problem: nie mogę wyświetlić ani edytować modelu szeregów czasowych

- Być może uzyskujesz dostęp do środowiska usługi Time Series Insights S1 lub S2.

   Modele szeregów czasowych są obsługiwane tylko w środowiskach płatności zgodnie z rzeczywistymu po podróży. Aby uzyskać więcej informacji na temat uzyskiwania dostępu do środowiska S1 lub S2 z eksploratora Time Series Insights Preview, przeczytaj [wizualizację danych w eksploratorze.](./time-series-insights-update-explorer.md)

   [![Brak zdarzeń w środowisku.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- Być może nie masz uprawnień do wyświetlania i edytowania modelu.

   Użytkownicy potrzebują dostępu na poziomie współautora, aby edytować i wyświetlać swój model szeregów czasowych. Aby zweryfikować bieżące poziomy dostępu i udzielić dodatkowego dostępu, przejdź do sekcji **Zasady dostępu** do danych w zasobie usługi Time Series Insights w witrynie Azure portal.

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>Problem: wszystkie moje wystąpienia w eksploratorze podglądu nie mają rodzica

Ten problem może wystąpić, jeśli środowisko nie ma hierarchii modelu szeregów czasowych zdefiniowane. Aby uzyskać więcej informacji, zobacz [Praca z modelami szeregów czasowych](./time-series-insights-update-how-to-tsm.md).

  [![Nieparzyszone wystąpienia będą wyświetlać ostrzeżenie.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="next-steps"></a>Następne kroki

- Przeczytaj [artykuł Praca z modelami szeregów czasowych](./time-series-insights-update-how-to-tsm.md).

- Dowiedz się więcej o [obsługiwanych kształtach JSON](./how-to-shape-query-json.md).

- Przejrzyj [planowanie i limity](./time-series-insights-update-plan.md) w usłudze Azure Time Series Insights Preview.
