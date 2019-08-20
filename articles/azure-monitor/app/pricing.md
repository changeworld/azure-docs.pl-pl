---
title: Zarządzanie użyciem i kosztami dla usługi Azure Application Insights | Microsoft Docs
description: Zarządzanie woluminami telemetrii i monitorowanie kosztów w Application Insights.
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
ms.date: 08/19/2019
ms.author: dalek
ms.openlocfilehash: c3da37d89da8c70f6acdfb1b5ab9c5b10edb86f0
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624391"
---
# <a name="manage-usage-and-costs-for-application-insights"></a>Zarządzanie użyciem i kosztami Application Insights

> [!NOTE]
> W tym artykule opisano sposób analizowania Application Insights użycia danych.  Zapoznaj się z następującymi artykułami, aby uzyskać powiązane informacje.
> - [Monitorowanie użycia i szacowanych kosztów](../../monitoring-and-diagnostics/monitoring-usage-and-estimated-costs.md) zawiera opis sposobu wyświetlania użycie i szacowane koszty w wielu monitorowania funkcji różne modele cen platformy Azure. Opisuje ona również, jak można zmienić modelu cen.

Jeśli masz pytania dotyczące sposobu działania cen dla Application Insights, możesz ogłosić pytanie na naszym [forum](https://social.msdn.microsoft.com/Forums/home?forum=ApplicationInsights).

## <a name="pricing-model"></a>Model cen

Cennik [usługi Azure Application Insights][start] jest oparty na woluminie pozyskiwanym danych. Każdy zasób Application Insights jest rozliczany jako osobna usługa i przyczynia się do rozliczenia za subskrypcję platformy Azure.

### <a name="data-volume-details"></a>Szczegóły ilości danych

* Ilość danych to liczba bajtów telemetrii odebranych przez Application Insights. Ilość danych jest mierzona jako rozmiar nieskompresowanego pakietu danych JSON, który jest odbierany przez Application Insights z aplikacji. W przypadku [danych tabelarycznych zaimportowanych do analizy](https://docs.microsoft.com/azure/application-insights/app-insights-analytics-import)ilość danych jest mierzona jako nieskompresowany rozmiar plików wysyłanych do Application Insights.
* Opłaty za ilości danych aplikacji są teraz zgłaszane na nowym mierniku rozliczeniowym o nazwie pozyskiwanie **danych** od kwietnia 2018. Ten nowy licznik jest współużytkowany przez technologie monitorowania, takie jak Application Insights i Log Analytics, i jest obecnie pod nazwą usługi **log Analytics**. 
* Dane [Live Metrics Stream](../../azure-monitor/app/live-stream.md) nie są uwzględniane w celach cenowych.

> [!NOTE]
> Wszystkie ceny wyświetlane na zrzutach ekranu w tym artykule są przeznaczone tylko do celów. W przypadku bieżących cen w walucie i regionie zapoznaj się z tematem [Application Insights cenniku][pricing].

### <a name="multi-step-web-tests"></a>Wieloetapowe testy sieci Web

[Wieloetapowe testy sieci Web](../../azure-monitor/app/availability-multistep.md) wiążą się z dodatkowymi opłatami. Wieloetapowe testy sieci Web to testy sieci Web, które wykonują sekwencję akcji.

Nie ma oddzielnej opłaty za *testy ping* dla pojedynczej strony. Dane telemetryczne z testów ping i testów wieloetapowych są rozliczone tak samo jak inne dane telemetryczne z aplikacji.

## <a name="review-usage-and-estimate-costs"></a>Przegląd kosztów użytkowania i oszacowania

Application Insights ułatwia zrozumienie, jakie koszty są prawdopodobnie oparte na najnowszych wzorcach użycia. Aby rozpocząć, w Azure Portal, dla zasobu Application Insights, przejdź do strony **użycie i szacowane koszty** :

![Wybierz Cennik](./media/pricing/pricing-001.png)

A. Przejrzyj ilość danych w danym miesiącu. Obejmuje to wszystkie dane, które zostały odebrane i zachowane ( [](../../azure-monitor/app/sampling.md)po każdym próbkowaniu) z serwera i aplikacji klienckich oraz z testów dostępności.  
B. Dla wieloetapowych [testów sieci Web](../../azure-monitor/app/availability-multistep.md)dokonywana jest oddzielna opłata. (Nie obejmuje to prostych testów dostępności, które są uwzględnione w opłacie ilości danych).  
C. Wyświetlanie trendów ilości danych w ostatnim miesiącu.  
D. Włącz [próbkowanie](../../azure-monitor/app/sampling.md)pozyskiwania danych.   
E. Ustaw dzienny limit ilości danych.  

Aby dokładniej zbadać użycie Application Insights, Otwórz stronę metryki , Dodaj metrykę o nazwie "punkt danych", a następnie wybierz opcję *Zastosuj dzielenie* , aby podzielić dane przez "typ elementu telemetrii". 

Opłaty za Application Insights są dodawane do rachunku na korzystanie z platformy Azure. Szczegóły rachunku na korzystanie z platformy Azure można znaleźć w sekcji Rozliczenia w Azure Portal lub w portalu rozliczeń na [platformie Azure](https://account.windowsazure.com/Subscriptions). 

![W menu po lewej stronie wybierz pozycję rozliczenia](./media/pricing/02-billing.png)

## <a name="data-rate"></a>Szybkość danych
Ilość wysyłanych danych jest ograniczona na trzy sposoby:

* **Próbkowanie**: Możesz użyć próbkowania, aby zmniejszyć ilość danych telemetrycznych wysyłanych z serwera i aplikacji klienckich przy minimalnym zniekształceniu metryk. Próbkowanie to podstawowe narzędzie, za pomocą którego można dostroić ilość wysyłanych danych. Dowiedz się więcej o [funkcjach pobierania próbek](../../azure-monitor/app/sampling.md). 
* **Dzienny limit**: Po utworzeniu zasobu Application Insights w Azure Portal dzienny limit jest ustawiany na 100 GB/dzień. Podczas tworzenia zasobu Application Insights w programie Visual Studio wartość domyślna to małe (tylko 32,3 MB/dzień). Domyślna wartość limitu dziennego jest ustawiana na ułatwienia testowania. Przed wdrożeniem aplikacji w środowisku produkcyjnym należy zamierzać, że użytkownik będzie zgłaszał dzienny limit. 

    Maksymalny limit wynosi 1 000 GB/dzień, chyba że zostanie zażądana wyższa wartość maksymalna dla aplikacji o dużym ruchu. 

    Należy zachować ostrożność podczas ustawiania dziennego limitu. Zamiarem powinna być *nigdy nie trafiać dziennego limitu*. Po osiągnięciu dziennego limitu utracisz dane przez pozostałą część dnia i nie będzie można monitorować aplikacji. Aby zmienić dzienny limit, użyj opcji **dzienny limit ilości** . Możesz uzyskać dostęp do tej opcji w okienku **użycie i szacowane koszty** (jest to opisane w dalszej części artykułu).
    Usunięto ograniczenie dla niektórych typów subskrypcji, które mają środki kredytowe, których nie można użyć dla Application Insights. Wcześniej, jeśli subskrypcja ma limit wydatków, w oknie dialogowym dzienne zakończenie znajdują się instrukcje usuwania limitu wydatków i włączania codziennych limitów ponad 32,3 MB/dzień.
* **Ograniczanie przepustowości**: Ograniczanie przepustowości ogranicza szybkość danych do 32 000 zdarzeń na sekundę, średnio na 1 minutę na klucz Instrumentacji.

*Co się stanie, jeśli moja aplikacja przekroczy stawkę ograniczenia przepustowości?*

* Ilość danych wysyłanych przez aplikację jest szacowana co minutę. W przypadku przekroczenia stawki za sekundę obliczonej w ciągu minuty serwer odrzuca niektóre żądania. Zestaw SDK buforuje dane, a następnie próbuje ponownie wysłać ją. Rozłożenie przeskoku przez kilka minut. Jeśli aplikacja stale wysyła dane przy użyciu stawki ograniczenia, niektóre dane zostaną usunięte. (Zestawy SDK ASP.NET, Java i JavaScript próbują ponownie wysłać dane w ten sposób; inne zestawy SDK mogą po prostu porzucić dane ograniczające ograniczenia). W przypadku wystąpienia ograniczenia zostanie wyświetlone ostrzeżenie informujące o tym, że wystąpiło.

*Jak mogę wiesz, ile danych jest wysyłanych przez moją aplikację?*

Możesz użyć jednej z następujących opcji, aby sprawdzić, ile danych jest wysyłanych przez aplikację:

* Przejdź do okienka **użycie i szacowane koszty** , aby wyświetlić wykres dziennego wolumenu danych. 
* W Eksplorator metryk Dodaj nowy wykres. Dla metryki wykresu wybierz pozycję **wolumin punktu danych**. Włącz **grupowanie**, a następnie Grupuj według **typu danych**.

## <a name="reduce-your-data-rate"></a>Zmniejsz szybkość danych
Oto kilka rzeczy, które można zrobić, aby zmniejszyć ilość danych:

* Użyj [próbkowania](../../azure-monitor/app/sampling.md). Ta technologia zmniejsza szybkość danych bez pochylania metryk. Nie utracisz możliwości nawigowania między powiązanymi elementami w wyszukiwaniu. W obszarze aplikacje serwera próbkowanie działa automatycznie.
* [Ogranicz liczbę wywołań AJAX, które mogą być zgłaszane](../../azure-monitor/app/javascript.md#configuration) w każdym widoku strony, lub Wyłącz raportowanie AJAX.
* [Edytuj plik ApplicationInsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) , aby wyłączyć niepotrzebne moduły kolekcji. Na przykład można zdecydować, że liczniki wydajności lub dane zależności są nieistotne.
* Podziel dane telemetryczne między osobnymi kluczami Instrumentacji. 
* Metryki przed agregacją. W przypadku umieszczenia wywołań TrackMetric w aplikacji można zmniejszyć ruch przy użyciu przeciążenia, które akceptuje Obliczanie średniej i odchylenia standardowego partii pomiarów. Lub można użyć [wstępnie agregowanego pakietu](https://www.myget.org/gallery/applicationinsights-sdk-labs).

## <a name="manage-the-maximum-daily-data-volume"></a>Zarządzanie Maksymalna dzienna ilość danych

Możesz użyć dziennego limitu ilości, aby ograniczyć zbierane dane. Jeśli jednak zostanie osiągnięty limit, utracisz wszystkie dane telemetryczne wysyłane z aplikacji dla pozostałej części dnia. *Nie zaleca* się, aby aplikacja osiągnęła dzienny limit. Nie można śledzić kondycji i wydajności aplikacji po osiągnięciu dziennego limitu.

Zamiast używać dziennego limitu ilości, użyj [próbkowania](../../azure-monitor/app/sampling.md) , aby dostosować ilość danych do żądanego poziomu. Następnie użyj dziennego limitu tylko jako "ostatniej", jeśli aplikacja nieoczekiwanie zacznie wysyłać znacznie wyższe ilości danych telemetrycznych.

Aby zmienić dzienny limit, w sekcji **Konfigurowanie** zasobu Application Insights, na stronie **użycie i szacowane koszty** wybierz pozycję **dzienny limit**.

![Dostosuj dzienny limit ilości danych telemetrycznych](./media/pricing/pricing-003.png)

## <a name="sampling"></a>Próbkowanie
[Próbkowanie](../../azure-monitor/app/sampling.md) to metoda zmniejszania szybkości, z jaką dane telemetryczne są wysyłane do aplikacji, przy zachowaniu możliwości wyszukiwania pokrewnych zdarzeń podczas wyszukiwania diagnostycznego. Należy również zachować poprawne liczby zdarzeń.

Próbkowanie jest efektywnym sposobem zredukowania opłat i pozostawania w ramach miesięcznego limitu przydziału. Algorytm próbkowania zachowuje powiązane elementy telemetrii, dlatego na przykład podczas korzystania z wyszukiwania można znaleźć żądanie dotyczące konkretnego wyjątku. Algorytm zachowuje również poprawne liczby, aby zobaczyć poprawne wartości w Eksploratorze metryk dla stawek żądań, stawek wyjątków i innych liczników.

Istnieje kilka rodzajów próbkowania.

* [Próbkowanie adaptacyjne](../../azure-monitor/app/sampling.md) jest wartością domyślną dla zestawu SDK ASP.NET. Próbkowanie adaptacyjne automatycznie dostosowuje się do ilości danych telemetrycznych wysyłanych przez aplikację. Działa automatycznie w zestawie SDK w aplikacji sieci Web, dzięki czemu ruch telemetrii w sieci jest zmniejszany. 
* *Próbkowanie* do pozyskiwania jest alternatywą, która działa w punkcie, w którym dane telemetryczne z aplikacji stają się usługą Application Insights. Pobieranie próbek nie ma wpływu na ilość danych telemetrycznych wysyłanych z aplikacji, ale zmniejsza wolumin, który jest zachowywany przez usługę. Możesz użyć próbkowania pozyskiwania, aby zmniejszyć przydział, który jest używany przez dane telemetryczne z przeglądarek i innych zestawów SDK.

Aby ustawić próbkowanie pozyskiwania, przejdź do okienka **Cennik** :

![W okienku przydział i Cennik wybierz kafelek przykłady, a następnie wybierz ułamek próbkowania](./media/pricing/pricing-004.png)

> [!WARNING]
> Okienko **próbkowanie danych** kontroluje tylko wartość próbkowania pozyskiwania. Nie odzwierciedla ona częstotliwości próbkowania, która jest stosowana przez zestaw SDK Application Insights w aplikacji. Jeśli w zestawie SDK zostało już próbkowane przychodzące dane telemetryczne, pobieranie próbek pozyskiwania nie jest stosowane.
>

Aby wykryć rzeczywistą częstotliwość próbkowania bez względu na to, gdzie została zastosowana, użyj [zapytania analizy](analytics.md). Zapytanie wygląda następująco:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h)
    | render areachart

W każdym zachowanym rekordzie `itemCount` wskazuje liczbę oryginalnych rekordów, które reprezentuje. Jest równa 1 + liczba poprzednich odrzuconych rekordów. 

## <a name="change-the-data-retention-period"></a>Zmienić okres przechowywania danych

Domyślne przechowywanie dla zasobów Application Insights to 90 dni. Dla każdego zasobu Application Insights można wybrać różne okresy przechowywania. Pełny zestaw dostępnych okresów przechowywania to 30, 60, 90, 120, 180, 270, 365, 550 lub 730. 

Aby zmienić przechowywanie, z zasobu Application Insights przejdź na stronę **użycie i szacowane koszty** i wybierz opcję **przechowywania danych** :

![Dostosuj dzienny limit ilości danych telemetrycznych](./media/pricing/pricing-005.png)

Po włączeniu rozliczeń o dłuższym czasie przechowywania dane przechowywane przez czas dłuższy niż 90 dni będą rozliczane jako ta sama stawka, która jest rozliczana w przypadku przechowywania danych Log Analytics Azure. Dowiedz się więcej na [stronie cennika Azure monitor](https://azure.microsoft.com/pricing/details/monitor/). Bądź na bieżąco z zmiennym postępem przechowywania w ramach [tej sugestii](https://feedback.azure.com/forums/357324-azure-monitor-application-insights/suggestions/17454031). 

## <a name="limits-summary"></a>Podsumowanie limitów

[!INCLUDE [application-insights-limits](../../../includes/application-insights-limits.md)]

## <a name="disable-daily-cap-e-mails"></a>Wyłącz dzienne wiadomości e-mail

Aby wyłączyć dzienne wiadomości e-mail z limitem ilości, w sekcji **Konfiguracja** zasobu Application Insights, w okienku **użycie i szacowane koszty** wybierz pozycję **dzienny limit**. Istnieją ustawienia do wysyłania wiadomości e-mail po osiągnięciu limitu, a także czas osiągnięcia dodanego poziomu ostrzegawczego. Jeśli chcesz wyłączyć wszystkie wiadomości e-mail dotyczące woluminu dziennego, usuń zaznaczenie obu pól.

## <a name="legacy-enterprise-per-node-pricing-tier"></a>Warstwa cenowa starszej wersji przedsiębiorstwa (na węzeł)

W przypadku wczesnych użytkowników platformy Azure Application Insights nadal istnieją dwie możliwe warstwy cenowe: Basic i Enterprise. Podstawowa warstwa cenowa jest taka sama jak opisana powyżej i jest warstwą domyślną. Obejmuje ona wszystkie funkcje warstwy korporacyjnej bez dodatkowych kosztów. Podstawowa warstwa jest rozliczana głównie na ilość danych, które zostały pozyskane. 

> [!NOTE]
> Te starsze warstwy cenowe zostały zmienione. Warstwa cenowa przedsiębiorstwa jest teraz wywoływana **dla każdego węzła** , a podstawowa warstwa cenowa jest teraz wywoływana **na GB**. Te nowe nazwy są używane poniżej i w Azure Portal.  

Warstwa na węzeł (dawniej Enterprise) ma opłatą za węzeł, a każdy węzeł otrzymuje dzienny limit danych. W warstwie cenowej na węzeł opłaty są naliczane za dane pozyskane powyżej uwzględnionego przydziału. Jeśli używasz pakietu Operations Management Suite, wybierz warstwę na węzeł. 

W przypadku bieżących cen w walucie i regionie zapoznaj się z tematem [Application Insights cenniku](https://azure.microsoft.com/pricing/details/application-insights/).

> [!NOTE]
> W kwietniu 2018 wprowadziliśmy nowy [](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) model cen dla monitorowania platformy Azure. Ten model przyjmuje prosty model "płatność zgodnie z rzeczywistym użyciem" w całym portfolio usług monitorowania. Dowiedz się więcej o [nowym modelu cen](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs), sposobach [oceny wpływu przejścia na ten model](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#assessing-the-impact-of-the-new-pricing-model) na podstawie wzorców użycia i [sposobu wybierania nowego modelu](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-usage-and-estimated-costs#moving-to-the-new-pricing-model)

### <a name="per-node-tier-and-operations-management-suite-subscription-entitlements"></a>Uprawnienia do subskrypcji dla warstwy węzła i pakietu Operations Management Suite

Klienci, którzy kupują Pakiet Operations Management Suite E1 i E2, mogą uzyskać Application Insights na węzeł jako dodatkowy składnik bez dodatkowych kosztów zgodnie z [dotychczas ogłoszonymi](https://blogs.technet.microsoft.com/msoms/2017/05/19/azure-application-insights-enterprise-as-part-of-operations-management-suite-subscription/). Każda jednostka pakietu Operations Management Suite E1 i E2 obejmuje uprawnienie do jednego węzła Application Insights na warstwę węzła. Każdy węzeł Application Insights obejmuje nawet 200 MB danych pobieranych dziennie (oddzielenie od Log Analytics pozyskiwania danych), z 90m przechowywaniem danych bez dodatkowych kosztów. Warstwa została opisana bardziej szczegółowo w dalszej części artykułu. 

Ponieważ ta warstwa ma zastosowanie tylko do klientów z subskrypcją pakietu Operations Management Suite, klienci, którzy nie mają subskrypcji pakietu Operations Management Suite, nie widzą opcji wyboru tej warstwy.

> [!NOTE]
> Aby upewnić się, że te uprawnienia uzyskasz, Application Insights zasoby muszą znajdować się w warstwie cenowej węzła. To uprawnienie ma zastosowanie tylko do węzłów. Application Insights zasobów w warstwie na GB nie ma żadnej korzyści. To uprawnienie nie jest widoczne w szacowanych kosztach widocznych w okienku **użycie i szacowany koszt** . Ponadto Jeśli przeniesiesz subskrypcję do nowego modelu cen monitorowania platformy Azure w kwietniu 2018, dostępna jest tylko warstwa za GB. Nie zaleca się przeniesienia subskrypcji do nowego modelu cen monitorowania platformy Azure, jeśli masz subskrypcję pakietu Operations Management Suite.

### <a name="how-the-per-node-tier-works"></a>Jak działa warstwa na węzeł

* Płacisz za każdy węzeł, który wysyła dane telemetryczne dla wszystkich aplikacji w warstwie na węzeł.
  * *Węzeł* jest maszyną fizyczną lub wirtualną lub wystąpieniem roli "platforma jako usługa", które hostuje aplikację.
  * Komputery deweloperskie, przeglądarki klienta i urządzenia przenośne nie są liczone jako węzły.
  * Jeśli aplikacja zawiera kilka składników, które wysyłają dane telemetryczne, takie jak usługa sieci Web i proces roboczy zaplecza, składniki są zliczane osobno.
  * Dane [Live Metrics Stream](../../azure-monitor/app/live-stream.md) nie są uwzględniane w celach cenowych. Opłaty są naliczane za każdy węzeł, a nie za aplikację. Jeśli masz pięć węzłów, które wysyłają dane telemetryczne dla 12 aplikacji, opłata dotyczy pięciu węzłów.
* Mimo że opłaty są naliczane miesięcznie, opłata jest naliczana tylko za godzinę, w której węzeł wysyła dane telemetryczne z aplikacji. Opłata godzinowa to płatna opłata miesięczna podzielona przez 744 (liczba godzin w ciągu 31 dni).
* Przydział ilości danych 200 MB dziennie jest podawany dla każdego wykrytego węzła (z dokładnością co godzinę). Nieużywane alokacje danych nie są przenoszone z jednego dnia do następnego.
  * W przypadku wybrania warstwy cenowej na węzeł Każda subskrypcja pobiera dzienny limit danych na podstawie liczby węzłów, które wysyłają dane telemetryczne do Application Insights zasobów w ramach tej subskrypcji. Dlatego jeśli masz pięć węzłów, które wysyłają dane codziennie, będziesz mieć przynależność do puli o pojemności 1 GB do wszystkich zasobów Application Insights w tej subskrypcji. Nie ma znaczenia, czy niektóre węzły wysyłają więcej danych niż inne węzły, ponieważ zawarte dane są współużytkowane przez wszystkie węzły. Jeśli w danym dniu Application Insights zasoby otrzymają więcej danych niż jest uwzględniona w dziennej alokacji danych dla tej subskrypcji, zostaną naliczone opłaty za użycie nadwyżkowe za GB danych. 
  * Dzienny limit danych jest obliczany jako liczba godzin w ciągu dnia (przy użyciu czasu UTC), przez który każdy węzeł wysyła dane telemetryczne podzielone przez 24 pomnożone przez 200 MB. Tak więc, jeśli masz cztery węzły, które wysyłają dane telemetryczne przez 15 godzin w ciągu dnia, to w przypadku tego dnia uwzględniane są (( &#215; 4 15)/24 &#215; ) 200 MB = 500 MB. W cenie 2,30 USD za GB za nadwyżkę danych opłata wynosi 1,15 USD, jeśli węzły wysyłają 1 GB danych dziennie.
  * Dzienna dieta dla warstwy węzła nie jest współdzielona z aplikacjami, dla których wybrano warstwę za GB. Nieużywany dodatek nie jest przenoszony od bieżącego dnia. 

### <a name="examples-of-how-to-determine-distinct-node-count"></a>Przykłady sposobu określania odrębnej liczby węzłów

| Scenariusz                               | Łączna liczba węzłów dziennych |
|:---------------------------------------|:----------------:|
| 1 aplikacja używająca 3 wystąpień Azure App Service i 1 serwera wirtualnego | 4 |
| 3 aplikacje działające na 2 maszynach wirtualnych; zasoby Application Insights dla tych aplikacji znajdują się w tej samej subskrypcji i w warstwie na węzeł | 2 | 
| 4 aplikacje, których zasoby usługi Application Insights znajdują się w tej samej subskrypcji; Każda aplikacja uruchamia 2 wystąpienia w godzinach od 16 godzin szczytu i 4 wystąpienia w ciągu 8 godzin szczytu | 13.33 | 
| Usługi w chmurze z 1 rolą proces roboczy i 1 rolą sieci Web, każdy uruchomiony 2 wystąpienia | 4 | 
| Klaster programu Azure Service Fabric w systemie 5 z systemem 50 mikrousług; Każda mikrousługa z uruchomioną 3 wystąpieniami | 5|

* Precyzyjne zliczanie węzłów zależy od tego, który Application Insights używany przez aplikację. 
  * W zestawie SDK w wersji 2,2 i nowszych zarówno [zestaw Application Insights Core SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) i [zestaw SDK sieci Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) są raportowane przez każdego hosta aplikacji jako węzeł. Przykłady to nazwa komputera dla serwerów fizycznych i hostów maszyn wirtualnych lub nazwa wystąpienia usług Cloud Services.  Jedyny wyjątek to aplikacja, która korzysta tylko z [platformy .NET Core](https://dotnet.github.io/) i zestawu SDK Application Insights Core. W takim przypadku tylko jeden węzeł jest raportowany dla wszystkich hostów, ponieważ nazwa hosta jest niedostępna. 
  * W przypadku wcześniejszych wersji zestawu SDK zestaw SDK [sieci Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/) działa jak nowsze wersje zestawu SDK, ale [podstawowy zestaw SDK](https://www.nuget.org/packages/Microsoft.ApplicationInsights/) raportuje tylko jeden węzeł, niezależnie od liczby hostów aplikacji. 
  * Jeśli aplikacja używa zestawu SDK do ustawiania **roleInstance** na wartość niestandardową, domyślnie ta sama wartość jest używana do określenia liczby węzłów. 
  * W przypadku korzystania z nowej wersji zestawu SDK z aplikacją uruchomioną z komputerów klienckich lub urządzeń przenośnych liczba węzłów może zwracać liczbę bardzo dużą (z powodu dużej liczby komputerów klienckich lub urządzeń przenośnych). 

## <a name="automation"></a>Automatyzacja

Można napisać skrypt do ustawienia warstwy cenowej za pomocą usługi Azure Resource Management. [Dowiedz się, jak to zrobić](powershell.md#price).


## <a name="next-steps"></a>Następne kroki

* [Próbkowanie](../../azure-monitor/app/sampling.md)

[api]: app-insights-api-custom-events-metrics.md
[apiproperties]: app-insights-api-custom-events-metrics.md#properties
[start]: ../../azure-monitor/app/app-insights-overview.md
[pricing]: https://azure.microsoft.com/pricing/details/application-insights/