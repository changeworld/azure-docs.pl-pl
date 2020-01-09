---
title: Diagnozowanie i rozwiązywanie problemów dotyczących środowiska w wersji zapoznawczej — Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak zdiagnozować i rozwiązać problemy z Azure Time Series Insightsm w wersji zapoznawczej.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/13/2019
ms.custom: seodec18
ms.openlocfilehash: 0e4ec63ffe715b17f55fde2a53c15d96d391cdba
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452492"
---
# <a name="diagnose-and-troubleshoot-a-preview-environment"></a>Diagnozowanie i rozwiązywanie problemów dotyczących środowiska w wersji zapoznawczej

W tym artykule opisano kilka typowych problemów, które mogą wystąpić podczas pracy ze środowiskiem Azure Time Series Insights w wersji zapoznawczej. W tym artykule opisano również potencjalne przyczyny i rozwiązania każdego problemu.

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>Problem: nie można znaleźć mojego środowiska w Eksploratorze wersji zapoznawczej

Ten problem może wystąpić, jeśli nie masz uprawnień dostępu do środowiska Time Series Insights. Aby wyświetlić środowisko Time Series Insights, użytkownicy muszą mieć rolę dostępu na poziomie czytnika. Aby sprawdzić bieżące poziomy dostępu i udzielić dodatkowego dostępu, przejdź do sekcji **zasady dostępu do danych** w zasobie Time Series Insights w [Azure Portal](https://portal.azure.com/).

  [![sprawdzić zasady dostępu do danych.](media/preview-troubleshoot/verify-data-access-policies.png)](media/preview-troubleshoot/verify-data-access-policies.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>Problem: w Eksploratorze wersji zapoznawczej nie są widoczne żadne dane

Istnieje kilka typowych przyczyn, dla których Twoje dane mogą nie być widoczne w [eksploratorze Azure Time Series Insights w wersji zapoznawczej](https://insights.timeseries.azure.com/preview).

- Źródło zdarzenia może nie odbierać danych.

    Sprawdź, czy źródło zdarzeń, które jest centrum zdarzeń lub Centrum IoT, otrzymuje dane ze swoich tagów lub wystąpień. Aby sprawdzić, przejdź do strony Przegląd zasobu w Azure Portal.

    [Przegląd metryk pulpitu nawigacyjnego ![.](media/preview-troubleshoot/verify-dashboard-metrics.png)](media/preview-troubleshoot/verify-dashboard-metrics.png#lightbox)

- Dane źródłowe zdarzenia nie są w formacie JSON.

    Time Series Insights obsługuje tylko dane JSON. Aby uzyskać przykłady kodu JSON, zobacz [kształty JSON obsługiwany](./how-to-shape-query-json.md).

- Klucz źródła zdarzenia nie ma wymaganego uprawnienia.

  * W przypadku usługi IoT Hub należy podać klucz, który ma uprawnienia do **połączenia z usługą** .

    [![sprawdzić uprawnienia do centrum IoT Hub.](media/preview-troubleshoot/verify-correct-permissions.png)](media/preview-troubleshoot/verify-correct-permissions.png#lightbox)

    * Zarówno zasady **iothubowner** , jak i działanie **usługi** , ponieważ mają uprawnienie do **łączenia usług** .

  * W przypadku centrum zdarzeń należy podać klucz, który ma uprawnienia do **nasłuchiwania** .
  
    [![przeglądać uprawnień centrum zdarzeń.](media/preview-troubleshoot/verify-eh-permissions.png)](media/preview-troubleshoot/verify-eh-permissions.png#lightbox)

    * Zasady **odczytu** i **zarządzania** działają, ponieważ mają uprawnienia do **nasłuchiwania** .

- Podana Grupa odbiorców nie ma wyłącznej Time Series Insights.

    Podczas rejestracji Centrum IoT Hub lub centrum zdarzeń należy określić grupę konsumentów, która jest używana do odczytywania danych. Ta grupa odbiorców musi być unikatowa dla danego środowiska. Jeśli grupa konsumentów jest udostępniona, w podstawowym centrum zdarzeń automatycznie rozłącza jednego z czytelników. Podaj unikatową grupę odbiorców, w której ma zostać odczytana Time Series Insights.

- Właściwość identyfikatora szeregów czasowych określona w chwili aprowizacji jest nieprawidłowa, nie istnieje lub ma wartość null.

    Ten problem może wystąpić, jeśli właściwość identyfikatora szeregów czasowych została skonfigurowana niepoprawnie w momencie aprowizacji środowiska. Aby uzyskać więcej informacji, zobacz [najlepsze rozwiązania dotyczące wybierania identyfikatora szeregów czasowych](./time-series-insights-update-how-to-id.md). W tej chwili nie można zaktualizować istniejącego środowiska Time Series Insights, aby użyć innego identyfikatora szeregów czasowych.

## <a name="problem-some-data-shows-but-some-is-missing"></a>Problem: niektóre dane są widoczne, ale brakuje niektórych

Dane mogą być wysyłane bez identyfikatora szeregów czasowych.

- Ten problem może wystąpić, gdy wysyłasz zdarzenia bez pola identyfikatora szeregów czasowych w ładunku. Aby uzyskać więcej informacji, zobacz [obsługiwane kształty JSON](./how-to-shape-query-json.md).
- Ten problem może wystąpić, ponieważ Twoje środowisko jest ograniczone.

    > [!NOTE]
    > W tej chwili Time Series Insights obsługuje maksymalny współczynnik pozyskiwania wynoszący 6 MB/s.

## <a name="problem-my-event-sources-timestamp-property-name-doesnt-work"></a>Problem: Nazwa właściwości sygnatury czasowej źródła zdarzenia nie działa

Upewnij się, że z nazwą i wartością są zgodne z następującymi zasadami:

* W nazwie właściwości sygnatury czasowej jest uwzględniana wielkość liter.
* Wartość właściwości timestamp, która pochodzi ze źródła zdarzenia jako ciąg JSON, ma format `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`. Na przykład taki ciąg `“2008-04-12T12:53Z”`.

Najprostszym sposobem, aby upewnić się, że nazwa właściwości sygnatury czasowej została przechwycona i działa prawidłowo, to użycie Eksploratora Time Series Insights w wersji zapoznawczej. W Eksploratorze Time Series Insights w wersji zapoznawczej Użyj wykresu, aby wybrać okres czasu po podaniu nazwy właściwości sygnatury czasowej. Kliknij prawym przyciskiem myszy zaznaczenie i wybierz opcję **Eksploruj zdarzenia** . Pierwszy nagłówek kolumny jest nazwą właściwości znacznika czasu. Powinien on mieć `($ts)` obok słowa `Timestamp`, a nie:

* `(abc)`, która wskazuje, że Time Series Insights odczytuje wartości danych jako ciągi.
* Ikona **kalendarza** , która wskazuje, że Time Series Insights odczytuje wartość danych jako datę i godzinę.
* `#`wskazujący, że Time Series Insights odczytuje wartości danych jako liczbę całkowitą.

Jeśli właściwość sygnatury czasowej nie zostanie określona jawnie, jako domyślny znacznik czasu zostanie użyta godzina zdarzenia w centrum IoT lub centrum zdarzeń.

## <a name="problem-i-cant-view-data-from-my-warm-store-in-the-explorer"></a>Problem: nie można wyświetlić danych ze sklepu ciepłego w Eksploratorze

- Być może ostatnio zainicjowano aprowizacji sklepu, a dane nadal przepływają.
- Prawdopodobnie usunięto magazyn ciepły. w takim przypadku dane zostaną utracone.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Problem: nie można wyświetlić lub edytować mojego modelu szeregów czasowych

- Można uzyskać dostęp do środowiska Time Series Insights S1 lub S2.

   Modele szeregów czasowych są obsługiwane tylko w środowiskach z opcją płatność zgodnie z rzeczywistym użyciem. Aby uzyskać więcej informacji na temat uzyskiwania dostępu do środowiska S1 lub S2 z programu Time Series Insights Explorer w wersji zapoznawczej, zobacz [Wizualizacja danych w Eksploratorze](./time-series-insights-update-explorer.md).

   [Nie ![żadnych zdarzeń w środowisku.](media/preview-troubleshoot/troubleshoot-no-events.png)](media/preview-troubleshoot/troubleshoot-no-events.png#lightbox)

- Być może nie masz uprawnień do wyświetlania i edytowania modelu.

   Użytkownicy potrzebują dostępu na poziomie współautora, aby edytować i przeglądać swój model szeregów czasowych. Aby sprawdzić bieżące poziomy dostępu i udzielić dodatkowego dostępu, przejdź do sekcji **zasady dostępu do danych** w zasobie Time Series Insights w Azure Portal.

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>Problem: wszystkie moje wystąpienia w Eksploratorze podglądu nie mają elementu nadrzędnego

Ten problem może wystąpić, jeśli środowisko nie ma zdefiniowanej hierarchii modelu czasu szeregów czasowych. Aby uzyskać więcej informacji, zobacz temat [współpraca z modelami szeregów czasowych](./time-series-insights-update-how-to-tsm.md).

  [w ![wystąpienia nienadrzędne będą wyświetlane ostrzeżenie.](media/preview-troubleshoot/unparented-instances.png)](media/preview-troubleshoot/unparented-instances.png#lightbox)

## <a name="next-steps"></a>Następne kroki

- Czytaj informacje [o pracy z modelami szeregów czasowych](./time-series-insights-update-how-to-tsm.md).

- Poznaj [obsługiwane kształty JSON](./how-to-shape-query-json.md).

- Zapoznaj się z tematem [Planowanie i limity](./time-series-insights-update-plan.md) w programie Azure Time Series Insights Preview.
