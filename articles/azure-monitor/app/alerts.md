---
title: Ustawianie alertów w usłudze Azure Application Insights | Dokumentacja firmy Microsoft
description: Otrzymywanie powiadomień o wydłużają czas odpowiedzi, wyjątki oraz inne wydajności lub użycia zmian w aplikacji sieci web.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.reviewer: lagayhar
ms.assetid: f8ebde72-f819-4ba5-afa2-31dbd49509a5
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: mbullwin
ms.openlocfilehash: 8026576478b16b753ba960155c383ffec62c61ce
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469799"
---
# <a name="set-alerts-in-application-insights"></a>Ustawianie alertów w usłudze Application Insights
[Usługa Azure Application Insights] [ start] może generować alerty na zmiany w metrykach wydajności lub użycia aplikacji sieci web. 

Usługa Application Insights monitoruje aplikację na żywo na [różnymi platformami] [ platforms] ułatwiają diagnozowanie problemów z wydajnością i poznania wzorców użycia.

Istnieje wiele typów alertów:

* [**Alerty metryki** ](../../azure-monitor/platform/alerts-metric-overview.md) informujące o tym, gdy Metryka przekracza wartość progową przez pewien — na przykład czasy odpowiedzi, liczby wyjątków, użycie procesora CPU lub wyświetlenia stron.
* [**Alerty dzienników** ](../../azure-monitor/platform/alerts-unified-log.md) jest używane do opisywania alerty, gdy sygnał alertu opiera się na niestandardowe zapytanie Kusto.
* [**Testy sieci Web** ] [ availability] informujące o tym, gdy witryny jest niedostępna w Internecie lub odpowiada powoli. [Dowiedz się więcej][availability].
* [**Diagnostyka proaktywna** ](../../azure-monitor/app/proactive-diagnostics.md) są konfigurowane automatycznie Powiadomimy Cię o wydajności nietypowe wzorce.

## <a name="set-a-metric-alert"></a>Ustawianie alertu metryki
Otwórz kartę reguły alertu, a następnie użyj przycisku Dodaj.

![Na karcie reguły alertów wybierz pozycję Dodaj Alert. Ustaw aplikację jako zasób do mierzenia, podaj nazwę alertu, a następnie wybierz metrykę.](./media/alerts/01-set-metric.png)

* Ustaw zasób przed innymi właściwościami. **Wybierz zasób "(składniki)"** Jeśli chcesz ustawić alerty dotyczące metryk wydajności lub użycia.
* Nazwa która oferowanie alertu musi być unikatowa w obrębie grupy zasobów (nie tylko aplikację).
* Należy zachować ostrożność, należy pamiętać, jednostki, w których pojawi się prośba o podanie wartości progowej.
* Jeśli zaznaczysz pole "Adres E-mail właścicieli...", alerty są wysyłane za pośrednictwem poczty e-mail dla każdego, kto ma dostęp do tej grupy zasobów. Aby rozszerzyć ten zestaw osób, dodaj je do [grupy zasobów lub subskrypcji](../../azure-monitor/app/resources-roles-access-control.md) (nie zasobu).
* Jeśli określisz "Dodatkowe adresy e-mail", alerty są wysyłane do tych konkretnych osób lub grup (określa, czy zaznaczono pole "email właścicieli..."). 
* Ustaw [adres elementu webhook](../../azure-monitor/platform/alerts-webhooks.md) Jeśli skonfigurujesz aplikację internetową, która odpowiada na alerty. Jest on nazywany zarówno po aktywowaniu alertu, jak i kiedy zostanie on rozwiązany. (Ale należy pamiętać, że obecnie parametry zapytania nie są przekazywane jako właściwości elementu webhook).
* Możesz wyłączyć lub włączyć alert: wyświetlanie przycisków u góry.

*Przycisk Dodaj Alert nie jest widoczny.*

* Czy używasz konta organizacyjnego Możesz ustawić alerty, jeśli masz dostęp do tego zasobu aplikacji współautora lub właściciela. Spójrz na karcie kontrola dostępu. [Dowiedz się więcej na temat kontroli dostępu][roles].

> [!NOTE]
> W bloku alertów zostanie wyświetlony, że istnieje już zestaw alertu: [Diagnostyka proaktywna](../../azure-monitor/app/proactive-failure-diagnostics.md). Alert automatycznego monitoruje jednego częstość niepowodzeń żądań metryki, określonej. Chyba że zdecydujesz się wyłączanie alertu aktywnego, nie trzeba ustawiać własne alert częstość niepowodzeń żądań.
> 
> 

## <a name="see-your-alerts"></a>Wyświetlić alerty
Otrzymasz wiadomość e-mail, gdy stan alertu zmiany między nieaktywne i aktywne. 

Bieżący stan każdego alertu są wyświetlane na karcie reguły alertów.

Znajduje się podsumowanie, ostatnie działania w alertach listy rozwijanej:

![Alerty z listy rozwijanej](./media/alerts/010-alert-drop.png)

Historię zmian stanu znajduje się w dzienniku aktywności:

![Na karcie Przegląd kliknij pozycję Ustawienia, dzienniki inspekcji](./media/alerts/09-alerts.png)

## <a name="how-alerts-work"></a>Jak działają alerty
* Alert ma trzy stany: "Nigdy nie aktywowano", "Aktywowano" i "Rozwiązany". Aktywowano oznacza, że określony warunek był ma wartość true, został ostatnio oceniane.
* Powiadomienie jest generowany, gdy zmieni się stan alertu. (Jeśli warunek alertu zostało już wartość true, podczas tworzenia alertu, można otrzymać powiadomienie dopóki warunek spadnie wartość false.)
* Każde powiadomienie generuje wiadomość e-mail, jeśli zaznaczono pole wiadomości e-mail lub podane adresy e-mail. Można również przeglądać listy powiadomień.
* Alert jest oceniany na każdym razem, gdy nadejdzie metrykę, a w przeciwnym przypadku nie.
* Ocena zagregowane metryki w okresie poprzedzającym i porównuje go z wartością progową, aby określić nowy stan.
* Okresu, w którym możesz wybrać, określa interwał, przez który metryki są agregowane. Nie wpływa na jak często alert jest oceniany:, zależy od częstotliwości przybycia metryki.
* Jeśli żadne dane dla konkretnej metryki przez pewien czas, przerwa ma różne efekty na ocenę alertu i na wykresach w Eksploratorze metryk. W Eksploratorze metryk Jeśli żadne dane nie są widoczne przez czas dłuższy niż interwał próbkowania dla wykresu, wykres pokazuje wartość 0. Ale alert w oparciu o metryki tej samej nie jest ponownie oceniane, i stan alertów pozostaje bez zmian. 
  
    Po odebraniu ostatecznie danych, wykres skacze z powrotem do zera. Ocenia alertu na podstawie danych, które są dostępne na okres określony. Jeśli nowy punkt danych jest tylko jeden w okresie, agregacji jest oparte tylko na punktu danych.
* Alert można zmieniał kolor często między Stanami alertów i działa prawidłowo, nawet wtedy, gdy ustawisz długiego okresu. Może to nastąpić, jeśli wartość metryki unosi się wokół wartości progowej. Istnieje nie histerezy przez wartość progową: przejście do alertu odbywa się na tę samą wartość jak przejście do dobrej kondycji.

## <a name="what-are-good-alerts-to-set"></a>Co to są dobre alerty można ustawić?
To zależy od aplikacji. Na początek z najlepiej jest nie chcesz ustawiać zbyt wiele metryk. Poświęcić trochę czasu, analizując wykresów metryki w uruchomionej aplikacji, aby uzyskać pewne pojęcie dla jak będzie pracował normalnie. Pomaga to znaleźć sposoby zwiększenia wydajności. Następnie skonfiguruj alerty informujące o tym, kiedy metryki wychodzenia poza normalne strefy. 

Popularne alerty zawierają:

* [Metryki przeglądarki][client], szczególnie przeglądarki **stronie czasów ładowania**, są odpowiednie dla aplikacji sieci web. Jeśli strona ma wiele skryptów, należy szukać **wyjątków przeglądarki**. Aby uzyskać te metryki i alerty, należy skonfigurować [monitorowania strony sieci web][client].
* **Czas odpowiedzi serwera** po stronie serwera aplikacji sieci web. A także Konfigurowanie alertów, Zwracaj uwagę na tę metrykę, aby zobaczyć, jeśli zależy to nieproporcjonalnie z kursów żądania wysokiej: odmiany może wskazywać, że Twoja aplikacja działa brakuje zasobów. 
* **Wyjątki serwera** — aby je zobaczy, należy wykonać niektóre [dodatkowe ustawienia](../../azure-monitor/app/asp-net-exceptions.md).

Nie należy zapominać, że [aktywna współczynnik Diagnostyka błędów](../../azure-monitor/app/proactive-failure-diagnostics.md) automatycznie monitorować współczynnik, w którym aplikacja ma odpowiadać na żądania z kodami błędów.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Jak ustawić alert wyjątek przy użyciu wyszukiwania dziennika niestandardowego

W tej sekcji opiszemy sposób ustawiania alertu wyjątku na podstawie zapytania. W tym przykładzie załóżmy, że chcemy alert, gdy współczynnik zakończonych niepowodzeniem jest większa niż 10% w ciągu ostatnich 24 godzin.

1. Przejdź do zasobu usługi Application Insights w witrynie Azure portal.
2. Po lewej stronie, w obszarze skonfigurować kliknij na **alertu**.

    ![Po lewej stronie w obszarze skonfigurować możesz kliknąć alert](./media/alerts/1appinsightalert.png)

3. W górnej części karty alertu wybierz **Nowa reguła alertu**.

     ![W górnej części karty alertu kliknij przycisk Nowa reguła alertu](./media/alerts/2createalert.png)

4. Zasób powinien być automatycznie wybrana. Aby ustawić warunek, kliknij **Dodaj warunek**.

    ![Kliknij przycisk Dodaj warunek](./media/alerts/3addcondition.png)

5. Na karcie Konfigurowanie logiki sygnału, zaznacz **przeszukiwania dzienników niestandardowych**

    ![Kliknij przycisk Wyszukaj dziennika niestandardowego](./media/alerts/4customlogsearch.png)

6. Na karcie wyszukiwania dzienników niestandardowych wprowadź zapytanie w polu "Zapytania wyszukiwania". W tym przykładzie użyjemy poniżej zapytanie Kusto.
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
    > Procedurę można zastosować do innych typów alertów opartych na zapytaniach. Dowiedz się więcej o języku zapytania Kusto z tego [Kusto wprowadzenie doc](https://docs.microsoft.com/azure/kusto/concepts/) bądź [SQL do Kusto ściągawka](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. W obszarze "Alert logic" Wybierz, czy jest on oparty na liczbie wyników lub pomiar metryki. Następnie wybierz warunek (większa, równa mniej niż) i wartości progowej. Podczas zmieniania wartości te można zauważyć zmiany zdania Podgląd warunku. W tym przykładzie używamy "jest równe".

    ![W obszarze Alert logic, wybierając spośród opcji dostępnych na podstawie i warunek, a następnie wpisz wartości progowej](./media/alerts/6alertlogic.png)

8. W obszarze "Evaluated na podstawie" Ustaw okres i częstotliwości. Okres, w tym miejscu musi odpowiadać wartości, które umieściliśmy dla okresu w powyższym zapytaniu. Następnie kliknij przycisk **gotowe**.

    ![Ustaw okres i częstotliwość u dołu, a następnie kliknij przycisk Gotowe](./media/alerts/7evaluate.png)

9. Teraz widzimy warunek, który utworzyliśmy z szacowany koszt miesięczny. Poniżej w obszarze ["Grupy akcji"](../platform/action-groups.md) możesz utworzyć nową grupę lub wybierz istniejącą. Jeśli chcesz, możesz dostosować akcje.

    ![Kliknij Wybierz lub Utwórz przyciski w ramach grupy akcji](./media/alerts/8actiongroup.png)

10. Na koniec dodanie szczegółów dotyczących alertu (alertu z reguły nazwę, opis, ważność). Gdy wszystko będzie gotowe, kliknij przycisk **Utwórz regułę alertu** u dołu.

    ![W obszarze szczegółów alertu, wpisz nazwę reguły alertu, Napisz opis i wybierz ważność](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Jak anulować subskrypcję alertów klasycznych w wiadomości e-mail z powiadomieniami

Ta sekcja dotyczy **alerty klasyczne dostępności**, **alertów dotyczących metryk usługi Application Insights**, a **alerty dotyczące anomalii klasycznego błędów**.

Otrzymujesz wiadomości e-mail z powiadomieniami dla tych alertów klasycznych, jeśli dowolny z następujących czynności:

* Adres e-mail znajduje się w polu adresatów powiadomień e-mail, w ustawieniach reguły alertu.

* Opcja wysyłania powiadomień e-mail dla użytkowników posiadających pewnych ról dla subskrypcji jest aktywna i przechowywania odpowiedniej roli dla tej konkretnej subskrypcji platformy Azure.

![Zrzut ekranu z powiadomień o alertach](./media/alerts/alert-notification.png)

Aby lepiej kontrolować, co do bezpieczeństwa i prywatności ogólnie zaleca się jawnie określ adresatów powiadomienia dla alertów klasycznych w **adresatów wiadomości e-mail powiadomień** pola. Możliwość Powiadom wszystkich użytkowników, zawierający określone role towarzyszy zgodności z poprzednimi wersjami.

Aby otrzymywać powiadomienia e-mail generowane przez niektóre reguły alertu, należy usunąć swój adres e-mail, z **adresatów wiadomości e-mail powiadomień** pola.

Jeśli Twój adres e-mail nie ma jawnie, firma Microsoft zaleca wyłączenie opcji automatycznego powiadamiania wszyscy członkowie określonych ról i zamiast tego wyświetlić listę wszystkich wiadomości e-mail użytkowników, którzy będą otrzymywać powiadomienia dla tej reguły alertu w wiadomości e-mail z powiadomieniem pole adresatów.

## <a name="who-receives-the-classic-alert-notifications"></a>Kto otrzymuje powiadomienia o alertach (model klasyczny)?

W tej sekcji dotyczą alertów klasycznych i tylko pomoże Ci zoptymalizować swoje powiadomień o alertach, aby upewnić się, że tylko przez adresatów żądaną otrzymywać powiadomienia. Aby dowiedzieć się więcej o różnicach między [alertów klasycznych](../platform/alerts-classic.overview.md) i nowe alerty występują, zapoznaj się [artykuł z omówieniem alerty](../platform/alerts-overview.md). Aby kontrolować powiadomień o alertach w nowym środowisku alerty, należy użyć [grup akcji](../platform/action-groups.md).

* Firma Microsoft zaleca użycie określonych adresatów klasycznego powiadomień o alertach.

* Dla alertów dotyczących metryk usługi Application Insights, wszelkie (w tym metryki dostępności) **zbiorcze/grupę** pole wyboru opcji, jeśli włączona, wysyła do użytkowników z rolami właściciela, współautora lub czytelnika w ramach subskrypcji. W efekcie _wszystkich_ użytkowników z dostępem do subskrypcji zasobu usługi Application Insights znajdują się w zakresie i będą otrzymywać powiadomienia.

> [!NOTE]
> Jeśli obecnie używasz **zbiorcze/grupę** pole wyboru opcji i go wyłączyć, nie można przywrócić zmianę.

Jeśli chcesz powiadomić użytkowników na podstawie ich ról, należy użyć nowe alerty środowisko/niemal w czasie rzeczywistym. Za pomocą [grup akcji](../platform/action-groups.md), można skonfigurować powiadomienia e-mail do użytkowników z dowolną rolę właściciel/Współautor/reader (nie łączyć ze sobą jako pojedyncza opcja).

## <a name="automation"></a>Automatyzacja
* [Automatyzowanie konfigurowania alertów za pomocą programu PowerShell](../../azure-monitor/app/powershell-alerts.md)
* [Używanie elementów webhook w celu zautomatyzowania reagowanie na alerty](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Zobacz także
* [Testy dostępności sieci Web](../../azure-monitor/app/monitor-web-app-availability.md)
* [Automatyzowanie konfigurowania alertów](../../azure-monitor/app/powershell-alerts.md)
* [Aktywna diagnostyka](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

