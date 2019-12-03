---
title: Azure Monitor dla maszyn wirtualnych (GA) — często zadawane pytania | Microsoft Docs
description: Azure Monitor dla maszyn wirtualnych to rozwiązanie na platformie Azure, które łączy kondycję i monitorowanie wydajności systemu operacyjnego Azure VM, a także automatycznie odnajduje składniki aplikacji i zależności z innymi zasobami oraz mapuje komunikację między niego. W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące wersji GA.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 10/07/2019
ms.openlocfilehash: 88634777897341f4bd4d8c12b5f9d3b6d9982758
ms.sourcegitcommit: 265f1d6f3f4703daa8d0fc8a85cbd8acf0a17d30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74671531"
---
# <a name="azure-monitor-for-vms-generally-available-ga-frequently-asked-questions"></a>Azure Monitor dla maszyn wirtualnych ogólnie dostępna (GA) często zadawanych pytań

Ostatnio ogłoszono w blogu dotyczącym [usługi Azure Update](https://azure.microsoft.com/blog/) niektórych planowanych zmian, które będą miały miejsce w październiku i listopadzie 2019. Więcej informacji o tych zmianach znajduje się w tej ogólnej dostępności — często zadawane pytania.

## <a name="updates-for-azure-monitor-for-vms"></a>Aktualizacje dla Azure Monitor dla maszyn wirtualnych

Firma Microsoft publikuje nową wersję Azure Monitor dla maszyn wirtualnych w listopadzie. Klienci, którzy włączają monitory platformy Azure dla maszyn wirtualnych, gdy ta wersja automatycznie otrzymuje nową wersję, ale istniejący klienci korzystający już z Azure Monitor dla maszyn wirtualnych są monitowani o uaktualnienie.  Te często zadawane pytania i nasze dokumenty oferują wskazówki dotyczące przeprowadzania uaktualnienia zbiorczego, jeśli masz duże wdrożenia w wielu obszarach roboczych.

W przypadku tego uaktualnienia Azure Monitor dla maszyn wirtualnych zestawy danych wydajności są teraz przechowywane w tej samej tabeli `InsightsMetrics` jako [Azure monitor dla kontenerów](container-insights-overview.md)i ułatwiają wykonywanie zapytań do dwóch zestawów. Ponadto można przechowywać bardziej zróżnicowane zestawy danych, które nie mogły być przechowywane w wcześniej używanej tabeli.  Nasze widoki danych zostaną zaktualizowane, aby korzystały z tej nowej tabeli.

Przenosimy do nowych typów danych dla naszych zestawów danych połączenia. Dane przechowywane w `ServiceMapComputer_CL` i `ServiceMapProcess_CL`, które używają niestandardowych tabel dzienników, przechodzą do typów danych dedykowanych o nazwie `VMComputer` i `VMProcess`.  Przenosząc do typów danych dedykowanych, możemy zapewnić im priorytet pozyskiwania danych, a schemat tabeli zostanie znormalizowany dla wszystkich klientów.

Zdajemy sobie sprawę, że konieczność przeprowadzenia uaktualnienia przez istniejących klientów spowoduje zakłócenia ich przepływu pracy, dlatego zdecydowaliśmy się zrobić to teraz, w trakcie publicznej wersji zapoznawczej, a nie później, po ogólnym udostępnieniu.

## <a name="what-will-change"></a>Co się zmieni?

Obecnie po zakończeniu procesu dołączania do Azure Monitor dla maszyn wirtualnych należy włączyć rozwiązanie Service Map w obszarze roboczym wybranym do przechowywania danych monitorowania, a następnie skonfigurować liczniki wydajności dla zbieranych danych z maszyn wirtualnych. W nadchodzących tygodniach zostanie wydane nowe rozwiązanie o nazwie **VMInsights**, które będzie zawierać dodatkowe możliwości zbierania danych oraz nową lokalizację do przechowywania tych danych w log Analytics.

Nasz bieżący proces używania liczników wydajności w obszarze roboczym wysyła dane do tabeli wydajności w Log Analytics.  To nowe rozwiązanie wyśle dane do tabeli o nazwie `InsightsMetrics`, która jest również używana przez Azure Monitor dla kontenerów. Ten schemat tabeli umożliwia przechowywanie dodatkowych metryk i zestawów danych usług, które nie są zgodne z formatem tabeli wydajności.

## <a name="what-should-i-do-about-the-performance-counters-on-my-workspace-if-i-install-the-vminsights-solution"></a>Co należy zrobić o licznikach wydajności w obszarze mój obszar roboczy, jeśli zainstaluję rozwiązanie VMInsights?

Bieżąca metoda włączania Azure Monitor dla maszyn wirtualnych używa liczników wydajności w obszarze roboczym. Nowa metoda zapisuje te dane w nowej tabeli o nazwie `InsightsMetrics`.

Po zaktualizowaniu naszego interfejsu użytkownika w celu korzystania z danych w programie InsightsMetrics będziemy aktualizować naszą dokumentację i komunikować się z tym ogłoszeniem za pośrednictwem wielu kanałów, w tym do wyświetlania transparentu w Azure Portal. W tym momencie można wyłączyć te [liczniki wydajności](vminsights-enable-overview.md#performance-counters-enabled) w obszarze roboczym, jeśli ich nie chcesz już używać. 

>[!NOTE]
>Jeśli istnieją reguły alertów odwołujące się do tych liczników w tabeli wydajności, należy je zaktualizować w celu odwoływania się do nowych danych w tabeli `InsightsMetrics`.  Zapoznaj się z naszą dokumentacją dotyczącą przykładowych zapytań dzienników, których można użyć w odniesieniu do tej tabeli.
>

W przypadku podjęcia decyzji o włączeniu włączonych liczników wydajności opłaty zostaną naliczone za dane pozyskane i zachowane w tabeli wydajności w oparciu o [Log Analytics ceny [(https://azure.microsoft.com/pricing/details/monitor/).

## <a name="how-will-this-change-affect-my-alert-rules"></a>Jak ta zmiana wpłynie na moje reguły alertów?

W przypadku utworzenia [alertów dziennika](../platform/alerts-unified-log.md) , które wysyłają zapytania do tabeli `Perf`, dla liczników wydajności, które zostały włączone w obszarze roboczym, należy zaktualizować te reguły, aby odwoływać się do tabeli `InsightsMetrics`. Te wskazówki dotyczą również wszelkich reguł przeszukiwania dzienników przy użyciu `ServiceMapComputer_CL` i `ServiceMapProcess_CL`, ponieważ te zestawy danych są przenoszone do tabel `VMComputer` i `VMProcess`.

Będziemy aktualizować te często zadawane pytania i naszą dokumentację, aby uwzględnić przykładowe reguły alertów wyszukiwania w dzienniku dla zbieranych danych.

## <a name="will-there-be-any-changes-to-billing"></a>Czy istnieją zmiany dotyczące rozliczeń?

Opłaty są naliczane w oparciu o dane pozyskane i zachowane w obszarze roboczym Log Analytics.

Gromadzone dane wydajności na poziomie komputera są takie same, jak podobne wielkości do danych przechowywanych w tabeli wydajności, a koszt będzie kosztował mniej więcej.

## <a name="what-if-i-only-want-to-use-service-map"></a>Co zrobić, jeśli chcę używać tylko Service Map?

Jest to dokładne.  Podczas przeglądania Azure Monitor dla maszyn wirtualnych dotyczącej nadchodzącej aktualizacji zobaczysz w Azure Portal wyświetlane są komunikaty. Po wydaniu zostanie wyświetlony monit z prośbą o aktualizację do nowej wersji. Jeśli wolisz korzystać tylko z funkcji [Maps](vminsights-maps.md) , możesz zrezygnować z uaktualnienia i nadal korzystać z funkcji Maps Azure monitor dla maszyn wirtualnych i rozwiązania Service map dostępnego w obszarze roboczym lub kafelku pulpitu nawigacyjnego.

Jeśli wybrano opcję ręcznego włączania liczników wydajności w obszarze roboczym, można zobaczyć dane na niektórych z naszych wykresów wydajności wyświetlanych w Azure Monitor. Po wydaniu nowego rozwiązania będziemy aktualizować nasze wykresy wydajności, aby wykonywać zapytania dotyczące danych przechowywanych w tabeli `InsightsMetrics`. Jeśli chcesz zobaczyć dane z tej tabeli na tych wykresach, musisz przeprowadzić uaktualnienie do nowej wersji Azure Monitor dla maszyn wirtualnych.

Zmiany dotyczące przenoszenia danych z `ServiceMapComputer_CL` i `ServiceMapProcess_CL` mają wpływ na Service Map i Azure Monitor dla maszyn wirtualnych, dlatego należy zaplanować tę aktualizację.

W przypadku wybrania opcji Nie uaktualniaj do rozwiązania **VMInsights** będziemy nadal podawać starsze wersje naszych skoroszytów wydajności, które odwołują się do danych w tabeli `Perf`.  

## <a name="will-the-service-map-data-sets-also-be-stored-in-insightsmetrics"></a>Czy Service Map zestawy danych będą również przechowywane w InsightsMetrics?

Zestawy danych nie będą duplikowane, jeśli używasz obu rozwiązań. Obie oferty współdzielą zestawy danych, które będą przechowywane w `VMComputer` (dawniej ServiceMapComputer_CL), `VMProcess` (dawniej ServiceMapProcess_CL), `VMConnection`i `VMBoundPort` tabele do przechowywania zbieranych przez nas zestawów danych mapy.  

Tabela `InsightsMetrics` zostanie użyta do przechowywania zestawów danych maszyn wirtualnych, procesów i usług zbieranych i zostanie wypełniona tylko w przypadku korzystania z Azure Monitor dla maszyn wirtualnych.

## <a name="will-i-be-double-charged-if-i-have-the-service-map-and-vminsights-solutions-on-my-workspace"></a>Czy przy użyciu rozwiązań Service Map i VMInsights w obszarze mój obszar roboczy zostanie podwojona opłata?

Nie, dwa rozwiązania korzystają z zestawów danych mapy, które są przechowywane w `VMComputer` (dawniej ServiceMapComputer_CL), `VMProcess` (dawniej ServiceMapProcess_CL), `VMConnection`i `VMBoundPort`.  Jeśli w obszarze roboczym znajdują się oba rozwiązania, nie będziesz mieć podwójnej opłaty za te dane.

## <a name="if-i-remove-either-the-service-map-or-vminsights-solution-will-it-remove-my-data-in-log-analytics"></a>Jeśli usunę rozwiązanie Service Map lub VMInsights, usuniemy moje dane w Log Analytics?

Nie, dwa rozwiązania korzystają z zestawów danych mapy, które są przechowywane w `VMComputer` (dawniej ServiceMapComputer_CL), `VMProcess` (dawniej ServiceMapProcess_CL), `VMConnection`i `VMBoundPort`.  W przypadku usunięcia jednego z tych rozwiązań te zestawy danych będą inejść, że nadal istnieje rozwiązanie, które korzysta z danych i pozostanie w Log Analytics.  Musisz usunąć oba rozwiązania z obszaru roboczego, aby dane zostały usunięte z obszaru roboczego Log Analytics.

## <a name="when-will-this-update-be-released"></a>Kiedy ta aktualizacja zostanie wydana?

Oczekujemy opublikowania aktualizacji Azure Monitor dla maszyn wirtualnych w połowie listopada. Ponieważ zbliżamy się do daty wydania, opublikujemy tutaj aktualizacje i zaprezentuje powiadomienia w Azure Portal po przejściu do Azure Monitor.

## <a name="health-feature-to-enter-limited-public-preview"></a>Funkcja kondycji do wprowadzania ograniczonej publicznej wersji zapoznawczej

Otrzymamy wiele doskonałych opinii od klientów dotyczących zestawu funkcji kondycji maszyn wirtualnych.  Ta funkcja wzbudza duże zainteresowanie, a jej możliwości obsługi monitorowania przepływów pracy są bardzo pożądane. Planujemy wprowadzić serię zmian, aby dodać funkcje i odpowiedzieć na otrzymane opinie. Aby zminimalizować wpływ tych zmian na nowych klientów, przenosimy tę funkcję do ograniczonej publicznej wersji zapoznawczej.

To przejście rozpocznie się na początku października i powinno zostać ukończone do końca miesiąca.

Niektóre z obszarów, na których będzie się koncentrować:

- Większa kontrola nad modelem kondycji i jego progami
- Tworzenie przy użyciu modeli kondycji skalowania, które mają zastosowanie do zakresu maszyn wirtualnych
- Natywne użycie platformy alertów do zarządzania regułami alertów opartych na kondycji
- Możliwość wyświetlania informacji o kondycji w szerszym zakresie, takich jak co najmniej jedna subskrypcja
- Skrócenie opóźnienia w przejściach kondycji i powiadomieniach o alertach

## <a name="how-do-existing-customers-access-the-health-feature"></a>Jak istniejący klienci uzyskują dostęp do funkcji kondycji?

Istniejący klienci korzystający z funkcji kondycji nadal będą mieć do niej dostęp, ale nie będzie oferowany nowym klientom.  

Aby uzyskać dostęp do tej funkcji, można dodać następującą flagę funkcji `feature.vmhealth=true` do adresu URL portalu [https://portal.azure.com](https://portal.azure.com). Przykład `https://portal.azure.com/?feature.vmhealth=true`.

Możesz również użyć tego krótkiego adresu URL, który ustawia flagę funkcji automatycznie: [https://aka.ms/vmhealthpreview](https://aka.ms/vmhealthpreview).

Jako istniejący klient można nadal korzystać z funkcji kondycji na maszynach wirtualnych, które są podłączone do istniejącej konfiguracji obszaru roboczego z funkcjonalnością kondycji.  

## <a name="i-use-vm-health-now-with-one-environment-and-would-like-to-deploy-it-for-a-new-environment"></a>Teraz korzystam z kondycji maszyny wirtualnej z jednym środowiskiem i chcę ją wdrożyć dla nowego środowiska

Jeśli jesteś istniejącym klientem korzystającym z funkcji kondycji i chcesz go użyć do nowego wdrożenia, skontaktuj się z nami w vminsights@microsoft.com, aby zażądać instrukcji.

## <a name="next-steps"></a>Następne kroki

Aby poznać wymagania i metody, które ułatwiają monitorowanie maszyn wirtualnych, zobacz [wdrażanie Azure monitor dla maszyn wirtualnych](vminsights-enable-overview.md).
