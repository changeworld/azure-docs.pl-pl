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
ms.date: 11/27/2018
ms.openlocfilehash: d13d373169287a0ec5931d5437b0a3bc70ecd79a
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/04/2018
ms.locfileid: "52856355"
---
# <a name="how-to-diagnose-and-troubleshoot"></a>Sposób diagnozowania i rozwiązywania problemów

Ten artykuł zawiera podsumowanie kilka typowych problemów, które można napotkać pracy ze środowiskiem usługi Azure Time Series Insights (TSI). Tego artykułu opisano możliwe przyczyny i potencjalne rozwiązania dla każdego.

## <a name="problem-no-data-is-seen-in-the-time-series-insights-preview-explorer"></a>Problem: Brak danych jest widoczne w usługi Time Series Insights Explorer (wersja zapoznawcza)

Istnieje kilka typowych przyczyn, dlaczego mogą nie być wyświetlane dane w Eksploratorze usługi Azure TSI:

1. Źródło zdarzeń nie może odbierać dane.

    Sprawdź, czy źródło zdarzeń (Centrum zdarzeń lub usługi IoT Hub) otrzymuje dane ze znaczników / wystąpień. Aby to zrobić, przechodząc do strony Przegląd zasobu w witrynie Azure portal.

    ![pulpit nawigacyjny analizy][1]

1. Źródło danych zdarzeń nie jest w formacie JSON

    Usługa Azure TSI obsługuje tylko dane JSON. Aby uzyskać przykłady kodu JSON, zobacz [kształty JSON obsługiwany](./how-to-shape-query-json.md).

1. Klucz źródła zdarzeń nie ma wymaganych uprawnień

    ![konfiguracja][2]

    * Dla usługi IoT Hub musisz podać klucz, który ma uprawnienia do połączenia z usługi.
    * Jak pokazano na poprzedniej ilustracji albo zasady *iothubowner* i usługa będzie działać, ponieważ mają uprawnienie połączenie z usługą.
    * Centrum zdarzeń musisz podać klucz, który ma uprawnienia do nasłuchiwania.
    * Pokazany na wcześniejszej ilustracji, przeczytaj jedną z zasad i zarządzanie nimi będzie działać, ponieważ mają uprawnienie do nasłuchiwania.

    ![uprawnienia][3]

1. Podane grupy odbiorców nie jest zarezerwowana TSI

    Podczas rejestracji am usługi IoT Hub lub Centrum zdarzeń można określić grupy odbiorców, które mają być używane do odczytywania danych. Ta grupa odbiorców nie może być udostępniany. Jeśli grupa odbiorców jest udostępniony, podstawowe Centrum zdarzeń automatycznie Odłącza jeden czytników losowo. Podaj grupy odbiorców unikatowy dla TSI do odczytu.

## <a name="problem-some-data-is-shown-but-some-is-missing"></a>Problem: Niektóre dane są wyświetlane, ale brakuje

Może to nastąpić, ponieważ w danym środowisku jest ograniczane.

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

## <a name="problem-my-time-series-id-property-is-incorrect-missing-or-null"></a>Problem: Moje właściwość ID serii czasu lub jest ona nieprawidłowa, o wartości null

Może to nastąpić, jeśli **identyfikator serii czasu** właściwość jest nieprawidłowo skonfigurowany w czasie inicjowania obsługi administracyjnej w środowisku. Zobacz [najlepsze rozwiązania dotyczące wybierania Identyfikatora serii czasu](./time-series-insights-update-how-to-id.md) artykuł podczas wybierania **identyfikator serii czasu**. W tej chwili nie można zaktualizować istniejącego środowiska TSI (wersja zapoznawcza), aby użyć innego **identyfikator serii czasu**.

## <a name="problem-all-my-instances-in-time-series-insights-preview-explorer-dont-have-a-parent"></a>Problem: Moje wystąpienia w usługi Time Series Insights (wersja zapoznawcza) Explorer nie ma elementu nadrzędnego

Może to nastąpić, jeśli Twoje środowisko nie ma **modelu szeregów czasowych** hierarchii zdefiniowane. Zobacz ten artykuł, aby uzyskać więcej informacji na [sposób pracy z modeli szeregów czasowych](./time-series-insights-update-how-to-tsm.md).

## <a name="next-steps"></a>Kolejne kroki

* Odczyt [sposób pracy z modeli szeregów czasowych](./time-series-insights-update-how-to-tsm.md).

* Odczyt [kształty JSON obsługiwany](./how-to-shape-query-json.md).

<!-- Images -->
[1]: media/v2-update-diagnose-and-troubleshoot/dashboard-insights.png
[2]: media/v2-update-diagnose-and-troubleshoot/configuration.png
[3]: media/v2-update-diagnose-and-troubleshoot/permissions.png