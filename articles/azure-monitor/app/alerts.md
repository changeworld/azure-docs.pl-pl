---
title: Ustawianie alertów w usłudze Azure Application Insights
description: Otrzymuj powiadomienia o powolnym czasie reakcji, wyjątkach i innych zmianach wydajności lub użycia w aplikacji sieci web.
ms.topic: conceptual
ms.date: 01/23/2019
ms.reviewer: lagayhar
ms.subservice: alerts
ms.openlocfilehash: 28fd59556a586b85a6d3caf188d9e02c11d31e3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295075"
---
# <a name="set-alerts-in-application-insights"></a>Ustawianie alertów w usłudze Application Insights

[Usługa Azure Application Insights][start] może ostrzegać o zmianach w metrykach wydajności lub użycia w aplikacji sieci web. 

Usługa Application Insights monitoruje twoją aplikację na żywo na [wielu różnych platformach,][platforms] aby ułatwić diagnozowanie problemów z wydajnością i zrozumienie wzorców użycia.

Istnieje wiele typów alertów:

* [**Alerty metryki**](../../azure-monitor/platform/alerts-metric-overview.md) informują, gdy metryka przekroczy wartość progową dla niektórych okresów — takie jak czasy odpowiedzi, liczba wyjątków, użycie procesora CPU lub widoki strony.
* [**Alerty dziennika**](../../azure-monitor/platform/alerts-unified-log.md) służy do opisywania alertów, gdzie sygnał alertu jest oparty na niestandardowej kwerendzie Kusto.
* [**Testy sieci Web**][availability] informują, gdy witryna jest niedostępna w Internecie lub odpowiada powoli. [Dowiedz się więcej][availability].
* [**Proaktywna diagnostyka**](../../azure-monitor/app/proactive-diagnostics.md) jest konfigurowana automatycznie w celu powiadamiania o nietypowych wzorcach wydajności.

## <a name="how-to-set-an-exception-alert-using-custom-log-search"></a>Jak ustawić alert o wyjątku przy użyciu niestandardowego wyszukiwania dziennika

W tej sekcji przejdziemy przez sposób ustawiania alertu wyjątku opartego na kwerendzie. W tym przykładzie załóżmy, że chcemy alert, gdy wskaźnik nie powiodło się jest większa niż 10% w ciągu ostatnich 24 godzin.

1. Przejdź do zasobu usługi Application Insight w witrynie Azure portal.
2. Po lewej stronie, w obszarze Konfiguruj kliknij **Alert**.

    ![Po lewej stronie w obszarze Konfigurowanie alertu o kliknięciu](./media/alerts/1appinsightalert.png)

3. U góry karty alertu wybierz pozycję **Nowa reguła alertu**.

     ![U góry karty alertu kliknij nową regułę alertu](./media/alerts/2createalert.png)

4. Zasób powinien być wybierany automatycznie. Aby ustawić warunek, kliknij przycisk **Dodaj warunek**.

    ![Kliknij warunek dodaj](./media/alerts/3addcondition.png)

5. Na karcie Konfigurowanie logiki sygnału wybierz **pozycję Niestandardowe wyszukiwanie dzienników**

    ![Kliknij niestandardowe wyszukiwanie w dzienniku](./media/alerts/4customlogsearch.png)

6. Na karcie niestandardowego wyszukiwania dzienników wprowadź zapytanie w polu "Zapytanie wyszukiwania". W tym przykładzie użyjemy poniższej kwerendy Kusto.
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

    ![Wpisz kwerendę w polu kwerendy wyszukiwania](./media/alerts/5searchquery.png)
    
    > [!NOTE]
    > Można również zastosować te kroki do innych typów alertów opartych na kwerendach. Możesz dowiedzieć się więcej o języku zapytania Kusto z tego [Kusto wprowadzenie doc](https://docs.microsoft.com/azure/kusto/concepts/) lub tego [SQL do Kusto ściągawki](https://docs.microsoft.com/azure/kusto/query/sqlcheatsheet)

7. W obszarze "Logika alertu" wybierz, czy jest ona oparta na liczbie wyników, czy na pomiarze metryki. Następnie wybierz warunek (większy niż, równy, mniejszy niż) i próg. Podczas zmiany tych wartości można zauważyć zmiany zdania podglądu warunku. W tym przykładzie używamy "równa".

    ![W obszarze Logika alertu wybierz jedną z opcji dostępnych na podstawie i warunek, a następnie wpisz próg](./media/alerts/6alertlogic.png)

8. W obszarze "Oceniane na podstawie" ustaw okres i częstotliwość. Kropka w tym miejscu musi odpowiadać wartości, którą umieściliśmy dla okresu w powyższej kwerendzie. Następnie kliknij **przycisk Gotowe**.

    ![Ustaw kropkę i częstotliwość na dole, a następnie kliknij gotowe](./media/alerts/7evaluate.png)

9. Teraz widzimy warunek, który stworzyliśmy z szacowanym miesięcznym kosztem. Poniżej w [obszarze "Grupy akcji"](../platform/action-groups.md) możesz utworzyć nową grupę lub wybrać istniejącą. Jeśli chcesz, możesz dostosować akcje.

    ![kliknij na wybierz lub utwórz przyciski w grupie akcji](./media/alerts/8actiongroup.png)

10. Na koniec dodaj szczegóły alertu (nazwa reguły alertu, opis, ważność). Po zakończeniu kliknij pozycję **Utwórz regułę alertu** u dołu.

    ![W obszarze Szczegóły alertu wpisz nazwę reguły alertu, napisz opis i wybierz ważność](./media/alerts/9alertdetails.png)

## <a name="how-to-unsubscribe-from-classic-alert-e-mail-notifications"></a>Jak anulować subskrypcję klasycznych powiadomień e-mail z alertami

Ta sekcja dotyczy **klasycznych alertów o dostępności, klasycznych** **alertów metryk usługi Application Insights**oraz **alertów o klasycznych anomaliach błędów.**

Otrzymujesz powiadomienia e-mail dla tych klasycznych alertów, jeśli ma zastosowanie dowolna z następujących sytuacji:

* Twój adres e-mail jest wyświetlany w polu Adresaci wiadomości e-mail powiadomień w ustawieniach reguły alertu.

* Opcja wysyłania powiadomień e-mail do użytkowników posiadających określone role dla subskrypcji jest aktywowana i pełnisz odpowiednią rolę dla tej konkretnej subskrypcji platformy Azure.

![Zrzut ekranu powiadomienia o alertach](./media/alerts/alert-notification.png)

Aby lepiej kontrolować bezpieczeństwo i prywatność, zazwyczaj zalecamy jawne określenie adresatów powiadomień dla klasycznych alertów w polu **Adresaci wiadomości e-mail z powiadomieniem.** Opcja powiadamiania wszystkich użytkowników posiadających określone role jest dostępna dla zgodności z powrotem.

Aby anulować subskrypcję powiadomień e-mail generowanych przez określoną regułę alertu, usuń swój adres e-mail z pola **Adresaci wiadomości e-mail z powiadomieniem.**

Jeśli Twój adres e-mail nie jest wymieniony jawnie, zalecamy wyłączenie opcji automatycznego powiadamiania wszystkich członków niektórych ról, a zamiast tego wyświetlenie listy wszystkich wiadomości e-mail użytkowników, którzy muszą otrzymywać powiadomienia o tej regule alertów w wiadomości e-mail z powiadomieniem. adresatów.

## <a name="who-receives-the-classic-alert-notifications"></a>Kto otrzymuje (klasyczne) powiadomienia o alertach?

Ta sekcja dotyczy tylko alertów klasycznych i pomoże ci zoptymalizować powiadomienia o alertach, aby upewnić się, że tylko pożądani odbiorcy otrzymują powiadomienia. Aby dowiedzieć się więcej o różnicy między [klasycznymi alertami](../platform/alerts-classic.overview.md) a nowym działaniem alertów, zapoznaj się z [omówieniem alertów w artykule](../platform/alerts-overview.md). Aby kontrolować powiadomienia o alertach w nowym obszarze alertów, użyj [grup akcji](../platform/action-groups.md).

* Zalecamy używanie określonych adresatów do klasycznych powiadomień o alertach.

* W przypadku alertów dotyczących dowolnych metryk usługi Application Insights (w tym metryk dostępności) opcja pola wyboru **zbiorczo/grupowa,** jeśli jest włączona, wysyła do użytkowników z rolami właściciela, współautora lub czytelnika w subskrypcji. W efekcie _wszyscy_ użytkownicy z dostępem do subskrypcji zasobów usługi Application Insights są w zakresie i będą otrzymywać powiadomienia.

> [!NOTE]
> Jeśli obecnie używasz opcji pola wyboru **zbiorczo/grupowego** i wyłączysz ją, nie będzie można przywrócić zmiany.

Użyj nowego środowiska alertów/alertów w czasie zbliżonym do rzeczywistego, jeśli chcesz powiadomić użytkowników na podstawie ich ról. W [przypadku grup akcji](../platform/action-groups.md)można skonfigurować powiadomienia e-mail dla użytkowników z dowolną rolą współautora/właściciela/czytelnika (nie łączoną razem jako pojedyncza opcja).

## <a name="automation"></a>Automatyzacja
* [Automatyzacja alertów za pomocą programu PowerShell](../../azure-monitor/app/powershell-alerts.md)
* [Automatyzowanie reagowania na alerty za pomocą elementów webhook](../../azure-monitor/platform/alerts-webhooks.md)

## <a name="see-also"></a>Zobacz też
* [Testy dostępności sieci Web](../../azure-monitor/app/monitor-web-app-availability.md)
* [Automatyzacja konfigurowania alertów](../../azure-monitor/app/powershell-alerts.md)
* [Aktywna diagnostyka](../../azure-monitor/app/proactive-diagnostics.md) 

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[client]: ../../azure-monitor/app/javascript.md
[platforms]: ../../azure-monitor/app/platforms.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
[start]: ../../azure-monitor/app/app-insights-overview.md

