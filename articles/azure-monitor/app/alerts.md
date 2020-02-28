---
title: Ustawianie alertów w usłudze Azure Application Insights
description: Otrzymuj powiadomienia o wolnym czasie odpowiedzi, wyjątkach i innych zmianach wydajności lub użycia w aplikacji sieci Web.
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 80759c94d7cc5b60b6e38a34b85fb64c3c18fd2e
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77666721"
---
# <a name="set-alerts-in-application-insights"></a>Ustawianie alertów w Application Insights

[Usługa Azure Application Insights][start] może wysyłać alerty o zmianach metryk wydajności lub użycia w aplikacji sieci Web. 

Application Insights monitoruje aktywną aplikację na wielu [platformach][platforms] , aby ułatwić diagnozowanie problemów z wydajnością i zrozumienie wzorców użycia.

Istnieje wiele typów alertów:

* [**Alerty metryk**](../../azure-monitor/platform/alerts-metric-overview.md) informują użytkownika, gdy Metryka przekroczy wartość progową dla pewnego okresu, na przykład czasy odpowiedzi, liczby wyjątków, użycie procesora CPU lub wyświetlenie strony.
* [**Alerty dzienników**](../../azure-monitor/platform/alerts-unified-log.md) są używane do opisywania alertów, w których sygnał alertu jest oparty na niestandardowej kwerendzie Kusto.
* [**Testy sieci Web**][availability] informują użytkownika, gdy witryna jest niedostępna w Internecie lub wolno reagować. [Dowiedz się więcej][availability].
* [**Proaktywna Diagnostyka**](../../azure-monitor/app/proactive-diagnostics.md) jest konfigurowana automatycznie w celu powiadomienia o nietypowych wzorcach wydajności.

## <a name="set-a-metric-alert"></a>Ustawianie alertu dotyczącego metryki

Otwórz kartę reguły alertów, a następnie użyj przycisku Dodaj.

![Na karcie reguły alertów wybierz pozycję Dodaj alert. Ustaw aplikację jako zasób do mierzenia, podaj nazwę alertu i wybierz metrykę.](./media/alerts/01-set-metric.png)

* Ustaw zasób przed innymi właściwościami. **Wybierz zasób "(składniki)"** , jeśli chcesz ustawić alerty dotyczące wydajności lub metryk użycia.
* Nazwa przydana do alertu musi być unikatowa w obrębie grupy zasobów (nie tylko aplikacja).
* Należy zwrócić uwagę na jednostki, w których zostanie wyświetlony monit o wprowadzenie wartości progowej.
* W przypadku zaznaczenia pola "właściciele poczty E-mail..." alerty są wysyłane pocztą e-mail do wszystkich użytkowników, którzy mają dostęp do tej grupy zasobów. Aby rozwinąć ten zestaw osób, należy dodać ich do [grupy zasobów lub subskrypcji](../../azure-monitor/app/resources-roles-access-control.md) (a nie zasobu).
* W przypadku określenia opcji "dodatkowe wiadomości e-mail" alerty są wysyłane do tych osób lub grup (bez względu na to, czy "właściciele poczty e-mail..." są Zaewidencjonuj. ). 
* Ustaw [adres elementu webhook](../../azure-monitor/platform/alerts-webhooks.md) , jeśli skonfigurowano aplikację sieci Web, która reaguje na alerty. Jest on wywoływany, gdy alert jest aktywowany i po jego rozwiązaniu. (Ale należy zauważyć, że w tym momencie parametry zapytania nie są przenoszone przez właściwości elementu webhook).
* Alert można wyłączyć lub włączyć: Zobacz przyciski w górnej części strony.

*Nie widzę przycisku Dodaj alert.*

* Czy używasz konta organizacyjnego? Możesz ustawić alerty, jeśli masz uprawnienia właściciela lub współautora do tego zasobu aplikacji. Spójrz na kartę Access Control. więcej informacji na [temat kontroli dostępu][roles].

> [!NOTE]
> W bloku alerty zobaczysz, że skonfigurowano już Alert: [Proactive Diagnostics](../../azure-monitor/app/proactive-failure-diagnostics.md). Alert automatyczny monitoruje jedną konkretną metrykę, częstotliwość niepowodzeń żądań. Jeśli nie zdecydujesz się wyłączyć aktywnego alertu, nie musisz ustawiać własnego alertu w przypadku niepowodzenia żądań.
> 
> 

## <a name="see-your-alerts"></a>Wyświetlanie alertów
Otrzymasz wiadomość e-mail, gdy alert zmieni stan między nieaktywnym i aktywnym. 

Bieżący stan każdego alertu jest wyświetlany na karcie reguły alertów.

Na liście rozwijanej alerty znajduje się podsumowanie ostatnich działań:

![Lista rozwijana alertów](./media/alerts/010-alert-drop.png)

Historia zmian stanu znajduje się w dzienniku aktywności:

![Na karcie Przegląd kliknij pozycję Ustawienia, dzienniki inspekcji.](./media/alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Jak działają alerty
* Alert ma trzy stany: "nigdy nie uaktywnione", "aktywowany" i "rozwiązany". Aktywowany oznacza, że określony warunek ma wartość true, kiedy został ostatnio oceniony.
* Po zmianie stanu alertu zostanie wygenerowane powiadomienie. (Jeśli warunek alertu był już prawdziwy podczas tworzenia alertu, możesz nie otrzymać powiadomienia, dopóki warunek nie zostanie spełniony.)
* Każde powiadomienie generuje wiadomość e-mail, jeśli zaznaczono pole wiadomości e-mail lub podano adresy e-mail. Możesz również sprawdzić listę rozwijaną powiadomienia.
* Alert jest oceniany za każdym razem, gdy Metryka dociera, ale nie w inny sposób.
* Ocena agreguje metrykę w poprzednim okresie, a następnie porównuje ją z progiem w celu określenia nowego stanu.
* Wybrany okres określa przedział czasu, w którym metryki są agregowane. Nie ma to wpływu na częstotliwość szacowania alertu: zależy od częstotliwości przybycia metryk.
* Jeśli dane nie dotarły do określonej metryki przez jakiś czas, różnica ma różny wpływ na ocenę alertu i na wykresach w Eksploratorze metryk. W Eksploratorze metryk, jeśli żadne dane nie są wyświetlane dłużej niż interwał próbkowania wykresu, wykres pokazuje wartość 0. Jednak alert oparty na tej samej metryce nie jest obliczany, a stan alertu pozostaje niezmieniony. 
  
    Po nadejściu danych, wykres wraca z powrotem do wartości innej niż zero. Alert jest obliczany na podstawie danych dostępnych dla określonego okresu. Jeśli nowy punkt danych jest jedynym dostępnym w danym okresie, agregacja opiera się tylko na tym punkcie danych.
* Alert może często migotać między Stanami alertu i prawidłowym, nawet jeśli ustawisz długi okres. Może się tak zdarzyć, gdy wartość metryki zostanie zatrzymana wokół progu. W wartości progowej nie ma żadnych hysteresis: przejście do alertu ma taką samą wartość, jak przejście w dobrej kondycji.

## <a name="what-are-good-alerts-to-set"></a>Jakie są dobre alerty do ustawienia?
Zależy to od aplikacji. Aby rozpocząć od, najlepiej nie ustawiać zbyt wielu metryk. Poświęcaj nieco czasu na przeszukiwanie wykresów metrycznych w czasie, gdy aplikacja jest uruchomiona, i zapoznaj się z zachowaniem normalnego działania. To rozwiązanie pomaga znaleźć sposoby ulepszania wydajności. Następnie skonfiguruj alerty, aby określić, kiedy metryki wykraczają poza normalną strefę. 

Popularne alerty obejmują:

* [Metryki przeglądarki][client], zwłaszcza **czasy ładowania stron**przeglądarki, są dobre dla aplikacji sieci Web. Jeśli strona zawiera wiele skryptów, należy szukać **wyjątków przeglądarki**. Aby można było uzyskać te metryki i alerty, należy skonfigurować [monitorowanie stron sieci Web][client].
* **Czas odpowiedzi serwera** po stronie serwera aplikacji sieci Web. Podobnie jak w przypadku konfigurowania alertów, należy śledzić tę metrykę, aby sprawdzić, czy jest ona różna proporcjonalnie do liczby żądań o wysokim stopniu: odmiana może wskazywać na brak zasobów w aplikacji. 
* **Wyjątki serwera** — aby je wyświetlić, należy wykonać [dodatkowe czynności konfiguracyjne](../../azure-monitor/app/asp-net-exceptions.md).

Nie zapomnij, że [usługa automatycznej częstotliwości niepowodzeń diagnostyki](../../azure-monitor/app/proactive-failure-diagnostics.md) automatycznie monitoruje szybkość, z jaką aplikacja reaguje na żądania z kodem błędu.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Jak ustawić alert dotyczący wyjątku przy użyciu wyszukiwania w dziennikach niestandardowych

W tej sekcji opisano sposób ustawiania alertu dotyczącego wyjątku opartego na zapytaniu. Na potrzeby tego przykładu Załóżmy, że chcemy, aby alert, gdy Częstotliwość powodzeń była większa niż 10% w ciągu ostatnich 24 godzin.

1. Przejdź do zasobu usługi Application Insights w Azure Portal.
2. Po lewej stronie w obszarze Konfiguruj kliknij **alert**.

    ![Po lewej stronie w obszarze Konfiguruj alert kliknięcia](./media/alerts/1appinsightalert.png)

3. W górnej części karty alert wybierz pozycję **Nowa reguła alertu**.

     ![W górnej części karty Alert kliknij pozycję Nowa reguła alertu.](./media/alerts/2createalert.png)

4. Zasób powinien być wybrany jako zaznaczony. Aby ustawić warunek, kliknij przycisk **Dodaj warunek**.

    ![Kliknij pozycję Dodaj warunek](./media/alerts/3addcondition.png)

5. Na karcie Konfigurowanie logiki sygnału wybierz opcję **Wyszukiwanie w dzienniku niestandardowym**

    ![Kliknij pozycję Wyszukiwanie w dzienniku niestandardowym](./media/alerts/4customlogsearch.png)

6. Na karcie wyszukiwanie w dzienniku niestandardowym wprowadź swoje zapytanie w polu "wyszukiwanie zapytania". W tym przykładzie użyjemy poniższego zapytania Kusto.
    ```kusto
    let percentthreshold = 10;
    let period = 24h;
    requests
    | where timestamp >ago(period)
    | summarize requestsCount = sum(itemCount)
    | project requestsCount, exceptionsCount = toscalar(exceptions | where timestamp >ago(period) | summarize sum(itemCount))
    | extend exceptionsRate = toreal(exceptionsCount)/toreal(requestsCount) * 100
    | where exceptionsRate > percentthreshold

    ```

    ![Wpisz zapytanie w polu zapytania wyszukiwania](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > Te kroki można również zastosować do innych typów alertów opartych na zapytaniach. Więcej informacji na temat języka zapytań Kusto można znaleźć w arkuszu [wprowadzenie](https://docs.microsoft.com/azure/kusto/concepts/) do tego Kusto lub ten [kod SQL do Kusto Ściągawka](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. W obszarze "logika alertu" Wybierz, czy jest on oparty na liczbie wyników, czy pomiarze metryki. Następnie wybierz warunek (większe niż, równe, mniejsze niż) i próg. Podczas zmieniania tych wartości można zauważyć, że zmieni się zdanie w wersji zapoznawczej. W tym przykładzie używamy "równej".

    ![W obszarze logika alertu wybierz spośród opcji dostępnych na podstawie warunku, a następnie wpisz próg](./media/alerts/6alertlogic.png)

8. W obszarze "obliczone na podstawie wartości" Ustaw okres i częstotliwość. Ten okres musi być zgodny z wartością, która została wprowadzona dla okresu w powyższym zapytaniu. Następnie kliknij przycisk **gotowe**.

    ![Ustaw wartość opcji okres i częstotliwość u dołu, a następnie kliknij pozycję Gotowe.](./media/alerts/7evaluate.png)

9. Zobaczymy teraz utworzony przez nas warunek szacunkowy koszt miesięczny. Poniżej w obszarze ["grupy akcji"](../platform/action-groups.md) można utworzyć nową grupę lub wybrać istniejącą. Jeśli chcesz, możesz dostosować akcje.

    ![Kliknij przycisk Wybierz lub Utwórz w obszarze Grupa akcji.](./media/alerts/8actiongroup.png)

10. Na koniec Dodaj szczegóły alertu (Nazwa reguły alertu, opis, ważność). Gdy skończysz, kliknij przycisk **Utwórz regułę alertu** u dołu.

    ![W obszarze Szczegóły alertu wpisz nazwę reguły alertu, napisz opis i wybierz ważność](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Jak anulować subskrypcję powiadomień e-mail z alertami klasycznymi

Ta sekcja ma zastosowanie do **klasycznych**alertów dostępności, **alertów metryk klasycznych Application Insights**oraz do **klasycznych alertów o błędach**.

Powiadomienia e-mail dotyczące tych klasycznych alertów są wysyłane w przypadku następujących warunków:

* Twój adres e-mail jest wyświetlany w polu adresaci wiadomości e-mail w obszarze Ustawienia reguły alertu.

* Opcja wysyłania powiadomień e-mail do użytkowników przechowujących pewne role dla subskrypcji została aktywowana i masz odpowiednią rolę dla danej subskrypcji platformy Azure.

![Zrzut ekranu powiadomień o alertach](./media/alerts/alert-notification.png)

Aby lepiej kontrolować zabezpieczenia i prywatność, zwykle zalecamy, aby jawnie określić adresatów powiadomień dla alertów klasycznych w polu **Adresaci wiadomości e-mail z powiadomieniem** . Opcja powiadamiania wszystkich użytkowników przechowujących pewne role jest zapewniana w celu zapewnienia zgodności z poprzednimi wersjami.

Aby anulować subskrypcję powiadomień e-mail wygenerowanych przez określoną regułę alertu, Usuń adres e-mail z pola **adresat wiadomości e-mail z powiadomieniem** .

Jeśli Twój adres e-mail nie znajduje się na liście jawnie, zalecamy wyłączenie opcji automatycznego powiadamiania wszystkich członków niektórych ról, a zamiast tego lista wszystkich wiadomości e-mail użytkowników, którzy muszą otrzymać powiadomienia dla tej reguły alertu w wiadomości e-mail z powiadomieniem. pole adresatów.

## <a name="who-receives-the-classic-alert-notifications"></a>Kto otrzymuje powiadomienia o alertach (klasyczny)?

Ta sekcja ma zastosowanie tylko do klasycznych alertów i pomoże zoptymalizować swoje powiadomienia o alertach, aby upewnić się, że tylko żądani adresaci otrzymają powiadomienia. Aby dowiedzieć się więcej o różnicach między [klasycznymi alertami](../platform/alerts-classic.overview.md) i nowym działaniem alertów, zapoznaj się z [artykułem przegląd alertów](../platform/alerts-overview.md). Aby kontrolować powiadamianie o alertach w nowym środowisku alertów, użyj [grup akcji](../platform/action-groups.md).

* Zalecamy użycie określonych odbiorców w przypadku klasycznych powiadomień o alertach.

* W przypadku alertów dotyczących dowolnych metryk Application Insights (w tym metryk dostępności) opcja pola wyboru **Zbiorcza/Grupa** , jeśli jest włączona, wysyła do użytkowników z rolami właściciela, współautora lub czytelnika w ramach subskrypcji. W efekcie _Wszyscy_ użytkownicy z dostępem do subskrypcji, w której znajduje się zasób Application Insights, znajdują się w zakresie i będą otrzymywać powiadomienia.

> [!NOTE]
> Jeśli obecnie używasz opcji **zbiorczych/grupowych** pól wyboru i go wyłączysz, nie będzie można przywrócić zmiany.

Jeśli musisz powiadomić użytkowników na podstawie ich ról, Użyj nowego środowiska alertu/alertów w czasie rzeczywistym. Za pomocą [grup akcji](../platform/action-groups.md)można skonfigurować powiadomienia e-mail dla użytkowników z dowolnymi rolami współautor/właściciela/czytnika (nie razem ze sobą jako pojedynczą opcją).

## <a name="automation"></a>Automatyzacja
* [Automatyzowanie konfigurowania alertów za pomocą programu PowerShell](../../azure-monitor/app/powershell-alerts.md)
* [Używanie elementów webhook do automatyzowania reagowania na alerty](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Zobacz też
* [Testy sieci Web dostępności](../../azure-monitor/app/monitor-web-app-availability.md)
* [Automatyzowanie konfigurowania alertów](../../azure-monitor/app/powershell-alerts.md)
* [Proaktywna Diagnostyka](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

