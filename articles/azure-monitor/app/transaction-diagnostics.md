---
title: Diagnostyka transakcji usługi Azure Application Insights | Dokumenty firmy Microsoft
description: Kompleksowa diagnostyka transakcji usługi Application Insights
ms.topic: conceptual
ms.date: 01/19/2018
ms.reviewer: sdash
ms.openlocfilehash: 39c4c51a1bc84e06efac3674b1ee5b487f9e6729
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671141"
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Ujednolicona diagnostyka transakcji międzyskcowych

Ujednolicone środowisko diagnostyczne automatycznie koreluje dane telemetryczne po stronie serwera ze wszystkich monitorowanych składników usługi Application Insights w jednym widoku. Nie ma znaczenia, czy masz wiele zasobów z oddzielnymi kluczami instrumentacji. Usługa Application Insights wykrywa podstawową relację i umożliwia łatwe diagnozowanie składnika aplikacji, zależności lub wyjątku, który spowodował spowolnienie lub niepowodzenie transakcji.

## <a name="what-is-a-component"></a>Co to jest składnik?

Składniki są niezależnie wdrażalne części aplikacji rozproszonych/mikrousług. Deweloperzy i zespoły operacyjne mają wgląd na poziomie kodu lub dostęp do danych telemetrycznych generowanych przez te składniki aplikacji.

* Składniki różnią się od "obserwowanych" zależności zewnętrznych, takich jak SQL, EventHub itp., do których twój zespół/organizacja może nie mieć dostępu (kod lub dane telemetryczne).
* Składniki są uruchamiane na dowolnej liczbie wystąpień serwera/roli/kontenera.
* Składniki mogą być oddzielne klucze instrumentacji usługi Application Insights (nawet jeśli subskrypcje są różne) lub różne role raportowania do jednego klucza instrumentacji usługi Application Insights. Nowe środowisko pokazuje szczegóły we wszystkich składnikach, niezależnie od tego, jak zostały skonfigurowane.

> [!NOTE]
> * **Brakuje cisów powiązanych elementów?** Wszystkie powiązane dane telemetryczne znajdują się w [górnej](#cross-component-transaction-chart) i [dolnej](#all-telemetry-with-this-operation-id) części lewej strony. 

## <a name="transaction-diagnostics-experience"></a>Diagnostyka transakcji
Ten widok zawiera cztery kluczowe części: listę wyników, wykres transakcji międzyskładnikowych, listę sekwencji czasowych wszystkich danych telemetrycznych związanych z tą operacją oraz okienko szczegółów dla dowolnego wybranego elementu telemetrii po lewej stronie.

![Kluczowe części](media/transaction-diagnostics/4partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Wykres transakcji międzyskcjowych

Ten wykres zawiera oś czasu z poziomymi słupkami dla czasu trwania żądań i zależności między składnikami. Wszelkie wyjątki, które są zbierane są również oznaczone na osi czasu.

* Górny wiersz na tym wykresie reprezentuje punkt wejścia, przychodzące żądanie do pierwszego składnika o nazwie w tej transakcji. Czas trwania to całkowity czas ukończenia transakcji.
* Wszelkie wywołania zależności zewnętrznych są proste wiersze nie zwijalne, z ikonami reprezentującymi typ zależności.
* Wywołania innych składników są zwijane wiersze. Każdy wiersz odpowiada określonej operacji wywoływane w komponencie.
* Domyślnie wybrane żądanie, zależność lub wyjątek są wyświetlane po prawej stronie.
* Wybierz dowolny wiersz, aby wyświetlić jego [szczegóły po prawej stronie](#details-of-the-selected-telemetry). 

> [!NOTE]
> Wywołania do innych składników mają dwa wiersze: jeden wiersz reprezentuje wywołanie wychodzące (zależność) ze składnika wywołującego, a drugi wiersz odpowiada żądaniu przychodzącemu w wywoływanemu składniku. Ikona wiodąca i wyrazisty styl pasków czasu trwania pomagają odróżnić je od nich.

## <a name="all-telemetry-with-this-operation-id"></a>Wszystkie dane telemetryczne z tym identyfikatorem operacji

W tej sekcji przedstawiono widok listy płaskiej w sekwencji czasu wszystkich danych telemetrycznych związanych z tą transakcją. Pokazuje również zdarzenia niestandardowe i ślady, które nie są wyświetlane na wykresie transakcji. Tę listę można filtrować do danych telemetrycznych generowanych przez określony składnik/wywołanie. Można wybrać dowolny element telemetrii [na](#details-of-the-selected-telemetry)tej liście, aby wyświetlić odpowiednie szczegóły po prawej stronie .

![Sekwencja czasu wszystkich danych telemetrycznych](media/transaction-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Szczegóły wybranej telemetrii

To zwijane okienko zawiera szczegóły dowolnego zaznaczonego elementu z wykresu transakcji lub listy. "Pokaż wszystko" zawiera listę wszystkich standardowych atrybutów, które są zbierane. Wszystkie atrybuty niestandardowe są oddzielnie wymienione poniżej zestawu standardowego. Kliknij na "..." poniżej okna śledzenia stosu, aby uzyskać opcję skopiowania śledzenia. "Otwórz ślady profilera" lub "Otwórz migawkę debugowania" pokazuje diagnostykę poziomu kodu w odpowiednich okienkach szczegółów.

![Szczegóły wyjątku](media/transaction-diagnostics/exceptiondetail.png)

## <a name="search-results"></a>Wyniki wyszukiwania

To okienko zwijane pokazuje inne wyniki, które spełniają kryteria filtru. Kliknij dowolny wynik, aby zaktualizować odpowiednie szczegóły 3 sekcje wymienione powyżej. Staramy się znaleźć próbki, które są najbardziej prawdopodobne, aby uzyskać szczegółowe informacje dostępne ze wszystkich składników, nawet jeśli pobieranie próbek jest w życie w każdym z nich. Są one wyświetlane jako "sugerowane" próbki.

![Wyniki wyszukiwania](media/transaction-diagnostics/searchResults.png)

## <a name="profiler-and-snapshot-debugger"></a>Debuger profilera i migawki

[Profiler usługi Application Insights](../../azure-monitor/app/profiler.md) lub [debuger migawki](snapshot-debugger.md) pomocy z diagnostyki na poziomie kodu problemów z wydajnością i błędami. Dzięki temu doświadczeniu można wyświetlić ślady profilera lub migawki z dowolnego składnika za pomocą jednego kliknięcia.

Jeśli nie możesz uzyskać profiler pracy, prosimy o kontakt **serviceprofilerhelp\@microsoft.com**

Jeśli nie można uzyskać Debuger migawki pracy, skontaktuj się **snapshothelp\@microsoft.com**

![Integracja profilera](media/transaction-diagnostics/profilerTraces.png)

## <a name="faq"></a>Najczęściej zadawane pytania

*Widzę jeden składnik na wykresie, a pozostałe są wyświetlane tylko jako zależności zewnętrzne bez żadnych szczegółów, co się stało w tych składnikach.*

Potencjalne powody:

* Czy inne składniki są instrumentowane za pomocą usługi Application Insights?
* Czy używają najnowszego stabilnego SDK usługi Application Insights?
* Jeśli te składniki są oddzielne zasoby usługi Application Insights, czy masz wymagany dostęp do ich danych telemetrycznych?

Jeśli masz dostęp, a składniki są instrumentowane za pomocą najnowszych zestawów SDK usługi Application Insights, połóż nas za pośrednictwem kanału opinii w prawym górnym rogu.

*Widzę zduplikowane wiersze zależności. Czy to się spodziewa?*

W tej chwili pokazujemy wywołanie zależności wychodzącej oddzielnie od żądania przychodzącego. Zazwyczaj dwa wywołania wyglądają identycznie, a tylko wartość czasu trwania jest inna ze względu na sieć w obie strony. Ikona wiodąca i wyrazisty styl pasków czasu trwania pomagają odróżnić je od nich. Czy ta prezentacja danych jest myląca? Przejrzyj nam swoją opinię!

*Co z przeciąwienia zegara w różnych wystąpieniach składników?*

Osie czasu są dostosowywane do pochylenia zegara na wykresie transakcji. Możesz zobaczyć dokładne sygnatury czasowe w okienku szczegółów lub za pomocą Analytics.

*Dlaczego w nowym doświadczeniu brakuje większości zapytań powiązanych elementów?*

Jest to celowe. Wszystkie powiązane elementy, we wszystkich komponentach, są już dostępne po lewej stronie (górna i dolna sekcja). Nowe środowisko ma dwa powiązane elementy, które po lewej stronie nie obejmuje: wszystkie dane telemetryczne z pięciu minut przed i po tym zdarzeniu i osi czasu użytkownika.
