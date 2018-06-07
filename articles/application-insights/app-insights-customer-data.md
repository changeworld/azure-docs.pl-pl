---
title: Wskazówki dla danych przechowywanych w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób zarządzania osobistych danych przechowywanych w Azure Application Insights i metody, aby zidentyfikować i usuń go.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: Evgeny.Ternovsky;mbullwin
ms.openlocfilehash: 1f9f7d53fc09111e0060c934e3869326fcaadb7e
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34655074"
---
# <a name="guidance-for-personal-data-stored-in-application-insights"></a>Wskazówki dla danych przechowywanych w usłudze Application Insights

Usługa Application Insights jest magazynem danych, gdzie jest zazwyczaj znajdują się dane osobowe. W tym artykule opisano, gdzie w usłudze Application Insights tych danych zazwyczaj używa się, a także możliwości dostępne do obsługi tych danych.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategia obsługi danych osobowych

Gdy będzie do firmy i ostatecznie ustalenie strategii, z którym będą obsługiwać Twoje prywatne dane (Jeśli w ogóle), poniżej przedstawiono niektóre możliwe metod. Są one wymienione w kolejności preferencji z technicznego punktu widzenia od najbardziej do najmniej preferowane:
* Jeśli to możliwe, Zatrzymaj kolekcję, zasłaniają, anonimowe lub w przeciwnym razie dostosować dane są zbierane w celu wykluczenia go jako _prywatnej_. Ta metoda jest preferowana metoda, co pozwala zaoszczędzić trzeba tworzyć obsługi strategii danych kosztowne i istotna.
* W przypadku, gdy nie jest możliwe, spróbuj normalizacji danych, aby zmniejszyć wpływ na platformie, danych i wydajności. Na przykład zamiast rejestrowania jawnego Identyfikatora użytkownika, utworzyć wyszukiwania, aby dane, które będą skorelowania nazwy użytkownika i ich szczegóły identyfikatorowi wewnętrznego, które następnie mogą być rejestrowane w innym miejscu. W ten sposób, jeśli jeden z użytkowników monit z prośbą o ich dane osobowe, Usuń to możliwe, że tylko usunięcie wiersza w tabeli odnośników przypisana użytkownikowi będą wystarczające. 
* Na koniec należy zbierać dane prywatne, Utwórz proces wokół ścieżki interfejsu API przeczyszczania i istniejącą ścieżkę interfejsu API zapytania zobowiązań wiążące wokół eksportowania i usunięcie wszystkich danych prywatny skojarzony z użytkownikiem.

## <a name="where-to-look-for-private-data-in-application-insights"></a>Gdzie szukać danych prywatnych w usłudze Application Insights?

Usługa Application Insights jest całkowicie elastyczne magazynu, w którym podczas określający schematu do swoich danych pozwala zastąpić każdego pola z wartościami niestandardowymi. Tak nie jest możliwe powiedzieć dokładnie gdzie dane prywatne znajdą się w określonej aplikacji. Następujące lokalizacje, jednak są dobrym punkt wyjścia w spisie:

* *Adresy IP*: gdy usługa Application Insights zostanie domyślnie zasłaniają wszystkich pól adresu IP "0.0.0.0", jest dość wspólnego wzorca do zastąpienia tej wartości, oraz adres IP rzeczywistego użytkownika, aby zachować informacje o sesji. Poniższe zapytanie Analytics można znaleźć żadnych tabeli, która zawiera wartości w kolumnie adres IP innego niż "0.0.0.0" w ciągu ostatnich 24 godzin:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *Identyfikatory użytkowników*: Domyślnie usługi Application Insights użyje identyfikatorów losowo wygenerowany dla użytkownika i śledzenia sesji. Jest jednak częściej można zobaczyć te pola zastąpiona w celu przechowywania Identyfikatora związaną z aplikacją. Na przykład: nazwy użytkowników, identyfikatory GUID AAD, itp. Te identyfikatory są często traktowane jako zakres jako dane osobowe i dlatego powinien zostać obsłużony odpowiednio. Nasze zalecenie jest zawsze próbuje zasłaniają lub anonimowe tych identyfikatorów. Gdy te wartości są często znaleziono pól obejmują session_Id, funkcja user_Id, user_AuthenticatedId, user_AccountId, a także customDimensions.
* *Niestandardowe dane*: usługi Application Insights umożliwia dołączanie zestaw niestandardowych wymiarów do dowolnego typu danych. Te wymiary mogą być *żadnych* danych. Użyj następującego zapytania, aby zidentyfikować wszelkie niestandardowe wymiary pobrane w ciągu ostatnich 24 godzin:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Dane w pamięci i podczas przesyłania*: usługi Application Insights będzie śledzić wyjątki, żądania wywołania zależności i śladów. Często można zbierać dane prywatne na poziomie kodu i wywołania HTTP. Przegląd wyjątki, żądania, zależności i śladów tabel do identyfikowania tych danych. Użyj [inicjatory telemetrii](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) do zasłaniają tych danych.
* *Migawki przechwytywania debugera*: [debugera migawki](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) funkcji w usłudze Application Insights umożliwia zbieranie migawek debugowania zawsze, gdy wystąpił wyjątek w wystąpieniu produkcyjnej aplikacji. Powoduje to udostępnienie migawki ślad stosu pełne, co może prowadzić do wyjątki, a także wartości zmiennych lokalnych w każdym kroku w stosie. Niestety ta funkcja nie zezwala na selektywne usunięcie punktów przyciągania, programistyczny dostęp do danych w migawce. W związku z tym Jeśli domyślna częstotliwość przechowywania migawki nie spełnia wymagania dotyczące zgodności, zalecane jest, aby wyłączyć funkcję.

## <a name="how-to-export-and-delete-private-data"></a>Eksportowanie i usunięcie danych prywatnych

Jest __silnie__ zalecane, jeśli to możliwe, restrukturyzacji zasady zbierania danych, aby wyłączyć zbieranie danych prywatnych obfuscating lub nadanie, lub w inny sposób, modyfikowanie, aby usunąć go z rozważanych "prywatny". Obsługa danych po jego zostały zebrane, spowoduje koszty do Ciebie i Twojego zespołu Zdefiniuj i automatyzować strategii, kompilacji dla klientów na interakcję z danymi za pomocą interfejsu i rutynowej konserwacji. Ponadto jest praktyce kosztowne dla usługi Application Insights i dużej liczby równoczesnych zapytań lub wywołania interfejsu API przeczyszczania mają może mieć negatywny wpływ na wszystkie inne interakcji z funkcjami usługi Application Insights. Które jednak są w rzeczywistości prawidłowe sytuacje, w którym należy zbierać dane prywatne. W tych przypadkach dane powinien zostać obsłużony zgodnie z opisem w tej sekcji.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Wyświetlanie i eksportowanie

Dla obu wyświetlanie i eksportowanie żądania danych [zapytania interfejsu API](https://dev.applicationinsights.io/quickstart) powinien być używany. Logiki można przekonwertować kształt danych do jednego z odpowiednią, aby dostarczać użytkownikom będzie maksymalnie do wdrożenia. [Środowisko Azure Functions](https://azure.microsoft.com/services/functions/) jest doskonałym miejscem do obsługi takich logiki.

### <a name="delete"></a>Usuwanie

> [!WARNING]
> Usuwa w usłudze Application Insights to destrukcyjnego i nieodwracalny! Użyj wyjątkową ostrożność w ich wykonanie.

Wprowadzono dostępne jako część Obsługa prywatności wątek ścieżkę interfejsu API "przeczyścić". Ta ścieżka powinny być używane rzadko z powodu ryzyka związanego z tych czynności potencjalnego wpływu na wydajność oraz możliwości fałszować all-up agregacji, pomiarów i inne aspekty danych usługi Application Insights. Zobacz [strategii obsługi danych osobowych](#strategy-for-personal-data-handling) Powyższa sekcja alternatywnych metod do obsługi danych prywatnych.

Przeczyszczanie jest operacją wysoko uprzywilejowane, że nie aplikacji lub użytkownika na platformie Azure (w tym nawet właściciel zasobu) nie odniesie uprawnień do wykonania nie jest jawnie udzielone roli usługi Azure Resource Manager. Ta rola jest _Purger danych_ i powinna być dokładnie delegowane z powodu potencjalnej utraty danych.

Po przypisaniu roli Menedżera zasobów Azure, dwa nowe ścieżki interfejsu API są dokumentacja dla deweloperów dostępna, pełne i wywołać kształtu połączone:

* [POST przeczyścić](https://docs.microsoft.com/rest/api/application-insights/components/purge) — Trwa określanie parametrów danych do usunięcia obiektu i zwraca identyfikator GUID odniesienia
* GET, Wyczyść stan — wywołanie metody POST przeczyszczania zwróci nagłówek "x-ms stan location", który będzie zawierać adres URL, który można wywołać w celu określenia stanu przeczyszczania interfejsu API. Na przykład:
   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

Podczas gdy przeważająca większość operacji przeczyszczania może zakończyć się znacznie szybszy niż umowy dotyczącej poziomu usług, z powodu ich duże wpływu na platformie dane używane przez usługi Application Insights formalnych SLA zakończenia operacji przeczyszczania wynosi 30 dni.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat sposobu dane są zbierane, przetwarzane i zabezpieczone, zobacz [bezpieczeństwo danych w usłudze Application Insights](app-insights-data-retention-privacy.md).