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
ms.openlocfilehash: 22c8616c1585e3f728a03a794c527cb34fc0c4eb
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/03/2018
---
# <a name="manage-pricing-and-data-volume-in-application-insights"></a>Zarządzanie wolumin ceny i danych w usłudze Application Insights

Cennik [Azure Application Insights] [ start] opiera się na ilość danych na aplikację. Każdy zasób usługi Application Insights jest rozliczany jako osobne usługi i przyczynia się do kwota rachunku dla subskrypcji platformy Azure.

Usługa Application Insights ma dwa planów cenowych: Basic i Enterprise. Podstawowe planu cenowego jest domyślny plan. Zawiera wszystkie funkcje plan Enterprise, bez ponoszenia dodatkowych kosztów. Podstawowy plan BOM przede wszystkim od ilości danych, która jest pozyskanych. 

Enterprise plan ma opłat każdego węzła i każdego węzła otrzyma dziennych dodatków danych. W przedsiębiorstwie cenową planu, naliczane są opłaty danych pozyskanych powyżej dołączone dodatku. Jeśli używasz usługi Operations Management Suite, należy wybrać Enterprise plan. 

Jeśli masz pytania dotyczące sposobu działania ceny dla usługi Application Insights, należy wysłać zapytanie w naszym [forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=ApplicationInsights).

## <a name="pricing-plans"></a>Planów cenowych

Dla bieżącego cen w walucie i regionu, zobacz [usługi Application Insights wyceny][pricing].

> [!NOTE]
> W kwietniu 2018 firma Microsoft [wprowadzone](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) nowego modelu cenowego monitorowania Azure. Ten model przyjmuje prostego modelu "z" między pełną gamę monitorowanie usług. Dowiedz się więcej o [nowy model cenowy](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), jak do [ocenić wpływ przenoszenia do tego modelu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) oparte na Twoich wzorców użycia i [jak zgłosić do nowego modelu do](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model).

### <a name="basic-plan"></a>Podstawowy plan

Podstawowy plan jest ustawieniem domyślnym cennik planu, gdy zostanie utworzony nowy zasób usługi Application Insights. Podstawowy plan jest optymalna dla wszystkich klientów z wyjątkiem tych, którzy mają subskrypcję usługi Operations Management Suite.

* W planie Basic naliczane są opłaty za ilość danych. Ilość danych jest to liczba bajtów odebranych przez usługę Application Insights telemetrii. Ilość danych jest mierzony jako rozmiar nieskompresowanych pakiet danych JSON, który jest odbierany przez usługę Application Insights z poziomu aplikacji. Aby uzyskać [zaimportowany do analizy danych tabelarycznych](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import), ilość danych jest mierzony jako rozmiar nieskompresowanych plików, które są wysyłane do usługi Application Insights.
* Opłaty wolumin danych aplikacji są teraz zgłaszanych na nowy licznik rozliczeń o nazwie **wprowadzanie danych** od 2018 kwietnia. Nowy licznik jest udostępniany przez monitorowanie technologii, takich jak aplikacje wgląd i analizy dzienników i jest obecnie pod nazwą usługi **usługi aplikacji** (i szybko zmienić **analizy dzienników**). 
* [Metryki strumień na żywo](app-insights-live-stream.md) danych nie są one uwzględniane dla celów cennik.
* [Eksport ciągły](app-insights-export-telemetry.md) i [łącznika Azure Log Analytics](https://go.microsoft.com/fwlink/?LinkId=833039&amp;clcid=0x409) dostępnych bez dodatkowych opłat w planie Basic lub nowszym 2018 kwietnia.

### <a name="enterprise-plan-and-operations-management-suite-subscription-entitlements"></a>Enterprise plan i uprawnienia do subskrypcji usługi Operations Management Suite

Klientów, którzy kupili E1 Operations Management Suite i E2 można uzyskać Application Insights przedsiębiorstwa jako dodatkowy składnik bez ponoszenia dodatkowych kosztów jako [poprzednio](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). W szczególności każdej jednostki E1 Operations Management Suite i E2 obejmuje uprawnienie do jednego węzła plan Enterprise Insights aplikacji. Każdy węzeł usługi Application Insights zawiera maksymalnie 200 MB danych pozyskanych dziennie (niezależnie od wprowadzanie danych analizy dzienników), z przechowywaniem danych 90-dniowy bez ponoszenia dodatkowych kosztów. Plan jest opisany bardziej szczegółowo w dalszej części tego artykułu. 

Ponieważ ten plan ma zastosowanie tylko do klientów z subskrypcją usługi Operations Management Suite, klientów, którzy nie mają subskrypcji usługi Operations Management Suite nie widzisz opcję, aby wybrać ten plan.

> [!NOTE]
> Aby mieć pewność, że to uprawnienie, zasobów usługi Application Insights musi być w przedsiębiorstwie cenową planu. To prawo dotyczy tylko jako węzły. Zasoby aplikacji wgląd w planie Basic okaże się żadnych korzyści. To uprawnienie nie jest widoczny w szacowanych kosztów pokazano **użycia i szacowany koszt** okienka. Ponadto po przeniesieniu subskrypcji Azure nowe, monitorowanie modelu cenowego 2018 kwietnia podstawowy plan jest tylko plan dostępne. Przenoszenie subskrypcji Azure nowego modelu cenowego monitorowania nie jest zalecane, jeśli masz subskrypcję usługi Operations Management Suite.

Aby uzyskać więcej informacji o planie przedsiębiorstwa, zobacz [Enterprise szczegóły cennika](app-insights-pricing-enterprise-details.md).

### <a name="multi-step-web-tests"></a>Wieloetapowe testy sieci Web

[Testy sieci web wieloetapowych](app-insights-monitor-web-app-availability.md#multi-step-web-tests) naliczenie dodatkowych opłat. Testy sieci web wieloetapowych są testy sieci web, które wykonują sekwencję akcji.

Nie istnieje bezpłatna oddzielnych *polecenie ping testy* z jednej strony. Dane telemetryczne z testów ping i wieloetapowych jest pobierana identyczny inne dane telemetryczne z aplikacji.

## <a name="review-pricing-plans-and-estimate-costs"></a>Przejrzyj planów cenowych i Szacowanie kosztów

Usługi Application Insights ułatwia zrozumienie planów cenowych, które są dostępne, i jakie koszty mogą być oparte na najnowszych wzorców użycia. Aby rozpocząć, w portalu Azure, dla zasobu usługi Application Insights, przejdź do **użycia i szacowane koszty** okienka:

![Wybierz cennik](./media/app-insights-pricing/pricing-001.png)

A. Przejrzyj woluminu dane dla danego miesiąca. Dotyczy to wszystkich danych, które odebrał i przechowywane (po jednej [próbkowania](app-insights-sampling.md)) z serwera i klienta, aplikacji oraz z testów dostępności.  
B. Wykonywane oddzielne opłat [testy sieci web wieloetapowych](app-insights-monitor-web-app-availability.md#multi-step-web-tests). (Ta nie obejmuje testów dostępności proste, które są zawarte w danych opłat woluminu).  
C. Wyświetlanie danych woluminu trendów w ciągu ostatnich miesiącu.  
D. Włącz wprowadzanie danych [próbkowania](app-insights-sampling.md).   
E. Ustaw codzienne zakończenia woluminów danych.  

Application Insights opłaty są dodawane do rachunku platformy Azure. Można wyświetlić szczegóły Azure naliczać opłaty w **rozliczeń** sekcji z portalu Azure lub w [portalu Azure rozliczeń](https://account.windowsazure.com/Subscriptions). 

![W menu po lewej stronie wybierz rozliczeń](./media/app-insights-pricing/02-billing.png)

## <a name="data-rate"></a>Szybkość danych
Ilość danych wysyłanych jest ograniczona na trzy sposoby:

* **Próbkowanie**: próbkowania umożliwia zmniejszenie ilości dane telemetryczne są wysyłane z serwera i klienta aplikacji, z minimalnym zakłócenia metryki. Próbkowanie jest podstawowym narzędziem, którego można użyć do dostrajania ilość danych wysyłanych. Dowiedz się więcej o [próbkowania funkcji](app-insights-sampling.md). 
* **Dzienny limit**: podczas tworzenia zasobu usługi Application Insights w portalu Azure, dzienny limit wynosi 100 GB/dzień. Podczas tworzenia zasobu usługi Application Insights w programie Visual Studio, wartość domyślna to mały (tylko 32,3 MB na dzień). Codzienne domyślne zakończenia ustawiono ułatwić testowanie. Jest on przeznaczony, użytkownik zostanie podniesiony codzienne centralnych zasad dostępu przed wdrożeniem aplikacji w środowisku produkcyjnym. 

    Maksymalny limit jest 1000 GB/dzień, chyba że żądanie maksymalną wyższą dla aplikacji o dużym natężeniu ruchu. 

    Po ustawieniu codzienne centralnych zasad dostępu, należy zachować ostrożność. Z celem powinno być *nigdy nie osiągnęła limit dzienny*. Jeśli zostanie osiągnięty dzienny limit, utratę danych do końca dnia, a nie można monitorować aplikacji. Aby zmienić dzienny limit, użyj **dzienny limit woluminu** opcji. Można uzyskać dostępu do tej opcji w **użycia i szacowane koszty** okienka (opisany bardziej szczegółowo w dalszej części tego artykułu).
    Usunęliśmy ograniczenie niektórych typów subskrypcji, które mają kredyt, który nie może być używane dla usługi Application Insights. Wcześniej, jeśli subskrypcja jest objęta limitem wydatków, codzienne zakończenia okno dialogowe ma okno instrukcjami, aby usunąć limit wydatków i włączyć codzienne WPR wygenerowany poza 32,3 MB/dzień.
* **Ograniczanie**: ograniczenie szybkość transmisji do 32 000 zdarzeń na sekundę, średnio ponad 1 minutę.

*Co się stanie, jeśli Moja aplikacja przekracza ograniczenie przepustowości?*

* Ilość danych aplikacji wysyłanych przez jest oceniane co minutę. W przypadku przekroczenia na sekundę szybkość uśrednionej za minutę, serwer odrzuca niektórych żądań. Zestaw SDK buforów danych, a następnie próbuje ponownie. Rozprzestrzenia się skoków w ciągu kilku minut. Jeśli aplikacja stale wysyła dane większa od wartości ograniczenia przepustowości, niektóre dane zostaną usunięte. (ASP.NET, Java i JavaScript SDK ponownie wysłać dane w ten sposób; innych zestawów SDK może po prostu Usuń ograniczenie danych). W przypadku ograniczania przepustowości, ostrzeżenie powiadomienie ostrzega o tym, że ten błąd wystąpił.

*Jak sprawdzić, jak dużo danych wysyła mojej aplikacji?*

Służy jedną z poniższych opcji, aby zobaczyć, jak dużo danych wysyła aplikacji:

* Przejdź do **użycia i szacowany koszt** okienko, aby zobaczyć codzienne wykresu woluminów danych. 
* W Eksploratorze metryk należy dodać nowego wykresu. Metryki wykresu wybierz **ilość punktów danych**. Włącz **grupowanie**, a następnie Grupuj według **— typ danych**.

## <a name="reduce-your-data-rate"></a>Zmniejsz częstotliwość danych
Poniżej przedstawiono niektóre czynności, które można wykonać, aby zmniejszyć wolumin danych:

* Użyj [próbkowania](app-insights-sampling.md). Ta technologia zmniejsza szybkość danych bez pochylanie Twoje metryki. Nie utracisz możliwość przechodzić między elementów pokrewnych wyszukiwania. W aplikacjach server próbkowania działa automatycznie.
* [Ogranicz liczbę wywołań Ajax, które mogą być zgłaszane](app-insights-javascript.md#detailed-configuration) w każdy widok strony, albo Przełącz funkcję Raportowanie Ajax.
* [Edytuj ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md) wyłączyć kolekcja modułów, które nie jest potrzebny. Na przykład można zdecydować, że liczniki wydajności lub dane zależności są inessential.
* Podziel telemetrii wśród kluczy oddzielne instrumentacji. 
* Metryki wstępnie agregacji. Jeśli umieścisz wywołania TrackMetric w aplikacji może zmniejszyć ruch za pomocą przeciążenia, które akceptuje obliczenia średniej i odchylenie standardowe partii pomiarów. Możesz też użyć [wstępnie agregację pakietu](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="manage-the-maximum-daily-data-volume"></a>Maksymalna ilość danych codziennego zarządzania

Dzienny limit woluminu umożliwia ograniczenie zebranych danych. Jednak spełnieniu centralnych zasad dostępu występuje utratę wszystkich danych telemetrycznych wysłanych z aplikacji w pozostałej dnia. Jest *nie zaleca się* aby aplikacja osiągnęła limit dzienny. Nie można śledzić kondycji i wydajności aplikacji po dotarciu serwerów do codziennego centralnych zasad dostępu.

Zamiast korzystać z codziennych zakończenia wolumin, użyj [próbkowania](app-insights-sampling.md) dostrajania ilość danych do poziomu, który ma. Następnie należy użyć dzienny limit jedynie w ostateczności"", w przypadku, gdy aplikacja nieoczekiwanie zaczyna wysyłać znacznie wyższe woluminów danych telemetrycznych.

Aby zmienić codzienne centralnych zasad dostępu, w **Konfiguruj** sekcji zasobu usługi Application Insights w **użycia i szacowane koszty** okienku, wybierz **dzienny limit**.

![Dostosuj codzienne zakończenia woluminu telemetrii](./media/app-insights-pricing/pricing-003.png)

## <a name="sampling"></a>Próbkowanie
[Próbkowanie](app-insights-sampling.md) jest metoda zmniejsza szybkość, z jaką dane telemetryczne są wysyłane do aplikacji, przy zachowaniu możliwość wyszukiwania powiązanych zdarzeń podczas wyszukiwania diagnostycznych. Możesz również zachować liczby poprawnego zdarzenia.

Próbkowanie to efektywny sposób zmniejszyć koszty i pozostać w wykorzystaniu całego przydziału miesięcznego. Algorytm próbkowania zachowuje pozycje powiązane dane telemetryczne tak, na przykład podczas wyszukiwania, możesz znaleźć żądania dotyczące określonego wyjątku. Algorytm również zachowuje poprawne liczby, tak aby widoczne poprawne wartości w Eksploratorze metrykę dla żądań, wyjątków stawki i inne liczby.

Istnieje kilka metod pobierania próbek.

* [Próbkowanie adaptacyjną](app-insights-sampling.md) jest wartością domyślną dla tego zestawu SDK platformy ASP.NET. Próbkowanie adaptacyjną automatycznie dostosowuje ilość danych telemetrycznych, wysyłanej przez aplikację. Działa automatycznie w zestawie SDK w aplikacji sieci web tak, aby zmniejsza telemetrii ruch w sieci. 
* *Wprowadzanie próbkowania* stanowi alternatywę, który działa w momencie, w którym dane telemetryczne z aplikacji wprowadza usługa Application Insights. Wprowadzanie próbkowania nie wpływa na ilość danych telemetrycznych wysłanych z aplikacji, ale zmniejsza woluminu, który jest zachowywana przez usługę. Wprowadzanie próbkowania umożliwia obniżenie limit przydziału używany przez dane telemetryczne z przeglądarki i innych zestawów SDK.

Aby ustawić wprowadzanie próbkowania, przejdź do **cennik** okienka:

![W okienku cenową i limit przydziału wybierz Kafelek próbek, a następnie wybierz ułamek pobierania próbek](./media/app-insights-pricing/pricing-004.png)

> [!WARNING]
> **Danych próbkowania** okienka kontrolki tylko wartość wprowadzanie próbkowania. Go nie odzwierciedlają próbkowania stosowany przez zestaw SDK usługi Application Insights w aplikacji. Jeśli przychodzące dane telemetryczne już uzyskane podczas próbkowania w zestawie SDK, nie jest stosowana wprowadzanie próbkowania.
>

Aby odnaleźć rzeczywiste próbkowania, niezależnie od tego, gdzie jest zastosowane, należy użyć [Analytics query](app-insights-analytics.md). Zapytanie wygląda następująco:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

W każdym zachowane rekordu, `itemCount` wskazuje liczbę oryginalnego rekordów, które reprezentuje. Ta jest równa 1 + Liczba poprzednich odrzuconych rekordów. 

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