---
title: Diagnozowanie i rozwiązywanie problemów z podglądem Azure Time Series Insights | Microsoft Docs
description: Dowiedz się, jak diagnozować i rozwiązywać problemy w wersji zapoznawczej Azure Time Series Insights.
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: cdcbe62fdba4f111233451680f95abc757e80ee3
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68883335"
---
# <a name="diagnose-and-troubleshoot"></a>Diagnozowanie i rozwiązywanie problemów

W tym artykule opisano kilka typowych problemów, które mogą wystąpić podczas pracy ze środowiskiem Azure Time Series Insights w wersji zapoznawczej. W tym artykule opisano również potencjalne przyczyny i rozwiązania każdego problemu.

## <a name="problem-i-cant-find-my-environment-in-the-preview-explorer"></a>Problem: Nie mogę znaleźć mojego środowiska w Eksploratorze wersji zapoznawczej

Ten problem może wystąpić, jeśli nie masz uprawnień dostępu do środowiska Time Series Insights. Aby wyświetlić środowisko Time Series Insights, użytkownicy muszą mieć rolę dostępu na poziomie czytnika. Aby sprawdzić bieżące poziomy dostępu i udzielić dodatkowego dostępu, odwiedź sekcję zasady dostępu do danych w zasobie Time Series Insights w [Azure Portal](https://portal.azure.com/).

  [![Naturalne](media/v2-update-diagnose-and-troubleshoot/environment.png)](media/v2-update-diagnose-and-troubleshoot/environment.png#lightbox)

## <a name="problem-no-data-is-seen-in-the-preview-explorer"></a>Problem: w Eksploratorze wersji zapoznawczej nie są widoczne żadne dane

Istnieje kilka typowych przyczyn, dla których Twoje dane mogą nie być widoczne w [eksploratorze Azure Time Series Insights w wersji](https://insights.timeseries.azure.com/preview)zapoznawczej.

- Źródło zdarzenia może nie odbierać danych.

    Sprawdź, czy źródło zdarzeń, które jest centrum zdarzeń lub Centrum IoT, otrzymuje dane ze swoich tagów lub wystąpień. Aby sprawdzić, przejdź do strony Przegląd zasobu w Azure Portal.

    [![Pulpit nawigacyjny — szczegółowe informacje](media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png)](media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png#lightbox)

- Dane źródłowe zdarzenia nie są w formacie JSON.

    Time Series Insights obsługuje tylko dane JSON. Aby uzyskać przykłady kodu JSON, zobacz [kształty JSON obsługiwany](./how-to-shape-query-json.md).

- Klucz źródła zdarzenia nie ma wymaganego uprawnienia.

  * W przypadku usługi IoT Hub należy podać klucz, który ma uprawnienia do **połączenia z usługą** .

    [![Konfiguracja](media/v2-update-diagnose-and-troubleshoot/configuration.png)](media/v2-update-diagnose-and-troubleshoot/configuration.png#lightbox)

  * Jak pokazano na powyższym obrazie, oba zasady **iothubowner** i pracują z **usługą** , ponieważ mają uprawnienia do **połączenia z usługą** .
  * W przypadku centrum zdarzeń należy podać klucz, który ma uprawnienia do nasłuchiwania.
  
    [![Uprawnienia](media/v2-update-diagnose-and-troubleshoot/permissions.png)](media/v2-update-diagnose-and-troubleshoot/permissions.png#lightbox)

  * Jak pokazano na powyższym obrazie, obie zasady **odczytu** i **zarządzania** działają, ponieważ mają one uprawnienia do nasłuchiwania.

- Podana Grupa odbiorców nie ma wyłącznej Time Series Insights.

    Podczas rejestracji Centrum IoT Hub lub centrum zdarzeń należy określić grupę konsumentów, która jest używana do odczytywania danych. Nie udostępniaj tej grupy odbiorców. Jeśli grupa konsumentów jest udostępniona, w podstawowym centrum zdarzeń automatycznie rozłącza jednego z czytelników. Podaj unikatową grupę odbiorców, w której ma zostać odczytana Time Series Insights.

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
* Wartość właściwości timestamp, która pochodzi ze źródła zdarzenia, jako ciąg JSON ma format `yyyy-MM-ddTHH:mm:ss.FFFFFFFK`. Na przykład taki ciąg `“2008-04-12T12:53Z”`.

Najprostszym sposobem, aby upewnić się, że nazwa właściwości sygnatury czasowej została przechwycona i działa prawidłowo, to użycie Eksploratora Time Series Insights w wersji zapoznawczej. W Eksploratorze Time Series Insights w wersji zapoznawczej Użyj wykresu, aby wybrać okres czasu po podaniu nazwy właściwości sygnatury czasowej. Kliknij prawym przyciskiem myszy zaznaczenie i wybierz opcję **Eksploruj zdarzenia** . Pierwszy nagłówek kolumny jest nazwą właściwości znacznika czasu. Powinien zawierać `($ts)` obok wyrazu `Timestamp`, a nie:

* `(abc)`, co oznacza, że Time Series Insights odczytuje wartości danych jako ciągi.
* Ikona kalendarza, która wskazuje, że Time Series Insights odczytuje wartość danych jako datę i godzinę.
* `#`, co oznacza, że Time Series Insights odczytuje wartości danych jako liczbę całkowitą.

Jeśli właściwość sygnatury czasowej nie zostanie określona jawnie, jako domyślny znacznik czasu zostanie użyta godzina zdarzenia w centrum IoT lub centrum zdarzeń.

## <a name="problem-i-cant-view-or-edit-my-time-series-model"></a>Problem: Nie można wyświetlić lub edytować modelu szeregów czasowych

- Można uzyskać dostęp do środowiska Time Series Insights S1 lub S2.

   Modele szeregów czasowych są obsługiwane tylko w środowiskach PAYG. Aby uzyskać więcej informacji na temat sposobu dostęp do środowiska S1/S2 Eksploratora czasu Series Insights w wersji zapoznawczej, zobacz [wizualizowanie danych w Eksploratorze](./time-series-insights-update-explorer.md).

   [![Niego](media/v2-update-diagnose-and-troubleshoot/access.png)](media/v2-update-diagnose-and-troubleshoot/access.png#lightbox)

- Być może nie masz uprawnień do wyświetlania i edytowania modelu.

   Użytkownicy potrzebują dostępu na poziomie współautora, aby edytować i przeglądać swój model szeregów czasowych. Aby sprawdzić bieżące poziomy dostępu i udzielić dodatkowego dostępu, odwiedź sekcję zasady dostępu do danych w zasobie Time Series Insights w Azure Portal.

## <a name="problem-all-my-instances-in-the-preview-explorer-lack-a-parent"></a>Problem: wszystkie moje wystąpienia w Eksploratorze podglądu nie mają elementu nadrzędnego

Ten problem może wystąpić, jeśli środowisko nie ma zdefiniowanej hierarchii modelu czasu szeregów czasowych. Aby uzyskać więcej informacji, zobacz temat [współpraca z modelami szeregów czasowych](./time-series-insights-update-how-to-tsm.md).

  [![Modele szeregów czasowych](media/v2-update-diagnose-and-troubleshoot/tsm.png)](media/v2-update-diagnose-and-troubleshoot/tsm.png#lightbox)

## <a name="next-steps"></a>Następne kroki

- Czytaj informacje [o pracy z modelami szeregów czasowych](./time-series-insights-update-how-to-tsm.md).

- Poznaj [obsługiwane kształty JSON](./how-to-shape-query-json.md).
