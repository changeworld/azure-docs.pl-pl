---
title: Diagnostyka transakcji Insights aplikacji Azure | Dokumentacja firmy Microsoft
description: Usługa Application diagnostics transakcji end-to-end Insights
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/19/2018
ms.author: mbullwin;sdash
ms.openlocfilehash: 7a4e4f74c02358fc117e0a66977ee3f0aef5b1dd
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/08/2018
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Diagnostyka ujednoliconego transakcji między składnikami

*To środowisko jest obecnie w wersji zapoznawczej i zastępuje istniejące bloków diagnostyki dla żądania po stronie serwera, zależności i wyjątki.*

Wersja zapoznawcza wprowadza nową funkcjonalność ujednoliconego diagnostyki odpowiadająca automatycznie telemetrii po stronie serwera z dotyczące wszystkich składników usługi Application Insights monitorowane w jednym widoku. Nie ma znaczenia, jeśli masz wiele zasobów z kluczami oddzielne instrumentacji. Usługa Application Insights wykrycia relacji podstawowej i umożliwia łatwe diagnozowanie składnika aplikacji, zależności lub wyjątek, która spowodowała spowolnienie transakcji lub niepowodzenie.

## <a name="what-is-a-component"></a>Co to jest składnik?

Składniki są niezależnie można wdrożyć części aplikacji rozproszonych mikrousług. Zespoły deweloperów i operacji mają widoczność na poziomie kodu lub dostępu do danych telemetrycznych generowane przez te składniki aplikacji.

* Składniki różnią się od "obserwowanych" zależności zewnętrzne, takie jak SQL, EventHub itp., które zespół/organizacji nie mogą mieć dostęp do (kod lub dane telemetryczne).
* Składniki uruchamiane na dowolnej liczbie wystąpień kontenera server/roli.
* Składniki mogą być osobne klucze Instrumentacji usługi Application Insights (nawet jeśli różnią się subskrypcje) lub różne role podlegające jednego klucza Instrumentacji usługi Application Insights. Nowe środowisko przedstawia szczegółowe informacje dotyczące wszystkich składników, niezależnie od tego, jak ich zostały zdefiniowane.

> [!NOTE]
> * **Brak elementów pokrewnych łącza?** Wszystkie dane telemetryczne, powiązane z żądania po stronie serwera, zależności i wyjątków znajdują się w [górnej](#cross-component-transaction-chart) i [dolnej](#all-telemetry-related-to-the-selected-component-operation) sekcje po lewej stronie. 
> * [Górnej](#cross-component-transaction-chart) sekcji skorelowany transakcji dotyczące wszystkich składników. Aby uzyskać najlepsze wyniki upewnij się, że wszystkie składniki są instrumentowane przy użyciu najnowszych SDK stabilna usługi Application Insights. W przypadku różnych zasobów usługi Application Insights, upewnij się, że masz odpowiednie uprawnienia, aby wyświetlić ich telemetrii.
> * [Dolnej](#all-telemetry-related-to-the-selected-component-operation) sekcji przedstawiono po lewej stronie **wszystkie** tym śladów i zdarzenia związane z na żądanie z wybranego składnika.

## <a name="enable-transaction-diagnostics-experience"></a>Włącz wyniki diagnostyki transakcji
Włącz "Unified szczegółów: Diagnostyka transakcji E2E" z [listy podglądy](app-insights-previews.md)

![Włącz podgląd](media/app-insights-e2eTxn-diagnostics/previews.png)

Ta wersja zapoznawcza jest obecnie dostępny dla żądania po stronie serwera, zależności i wyjątki. Są dostępne nowe środowisko w **wyniki wyszukiwania**, **wydajności**, lub **błąd** klasyfikowanie środowiska. Wersja zapoznawcza zastępuje odpowiednie bloki szczegóły klasycznego.

![Próbek wydajności](media/app-insights-e2eTxn-diagnostics/performanceSamplesClickThrough.png)

## <a name="transaction-diagnostics-experience"></a>Wyniki diagnostyki transakcji
Ten widok zawiera trzy części klucza: wykres transakcji między składnikami, listę sekwencji czas wszystkie dane telemetryczne z operacją określonego składnika, a w okienku szczegółów dowolnego elementu wybrane dane telemetryczne po lewej stronie.

![Części klucza](media/app-insights-e2eTxn-diagnostics/3partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Wykres transakcji między składnikami

Ten wykres zawiera oś czasu z słupkach poziomych na czas trwania żądania i zależności między składnikami. Wszelkie wyjątki, które są zbierane są również oznaczane na osi czasu.

* Pierwszy wiersz na tym wykresie reprezentuje punkt wejścia żądania przychodzącego, jeśli na pierwszym składnikiem o nazwie w tej transakcji. Czas trwania jest łączny czas do ukończenia transakcji.
* Wszystkie wywołania zależności zewnętrznych są proste-zwijanej wiersze, w których ikony reprezentujący typ zależności.
* Wywołania z innymi składnikami są zwijane wierszy. Każdy wiersz odpowiada określonej operacji wywołać składnika.
* Domyślnie żądania, zależności lub wyjątek, który początkowo zaznaczone jest wyświetlane na wykresie.
* Wybierz wszystkie wiersze, aby wyświetlić jego [szczegóły po prawej stronie](#details-of-the-selected-telemetry). 

> [!NOTE]
Wywołania z innymi składnikami mają dwa wiersze: jeden wiersz reprezentuje wywołanie wychodzącego (zależności) ze składnika wywołującego i drugiego wiersza odpowiada żądania przychodzącego na składnik o nazwie. Ikona początkowych i różne style pasków czas trwania pomocy rozróżnianie między nimi.

## <a name="all-telemetry-related-to-the-selected-component-operation"></a>Wszystkie dane telemetryczne dotyczące wybranej operacji składników

Każdy wiersz na wykresie między składnikami transakcji jest powiązany z operacji wywoływanej w poszczególnych składników. Ta operacja wybranego składnika znajduje odzwierciedlenie w tytule dolnej części. Otworzyć tę sekcję, aby zobaczyć sekwencji płaskiej czas wszystkie dane telemetryczne dotyczące tej operacji. Można wybrać dowolny element telemetrii na tej liście, aby wyświetlić odpowiednie [szczegóły po prawej stronie](#details-of-the-selected-telemetry).

![Wszystkie dane telemetryczne czasu sekwencji](media/app-insights-e2eTxn-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Szczegóły wybranego telemetrii

Szczegóły wybranego elementu z jednej z dwóch części w tym okienku są wyświetlane po lewej stronie. "Pokaż wszystko" zawiera wszystkie standardowe atrybuty, które są zbierane. Atrybuty niestandardowe oddzielnie są wymienione poniżej standardowego zestawu. Kliknij przycisk "ślady profilera Otwórz" lub "debugowania Otwórz migawkę" dla poziomu diagnostyki kodu w odpowiednich okienka szczegółów.

![Szczegóły wyjątku](media/app-insights-e2eTxn-diagnostics/exceptiondetail.png)

## <a name="profiler-and-snapshot-debugger"></a>Debuger profilera i migawki

[Application Insights profilera](app-insights-profiler.md) lub [debugera migawki](app-insights-snapshot-debugger.md) pomoc w diagnostyce poziomie kodu problemów z wydajnością i niepowodzenie. Z tym środowiskiem widać ślady profilera, lub kliknij przycisk migawek z każdego składnika za pomocą pojedynczej.

Jeśli nie można pobrać profilera pracy, skontaktuj się z **serviceprofilerhelp@microsoft.com**

Jeśli nie można pobrać debugera migawki pracy, skontaktuj się z **snapshothelp@microsoft.com**

![Integracji debugera](media/app-insights-e2eTxn-diagnostics/debugSnapshot.png)

## <a name="faq"></a>Często zadawane pytania

*Jeden składnik są widoczne na wykresie, a inne tylko są wyświetlane jako zależności zewnętrzne bez szczegółowości co się stało, w ramach tych składników.*

Potencjalne przyczyny:

* Czy inne składniki są instrumentowane przy użyciu usługi Application Insights?
* Są one przy użyciu najnowszych stabilna Insights zestaw SDK usługi Application?
* Jeśli te składniki są osobne zasobów usługi Application Insights, czy jest wymagany dostęp do swoich danych telemetrycznych?

Jeśli masz dostęp i składniki są instrumentowane przy użyciu najnowszych zestawów SDK Insights aplikacji, Daj nam znać za pośrednictwem kanału górnym prawym opinii.

*Masz tylko zależności zewnętrzne. Należy się o tej wersji zapoznawczej?*

Tak. Nowe środowisko łączy wszystkie powiązane dane telemetryczne po stronie serwera w ramach jednego widoku. Starsze bloków szczegółów zostanie zastąpione przez to doświadczenie w przyszłości umożliwiające wypróbować jej możliwości i przekaż nam swoją opinię. Oto wygląda następująco dla transakcji pojedynczego składnika:

![Środowisko pojedynczego składnika](media/app-insights-e2eTxn-diagnostics/singleComponent.png)

*Widać zduplikowane wiersze dla zależności. Jest to oczekiwane?*

W tej chwili możemy są wyświetlane wywołania zależności wychodzącego oddzielony od ruchu przychodzącego żądania. Zazwyczaj dwa wywołania Szukaj taka sama, jak są różne z powodu sieci Rundy tylko wartości czasu trwania. Ikona początkowych i różne style pasków czas trwania pomocy rozróżnianie między nimi. Jest myląca ze tej prezentacji danych? Przekaż nam swoją opinię!

*Jak zegar pochyla w wystąpieniach różnych składników?*

Osiach czasu są dostosowane do zegara pochyla na wykresie transakcji. Widać dokładne sygnatury czasowe, w okienku szczegółów lub za pomocą analizy.

*Dlaczego nowe środowisko brakuje większości kwerend powiązane elementy?*

Jest to celowe. Wszystkie powiązane elementy dotyczące wszystkich składników są już dostępne po lewej stronie (sekcje górny i dolny). Nowe środowisko zawiera dwie pozycje powiązane, które po lewej stronie nie obejmuje: wszystkie dane telemetryczne z pięciu minut przed i po to zdarzenie i harmonogram użytkownika.

*Dlaczego nowe środowisko nie ma funkcji I loved w blokach starsze?*

Prześlij nam swoją opinię! Chcemy w celu rozwiązania problemów, zanim to środowisko jest ogólnie dostępna po tym czasie zostaną wycofane starsze widoków. Teraz możesz wyłączyć podgląd, aby powrócić do starszych bloków.

## <a name="known-issues"></a>Znane problemy

* Błąd próbek z łącza mapowanie aplikacji do starszej karty szczegółów.
* Na podstawie autocluster szczegółowych danych w wynikach wyszukiwania łącze do starszej karty szczegółów.
* Integrację elementu roboczego nie jest dostępna w nowym środowisku.
