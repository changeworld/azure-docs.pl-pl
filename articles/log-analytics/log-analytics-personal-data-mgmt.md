---
title: Wskazówki dla danych przechowywanych w Azure Log Analytics | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób zarządzania osobistych danych przechowywanych w Analiza dzienników Azure oraz metody, aby zidentyfikować i usuń go.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 3692c83a4991fc67ec176687bd076ab14e4c640d
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129374"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics"></a>Wskazówki dla danych przechowywanych w analizy dzienników

Analiza dzienników jest magazynem danych, których dane osobiste prawdopodobnie można znaleźć. W tym artykule przedstawimy, gdzie w analizy dzienników takich danych zazwyczaj używa się, a także możliwości dostępne do obsługi tych danych.

[!INCLUDE [gdpr-dsr-and-stp-note](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategia obsługi danych osobowych

Gdy będzie do firmy i ostatecznie ustalenie strategii, z którym będą obsługiwać Twoje prywatne dane (Jeśli w ogóle), poniżej przedstawiono niektóre możliwe metod. Są one wymienione w kolejności preferencji z technicznego punktu widzenia od najbardziej do najmniej preferowane:

* Jeśli to możliwe, Zatrzymaj kolekcję, zasłaniają, anonimowe lub w przeciwnym razie dostosować dane są zbierane w celu wykluczenia go jako "prywatny". Jest to _znacznie_ to preferowane rozwiązanie, co pozwala zaoszczędzić trzeba tworzyć obsługi strategii danych bardzo kosztowne i istotna.
* W przypadku, gdy nie jest możliwe, spróbuj normalizacji danych, aby zmniejszyć wpływ na platformie, danych i wydajności. Na przykład zamiast rejestrowania jawnego Identyfikatora użytkownika, należy utworzyć wyszukiwanie danych, który będzie skorelowania nazwa użytkownika oraz ich szczegóły identyfikatorowi wewnętrznego, które następnie mogą być rejestrowane w innym miejscu. W ten sposób powinien jeden z użytkowników prosić o usunięcie ich dane osobowe, jest to możliwe, że tylko usunięcie wiersza w tabeli odnośników przypisana użytkownikowi będą wystarczające. 
* Na koniec należy zbierać dane prywatne, Utwórz proces wokół ścieżki interfejsu API przeczyszczania i istniejącą ścieżkę interfejsu API zapytania zobowiązań wiążące wokół eksportowania i usunięcie wszystkich danych prywatny skojarzony z użytkownikiem. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Gdzie szukać danych prywatnych w Log Analytics?

Analiza dzienników jest magazynem elastyczne, co podczas określający schematu do swoich danych umożliwia zastąpienie każdego pola z wartościami niestandardowymi. Ponadto można pozyskanych żadnego schematu niestandardowego. W efekcie nie jest możliwe powiedzieć dokładnie gdzie dane prywatne znajdą się w określonym obszarze roboczym. Następujące lokalizacje, jednak są dobrym punkt wyjścia w spisie:

* *Adresy IP*: analizy dzienników zbiera różne informacje IP przez wiele różnych tabel. Na przykład następujące zapytanie Wyświetla wszystkich tabel gdzie adresów IPv4 zostały pobrane w ciągu ostatnich 24 godzin:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *Identyfikatory użytkowników*: identyfikatory użytkowników znajdują się w wielu różnych rozwiązań i tabele. Przez cały zestaw danych za pomocą polecenia wyszukiwania można wyszukać określonego użytkownika:
    ```
    search "[username goes here]"
    ```
Pamiętaj wyglądały nie tylko dla nazwy użytkownika zrozumiałą dla użytkownika, ale także identyfikatory GUID, które bezpośrednio można prześledzić dla określonego użytkownika!
* *Identyfikatory urządzeń*: takie jak nazwy użytkowników, identyfikatory urządzeń są czasami traktowane jako "prywatny". Zastosować te same podejście wymienionych powyżej dla nazwy użytkownika do identyfikacji tabel w przypadku, gdy może to być problemem. 
* *Niestandardowe dane*: Log Analytics umożliwia to zbieranie danych w różnych metod: dzienników niestandardowych i pól niestandardowych, [interfejsu API modułów zbierających dane HTTP](log-analytics-data-collector-api.md) , i niestandardowych danych zebranych w ramach dzienniki zdarzeń systemowych. Wszystkie te są podatne na zawierające dane prywatne, a należy zbadać, aby sprawdzić, czy istnieje tych danych.
* *Rozwiązanie, przechwycone dane*: ponieważ mechanizm rozwiązania jest otwarty, firma Microsoft zaleca przejrzenie wszystkich tabel generowane przez rozwiązań w celu zapewnienia zgodności.

## <a name="how-to-export-and-delete-private-data"></a>Eksportowanie i usunięcie danych prywatnych

Jak wspomniano w [strategii obsługi danych osobowych](#strategy-for-personal-data-handling) wcześniejszej sekcji, jest __silnie__ zalecane, jeśli wszystkie możliwe restrukturyzacji zasady kolekcji danych, aby wyłączyć zbieranie dane prywatne, obfuscating nadanie go lub modyfikowanie go, aby usunąć go z rozważanych "prywatny". Obsługa danych przedniej spowoduje koszty do Ciebie i Twojego zespołu Zdefiniuj i automatyzować strategii, kompilacji dla klientów na interakcję z danymi za pomocą interfejsu i rutynowej konserwacji. Ponadto jest praktyce kosztowne analizy dzienników i dużej liczby równoczesnych zapytań lub wywołania interfejsu API przeczyszczania mają może mieć negatywny wpływ na wszystkie inne interakcji z funkcją analizy dzienników. Które jednak są w rzeczywistości prawidłowe sytuacje, w którym należy zbierać dane prywatne. W tych przypadkach dane powinien zostać obsłużony zgodnie z opisem w tej sekcji.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Wyświetlanie i eksportowanie

Dla obu wyświetlanie i eksportowanie żądania danych [zapytania interfejsu API](https://dev.loganalytics.io/) powinien być używany. Logiki można przekonwertować kształt danych do jednego z odpowiednią, aby dostarczać użytkownikom będzie maksymalnie do wdrożenia. [Środowisko Azure Functions](https://azure.microsoft.com/services/functions/) sprawia, że doskonałym miejscem do obsługi takich logiki.

### <a name="delete"></a>Usuwanie

> [!WARNING]
> Usuwa w analizy dzienników są destrukcyjnego i nieodwracalny! Użyj wyjątkową ostrożność w ich wykonanie.

Wprowadzono dostępne w ramach obsługi prywatności *przeczyścić* ścieżkę interfejsu API. Ta ścieżka powinny być używane rzadko z powodu ryzyka związanego z tych czynności potencjalnego wpływu na wydajność oraz możliwości fałszować all-up agregacji, pomiarów i inne aspekty danych analizy dzienników. Zobacz [strategii obsługi danych osobowych](#strategy-for-personal-data-handling) sekcji alternatywnych metod do obsługi danych prywatnych.

Przeczyszczanie jest operacją wysoko uprzywilejowane, że nie aplikacji lub użytkownika na platformie Azure (w tym nawet właściciel zasobu) nie odniesie uprawnień do wykonania nie jest jawnie udzielone roli usługi Azure Resource Manager. Ta rola jest _Purger danych_ i powinny być delegowane ostrożnie z powodu potencjalnej utraty danych. 

Po przypisaniu roli Menedżera zasobów Azure, dostępne są dwa nowe ścieżki interfejsu API: 

* [POST przeczyszczania] (https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) — Trwa określanie parametrów danych do usunięcia obiektu i zwraca identyfikator GUID odniesienia 
* GET, Wyczyść stan — wywołanie metody POST przeczyszczania zwróci nagłówek "x-ms stan location", który będzie zawierać adres URL, który można wywołać w celu określenia stanu przeczyszczania interfejsu API. Na przykład:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperatonalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

Gdy oczekujemy, większość operacji przeczyszczania przeprowadzenie znacznie szybszy niż naszych umowy dotyczącej poziomu usług, z powodu ich duże wpływu na platformie danych używany przez analizy dzienników formalnych SLA zakończenia operacji przeczyszczania jest ustawiona na 30 dni. 

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat sposobu dane są zbierane, przetwarzane i zabezpieczone, zobacz [bezpieczeństwo danych analizy dzienników](log-analytics-data-security.md).