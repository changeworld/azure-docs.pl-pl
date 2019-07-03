---
title: Tworzenie niestandardowych pulpitów nawigacyjnych w usłudze Azure Application Insights | Microsoft Docs
description: Samouczek dotyczący tworzenia niestandardowych pulpitów nawigacyjnych wskaźników KPI przy użyciu usługi Azure Application Insights.
keywords: ''
services: application-insights
author: lgayhardt
ms.author: lagayhar
ms.date: 07/3/2019
ms.service: application-insights
ms.custom: mvc
ms.topic: tutorial
manager: carmonm
ms.openlocfilehash: fa91933c2243cd1a2737f93f796314330b6cf5a0
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/03/2019
ms.locfileid: "67541507"
---
# <a name="create-custom-kpi-dashboards-using-azure-application-insights"></a>Tworzenie niestandardowych pulpitów nawigacyjnych wskaźników KPI przy użyciu usługi Azure Application Insights

W witrynie Azure Portal można utworzyć wiele pulpitów nawigacyjnych. Każdy z nich może zawierać kafelki z wizualizacją danych pochodzących z wielu zasobów platformy Azure znajdujących się w różnych subskrypcjach i grupach zasobów.  Można przypinać różne wykresy i widoki z usługi Azure Application Insights, tworząc niestandardowe pulpity nawigacyjne, które dostarczają pełny obraz kondycji i wydajności aplikacji. Ten samouczek przedstawia kroki tworzenia niestandardowego pulpitu nawigacyjnego, który zawiera różne typy danych i wizualizacji z usługi Azure Application Insights.  Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie niestandardowego pulpitu nawigacyjnego na platformie Azure
> * Dodawanie kafelka z galerii kafelków
> * Dodawanie standardowych metryk usługi Application Insights do pulpitu nawigacyjnego
> * Dodawanie niestandardowego wykresu metryk usługi Application Insights do pulpitu nawigacyjnego
> * Dodawanie wyników zapytania dzienniki (analiza) do pulpitu nawigacyjnego



## <a name="prerequisites"></a>Wymagania wstępne

W celu ukończenia tego samouczka:

- Wdróż aplikację .NET na platformie Azure i [włącz zestaw Application Insights SDK](../../azure-monitor/app/asp-net.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do witryny Azure Portal pod adresem [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-new-dashboard"></a>Tworzenie nowego pulpitu nawigacyjnego
Pojedynczy pulpit nawigacyjny może zawierać zasoby z wielu aplikacji, subskrypcji i grup zasobów.  Aby rozpocząć wykonywanie kroków tego samouczka, utwórz nowy pulpit nawigacyjny aplikacji.  

1. W okienku pulpitów nawigacyjnych wybierz pozycję **Nowy pulpit nawigacyjny**.

   ![Nowy pulpit nawigacyjny](media/tutorial-app-dashboards/1newdashboard.png)

1. Wpisz nazwę pulpitu nawigacyjnego.
1. Otwórz **galerię kafelków**, aby zobaczyć, jak wiele różnych kafelków możesz dodać do pulpitu nawigacyjnego.  Oprócz dodawania kafelków z galerii, możesz przypinać wykresy i inne widoki bezpośrednio z usługi Application Insights do pulpitu nawigacyjnego.
1. Znajdź kafelek **Markdown** i przeciągnij go na pulpit nawigacyjny.  Ten Kafelek pozwala dodawać tekst sformatowany w języku znaczników markdown, co jest idealnym rozwiązaniem w przypadku dodawania tekstu opisowego do pulpitu nawigacyjnego.
1. Dodaj tekst we właściwościach kafelka i zmień jego rozmiar na kanwie pulpitu nawigacyjnego.
    
    ![Edytowanie kafelka Markdown](media/tutorial-app-dashboards/2dashboard-text.png)

1. Kliknij pozycję **Zakończono dostosowywanie** w górnej części ekranu, aby wyjść z trybu dostosowywania kafelka.

## <a name="add-health-overview"></a>Dodawanie ogólnych informacji o kondycji
Pulpit nawigacyjny z statyczny tekst nie jest specjalnie ciekawy, dlatego dodamy teraz Kafelek z usługi Application Insights, aby wyświetlić informacje o aplikacji.  Kafelki usługi Application Insights można dodawać z galerii kafelków lub przypinać je bezpośrednio z ekranów usługi Application Insights.  Pozwala to konfigurować znane wykresy i widoki przed przypięciem ich do pulpitu nawigacyjnego.  Na początku dodamy przegląd standardowych informacji o kondycji aplikacji.  Nie wymaga on konfiguracji i udostępnia minimalne możliwości dostosowania na pulpicie nawigacyjnym.


1. Wybierz zasób usługi **Application Insights** na ekranie głównym.
2. W **Przegląd** okienku kliknij ikonę pinezki ![ikonę pinezki](media/tutorial-app-dashboards/pushpin.png) Dodawanie kafelka do ostatnio wyświetlanego pulpitu nawigacyjnego.  
 
3. W prawym górnym rogu zostanie wyświetlone powiadomienie, że kafelka został przypięty do pulpitu nawigacyjnego. Kliknij pozycję **Przypięto do pulpitu nawigacyjnego** w powiadomieniu, aby wrócić do pulpitu nawigacyjnego lub użyć okienka pulpitów nawigacyjnych.
4. Kafelek jest teraz dodany do pulpitu nawigacyjnego. Wybierz pozycję **Edytuj**, aby zmienić położenie kafelka. Kliknij go i przeciągnij w odpowiednie miejsce, a następnie kliknij pozycję **Zakończono dostosowywanie**. Teraz pulpit nawigacyjny zawiera kafelek z przydatnymi informacjami.

    ![Pulpit nawigacyjny z osią czasu przeglądu](media/tutorial-app-dashboards/4dashboard-edit.png)

## <a name="add-custom-metric-chart"></a>Dodawanie niestandardowego wykresu metryki
Panel **Metryki** umożliwia graficzne przedstawienie metryki zebranej przez usługę Application Insights w określonym czasie oraz udostępnia opcjonalne filtry i grupowanie.  Podobnie jak w przypadku innych elementów usługi Application Insights, można dodać ten wykres do pulpitu nawigacyjnego.  Wymaga to jednak wykonania pewnych czynności w zakresie dostosowania.

1. Wybierz zasób usługi **Application Insights** na ekranie głównym.
1. Wybierz pozycję **Metryki**.  
2. Ponieważ pusty wykres już istnieje, zostanie wyświetlony monit o dodanie metryki.  Dodaj metrykę do wykresu. Opcjonalnie dodaj filtr i grupowanie.  Poniższy przykład przedstawia liczbę żądań serwera, pogrupowanych według ich powodzenia.  Dzięki temu na bieżąco widać, które żądania zakończyły się powodzeniem, a które niepowodzeniem.

    ![Dodawanie metryki](media/tutorial-app-dashboards/metrics.png)

4. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego** po prawej stronie. Spowoduje to dodanie widoku do ostatnio wyświetlanego pulpitu nawigacyjnego.

3.  W prawym górnym rogu zostanie wyświetlone powiadomienie, że kafelka został przypięty do pulpitu nawigacyjnego. Kliknij pozycję **Przypięto do pulpitu nawigacyjnego** w powiadomieniu, aby wrócić do pulpitu nawigacyjnego lub użyć bloku pulpitów nawigacyjnych.

4. Kafelek jest teraz dodany do pulpitu nawigacyjnego. Wybierz pozycję **Edytuj**, aby zmienić położenie kafelka. Kliknij go i przeciągnij w odpowiednie miejsce, a następnie kliknij pozycję **Zakończono dostosowywanie**.

## <a name="add-logs-analytics-query"></a>Dodaj zapytanie o dzienniki (analiza)
Dzienniki szczegółowych informacji w usłudze Azure aplikacji (analiza) udostępnia zaawansowany język zapytań, który pozwala analizować wszystkie dane zebrane usługi Application Insights. Podobnie jak wykresy i inne widoki możesz dodać dane wyjściowe zapytań dzienników do pulpitu nawigacyjnego.

Ponieważ dzienników Insights platformy Azure — aplikacje (analiza) jest oddzielną usługą, musisz udostępnić pulpit nawigacyjny, aby dołączyć zapytanie dzienniki. Po udostępnieniu pulpitu nawigacyjnego platformy Azure możesz opublikować go jako zasób platformy Azure można udostępnić go innym użytkownikom i zasobom.  

1. W górnej części ekranu pulpitu nawigacyjnego kliknij pozycję **Udostępnij**.

    ![Publikowanie pulpitu nawigacyjnego](media/tutorial-app-dashboards/8dashboard-share.png)

2. Nie zmieniaj zawartości pola **Nazwa pulpitu nawigacyjnego**. Wybierz pozycję **Nazwa subskrypcji**, aby udostępnić pulpit nawigacyjny.  Kliknij przycisk **publikowania**.  Od tej chwili pulpit nawigacyjny jest dostępny dla innych usług i subskrypcji.  Opcjonalnie możesz zdefiniować listę użytkowników, którzy powinni mieć dostęp do pulpitu nawigacyjnego.
1. Wybierz zasób usługi **Application Insights** na ekranie głównym.
2. Kliknij przycisk **dzienniki (analiza)** po lewej stronie w obszarze monitorowania, aby otworzyć portal dzienniki (analiza).
3. Wpisz następujące zapytanie, które zwraca 10 najczęściej wyświetlanych stron oraz liczbę żądań dostępu do nich:

    ``` Kusto
    requests
    | summarize count() by name
    | sort by count_ desc
    | take 10
    ```

4. Kliknij pozycję **Uruchom**, aby zweryfikować wyniki zapytania.
5. Kliknij ikonę przypinania ![Ikona pinezki](media/tutorial-app-dashboards/pushpin.png) a następnie wybierz nazwę pulpitu nawigacyjnego. Przyczyna, ta opcja ma, możesz wybrać pulpit nawigacyjny, w przeciwieństwie do poprzednich kroków, której użyto ostatniego pulpitu nawigacyjnego jest, ponieważ konsola dzienniki (analiza) jest oddzielną usługą i wymaga dokonać wyboru spośród wszystkich udostępnionych pulpitów nawigacyjnych.

5. Zanim wrócisz do pulpitu nawigacyjnego, Dodaj kolejne zapytanie, ale tym razem ustaw je jako wykres tak aby było widać różne sposoby wizualizować zapytania dzienniki na pulpicie nawigacyjnym. Rozpocznij od następującego zapytania, które podsumowuje 10 operacji z największą liczbą wyjątków.

    ``` Kusto
    exceptions
    | summarize count() by operation_Name
    | sort by count_ desc
    | take 10
    ```

6. Wybierz pozycję **Wykres**, a następnie wybierz opcję **Pierścieniowy**, aby zwizualizować wyniki.

    ![Wykres dzienniki (analiza)](media/tutorial-app-dashboards/11querychart.png)

6. Kliknij ikonę przypinania ![Ikona pinezki](media/tutorial-app-dashboards/pushpin.png) w prawym górnym rogu, aby przypiąć wykres do pulpitu nawigacyjnego i tym razem wybierz link umożliwiający powrót do pulpitu nawigacyjnego.
4. Wyniki zapytań w wybranym formacie zostały dodane do pulpitu nawigacyjnego.  Przeciągnij poszczególne wykresy we właściwe miejsce, a następnie kliknij pozycję **Zakończono dostosowywanie**.
5. Wybierz ikonę ołówka ![Ikona ołówka](media/tutorial-app-dashboards/pencil.png) w ramach tytułów poszczególnych ciągowych opisowy tytuł.

5. Wybierz pozycję **Udostępnij**, aby opublikować ponownie zmiany na pulpicie nawigacyjnym, który teraz zawiera różne wykresy i wizualizacje z usługi Application Insights.


## <a name="next-steps"></a>Kolejne kroki
Teraz, gdy już wiesz, jak tworzyć niestandardowe pulpity nawigacyjne, zapoznaj się z pozostałą częścią dokumentacji usługi Application Insights, w tym analizą przypadku.

> [!div class="nextstepaction"]
> [Szczegółowa diagnostyka](../../azure-monitor/app/devops.md)
