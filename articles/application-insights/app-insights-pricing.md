---
title: Zarządzanie cenami i ilością danych dla usługi Azure Application Insights | Dokumentacja firmy Microsoft
description: Zarządzanie woluminami danych telemetrycznych i monitorowanie kosztów w usłudze Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: ebd0d843-4780-4ff3-bc68-932aa44185f6
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: Dale.Koetke
ms.date: 08/11/2018
ms.author: mbullwin
ms.openlocfilehash: a81cb9041b905cfb00183981036116fbc61f376a
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000876"
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Zarządzanie cenami i ilością danych w usłudze Application Insights

> [!NOTE]
> W tym artykule opisano, jak analizować użycie danych usługi Application Insights.  Zapoznaj się z następującymi artykułami, aby uzyskać powiązane informacje.
> - [Monitorowanie użycia i szacowanych kosztów](../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) zawiera opis sposobu wyświetlania użycie i szacowane koszty w wielu monitorowania funkcji różne modele cen platformy Azure. Opisuje ona również, jak można zmienić modelu cen.

Cennik usługi [usługi Azure Application Insights] [ start] jest oparty na wolumenie danych na aplikację. Każdy zasób usługi Application Insights jest rozliczane jako osobną usługą i przyczynia się do rachunek dla subskrypcji platformy Azure.

Usługa Application Insights ma dwa plany cenowe: Basic i Enterprise. Podstawowy plan taryfowy jest domyślny plan. Obejmuje wszystkie funkcje planu Enterprise, bez ponoszenia dodatkowych kosztów. Przede wszystkim od ilości danych, które są pozyskiwane w ramach planu podstawowego. 

Enterprise plan ma opłaty za węzeł i każdy węzeł otrzyma dzienny przydział danych. W przedsiębiorstwie cennikiem, opłaty są naliczane za dane pozyskane powyżej uwzględnione dopuszczalnej wartości. Jeśli używasz pakietu Operations Management Suite, należy wybrać Enterprise plan. 

Jeśli masz pytania na temat cen usługi Application Insights, możesz zadać pytanie na naszym [forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="pricing-plans"></a>Plany cenowe

Dla bieżącego cen w regionie i waluty, zobacz [cen usługi Application Insights][pricing].

> [!NOTE]
> W kwietniu 2018 r. Firma Microsoft [wprowadzone](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) nowego modelu cenowego monitorowania platformy Azure. Ten model przyjmuje prosty model "zgodnie z rzeczywistym użyciem" w pełnym portfolio monitorowanie usług. Dowiedz się więcej o [nowego modelu cen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), jak do [ocenić wpływ przenoszenia do tego modelu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) na podstawie Twojej wzorców użycia i [sposób dołączenia do nowego modelu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model).

### <a name="basic-plan"></a>Podstawowy plan

Podstawowy plan jest domyślny plan cenowy, gdy jest tworzony nowy zasób usługi Application Insights. Planem Basic jest optymalna dla wszystkich klientów z wyjątkiem tych, którzy mają subskrypcję pakietu Operations Management Suite.

* W planie Basic opłata jest naliczana według ilości danych. Ilość danych jest liczba bajtów odebranych przez usługę Application Insights dane telemetryczne. Ilość danych jest mierzony jako rozmiar nieskompresowanych pakietów danych JSON, które jest odebrane przez usługę Application Insights z aplikacji. Aby uzyskać [zaimportowane do analizy danych tabelarycznych](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), ilość danych jest mierzony jako rozmiar nieskompresowanych plików, które są wysyłane do usługi Application Insights.
* Opłaty za objętość danych aplikacji są teraz zgłaszane na nowych liczników rozliczeń o nazwie **pozyskiwanie danych** od kwietnia 2018 r. To nowego licznika można współdzielić w ramach monitorowania technologii, takich jak aplikacje Insights i Log Analytics i jest obecnie dostępna w obszarze nazwy usługi **App Services** (i szybko zmienić **usługi Log Analytics**). 
* [Live Stream metryki](app-insights-live-stream.md) danych nie jest liczony cennik celów.
* [Eksport ciągły](app-insights-export-telemetry.md) i [łącznika usługi Azure Log Analytics](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) są dostępne bez dodatkowych opłat w planie Basic, począwszy od kwietnia 2018 r.

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Enterprise plan i uprawnienia subskrypcji pakietu Operations Management Suite

Klienci, którzy wykupią pakietu Operations Management Suite E1 i E2 można uzyskać usługi Application Insights Enterprise jako dodatkowych składników, bez ponoszenia dodatkowych kosztów jako [ogłoszonej wcześniej](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). W szczególności każda jednostka wersji pakietu Operations Management Suite E1 i E2 obejmuje uprawnienie do jednego węzła planu usługi Application Insights Enterprise. Każdy węzeł usługi Application Insights obejmuje maksymalnie 200 MB danych przetwarzanych dziennie (oddzielony od pozyskiwania danych usługi Log Analytics), z przechowywaniem danych 90 dni bez ponoszenia dodatkowych kosztów. Plan jest opisany bardziej szczegółowo w dalszej części tego artykułu. 

Ponieważ ten plan dotyczy tylko klientów z subskrypcją pakietu Operations Management Suite, klienci, którzy nie masz subskrypcji pakietu Operations Management Suite nie ma możliwość wybrania tego planu.

> [!NOTE]
> Aby upewnić się, że uzyskać to uprawnienie, zasobów usługi Application Insights musi być w przedsiębiorstwie plan cenowy. To uprawnienie ma zastosowanie tylko jako węzły. Zasoby usługi Application Insights w planie Basic okaże się żadnych korzyści. To uprawnienie nie jest widoczny w szacowane koszty wyświetlane w **użycia i szacowanych kosztów** okienka. Po przeniesieniu subskrypcji do nowej platformy Azure, monitorowanie modelu cenowego z kwietnia 2018 r planem Basic jest także plan tylko dostępne. Przeniesienie subskrypcji do nowego modelu cenowego monitorowania platformy Azure nie jest zalecane, jeśli masz subskrypcję pakietu Operations Management Suite.

Aby uzyskać więcej informacji o planie Enterprise, zobacz [przedsiębiorstwa, w szczegółach cennika](app-insights-pricing-enterprise-details.md).

### <a name="multi-step-web-tests"></a>Wieloetapowe testy sieci Web

[Wieloetapowe testy sieci web](app-insights-monitor-web-app-availability.md#multi-step-web-tests) pociągnąć za sobą dodatkowych opłat. Wieloetapowe testy sieci web są testy sieci web, które wykonują sekwencję akcji.

Nie ma osobnych opłat dla *testów ping* z jednej strony. Dane telemetryczne z testów ping i wieloetapowe testy są naliczane opłaty taki sam jak inne dane telemetryczne z Twojej aplikacji.

## <a name="review-pricing-plans-and-estimate-costs"></a>Przejrzyj plany cenowe i Szacowanie kosztów

Usługa Application Insights ułatwia zrozumienie planów cenowych, które są dostępne, a jakie koszty mogą opierać się na bieżące wzorce użycia. Aby rozpocząć pracę w witrynie Azure portal dla zasobu usługi Application Insights, przejdź do **użycie i szacunkowe koszty** okienka:

![Wybierz cennik](./media/app-insights-pricing/pricing-001.png)

A. Sprawdź ilość danych na miesiąc. Obejmuje to wszystkie dane, które otrzymał i przechowywane (po jednej [próbkowania](app-insights-sampling.md)) z serwera i aplikacje klienckie i testy dostępności.  
B. Osobna opłata zostanie przeprowadzona dla [wieloetapowe testy sieci web](app-insights-monitor-web-app-availability.md#multi-step-web-tests). (Nie obejmuje to testy dostępności proste, które są zawarte w Twoich rękach woluminów danych.)  
C. Umożliwia wyświetlanie trendów wolumin danych dla ostatniego miesiąca.  
D. Włącz pozyskiwanie danych [próbkowania](app-insights-sampling.md).   
E. Ustaw limit dziennej ilości danych.  

Application Insights opłaty są dodawane na rachunku dotyczącym platformy Azure. Aby wyświetlić szczegóły platformy Azure są naliczane w **rozliczeń** sekcji w witrynie Azure Portal lub w [portalu rozliczeń systemu Azure](https://account.windowsazure.com/Subscriptions). 

![W menu po lewej stronie wybierz pozycję rozliczenia](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Szybkość danych
Ilość danych wysyłanych jest ograniczone na trzy sposoby:

* **Próbkowanie**: próbkowania można użyć, aby zmniejszyć ilość danych telemetrycznych wysyłanych z aplikacji serwera i klienta, z minimalnym zakłócenia metryki. Próbkowanie to podstawowe narzędzie używane do dostrojenia ilość danych wysyłanych. Dowiedz się więcej o [próbkowanie funkcji](app-insights-sampling.md). 
* **Dzienny limit**: podczas tworzenia zasobu usługi Application Insights w witrynie Azure portal, dzienny limit wynosi 100 GB/dzień. Podczas tworzenia zasobu usługi Application Insights w programie Visual Studio, wartość domyślna to mały (tylko 32,3 MB/dzień). Dzienny limit Domyślnie opcja jest zaznaczona ułatwić testowanie. Jest ona przeznaczona, użytkownik zostanie podnieść dziennego limitu przed wdrożeniem aplikacji do środowiska produkcyjnego. 

    Maksymalny limit jest 1000 GB/dzień, chyba że żądanie wyższą maksymalną dla aplikacji o wysokim natężeniu ruchu. 

    Po ustawieniu dzienny limit, należy zachować ostrożność. Powinno być zgodne z zamiarami użytkownika *nigdy osiągnięty dzienny limit*. W razie osiągnięcia dziennego limitu utratę danych przez pozostałą część dnia i nie może monitorować Twojej aplikacji. Aby zmienić dzienny limit, użyj **dzienny limit ilości** opcji. Możesz uzyskać dostęp ta opcja w **użycie i szacunkowe koszty** okienko (opisane bardziej szczegółowo w dalszej części tego artykułu).
    Usunęliśmy ograniczenie niektórych typów subskrypcji, obejmujących środki, które nie można użyć dla usługi Application Insights. Wcześniej, jeśli subskrypcja jest objęta limitem wydatków, dzienny limit okno dialogowe ma okno instrukcjami, aby usunąć limit wydatków i włączyć dzienny limit danych należy podnieść 32,3 MB/dzień.
* **Ograniczanie**: limity ograniczania przepływności danych do 32 000 zdarzeń na sekundę, szybkości średniej ponad 1 minutę na klucz instrumentacji.

*Co się stanie, jeśli Moja aplikacja przekracza ograniczenie przepustowości?*

* Ilość danych, które wysyła Twoja aplikacja jest oceniane na minutę. W przypadku przekroczenia stawki na sekundę uśrednionej za minutę, serwer odrzuca niektóre żądania. Zestaw SDK buforuje dane, a następnie próbuje wysłać ją ponownie. Rozprzestrzenia się skoków w ciągu kilku minut. Jeśli Twoja aplikacja stale wysyła dane więcej niż przepustowości, niektóre dane zostaną usunięte. (ASP.NET, Java i JavaScript SDK ponownie wysłać danych w ten sposób; innych zestawów SDK może po prostu upuszczania ograniczenia danych). Jeśli wystąpi ograniczanie przepustowości, ostrzeżenie powiadomienie ostrzega o tym, wystąpienia tego zdarzenia

*Jak sprawdzić, jak dużo danych, które wysyła mojej aplikacji?*

Aby zobaczyć, jak dużo danych, Twoja aplikacja wysyła, można użyć jednej z następujących opcji:

* Przejdź do **użycia i szacowanych kosztów** okienku, aby zobaczyć codzienne wykres danych w woluminie. 
* W Eksploratorze metryk Dodaj nowy wykres. Wykres metryk, wybierz **wolumin punktu danych**. Włącz **grupowanie**, a następnie Grupuj według **— typ danych**.

## <a name="reduce-your-data-rate"></a>Zmniejsz szybkość danych
Oto kilka rzeczy, które można wykonać, aby zmniejszyć ilość danych:

* Użyj [próbkowania](app-insights-sampling.md). Ta technologia zmniejsza szybkość danych bez pochylanie metryk. Nie strać zdolności do przechodzenia między powiązane elementy w polu wyszukiwania. W aplikacjach server próbkowania działa automatycznie.
* [Ogranicz liczbę wywołań Ajax, które mogą być zgłaszane](app-insights-javascript.md#detailed-configuration) każdego widoku strony lub switch Wyłącz raportowanie Ajax.
* [Edytuj plik ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) wyłączyć moduły kolekcji, których nie potrzebujesz. Na przykład można zdecydować, czy liczników wydajności lub dane zależności są zbędne.
* Podziel telemetrii między kluczy Instrumentacji oddzielne. 
* Wstępnej agregacji metryk. Wywołania TrackMetric umieszczenie w aplikacji, można zmniejszyć ruch za pomocą przeciążenia, które akceptuje obliczenia średniej i odchylenie standardowe w zadaniu wsadowym pomiarów. Alternatywnie można użyć [pakiet wstępnie agregacji](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="manage-the-maximum-daily-data-volume"></a>Zarządzanie Maksymalna dzienna ilość danych

Dzienny limit ilości służy do ograniczania zebranych danych. Jednak zakończenie po spełnieniu, występuje utrata wszystkie dane telemetryczne wysyłane z aplikacji dla pozostałych dnia. Jest *nie zaleca się włączenie* aby aplikacja osiągnięty dzienny limit. Po osiągnięciu dziennego limitu nie Śledź kondycję i wydajność aplikacji.

Zamiast używania limit dziennej ilości danych, należy użyć [próbkowania](app-insights-sampling.md) ilość danych do poziomu, który chcesz dostosować. Następnie należy użyć dziennego limitu jedynie w ostateczności"", w przypadku, gdy aplikacja nieoczekiwanie zaczyna wysyłać dużo wyższej ilości danych telemetrycznych.

Zmiany dzienny limit w **Konfiguruj** części zasobu usługi Application Insights w **użycie i szacunkowe koszty** okienku wybierz **dziennego limitu**.

![Dostosuj dzienny limit ilości danych telemetrycznych](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>Próbkowanie
[Próbkowanie](app-insights-sampling.md) jest metodą zmniejszenia szybkość, z jaką dane telemetryczne są wysyłane do aplikacji, przy zachowaniu umożliwia znajdowanie powiązanych zdarzeń podczas wyszukiwania diagnostycznego. Możesz również zachować dużymi liczbami zdarzeń prawidłowe.

Próbkowanie jest efektywną metodę, aby zmniejszyć koszty i w ramach miesięcznego limitu przydziału. Algorytm próbkowania zachowuje powiązane elementy danych telemetrycznych, więc, na przykład podczas korzystania z funkcji wyszukiwania można znaleźć żądania powiązaną z określonym wyjątkiem. Algorytm zachowuje także prawidłowe liczby, tak aby było widać poprawne wartości w Eksploratorze metryk dla żądań, wyjątków stawki oraz innych liczników.

Istnieje kilka postaci próbkowania.

* [Próbkowanie adaptacyjne](app-insights-sampling.md) jest ustawieniem domyślnym dla zestawu SDK platformy ASP.NET. Próbkowania adaptacyjnego automatycznie dostosowuje się ilość danych telemetrii wysyłanej przez aplikację. Działa ona automatycznie w zestawie SDK w aplikacji sieci web tak, aby dane telemetryczne ruch w sieci jest mniejsze. 
* *Próbkowanie fragmentaryczne* stanowi alternatywę, który działa w punkcie, w którym dane telemetryczne z Twojej aplikacji wprowadza usługę Application Insights. Próbkowanie fragmentaryczne nie ma wpływu na ilości danych telemetrycznych wysyłanych z aplikacji, ale zmniejsza ilość danych są przechowywane przez usługę. Próbkowanie fragmentaryczne można użyć w celu zmniejszenia limitu przydziału, używanym przez przetwarzanie danych telemetrycznych z przeglądarki i innych zestawów SDK.

Aby ustawić próbkowanie fragmentaryczne, przejdź do **ceny** okienka:

![W okienku cen i limit przydziału wybierz Kafelek próbki, a następnie wybierz ułamek próbkowania](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> **Próbkowanie danych** okienka kontrolki tylko wartość próbkowanie fragmentaryczne. Go nie odzwierciedla częstotliwość próbkowania, która jest stosowana przez zestaw SDK usługi Application Insights w swojej aplikacji. W przypadku przychodzących danych telemetrycznych już uzyskane podczas próbkowania w zestawie SDK, nie jest stosowane próbkowanie fragmentaryczne.
>

Aby odnaleźć rzeczywiste próbkowania, niezależnie od tego, gdzie jest zastosowano, należy użyć [zapytania usługi Analytics](app-insights-analytics.md). Zapytanie wygląda następująco:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

W każdym przechowywane rekordu, `itemCount` wskazuje liczbę oryginalnego rekordy, które reprezentuje. Jest równa 1 + Liczba poprzednich odrzuconych rekordów. 

## <a name="automation"></a>Automatyzacja

Można napisać skrypt, aby ustawić plan cenowy przy użyciu usługi Azure Resource Management. [Dowiedz się, jak to zrobić](app-insights-powershell.md#price).

## <a name="limits-summary"></a>Podsumowanie ograniczeń

[!INCLUDE [application-insights-limits](../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Wyłącz dzienny limit wiadomości e-mail.

Aby wyłączyć dzienny wolumin limit wiadomości e-mail, w obszarze **Konfiguruj** części zasobu usługi Application Insights w **użycie i szacunkowe koszty** okienku wybierz **dzienny limit** . Istnieją ustawienia, aby wysłać wiadomość e-mail, gdy limit zostanie osiągnięty, a także gdy zmienianych poziom ostrzeżeń został osiągnięty. Jeśli chcesz wyłączyć wszystkie codziennie woluminu limit związane z wiadomości e-mail obu polach, usuń zaznaczenie pola wyboru.

## <a name="next-steps"></a>Kolejne kroki

* [Próbkowanie](app-insights-sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/