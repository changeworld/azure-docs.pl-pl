---
title: Zarządzanie użycia i kosztów dla usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Zarządzanie woluminami danych telemetrycznych i monitorowanie kosztów w usłudze Application Insights.
services: application-insights
documentationcenter: ''
author: DaleKoetke
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 05/29/2019
ms.author: dalek
ms.openlocfilehash: ebcb0922335a2bdc5423ec4e4bfce7c1cd71c46a
ms.sourcegitcommit: 8e76be591034b618f5c11f4e66668f48c090ddfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66357278"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Zarządzanie użycia i kosztów dla usługi Application Insights

> [!NOTE]
> W tym artykule opisano, jak analizować użycie danych usługi Application Insights.  Zapoznaj się z następującymi artykułami, aby uzyskać powiązane informacje.
> - [Monitorowanie użycia i szacowanych kosztów](../../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) zawiera opis sposobu wyświetlania użycie i szacowane koszty w wielu monitorowania funkcji różne modele cen platformy Azure. Opisuje ona również, jak można zmienić modelu cen.

Jeśli masz pytania na temat cen usługi Application Insights, możesz zadać pytanie na naszym [forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="pricing-model"></a>Model cen

Cennik usługi [usługi Azure Application Insights] [ start] jest oparty na wolumenie danych pozyskanych. Każdy zasób usługi Application Insights jest rozliczane jako osobną usługą i przyczynia się do rachunek dla subskrypcji platformy Azure.

### <a name="data-volume-details"></a>Szczegóły wolumin danych

* Ilość danych jest liczba bajtów odebranych przez usługę Application Insights dane telemetryczne. Ilość danych jest mierzony jako rozmiar nieskompresowanych pakietów danych JSON, które jest odebrane przez usługę Application Insights z aplikacji. Aby uzyskać [zaimportowane do analizy danych tabelarycznych](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), ilość danych jest mierzony jako rozmiar nieskompresowanych plików, które są wysyłane do usługi Application Insights.
* Opłaty za objętość danych aplikacji są teraz zgłaszane na nowych liczników rozliczeń o nazwie **pozyskiwanie danych** od kwietnia 2018 r. Ten nowy licznik jest współużytkowany przez monitorowanie technologii, takich jak aplikacje Insights i Log Analytics i jest obecnie dostępna w obszarze nazwy usługi **usługi Log Analytics**. 
* [Live Stream metryki](../../azure-monitor/app/live-stream.md) danych nie jest liczony cennik celów.

Dla bieżącego cen w regionie i waluty, zobacz [cen usługi Application Insights][pricing].

### <a name="multi-step-web-tests"></a>Wieloetapowe testy sieci Web

[Wieloetapowe testy sieci web](../../azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests) pociągnąć za sobą dodatkowych opłat. Wieloetapowe testy sieci web są testy sieci web, które wykonują sekwencję akcji.

Nie ma osobnych opłat dla *testów ping* z jednej strony. Dane telemetryczne z testów ping i wieloetapowe testy są naliczane opłaty taki sam jak inne dane telemetryczne z Twojej aplikacji.

## <a name="review-usage-and-estimate-costs"></a>Sprawdź użycie i Szacowanie kosztów

Usługa Application Insights ułatwia zrozumienie, jakie mogą opierać się na bieżące wzorce użycia są koszty. Aby rozpocząć pracę w witrynie Azure portal dla zasobu usługi Application Insights, przejdź do **użycie i szacunkowe koszty** strony:

![Wybierz cennik](./media/pricing/pricing-001.png)

A. Sprawdź ilość danych na miesiąc. Obejmuje to wszystkie dane, które otrzymał i przechowywane (po jednej [próbkowania](../../azure-monitor/app/sampling.md)) z serwera i aplikacje klienckie i testy dostępności.  
B. Osobna opłata zostanie przeprowadzona dla [wieloetapowe testy sieci web](../../azure-monitor/app/monitor-web-app-availability.md#multi-step-web-tests). (Nie obejmuje to testy dostępności proste, które są zawarte w Twoich rękach woluminów danych.)  
C. Umożliwia wyświetlanie trendów wolumin danych dla ostatniego miesiąca.  
D. Włącz pozyskiwanie danych [próbkowania](../../azure-monitor/app/sampling.md).   
E. Ustaw limit dziennej ilości danych.  

Głębsze zbadanie użycia usługi Application Insights, otwórz **metryki** strony, Dodaj metryki o nazwie "punkt ilość danych", a następnie wybierz *zastosować podział* opcję, aby podzielić dane według "Telemetrii Typ elementu". 

Application Insights opłaty są dodawane na rachunku dotyczącym platformy Azure. Aby wyświetlić szczegóły platformy Azure są naliczane w **rozliczeń** sekcji w witrynie Azure Portal lub w [portalu rozliczeń systemu Azure](https://account.windowsazure.com/Subscriptions). 

![W menu po lewej stronie wybierz pozycję rozliczenia](./media/pricing/02-billing.png)

## <a name="data-rate"></a>Szybkość danych
Ilość danych wysyłanych jest ograniczone na trzy sposoby:

* **Próbkowanie**: Próbkowanie umożliwia zmniejszenie ilości telemetrii wysyłanych z serwera i klienta aplikacje z minimalnym zakłócenia metryki. Próbkowanie to podstawowe narzędzie używane do dostrojenia ilość danych wysyłanych. Dowiedz się więcej o [próbkowanie funkcji](../../azure-monitor/app/sampling.md). 
* **Dzienny limit**: Podczas tworzenia zasobu usługi Application Insights w witrynie Azure portal, dzienny limit wynosi 100 GB/dzień. Podczas tworzenia zasobu usługi Application Insights w programie Visual Studio, wartość domyślna to mały (tylko 32,3 MB/dzień). Dzienny limit Domyślnie opcja jest zaznaczona ułatwić testowanie. Jest ona przeznaczona, użytkownik zostanie podnieść dziennego limitu przed wdrożeniem aplikacji do środowiska produkcyjnego. 

    Maksymalny limit jest 1000 GB/dzień, chyba że żądanie wyższą maksymalną dla aplikacji o wysokim natężeniu ruchu. 

    Po ustawieniu dzienny limit, należy zachować ostrożność. Powinno być zgodne z zamiarami użytkownika *nigdy osiągnięty dzienny limit*. W razie osiągnięcia dziennego limitu utratę danych przez pozostałą część dnia i nie może monitorować Twojej aplikacji. Aby zmienić dzienny limit, użyj **dzienny limit ilości** opcji. Możesz uzyskać dostęp ta opcja w **użycie i szacunkowe koszty** okienko (opisane bardziej szczegółowo w dalszej części tego artykułu).
    Usunęliśmy ograniczenie niektórych typów subskrypcji, obejmujących środki, które nie można użyć dla usługi Application Insights. Wcześniej, jeśli subskrypcja jest objęta limitem wydatków, dzienny limit okno dialogowe ma okno instrukcjami, aby usunąć limit wydatków i włączyć dzienny limit danych należy podnieść 32,3 MB/dzień.
* **Ograniczanie**: Limity ograniczania przepływności danych do 32 000 zdarzeń na sekundę, szybkości średniej ponad 1 minutę na klucz instrumentacji.

*Co się stanie, jeśli Moja aplikacja przekracza ograniczenie przepustowości?*

* Ilość danych, które wysyła Twoja aplikacja jest oceniane na minutę. W przypadku przekroczenia stawki na sekundę uśrednionej za minutę, serwer odrzuca niektóre żądania. Zestaw SDK buforuje dane, a następnie próbuje wysłać ją ponownie. Rozprzestrzenia się skoków w ciągu kilku minut. Jeśli Twoja aplikacja stale wysyła dane więcej niż przepustowości, niektóre dane zostaną usunięte. (ASP.NET, Java i JavaScript SDK ponownie wysłać danych w ten sposób; innych zestawów SDK może po prostu upuszczania ograniczenia danych). Jeśli wystąpi ograniczanie przepustowości, ostrzeżenie powiadomienie ostrzega o tym, wystąpienia tego zdarzenia

*Jak sprawdzić, jak dużo danych, które wysyła mojej aplikacji?*

Aby zobaczyć, jak dużo danych, Twoja aplikacja wysyła, można użyć jednej z następujących opcji:

* Przejdź do **użycia i szacowanych kosztów** okienku, aby zobaczyć codzienne wykres danych w woluminie. 
* W Eksploratorze metryk Dodaj nowy wykres. Wykres metryk, wybierz **wolumin punktu danych**. Włącz **grupowanie**, a następnie Grupuj według **— typ danych**.

## <a name="reduce-your-data-rate"></a>Zmniejsz szybkość danych
Oto kilka rzeczy, które można wykonać, aby zmniejszyć ilość danych:

* Użyj [próbkowania](../../azure-monitor/app/sampling.md). Ta technologia zmniejsza szybkość danych bez pochylanie metryk. Nie strać zdolności do przechodzenia między powiązane elementy w polu wyszukiwania. W aplikacjach server próbkowania działa automatycznie.
* [Ogranicz liczbę wywołań Ajax, które mogą być zgłaszane](../../azure-monitor/app/javascript.md#detailed-configuration) każdego widoku strony lub switch Wyłącz raportowanie Ajax.
* [Edytuj plik ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) wyłączyć moduły kolekcji, których nie potrzebujesz. Na przykład można zdecydować, czy liczników wydajności lub dane zależności są zbędne.
* Podziel telemetrii między kluczy Instrumentacji oddzielne. 
* Wstępnej agregacji metryk. Wywołania TrackMetric umieszczenie w aplikacji, można zmniejszyć ruch za pomocą przeciążenia, które akceptuje obliczenia średniej i odchylenie standardowe w zadaniu wsadowym pomiarów. Alternatywnie można użyć [pakiet wstępnie agregacji](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="manage-the-maximum-daily-data-volume"></a>Zarządzanie Maksymalna dzienna ilość danych

Dzienny limit ilości służy do ograniczania zebranych danych. Jednak zakończenie po spełnieniu, występuje utrata wszystkie dane telemetryczne wysyłane z aplikacji dla pozostałych dnia. Jest *nie zaleca się włączenie* aby aplikacja osiągnięty dzienny limit. Po osiągnięciu dziennego limitu nie Śledź kondycję i wydajność aplikacji.

Zamiast używania limit dziennej ilości danych, należy użyć [próbkowania](../../azure-monitor/app/sampling.md) ilość danych do poziomu, który chcesz dostosować. Następnie należy użyć dziennego limitu jedynie w ostateczności"", w przypadku, gdy aplikacja nieoczekiwanie zaczyna wysyłać dużo wyższej ilości danych telemetrycznych.

Zmiany dzienny limit w **Konfiguruj** części zasobu usługi Application Insights w **użycie i szacunkowe koszty** okienku wybierz **dziennego limitu**.

![Dostosuj dzienny limit ilości danych telemetrycznych](./media/pricing/pricing-003.png)

## <a name="sampling"></a>Próbkowanie
[Próbkowanie](../../azure-monitor/app/sampling.md) jest metodą zmniejszenia szybkość, z jaką dane telemetryczne są wysyłane do aplikacji, przy zachowaniu umożliwia znajdowanie powiązanych zdarzeń podczas wyszukiwania diagnostycznego. Możesz również zachować dużymi liczbami zdarzeń prawidłowe.

Próbkowanie jest efektywną metodę, aby zmniejszyć koszty i w ramach miesięcznego limitu przydziału. Algorytm próbkowania zachowuje powiązane elementy danych telemetrycznych, więc, na przykład podczas korzystania z funkcji wyszukiwania można znaleźć żądania powiązaną z określonym wyjątkiem. Algorytm zachowuje także prawidłowe liczby, tak aby było widać poprawne wartości w Eksploratorze metryk dla żądań, wyjątków stawki oraz innych liczników.

Istnieje kilka postaci próbkowania.

* [Próbkowanie adaptacyjne](../../azure-monitor/app/sampling.md) jest ustawieniem domyślnym dla zestawu SDK platformy ASP.NET. Próbkowania adaptacyjnego automatycznie dostosowuje się ilość danych telemetrii wysyłanej przez aplikację. Działa ona automatycznie w zestawie SDK w aplikacji sieci web tak, aby dane telemetryczne ruch w sieci jest mniejsze. 
* *Próbkowanie fragmentaryczne* stanowi alternatywę, który działa w punkcie, w którym dane telemetryczne z Twojej aplikacji wprowadza usługę Application Insights. Próbkowanie fragmentaryczne nie ma wpływu na ilości danych telemetrycznych wysyłanych z aplikacji, ale zmniejsza ilość danych są przechowywane przez usługę. Próbkowanie fragmentaryczne można użyć w celu zmniejszenia limitu przydziału, używanym przez przetwarzanie danych telemetrycznych z przeglądarki i innych zestawów SDK.

Aby ustawić próbkowanie fragmentaryczne, przejdź do **ceny** okienka:

![W okienku cen i limit przydziału wybierz Kafelek próbki, a następnie wybierz ułamek próbkowania](./media/pricing/pricing-004.png)

> [!WARNING]
> **Próbkowanie danych** okienka kontrolki tylko wartość próbkowanie fragmentaryczne. Go nie odzwierciedla częstotliwość próbkowania, która jest stosowana przez zestaw SDK usługi Application Insights w swojej aplikacji. W przypadku przychodzących danych telemetrycznych już uzyskane podczas próbkowania w zestawie SDK, nie jest stosowane próbkowanie fragmentaryczne.
>

Aby odnaleźć rzeczywiste próbkowania, niezależnie od tego, gdzie jest zastosowano, należy użyć [zapytania usługi Analytics](analytics.md). Zapytanie wygląda następująco:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

W każdym przechowywane rekordu, `itemCount` wskazuje liczbę oryginalnego rekordy, które reprezentuje. Jest równa 1 + Liczba poprzednich odrzuconych rekordów. 

## <a name="limits-summary"></a>Podsumowanie ograniczeń

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Wyłącz dzienny limit wiadomości e-mail.

Aby wyłączyć dzienny wolumin limit wiadomości e-mail, w obszarze **Konfiguruj** części zasobu usługi Application Insights w **użycie i szacunkowe koszty** okienku wybierz **dzienny limit** . Istnieją ustawienia, aby wysłać wiadomość e-mail, gdy limit zostanie osiągnięty, a także gdy zmienianych poziom ostrzeżeń został osiągnięty. Jeśli chcesz wyłączyć wszystkie dzienny limit, związanych z woluminami wiadomości e-mail Usuń zaznaczenie pola wyboru obu polach.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Warstwa cenowa starszej wersji Enterprise (na węzeł)

Dla wcześnie wdrażających użytkowników usługi Azure Application Insights nadal istnieją dwie możliwe warstwy cenowe: Podstawowe, jak i Enterprise. Warstwa cenowa podstawowa jest taka sama, jak opisano powyżej, a to domyślna warstwa. Obejmuje wszystkie funkcje warstwy Enterprise, bez ponoszenia dodatkowych kosztów. Przede wszystkim od ilości danych, które są pozyskiwane w ramach warstwy podstawowa. 

> [!NOTE]
> Zmieniono tych starszych warstw cenowych. Enterprise warstwa cenowa jest teraz nazywana **na węzeł** i warstwy cenowej podstawowa jest teraz nazywana **na GB**. Te nowe nazwy są używane w dół i w witrynie Azure portal.  

W warstwie na węzeł (dawniej Enterprise) ma opłaty za węzeł i każdy węzeł otrzyma dzienny przydział danych. W warstwie cenowej na węzeł opłaty są naliczane za dane pozyskane powyżej uwzględnione dopuszczalnej wartości. Jeśli używasz pakietu Operations Management Suite, wybierz warstwę na węzeł. 

Dla bieżącego cenach w regionie i waluty, zobacz [cen usługi Application Insights](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> W kwietniu 2018 r. Firma Microsoft [wprowadzone](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) nowego modelu cenowego monitorowania platformy Azure. Ten model przyjmuje prosty model "zgodnie z rzeczywistym użyciem" w pełnym portfolio monitorowanie usług. Dowiedz się więcej o [nowego modelu cen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), jak do [ocenić wpływ przenoszenia do tego modelu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) na podstawie Twojej wzorców użycia i [sposób dołączenia do nowego modelu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Na warstwie węzła oraz uprawnienia subskrypcji pakietu Operations Management Suite

Klienci, którzy wykupią pakietu Operations Management Suite E1 i E2 można uzyskać Application Insights na węzeł jako dodatkowych składników, bez ponoszenia dodatkowych kosztów jako [ogłoszonej wcześniej](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). W szczególności każda jednostka wersji pakietu Operations Management Suite E1 i E2 obejmuje uprawnienie do jednego węzła w warstwie Application Insights na węzeł. Każdy węzeł usługi Application Insights obejmuje maksymalnie 200 MB danych przetwarzanych dziennie (oddzielony od pozyskiwania danych usługi Log Analytics), z przechowywaniem danych 90 dni bez ponoszenia dodatkowych kosztów. Warstwy są opisane bardziej szczegółowo w dalszej części tego artykułu. 

Ponieważ ta warstwa dotyczy tylko klientów z subskrypcją pakietu Operations Management Suite, klienci, którzy nie masz subskrypcji pakietu Operations Management Suite nie ma możliwość wybrania tej warstwy.

> [!NOTE]
> Aby upewnić się, że uzyskać to uprawnienie, zasobów usługi Application Insights musi znajdować się w węźle na warstwę cenową. To uprawnienie ma zastosowanie tylko jako węzły. Zasoby usługi Application Insights w warstwie na GB okaże się żadnych korzyści. To uprawnienie nie jest widoczny w szacowane koszty wyświetlane w **użycia i szacowanych kosztów** okienka. Po przeniesieniu subskrypcji do nowej platformy Azure, monitorowanie modelu cen w kwietniu 2018 r. w warstwie na GB jest również jedynym dostępnej warstwy. Przeniesienie subskrypcji do nowego modelu cenowego monitorowania platformy Azure nie jest zalecane, jeśli masz subskrypcję pakietu Operations Management Suite.

### <a name="how-the-per-node-tier-works"></a>Jak działa w warstwie na węzeł

* Płacisz za każdy węzeł, który wysyła dane telemetryczne dotyczące wszystkich aplikacji w warstwie na węzeł.
  * A *węzła* jest komputer fizyczny lub wirtualny serwer lub wystąpienie roli platforma jako usługa, która jest hostem aplikacji.
  * Komputerach deweloperskich, przeglądarek klientów i urządzeń przenośnych nie są traktowane jako węzły.
  * Jeśli aplikacja ma kilka składników, które wysyłają dane telemetryczne, np. usługi sieci web i procesu roboczego zaplecza, składniki są zliczane osobno.
  * [Live Stream metryki](../../azure-monitor/app/live-stream.md) danych nie jest liczony cennik celów. W ramach subskrypcji usługi opłaty są naliczane za węzeł, a nie aplikacji. Jeśli masz pięć węzłów, które wysyłają dane telemetryczne dla 12 aplikacji, Opłata dotyczy pięć węzłów.
* Mimo, że opłaty są podane na miesiąc, opłaty są naliczane tylko za godziny, w których dany węzeł przesyła dane telemetryczne z aplikacji. Opłata za godziny jest cudzysłowie opłata miesięczna podzielona przez 744 (liczba godzin w miesiącu mającym 31 dni).
* Dane woluminu alokacji wynoszącej 200 MB dziennie znajduje się w każdym węźle, który został wykryty (z dokładnością co godzinę). Niewykorzystane dane alokacji nie jest przenoszone od 1 dnia następnego.
  * Jeśli wybierzesz warstwę cenową na węzeł, każda subskrypcja otrzymuje dzienny przydział danych na podstawie liczby węzłów, które wysyłają dane telemetryczne do zasobów usługi Application Insights w tej subskrypcji. Dlatego jeśli masz pięć węzłów wysyłających dane, cały dzień, będziesz mieć dzienny przydział 1 GB, stosowany do wszystkich zasobów usługi Application Insights w tej subskrypcji. Nie ma znaczenia, jeśli niektóre węzły wysyła więcej danych niż inne węzły, ponieważ uwzględnione dane są dzielone między wszystkie węzły. Jeśli w danym dniu zasoby usługi Application Insights, otrzymasz więcej danych niż przewiduje dzienny przydział danych dla tej subskrypcji, na GB nadwyżki danych opłaty. 
  * Dzienny przydział danych jest obliczany jako liczba godzin w ciągu dnia (przy użyciu czasu UTC) czy każdy węzeł wysyła dane telemetryczne, podzielona przez 24 pomnożona przez 200 MB. Dlatego w przypadku czterech węzłów, które wysyłają dane telemetryczne przez 15 godzin z 24 godzin dnia uwzględnione dane na ten dzień będzie ((4 &#215; 15) / 24) &#215; 200 MB = 500 MB. W cenie za nadwyżkowe użycie danych 2.30 USD za GB opłata byłaby 1,15 USD węzły wysłać 1 GB danych tego samego dnia.
  * Dzienny limit warstwę na węzeł nie jest dzielony między aplikacje, dla których wybrano warstwę na GB. Nieużywane dopuszczalnej wartości nie jest przenoszone z typowymi. 

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Przykłady sposobu ustalania liczby unikatowych węzłów

| Scenariusz                               | Łączna liczba węzeł dziennie |
|:---------------------------------------|:----------------:|
| 1 aplikacja, korzystająca z 3 wystąpień usługi Azure App Service i 1 serwer wirtualny | 4 |
| 3 działających na maszynach wirtualnych 2; zasoby usługi Application Insights dla tych aplikacji są w tej samej subskrypcji, a w warstwie na węzeł | 2 | 
| 4 aplikacji, którego zasoby aplikacji szczegółowe informacje znajdują się w tej samej subskrypcji; Każda aplikacja uruchomiona 2 wystąpienia 16 poza godzinami pracy, a 4 wystąpień w godzinach szczytu 8 | 13.33 | 
| Cloud services za pomocą roli procesu roboczego 1 oraz roli sieci Web 1, każdy uruchomiony 2 wystąpienia | 4 | 
| 5 węzłami klastra usługi Azure Service Fabric z systemem mikrousług 50; Każda mikrousługa uruchomionych wystąpień 3 | 5|

* Zliczanie dokładne węzła zależy od tego, w których zestaw SDK usługi Application Insights używa aplikacja. 
  * W wersji zestawu SDK 2,2 lub nowszą wersją, zarówno w usłudze Application Insights [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) i [zestawu SDK sieci Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) zgłosić każdego hosta aplikacji jako węzeł. Przykłady to nazwa komputera dla serwera fizycznego i hostów maszyn wirtualnych lub nazwę wystąpienia dla usług w chmurze.  Jedynym wyjątkiem jest aplikacja, która używa tylko [platformy .NET Core](https://dotnet.github.io/) i podstawowego zestawu SDK usługi Application Insights. W takim przypadku tylko jeden węzeł jest zgłaszany na wszystkich hostach, ponieważ nazwa hosta nie jest dostępna. 
  * We wcześniejszych wersjach zestawu SDK [zestawu SDK sieci Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) zachowuje się jak nowsze wersje zestawu SDK, ale [Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) zgłasza tylko jeden węzeł, niezależnie od liczby hostów aplikacji. 
  * Jeśli aplikacja używa zestawu SDK, aby ustawić **roleInstance** niestandardowej wartości domyślnie tej samej wartości służy do określania liczby węzłów. 
  * Jeśli używasz nowej wersji zestawu SDK z aplikacji, która jest uruchamiana z urządzeń przenośnych i komputerów klienckich, liczba węzłów może zwrócić liczbę, która jest bardzo duża (z powodu dużej liczby urządzeń przenośnych i komputerów klienckich). 

## <a name="automation"></a>Automatyzacja

Można napisać skrypt, aby ustawić warstwę cenową za pomocą usługi Azure Resource Management. [Dowiedz się, jak to zrobić](powershell.md#price).


## <a name="next-steps"></a>Kolejne kroki

* [Próbkowanie](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/
