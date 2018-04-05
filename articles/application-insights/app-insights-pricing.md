---
title: Zarządzanie woluminu cennik i danych w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Zarządzanie woluminami dane telemetryczne i monitorować kosztów w usłudze Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/02/2018
ms.author: mbullwin
ms.openlocfilehash: 42c54509cedf48e9ebabb5b50865ed56e54bee05
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2018
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Zarządzanie wolumin ceny i danych w usłudze Application Insights

Cennik [Azure Application Insights] [ start] opiera się na ilość danych na aplikację. Każdy zasób usługi Application Insights jest rozliczany jako osobne usługi i przyczynia się do kwota rachunku dla subskrypcji Azure.

Istnieją dwa planów cenowych. Domyślny plan jest nazywany Basic, która obejmuje wszystkie te same funkcje co plan Enterprise na nie dodawania kosztów i rachunków przede wszystkim od ilości danych pozyskanych. Jeśli używasz usługi Operations Management Suite, należy wybrać dla przedsiębiorstw planu, który ma każdego węzła obciążania wraz z codziennie dodatki danych, a następnie obciąży danych pozyskanych powyżej dołączone dodatku.

Jeśli masz pytania dotyczące sposobu działania ceny dla usługi Application Insights, możesz także Zadaj pytanie w naszym [forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="the-price-plans"></a>Plany cen

Zobacz [cennikiem usługi Application Insights] [ pricing] dla bieżącego cen w walucie i regionu.

### <a name="basic-plan"></a>Podstawowy plan

Podstawowy plan jest ustawieniem domyślnym, gdy jest tworzony nowy zasób usługi Application Insights i jest optymalna dla wszystkich klientów z wyjątkiem tych z subskrypcją usługi Operations Management Suite.

* W planie Basic, naliczane są opłaty za ilość danych: liczba bajtów odebranych przez usługę Application Insights telemetrii. Ilość danych jest mierzony jako rozmiar nieskompresowanych pakietu danych JSON odebranych przez usługę Application Insights z aplikacji.
Aby uzyskać [zaimportowane do analizy danych tabelarycznych](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), ilość danych jest mierzony jako rozmiar nieskompresowanych plików wysłane do usługi Application Insights.
* [Metryki strumień na żywo](app-insights-live-stream.md) danych nie są one uwzględniane dla celów cennik.
* [Eksport ciągły](app-insights-export-telemetry.md) i [łącznika analizy dzienników](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) w podstawowy plan z kwietnia 2018 nie są dostępne bez żadnych dodatkowych opłat.

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Enterprise plan i uprawnienia do subskrypcji usługi Operations Management Suite

Klientów, którzy kupili E1 programu Microsoft Operations Management Suite i E2 będą mogli uzyskać Application Insights przedsiębiorstwa jako dodatkowy składnik bez ponoszenia dodatkowych kosztów jako [poprzednio](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). W szczególności każdej jednostki E1 Operations Management Suite i E2 obejmuje uprawnienie do węzła 1 plan Enterprise usługi Application Insights. Zgodnie z poniższym opisem szczegółowo w każdym węźle usługi Application Insights zawiera maksymalnie 200 MB danych pozyskanych dziennie (niezależnie od wprowadzanie danych analizy dzienników), z przechowywaniem danych 90-dniowy bez ponoszenia dodatkowych kosztów. Ponieważ to ma zastosowanie tylko w przypadku klientów z subskrypcji usługi Operations Management Suite, klienci bez subskrypcji nie będą widzieć opcję, aby wybrać ten plan.

> [!NOTE]
> Aby mieć pewność, że to uprawnienie, musi mieć zasobów usługi Application Insights w przedsiębiorstwie cenową planu. To uprawnienie dotyczy tylko jako węzły, więc zasobów usługi Application Insights w planie Basic będą mieli żadnych korzyści. Należy pamiętać, że tych uprawnień nie będą widoczne na szacowane koszty wyświetlany na *użycia i szacowany koszt* strony. Ponadto po przeniesieniu subskrypcji do nowego modelu cenowego 2018 kwietnia monitorowania platformy Azure podstawowy plan będzie tylko dostępne planu, więc nie jest zalecana, jeśli masz subskrypcję usługi Operations Management Suite.

Można znaleźć więcej informacji na temat Enterprise plan [cenową strony szczegółów przedsiębiorstwa.](app-insights-pricing-enterprise-details.md)

### <a name="multi-step-web-tests"></a>Wieloetapowe testy sieci Web

Brak dodatkowych opłat dla [testy sieci web wieloetapowych](app-insights-monitor-web-app-availability.md#multi-step-web-tests). Odnosi się do testów sieci web, które wykonują sekwencję akcji.

Jest bezpłatna oddzielne "testów ping" jednej strony. Dane telemetryczne z testów ping i testy wieloetapowych jest pobierana oraz inne dane telemetryczne z aplikacji.

## <a name="review-pricing-plans-and-estimate-costs"></a>Przejrzyj planów cenowych i Szacowanie kosztów

Usługi Application Insights ułatwia zrozumienie dostępnych planów cenowych i jakie koszty mogą opierać się na ostatnie wzorców użycia. Uruchamianie przez otwarcie **użycia i szacowane koszty** strony w zasobu usługi Application Insights w portalu Azure:

![Wybierz cennik.](./media/app-insights-pricing/pricing-001.png)

**a.** Przejrzyj woluminu dane dla danego miesiąca. Dotyczy to wszystkich danych odebranych i zachowane (po jednej [próbkowania](app-insights-sampling.md) z serwera i klienta, aplikacji oraz z testów dostępności.

**b.** Wykonywane oddzielne opłat [testy sieci web wieloetapowych](app-insights-monitor-web-app-availability.md#multi-step-web-tests). (Ta nie obejmuje testów dostępności proste, które są zawarte w danych opłat woluminu).

**c.** Wyświetlanie trendów woluminu dane z ostatniego miesiąca.

**d.** Włącz wprowadzanie danych [próbkowania.](app-insights-sampling.md) 

**e.** Skonfiguruj codzienne zakończenia woluminów danych.

Application Insights opłaty są dodawane do rachunku platformy Azure. Można wyświetlić szczegóły Azure naliczać opłaty w sekcji rozliczeń w portalu Azure lub w [Azure Billing Portal](https://account.windowsazure.com/Subscriptions). 

![W menu po stronie wybierz rozliczeń.](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Szybkość danych
Istnieją trzy sposoby, w których jest ograniczony woluminu w przypadku wysłania danych:

* **Próbkowanie:** mechanizm ten może służyć zmniejszyć ilość danych telemetrycznych wysłanych z klienta i serwera aplikacji, z minimalnym zakłócenia metryki. To jest podstawowym narzędziem konieczne dostrojenie ilości danych. Dowiedz się więcej o [próbkowania funkcji](app-insights-sampling.md). 
* **Dzienny limit:** podczas tworzenia zasobu usługi Application Insights z portalu Azure, to ma ustawioną wartość 100 GB/dzień. Domyślnie podczas tworzenia zasobu usługi Application Insights z programu Visual Studio jest mały (tylko 32,3 MB na dzień) jest przeznaczony tylko do ułatwienia testowania. W takim przypadku jest przeznaczony, użytkownik zostanie podniesiony codzienne centralnych zasad dostępu przed wdrożeniem aplikacji w środowisku produkcyjnym. Maksymalny limit jest 1000 GB/dzień, chyba że żądanych maksymalną wyższą dla aplikacji dużego natężenia ruchu sieciowego. Zachować ostrożność podczas ustawiania codzienne centralnych zasad dostępu, zgodnie z celem powinno być **do nigdy nie osiągnęła limit dzienny**, ponieważ spowoduje utratę danych do końca dnia i nie w celu monitorowania danej aplikacji. Aby go zmienić, użyj codzienne opcja zakończenia woluminu połączone z użycia i szacowane koszty strony (patrz poniżej). Usunęliśmy ograniczenie niektórych typów subskrypcji, które mają środki, którego nie można użyć dla usługi Application Insights. Wcześniej, jeśli subskrypcja jest objęta limitem wydatków, codzienne zakończenia okno dialogowe będzie miało instrukcje jak ją usunąć i włączyć codzienne WPR wygenerowany poza 32,3 MB/dzień.
* **Ograniczanie:** ogranicza szybkość transmisji do 32 000 zdarzeń na sekundę, uśrednionej ponad 1 minutę.

*Co się stanie, jeśli Moja aplikacja przekracza ograniczenie przepustowości?*

* Ilość danych aplikacji wysyłanych przez jest oceniane co minutę. W przypadku przekroczenia na sekundę szybkość uśrednionej za minutę, serwer odrzuca niektórych żądań. Zestawu SDK buforów danych, a następnie spróbuje ponownie wysłać, rozkładanie skoków w ciągu kilku minut. Jeśli aplikacja stale wysyła dane w powyżej wartości ograniczenia przepustowości, niektóre dane zostaną usunięte. (ASP.NET, Java i JavaScript SDK ponownie wysłać w ten sposób; innych zestawów SDK może po prostu Usuń ograniczenie danych). W przypadku ograniczania przepustowości, zostanie wyświetlone powiadomienie ostrzeżenie, że miało to miejsce.

*Jak sprawdzić, jak dużo danych wysyła mojej aplikacji?*

* Otwórz **użycia i szacowany koszt** stronę, aby zobaczyć codzienne wykresu woluminów danych. 
* W Eksploratorze metryk, Dodaj nowy wykres i wybierz **ilość punktów danych** jako jego metryki. Przełącz na grupowanie i Grupuj według **— typ danych**.

## <a name="to-reduce-your-data-rate"></a>Aby zmniejszyć częstotliwość danych
Poniżej przedstawiono niektóre czynności, które można wykonać, aby zmniejszyć wolumin danych:

* Użyj [próbkowania](app-insights-sampling.md). Ta technologia zmniejsza szybkość danych bez pochylanie Twoje metryki i bez wpływu na możliwość przechodzić między elementów pokrewnych wyszukiwania. W aplikacjach server działa automatycznie.
* [Ogranicz liczbę wywołań Ajax, które mogą być zgłaszane](app-insights-javascript.md#detailed-configuration) w każdy widok strony, albo Przełącz funkcję Raportowanie Ajax.
* Wyłącz niepotrzebne przez moduły kolekcji [ApplicationInsights.config edycji](app-insights-configuration-with-applicationinsights-config.md). Na przykład można zdecydować, że liczniki wydajności lub dane zależności są inessential.
* Podziel telemetrii do oddzielania klucze instrumentacji. 
* Metryki wstępnie agregacji. Jeśli wywołania TrackMetric zostało umieszczone w aplikacji, można zmniejszyć ruch za pomocą przeciążenia, które akceptuje obliczenia średniej i odchylenie standardowe partii pomiarów. Możesz też użyć [wstępnie agregację pakietu](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="managing-the-maximum-daily-data-volume"></a>Maksymalna ilość danych codzienne Zarządzanie

Dzienny limit woluminu można używać, aby ograniczyć dane zebrane, ale spełnieniu zakończenie spowoduje utratę wszystkich danych telemetrycznych wysłanych z aplikacji w pozostałej dnia. Jest **nie zaleca się** aby aplikacja trafienie codzienne centralnych zasad dostępu, ponieważ nie można śledzić kondycji i wydajności aplikacji po jej zostaje trafiony.

Zamiast tego należy użyć [próbkowania](app-insights-sampling.md) dostrajania ilość danych do poziomu chcesz i użyj dzienny limit jedynie w ostateczności"", w przypadku, gdy aplikacja rozpoczyna wysyłanie znacznie wyższa woluminów telemetrii nieoczekiwane.

Aby zmienić codzienne centralnych zasad dostępu, w sekcji konfiguracji zasobu usługi Application Insights, z **użycia i szacowane koszty** kliknij przycisk **dzienny limit**.

![Dopasowywanie codzienne zakończenia woluminu telemetrii](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>Próbkowanie
[Próbkowanie](app-insights-sampling.md) liczby nadal z zachowaniem poprawnego zdarzenia i jest to metoda zmniejsza szybkość, z jaką dane telemetryczne są wysyłane do aplikacji, przy jednoczesnym zachowaniu możliwość wyszukiwania powiązanych zdarzeń podczas wyszukiwania diagnostycznych.

Próbkowanie to efektywny sposób zmniejszyć koszty i pozostać w wykorzystaniu całego przydziału miesięcznego. Algorytm próbkowania zachowuje pozycje powiązane dane telemetryczne, tak, aby na przykład podczas wyszukiwania, możesz znaleźć żądania dotyczące określonego wyjątku. Algorytm również zachowuje poprawne liczby tak, aby wyświetlić poprawne wartości w Eksploratorze metrykę dla żądań, wyjątków stawki i inne liczby.

Istnieje kilka metod pobierania próbek.

* [Próbkowanie adaptacyjną](app-insights-sampling.md) jest ustawieniem domyślnym dla zestawu SDK platformy ASP.NET, automatycznie dostosowuje ilość danych telemetrycznych, wysyłanej przez aplikację. Działa automatycznie w zestawie SDK w aplikacji sieci web, dzięki czemu zmniejsza ruch telemetrii w sieci. 
* *Wprowadzanie próbkowania* stanowi alternatywę, który działa w momencie, w którym dane telemetryczne z aplikacji wprowadza usługa Application Insights. Nie wpływa na ilość danych telemetrycznych wysłanych z aplikacji, ale zmniejsza woluminu przechowywane przez usługę. Służy on do zmniejszenia limitu przydziału wykorzystane przez dane telemetryczne z przeglądarki i innych zestawów SDK.

Wprowadzanie próbkowania ustawia formantu w oknie dialogowym cennik:

![Okno dialogowe cenową i przydziału kliknij Kafelek przykłady i wybierz polecenie ułamek próbkowania.](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> Okno dialogowe pobierania próbek danych określa tylko wartość wprowadzanie próbkowania. Go nie odzwierciedlają częstotliwość próbkowania, która jest stosowana przez zestaw SDK usługi Application Insights w aplikacji. Jeśli przychodzące dane telemetryczne już uzyskane podczas próbkowania w zestawie SDK, próbkowania wprowadzanie nie została zastosowana.
>

Aby odnaleźć rzeczywiste próbkowania niezależnie od tego, gdzie zostały zastosowane, należy użyć [Analytics query](app-insights-analytics.md) takich jak ta:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

W każdym zachowane rekordu, `itemCount` wskazuje liczbę oryginalnego rekordów, które reprezentuje on równą 1 + Liczba poprzednich odrzuconych rekordów. 

## <a name="automation"></a>Automatyzacja

Można napisać skrypt, aby ustawić plan cen za pomocą zarządzania zasobami Azure. [Dowiedz się, jak to zrobić](app-insights-powershell.md#price).

## <a name="limits-summary"></a>Podsumowanie limity

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="next-steps"></a>Kolejne kroki

* [Próbkowanie](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: http://azure.microsoft.com/pricing/details/application-insights/
