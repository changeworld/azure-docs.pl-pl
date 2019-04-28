---
title: Wskazówki dotyczące danych osobowych przechowywanych w usłudze Azure Log Analytics | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób zarządzania danych osobowych przechowywanych w usłudze Azure Log Analytics i metod do identyfikowania i usunąć go.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/18/2018
ms.author: magoedte
ms.openlocfilehash: 0cf5a80e3eedbe7efb8463162b5b3ed489ac08c8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "61087289"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Wskazówki dotyczące danych osobowych przechowywanych w usłudze Log Analytics i usługi Application Insights

Log Analytics jest magazyn danych, gdzie jest zazwyczaj znajdują się dane osobowe. Usługa Application Insights przechowuje dane w partycji usługi Log Analytics. W tym artykule przedstawimy, których w usłudze Log Analytics i usługi Application Insights tych danych jest zwykle można znaleźć, a także możliwości dostępne do obsługi tych danych.

> [!NOTE]
> Na potrzeby tego artykułu _możesz rejestrować dane_ odwołuje się do danych wysyłanych do obszaru roboczego usługi Log Analytics, podczas gdy _dane aplikacji_ odwołuje się do danych zbieranych przez usługę Application Insights.

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategia obsługi danych osobowych

Gdy będzie do firmy i ostatecznie ustalenie strategii, za pomocą którego będzie obsługiwać Twoje prywatne dane (Jeśli w ogóle), poniżej przedstawiono kilka możliwych podejść. Są one wymienione w kolejności preferencji z technicznego punktu widzenia od najbardziej do najmniej preferowane:

* Jeśli to możliwe, Zatrzymaj kolekcję, zaciemniania, zachowywania anonimowości lub inny sposób dostosować dane są zbierane w celu wykluczenia go ze są traktowane jako "private". Jest to _najpopularniejszą_ jest preferowanym podejściem zaoszczędzić konieczności tworzenia bardzo kosztowne i wpływie stosowanej strategii obsługi danych.
* W przypadku, gdy nie jest to możliwe, należy próbować normalizacji danych, aby zmniejszyć wpływ na platforma danych i wydajności. Na przykład zamiast rejestrowanie jawny identyfikator użytkownika, należy utworzyć dane wyszukiwania, które zostaną skorelowane nazwy użytkownika i ich szczegóły, aby wewnętrzny identyfikator, które następnie mogą być rejestrowane w innym miejscu. Dzięki temu jeden z użytkowników zapytaj możesz usunąć swoje informacje osobiste, jest możliwe, że tylko usunięcie wiersza w tabeli odnośników przypisana użytkownikowi okażą się wystarczające. 
* Na koniec należy zbierać dane prywatne, Utwórz proces wokół ścieżki API przeczyszczania i istniejącą ścieżkę interfejsu API zapytań żadnych zobowiązań, które mogą wiązać Ciebie wokół eksportowania i usuwania wszelkich skojarzonych z użytkownikiem prywatnych danych. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Gdzie szukać danych prywatnych w usłudze Log Analytics?

Log Analytics jest elastyczne magazynu, umożliwiająca podczas określający schematu z danymi, Zastąp każde pole z wartościami niestandardowymi. Ponadto można pozyskać żadnego schematu niestandardowego. Jako takie istnieje możliwość, że dokładnie gdzie prywatnych danych zostanie znaleziony w określonym obszarze roboczym. Następujące lokalizacje, jednak są dobre punktów początkowych w spisie:

### <a name="log-data"></a>Dane dziennika

* *Adresy IP*: Usługa log Analytics zbiera różne informacje o adresie IP w wielu różnych tabelach. Na przykład następujące zapytanie Wyświetla wszystkie tabele gdzie adresy IPv4 zostały połączone w ciągu ostatnich 24 godzin:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *Identyfikatory użytkowników*: Identyfikatory użytkowników znajdują się w wielu różnych rozwiązań i tabel. Można wyszukać określoną nazwę użytkownika dla całego zestawu danych za pomocą polecenia wyszukiwania:
    ```
    search "[username goes here]"
    ```
  Pamiętaj, że do wyszukiwania nie tylko dla nazwy użytkownika czytelny dla człowieka, ale także identyfikatory GUID, które można bezpośrednio prześledzić dla określonego użytkownika!
* *Identyfikatory urządzeń*: Np. identyfikatory użytkowników w identyfikatorach urządzeń są czasami traktowane jako "private". Użyj tej samej metody wymienionych powyżej dla nazwy użytkownika do identyfikowania tabel w przypadku, gdy może to być istotna. 
* *Dane niestandardowe*: Usługa log Analytics umożliwia zbieranie w różnych metod: dzienników niestandardowych i pól niestandardowych, [interfejsu API modułu zbierającego dane HTTP](../../azure-monitor/platform/data-collector-api.md) , i niestandardowe dane zbierane w ramach dzienniki zdarzeń systemu. Wszystkie te są podatne na zawierający dane prywatne i należy zbadać, aby sprawdzić, czy istnieje tych danych.
* *Dane przechwycone przez rozwiązanie*: Ponieważ mechanizm rozwiązania jest nieograniczony, zaleca się przegląd wszystkich tabel, generowane przez rozwiązania w celu zapewnienia zgodności.

### <a name="application-data"></a>Dane aplikacji

* *Adresy IP*: Usługa Application Insights będzie domyślnie zaciemniania wszystkich pól adresu IP "0.0.0.0", ale jest dość typowy wzorzec, aby zastąpić tę wartość za pomocą adresu IP rzeczywistego użytkownika, aby zachować informacje o sesji. Poniższe zapytanie analizy można znaleźć każda tabela, która zawiera wartości w kolumnie adres IP innego niż "0.0.0.0" w ciągu ostatnich 24 godzin:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *Identyfikatory użytkowników*: Domyślnie usługa Application Insights będzie używać losowo generowany identyfikatorów użytkowników i sesji śledzenia. Jednak jest częściej można zobaczyć te pola zastąpiona w celu przechowywania Identyfikatora związaną z aplikacją. Na przykład: nazw użytkowników, GUID usługi AAD itp. Te identyfikatory są często traktowane jako zakresu jako dane osobowe i dlatego powinien zostać obsłużony odpowiednio. Nasze zalecenie, jest zawsze próbuje zaciemniania lub zachowywania anonimowości tych identyfikatorów. Pola, w których często występują te wartości obejmują session_Id, definiując pole user_Id, user_AuthenticatedId, user_AccountId, a także tabeli customDimensions.
* *Dane niestandardowe*: Usługa Application Insights pozwala dołączyć zbiór wymiary niestandardowe dowolnego typu danych. Te wymiary mogą być *wszelkie* danych. Użyj następującego zapytania, aby zidentyfikować wszystkie wymiary niestandardowe zebrane w ciągu ostatnich 24 godzin:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Dane w pamięci i podczas przesyłania*: Usługa Application Insights będzie śledzić wyjątki, żądania, wywołań zależności i śladów. Prywatne dane często mogły być zbierane na poziomie kodu i wywołania HTTP. Przejrzyj wyjątki, żądania, zależności i śladów tabel do identyfikowania tych danych. Użyj [inicjatory telemetrii](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) możliwe do zaciemniania tych danych.
* *Snapshot Debugger przechwytywania*: [Rozszerzenia Snapshot Debugger](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) funkcji w usłudze Application Insights umożliwia zbieranie migawki debugowania zawsze wtedy, gdy wystąpił wyjątek w wystąpieniu produkcyjnych aplikacji. Migawki udostępni pełen ślad stosu prowadzące do wyjątków, a także wartości dla zmiennych lokalnych na każdym etapie w stosie. Niestety ta funkcja nie zezwala na selektywne usuwanie punktów przyciągania lub programowy dostęp do danych w migawce. W związku z tym Jeśli szybkość przechowywania migawek domyślny nie spełnia wymagań dotyczących zgodności, zaleca się wyłączyć funkcję.

## <a name="how-to-export-and-delete-private-data"></a>Jak eksportować i usuwać dane prywatne

Jak wspomniano w [strategii do obsługi danych osobowych](#strategy-for-personal-data-handling) wcześniejszej sekcji, jest __silnie__ zalecane, jeśli wszystkie możliwe restrukturyzacji zasady zbierania danych można wyłączyć kolekcji dane prywatne, polega na zaciemnianiu nadanie jej lub w przeciwnym razie sposobów usuwania są traktowane jako "private". Obsługa danych przedniej spowoduje kosztów dla Ciebie i Twojego zespołu do definiowania i zautomatyzować strategii, tworzenia interfejsu dla swoich klientów do interakcji z danymi za pośrednictwem i kosztów rutynowej konserwacji. Co więcej to w praktyce kosztowne dla usługi Log Analytics, Application Insights i dużej liczby równoczesnych zapytań lub wywołania interfejsu API przeczyszczania mają mogą mieć negatywny wpływ na wszystkie interakcje z funkcji usługi Log Analytics. Jednak są w rzeczywistości prawidłowe sytuacje, w której muszą być zbierane dane prywatne. W takich przypadkach dane powinny być traktowane zgodnie z opisem w tej sekcji.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Wyświetlanie i eksportowanie

Dla obu wyświetlanie i eksportowanie żądań danych [interfejsu API zapytań usługi Log Analytics](https://dev.loganalytics.io/) lub [usługi Application Insights zapytania interfejsu API](https://dev.applicationinsights.io/quickstart) powinny być używane. Logika do przekonwertowania kształtu danych na jeden z nich można dostarczać użytkownikom będzie maksymalnie można zaimplementować. [Usługa Azure Functions](https://azure.microsoft.com/services/functions/) ułatwia wspaniałego programu do obsługi takich logiki.

> [!IMPORTANT]
>  Chociaż większość operacji przeczyszczania może zakończyć się znacznie szybsze niż umowa SLA, **formalnych umowa SLA na ukończenie operacji przeczyszczanie równego 30 dni** ze względu na ich mocno wpływ na platformie danych używane. Jest to zautomatyzowanego procesu; nie ma sposobu na żądanie, szybciej obsługi operacji.

### <a name="delete"></a>Usuwanie

> [!WARNING]
> Usuwa w usłudze Log Analytics są destrukcyjne i nieodwracalny! Użyj wyjątkową ostrożność podczas ich wykonywania.

Udostępniliśmy obsługi ochrony prywatności w ramach *przeczyścić* ścieżkę interfejsu API. Tej ścieżki należy używać oszczędnie, z powodu ryzyka związanego z tych czynności potencjalnego wpływu na wydajność i potencjalnie pochylanie wszelkie agregacje, pomiarów i innych aspektów danych usługi Log Analytics. Zobacz [strategii do obsługi danych osobowych](#strategy-for-personal-data-handling) sekcji alternatywnych metod do obsługi danych prywatnych.

Przeczyszczanie jest operacją o wysokim poziomie uprawnień, czy nie aplikacji lub użytkownika na platformie Azure (w tym nawet właściciel zasobu) będą miały uprawnienia do wykonania nie zostały wyraźnie przyznane roli w usłudze Azure Resource Manager. Ta rola jest _Purger danych_ i powinny być delegowane ostrożnie ze względu na ryzyko utraty danych. 

Po przypisaniu roli usługi Azure Resource Manager dostępne są dwa nowe ścieżki interfejsu API: 

#### <a name="log-data"></a>Dane dziennika

* [POST przeczyścić](https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) — przyjmuje obiekt określający parametry danych do usunięcia i zwraca odwołanie identyfikatora GUID 
* GET, Wyczyść stan — wywołanie metody przeczyszczania POST będzie zwracać nagłówek "x-ms stan location", która będzie zawierać adres URL, który można wywołać w celu określenia stanu przeczyszczania interfejsu API. Na przykład:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperatonalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  Gdy oczekujemy, że większość operacji przeczyszczania do ukończenia znacznie szybsze niż umowach SLA, ze względu na ich wpływ dużych na platformie danych używane przez usługę Log Analytics **formalnych umowa SLA na ukończenie operacji przeczyszczanie równego 30 dni**. 

#### <a name="application-data"></a>Dane aplikacji

* [POST przeczyścić](https://docs.microsoft.com/rest/api/application-insights/components/purge) — przyjmuje obiekt określający parametry danych do usunięcia i zwraca odwołanie identyfikatora GUID
* GET, Wyczyść stan — wywołanie metody przeczyszczania POST będzie zwracać nagłówek "x-ms stan location", która będzie zawierać adres URL, który można wywołać w celu określenia stanu przeczyszczania interfejsu API. Na przykład:

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Chociaż większość operacji przeczyszczania może zakończyć się znacznie szybsze niż umowa SLA, ze względu na ich wpływ dużych na platformie danych używane przez usługę Application Insights **formalnych umowa SLA na ukończenie operacji przeczyszczanie równego 30 dni**.

## <a name="next-steps"></a>Kolejne kroki
- Aby dowiedzieć się więcej na temat jak usługi Log Analytics dane są zbierane, przetwarzane i zabezpieczane, zobacz [bezpieczeństwo danych usługi Log Analytics](../../azure-monitor/platform/data-security.md).
- Aby dowiedzieć się więcej na temat jak Application Insights dane są zbierane, przetwarzane i zabezpieczane, zobacz [bezpieczeństwo danych w usłudze Application Insights](../../azure-monitor/app/data-retention-privacy.md).
