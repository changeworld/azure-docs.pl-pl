---
title: Wskazówki dotyczące danych osobowych przechowywanych w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób zarządzania dane osobowe przechowywane w usługi Azure Application Insights i metody, aby zidentyfikować i usuń go.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.reviewer: Evgeny.Ternovsky
ms.author: mbullwin
ms.openlocfilehash: a59b57c546f18a7d91160f2ae7282af82fc42160
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/14/2018
ms.locfileid: "39044717"
---
# <a name="guidance-for-personal-data-stored-in-application-insights"></a>Wskazówki dotyczące danych osobowych przechowywanych w usłudze Application Insights

Application Insights to magazyn danych, gdzie jest zazwyczaj znajdują się dane osobowe. W tym artykule omówiono, w których w usłudze Application Insights tych danych jest zwykle można znaleźć, a także możliwości dostępne do obsługi tych danych.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategia obsługi danych osobowych

Gdy będzie do firmy i ostatecznie ustalenie strategii, za pomocą którego będzie obsługiwać Twoje prywatne dane (Jeśli w ogóle), poniżej przedstawiono kilka możliwych podejść. Są one wymienione w kolejności preferencji z technicznego punktu widzenia od najbardziej do najmniej preferowane:
* W przypadku, gdy jest to możliwe, Zatrzymaj kolekcję, zaciemniania, zachowywania anonimowości lub inny sposób dostosować dane są zbierane w celu wykluczenia go ze uznawane za _prywatnej_. Ta metoda jest preferowana, co trzeba utworzyć kosztowne i wpływie dane, stosowanej strategii obsługi.
* W przypadku, gdy nie jest to możliwe, należy próbować normalizacji danych, aby zmniejszyć wpływ na platforma danych i wydajności. Na przykład zamiast rejestrowanie jawny identyfikator użytkownika, utworzyć wyszukiwania, aby dane, które zostaną skorelowane nazwy użytkownika i ich szczegóły, aby wewnętrzny identyfikator, które następnie mogą być rejestrowane w innym miejscu. Dzięki temu, jeśli jeden z użytkowników zostaje monit o podanie usunąć swoje informacje osobiste, jest możliwe, że tylko usunięcie wiersza w tabeli odnośników przypisana użytkownikowi okażą się wystarczające. 
* Na koniec należy zbierać dane prywatne, Utwórz proces wokół ścieżki API przeczyszczania i istniejącą ścieżkę interfejsu API zapytań żadnych zobowiązań, które mogą wiązać Ciebie wokół eksportowania i usuwania wszelkich skojarzonych z użytkownikiem prywatnych danych.

## <a name="where-to-look-for-private-data-in-application-insights"></a>Gdzie szukać danych prywatnych w usłudze Application Insights?

Usługa Application Insights jest całkowicie elastycznego magazynu, umożliwiające podczas określający schematu z danymi, Zastąp każde pole z wartościami niestandardowymi. Jako takie istnieje możliwość, że dokładnie gdzie prywatnych danych zostanie znaleziony w określonej aplikacji. Następujące lokalizacje, jednak są dobre punktów początkowych w spisie:

* *Adresy IP*: gdy usługa Application Insights będzie domyślnie zaciemniania wszystkich pól adresu IP "0.0.0.0", jest dość typowy wzorzec, aby zastąpić tę wartość za pomocą adresu IP rzeczywistego użytkownika, aby zachować informacje o sesji. Poniższe zapytanie analizy można znaleźć każda tabela, która zawiera wartości w kolumnie adres IP innego niż "0.0.0.0" w ciągu ostatnich 24 godzin:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *Identyfikatory użytkowników*: Domyślnie usługa Application Insights użyje losowo generowany identyfikatorów użytkowników i sesji śledzenia. Jednak jest częściej można zobaczyć te pola zastąpiona w celu przechowywania Identyfikatora związaną z aplikacją. Na przykład: nazw użytkowników, GUID usługi AAD itp. Te identyfikatory są często traktowane jako zakresu jako dane osobowe i dlatego powinien zostać obsłużony odpowiednio. Nasze zalecenie, jest zawsze próbuje zaciemniania lub zachowywania anonimowości tych identyfikatorów. Pola, w których często występują te wartości obejmują session_Id, definiując pole user_Id, user_AuthenticatedId, user_AccountId, a także tabeli customDimensions.
* *Dane niestandardowe*: usługi Application Insights pozwala dołączyć zbiór wymiary niestandardowe dowolnego typu danych. Te wymiary mogą być *wszelkie* danych. Użyj następującego zapytania, aby zidentyfikować wszystkie wymiary niestandardowe zebrane w ciągu ostatnich 24 godzin:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Dane w pamięci i podczas przesyłania*: usługi Application Insights będzie śledzić wyjątki, żądania, wywołań zależności i śladów. Prywatne dane często mogły być zbierane na poziomie kodu i wywołania HTTP. Przejrzyj wyjątki, żądania, zależności i śladów tabel do identyfikowania tych danych. Użyj [inicjatory telemetrii](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) możliwe do zaciemniania tych danych.
* *Snapshot Debugger przechwytywania*: [rozszerzenia Snapshot Debugger](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) funkcji w usłudze Application Insights umożliwia zbieranie migawki debugowania zawsze wtedy, gdy wystąpił wyjątek w wystąpieniu produkcyjnych aplikacji. Migawki udostępni pełen ślad stosu prowadzące do wyjątków, a także wartości dla zmiennych lokalnych na każdym etapie w stosie. Niestety ta funkcja nie zezwala na selektywne usuwanie punktów przyciągania lub programowy dostęp do danych w migawce. W związku z tym Jeśli szybkość przechowywania migawek domyślny nie spełnia wymagań dotyczących zgodności, zaleca się wyłączyć funkcję.

## <a name="how-to-export-and-delete-private-data"></a>Jak eksportować i usuwać dane prywatne

Jest __silnie__ zalecane, gdy jest to możliwe, restrukturyzacji usługi zasady zbierania danych, aby wyłączyć zbieranie danych prywatnych polega na zaciemnianiu lub zachowywanie anonimowości, lub w inny sposób, modyfikowania go do usuwania są traktowane jako "private". Obsługa danych, po jego został zebranych, spowodują kosztów dla Ciebie i Twojego zespołu do definiowania i zautomatyzować strategii, tworzenia interfejsu dla swoich klientów do interakcji z danymi za pośrednictwem i kosztów rutynowej konserwacji. Co więcej to w praktyce kosztowne dla usługi Application Insights i dużej liczby równoczesnych zapytań lub wywołania interfejsu API przeczyszczania mają mogą mieć negatywny wpływ na wszystkie inne interakcje z funkcji usługi Application Insights. Jednak są w rzeczywistości prawidłowe sytuacje, w której muszą być zbierane dane prywatne. W takich przypadkach dane powinny być traktowane zgodnie z opisem w tej sekcji.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Wyświetlanie i eksportowanie

Dla obu wyświetlanie i eksportowanie żądań danych [interfejsu API zapytań](https://dev.applicationinsights.io/quickstart) powinny być używane. Logika do przekonwertowania kształtu danych na jeden z nich można dostarczać użytkownikom będzie maksymalnie można zaimplementować. [Usługa Azure Functions](https://azure.microsoft.com/services/functions/) jest doskonałym miejscem do obsługi takich logiki.

### <a name="delete"></a>Usuwanie

> [!WARNING]
> Usuwa w usłudze Application Insights są destrukcyjne i nieodwracalny! Użyj wyjątkową ostrożność podczas ich wykonywania.

Udostępniliśmy jako części obsługi zachowania historii ścieżkę interfejsu API "Wyczyść". Tej ścieżki należy używać oszczędnie, z powodu ryzyka związanego z tych czynności potencjalnego wpływu na wydajność i potencjalnie pochylanie wszelkie agregacje, pomiarów i innych aspektów danych usługi Application Insights. Zobacz [strategii do obsługi danych osobowych](#strategy-for-personal-data-handling) Powyższa sekcja alternatywnych metod do obsługi danych prywatnych.

Przeczyszczanie jest operacją o wysokim poziomie uprawnień, czy nie aplikacji lub użytkownika na platformie Azure (w tym nawet właściciel zasobu) będą miały uprawnienia do wykonania nie zostały wyraźnie przyznane roli w usłudze Azure Resource Manager. Ta rola jest _Purger danych_ i powinny być dokładnie delegowane ze względu na ryzyko utraty danych.

Po przypisaniu roli usługi Azure Resource Manager dwóch nowych ścieżek interfejsu API są dokumentacji dla deweloperów dostępne, pełne i wywołać kształtu połączonego:

* [POST przeczyścić](https://docs.microsoft.com/rest/api/application-insights/components/purge) — przyjmuje obiekt określający parametry danych do usunięcia i zwraca odwołanie identyfikatora GUID
* GET, Wyczyść stan — wywołanie metody przeczyszczania POST będzie zwracać nagłówek "x-ms stan location", która będzie zawierać adres URL, który można wywołać w celu określenia stanu przeczyszczania interfejsu API. Na przykład:
   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Chociaż większość operacji przeczyszczania może zakończyć się znacznie szybsze niż umowa SLA, ze względu na ich wpływ dużych na platformie danych używane przez usługę Application Insights **formalnych umowa SLA na ukończenie operacji przeczyszczanie równego 30 dni**.

## <a name="next-steps"></a>Kolejne kroki
Aby dowiedzieć się więcej na temat sposobu dane są zbierane, przetwarzane i zabezpieczane, zobacz [bezpieczeństwo danych w usłudze Application Insights](app-insights-data-retention-privacy.md).