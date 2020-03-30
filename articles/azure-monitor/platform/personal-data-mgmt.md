---
title: Wskazówki dotyczące danych osobowych przechowywanych w usłudze Azure Log Analytics| Dokumenty firmy Microsoft
description: W tym artykule opisano sposób zarządzania danymi osobistymi przechowywanymi w usłudze Azure Log Analytics oraz metodami ich identyfikowania i usuwania.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/18/2018
ms.openlocfilehash: a720627e1783d2e29ef180b7855132ea59444cab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248751"
---
# <a name="guidance-for-personal-data-stored-in-log-analytics-and-application-insights"></a>Wskazówki dotyczące danych osobowych przechowywanych w usłudze Log Analytics i usłudze Application Insights

Usługa Log Analytics to magazyn danych, w którym prawdopodobnie można znaleźć dane osobowe. Usługa Application Insights przechowuje swoje dane w partycji usługi Log Analytics. W tym artykule omówimy, gdzie w usłudze Log Analytics i usługi Application Insights takie dane są zazwyczaj znalezione, a także możliwości dostępne do obsługi takich danych.

> [!NOTE]
> Na potrzeby tego _artykułu dziennika danych_ odnosi się do danych wysyłanych do obszaru roboczego usługi Log Analytics, podczas gdy dane _aplikacji_ odnosi się do danych zebranych przez usługa Application Insights.

[!INCLUDE [gdpr-dsr-and-stp-note](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="strategy-for-personal-data-handling"></a>Strategia przetwarzania danych osobowych

Chociaż to do Ciebie i Twojej firmy, aby ostatecznie określić strategię, z którą będziesz obsługiwać swoje prywatne dane (jeśli w ogóle), oto kilka możliwych podejść. Są one wymienione w kolejności preferencji z technicznego punktu widzenia od większości do najmniej korzystnych:

* W miarę możliwości należy zatrzymać zbieranie, zaciemnić, anonimizować lub w inny sposób dostosować gromadzone dane, aby wykluczyć je z uznania ich za "prywatne". Jest to _zdecydowanie_ preferowane podejście, co pozwala zaoszczędzić na konieczności stworzenia bardzo kosztownej i wpływowej strategii obsługi danych.
* Jeśli nie jest to możliwe, spróbuj znormalizować dane, aby zmniejszyć wpływ na platformę danych i wydajność. Na przykład zamiast rejestrowania jawnego identyfikatora użytkownika, utwórz dane wyszukiwania, które skorelują nazwę użytkownika i ich szczegóły z wewnętrznym identyfikatorem, który następnie można zarejestrować w innym miejscu. W ten sposób, jeśli jeden z użytkowników poprosi cię o usunięcie swoich informacji osobistych, możliwe jest, że wystarczy tylko usunięcie wiersza w tabeli odnośnych odpowiadających użytkownikowi. 
* Na koniec jeśli dane prywatne muszą być zbierane, skompiluj proces wokół ścieżki interfejsu API przeczyszczania i istniejącej ścieżki interfejsu API kwerendy, aby spełnić wszelkie obowiązki związane z eksportem i usuwaniem wszelkich prywatnych danych skojarzonych z użytkownikiem. 

## <a name="where-to-look-for-private-data-in-log-analytics"></a>Gdzie szukać prywatnych danych w usłudze Log Analytics?

Usługa Log Analytics to elastyczny magazyn, który podczas przepisywania schematu danych umożliwia zastąpienie każdego pola wartościami niestandardowymi. Ponadto można przybyć dowolny schemat niestandardowy. W związku z tym nie można dokładnie określić, gdzie dane prywatne zostaną znalezione w określonym obszarze roboczym. Następujące lokalizacje są jednak dobrymi punktami wyjścia w ekwipunku:

### <a name="log-data"></a>Dane dziennika

* *Adresy IP:* Usługa Log Analytics zbiera różne informacje o adresach IP w wielu różnych tabelach. Na przykład następująca kwerenda pokazuje wszystkie tabele, w których adresy IPv4 zostały zebrane w ciągu ostatnich 24 godzin:
    ```
    search * 
    | where * matches regex @'\b((25[0-5]|2[0-4][0-9]|[01]?[0-9][0-9]?)(\.|$)){4}\b' //RegEx originally provided on https://stackoverflow.com/questions/5284147/validating-ipv4-addresses-with-regexp
    | summarize count() by $table
    ```
* *Identyfikatory użytkowników*: Identyfikatory użytkowników znajdują się w wielu różnych rozwiązaniach i tabelach. Możesz wyszukać określoną nazwę użytkownika w całym zestawie danych za pomocą polecenia wyszukiwania:
    ```
    search "[username goes here]"
    ```
  Pamiętaj, aby szukać nie tylko nazw użytkowników czytelnych dla człowieka, ale także identyfikatorów GUID, które można bezpośrednio prześledzić z konkretnym użytkownikiem!
* *Identyfikatory urządzeń:* Podobnie jak identyfikatory użytkowników, identyfikatory urządzeń są czasami uważane za "prywatne". Użyj tego samego podejścia, jak wymienione powyżej dla identyfikatorów użytkowników do identyfikowania tabel, gdzie może to być problemem. 
* *Dane niestandardowe:* Usługa Log Analytics umożliwia zbieranie w różnych metodach: dzienniki niestandardowe i pola niestandardowe, [interfejs API modułu zbierającego dane HTTP](../../azure-monitor/platform/data-collector-api.md) i dane niestandardowe zbierane w ramach dzienników zdarzeń systemowych. Wszystkie te dane są podatne na zawierające dane prywatne i należy je zbadać w celu sprawdzenia, czy takie dane istnieją.
* *Dane przechwycone przez rozwiązanie:* Ponieważ mechanizm rozwiązania jest otwarty, zalecamy przejrzenie wszystkich tabel generowanych przez rozwiązania w celu zapewnienia zgodności.

### <a name="application-data"></a>Dane aplikacji

* *Adresy IP:* Podczas gdy usługa Application Insights domyślnie zaciemni wszystkie pola adresu IP na "0.0.0.0", jest dość powszechnym wzorcem, aby zastąpić tę wartość rzeczywistym adresem IP użytkownika w celu zachowania informacji o sesji. Poniższe zapytanie Analytics może służyć do znajdowania dowolnej tabeli zawierającej wartości w kolumnie adres IP inne niż "0.0.0.0" w ciągu ostatnich 24 godzin:
    ```
    search client_IP != "0.0.0.0"
    | where timestamp > ago(1d)
    | summarize numNonObfuscatedIPs_24h = count() by $table
    ```
* *Identyfikatory użytkowników:* Domyślnie usługa Application Insights będzie używać losowo generowanych identyfikatorów do śledzenia użytkowników i sesji. Jednak jest często, aby zobaczyć te pola zastąpione do przechowywania identyfikatora bardziej istotne dla aplikacji. Na przykład: nazwy użytkowników, identyfikatory GUID AAD itp. Te identyfikatory są często uważane za w zakresie danych osobowych, a zatem powinny być traktowane odpowiednio. Naszą rekomendacją jest zawsze próba zaciemnienia lub anonimizacji tych identyfikatorów. Pola, w których te wartości są powszechnie spotykane, obejmują session_Id, user_Id, user_AuthenticatedId, user_AccountId, a także niestandardoweRozmień.
* *Dane niestandardowe:* Usługa Application Insights umożliwia dołączanie zestawu wymiarów niestandardowych do dowolnego typu danych. Te wymiary mogą być *dowolnymi* danymi. Użyj następującej kwerendy, aby zidentyfikować wszystkie wymiary niestandardowe zebrane w ciągu ostatnich 24 godzin:
    ```
    search * 
    | where isnotempty(customDimensions)
    | where timestamp > ago(1d)
    | project $table, timestamp, name, customDimensions 
    ```
* *Dane w pamięci i podczas przesyłania:* Usługa Application Insights będzie śledzić wyjątki, żądania, wywołania zależności i ślady. Prywatne dane często mogą być zbierane na poziomie kodu i wywołania HTTP. Przejrzyj tabele wyjątków, żądań, zależności i śledzenia, aby zidentyfikować takie dane. Użyj [inicjatorów telemetrii,](https://docs.microsoft.com/azure/application-insights/app-insights-api-filtering-sampling) jeśli to możliwe, aby zaciemnić te dane.
* *Debuger migawek przechwytuje:* Funkcja [debugera migawki](https://docs.microsoft.com/azure/application-insights/app-insights-snapshot-debugger) w usłudze Application Insights umożliwia zbieranie migawek debugowania za każdym razem, gdy wyjątek zostanie przechwycony w wystąpieniu produkcyjnym aplikacji. Migawki będą uwidaczniać śledzenia pełnego stosu prowadzi do wyjątków, jak również wartości dla zmiennych lokalnych na każdym kroku w stosie. Niestety ta funkcja nie pozwala na selektywne usuwanie punktów przyciągania lub programowy dostęp do danych w migawce. W związku z tym jeśli domyślna szybkość przechowywania migawki nie spełnia wymagań zgodności, zalecenie jest, aby wyłączyć funkcję.

## <a name="how-to-export-and-delete-private-data"></a>Jak eksportować i usuwać dane prywatne

Jak wspomniano w [sekcji strategii przetwarzania danych osobowych](#strategy-for-personal-data-handling) wcześniej, __zdecydowanie__ zaleca się, jeśli to wszystko możliwe, restrukturyzacji polityki gromadzenia danych, aby wyłączyć gromadzenie danych prywatnych, zaciemniając lub anonimizując je, lub w inny sposób modyfikując go, aby usunąć je z bycia uważanym za "prywatny". Obsługa danych spowoduje przede wszystkim koszty dla Ciebie i Twojego zespołu w celu zdefiniowania i zautomatyzowania strategii, utworzenia interfejsu dla klientów do interakcji z ich danymi za pośrednictwem i bieżących kosztów konserwacji. Ponadto jest obliczeniowo kosztowne dla usługi Log Analytics i usługi Application Insights, a duża ilość równoczesnych wywołań zapytań lub przeczyszczania interfejsu API może negatywnie wpłynąć na wszystkie inne interakcje z funkcjami usługi Log Analytics. To powiedzia po to, że rzeczywiście istnieją pewne ważne scenariusze, w których należy gromadzić prywatne dane. W takich przypadkach dane powinny być obsługiwane zgodnie z opisem w tej sekcji.

[!INCLUDE [gdpr-intro-sentence](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-and-export"></a>Wyświetlanie i eksportowanie

W przypadku żądań danych zarówno widoku, jak i eksportu należy użyć [interfejsu API kwerendy usługi Log Analytics](https://dev.loganalytics.io/) lub interfejsu API [kwerendy usługi Application Insights.](https://dev.applicationinsights.io/quickstart) Logika do konwersji kształtu danych do odpowiedniego do dostarczenia do użytkowników będzie do zaimplementowania. [Usługa Azure Functions](https://azure.microsoft.com/services/functions/) sprawia, że doskonałe miejsce do hosta takiej logiki.

> [!IMPORTANT]
>  Podczas gdy zdecydowana większość operacji oczyszczania może zakończyć się znacznie szybciej niż umowy SLA, **formalna umowy SLA na zakończenie operacji oczyszczania jest ustawiona na 30 dni** ze względu na ich duży wpływ na używaną platformę danych. Jest to zautomatyzowany proces; nie ma możliwości żądania szybszej obsługi operacji.

### <a name="delete"></a>Usuń

> [!WARNING]
> Usunięcia w log analytics są destrukcyjne i nie odwracalne! Należy zachować szczególną ostrożność podczas ich wykonywania.

Udostępniliśmy w ramach ochrony prywatności obsługi ścieżki interfejsu API *przeczyszczanie.* Ta ścieżka powinna być używana oszczędnie ze względu na ryzyko związane z tym, potencjalny wpływ na wydajność i możliwość wypaczenia wszystkich agregacji, pomiarów i innych aspektów danych usługi Log Analytics. Zobacz [sekcję Strategia przetwarzania danych osobowych,](#strategy-for-personal-data-handling) aby uzyskać alternatywne podejścia do obsługi danych prywatnych.

Przeczyść to wysoce uprzywilejowana operacja, że żadna aplikacja lub użytkownik na platformie Azure (w tym nawet właściciel zasobu) będzie miał uprawnienia do wykonania bez jawnie przyznanej roli w usłudze Azure Resource Manager. Ta rola jest _Data Purger_ i należy ostrożnie delegowane ze względu na możliwość utraty danych. 

> [!IMPORTANT]
> Aby zarządzać zasobami systemowymi, żądania przeczyszczenia są ograniczane do 50 żądań na godzinę. Należy partii wykonywania żądań przeczyszczania, wysyłając jedno polecenie, którego predykat zawiera wszystkie tożsamości użytkownika, które wymagają czyszczenia. Operator [in](/azure/kusto/query/inoperator) służy do określania wielu tożsamości. Należy uruchomić kwerendę przed wykonaniem żądania przeczyszczenie, aby sprawdzić, czy wyniki są oczekiwane. 



Po przypisaniu roli usługi Azure Resource Manager dostępne są dwie nowe ścieżki interfejsu API: 

#### <a name="log-data"></a>Dane dziennika

* [POST purge](https://docs.microsoft.com/rest/api/loganalytics/workspaces%202015-03-20/purge) - przyjmuje obiekt określający parametry danych do usunięcia i zwraca referencyjny identyfikator GUID 
* GET purge status - wywołanie przeczyszczanie POST zwróci nagłówek "x-ms-status-location", który będzie zawierał adres URL, który można wywołać w celu określenia stanu interfejsu API przeczyszczenia. Przykład:

    ```
    x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/Microsoft.OperationalInsights/workspaces/[WorkspaceName]/operations/purge-[PurgeOperationId]?api-version=2015-03-20
    ```

> [!IMPORTANT]
>  Chociaż oczekujemy, że zdecydowana większość operacji oczyszczania zakończy się znacznie szybciej niż nasza la SLA, ze względu na ich duży wpływ na platformę danych używaną przez Log Analytics, **formalna umowy SLA do zakończenia operacji oczyszczania jest ustawiona na 30 dni.** 

#### <a name="application-data"></a>Dane aplikacji

* [POST purge](https://docs.microsoft.com/rest/api/application-insights/components/purge) - przyjmuje obiekt określający parametry danych do usunięcia i zwraca referencyjny identyfikator GUID
* GET purge status - wywołanie przeczyszczanie POST zwróci nagłówek "x-ms-status-location", który będzie zawierał adres URL, który można wywołać w celu określenia stanu interfejsu API przeczyszczenia. Przykład:

   ```
   x-ms-status-location: https://management.azure.com/subscriptions/[SubscriptionId]/resourceGroups/[ResourceGroupName]/providers/microsoft.insights/components/[ComponentName]/operations/purge-[PurgeOperationId]?api-version=2015-05-01
   ```

> [!IMPORTANT]
>  Podczas gdy zdecydowana większość operacji oczyszczania może zakończyć się znacznie szybciej niż umowy SLA, ze względu na ich duży wpływ na platformę danych używaną przez usługa Application Insights, **formalna umowy SLA na zakończenie operacji oczyszczania jest ustawiona na 30 dni.**

## <a name="next-steps"></a>Następne kroki
- Aby dowiedzieć się więcej o sposobie zbierania, przetwarzania i zabezpieczać dane usługi Log [Analytics.](../../azure-monitor/platform/data-security.md)
- Aby dowiedzieć się więcej o sposobie zbierania, przetwarzania i zabezpieczać dane usługi Application [Insights,](../../azure-monitor/app/data-retention-privacy.md)zobacz Bezpieczeństwo danych usługi Application Insights .
