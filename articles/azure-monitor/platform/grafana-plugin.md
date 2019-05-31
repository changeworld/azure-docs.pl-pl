---
title: Monitorowanie usług platformy Azure i aplikacji przy użyciu narzędzia Grafana
description: Dane trasy usługi Azure Monitor i Application Insights aby można było wyświetlić Grafana.
services: azure-monitor
keywords: ''
author: rboucher
ms.author: robb
ms.date: 11/06/2017
ms.topic: conceptual
ms.service: azure-monitor
ms.subservice: ''
ms.openlocfilehash: e9a20aba84e79e87f84d63e4bdae3ba1aac062f5
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66387187"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Monitorowanie usług platformy Azure w narzędzia Grafana
Teraz możesz monitorować usługi systemu Azure i aplikacji w [Grafana](https://grafana.com/) przy użyciu [wtyczki źródła danych usługi Azure Monitor](https://grafana.com/plugins/grafana-azure-monitor-datasource). Wtyczka zbiera dane dotyczące wydajności aplikacji zbieranych przez usługi Azure Monitor, łącznie z różnych dzienników i metryk. Następnie można wyświetlić te dane na pulpicie nawigacyjnym Grafana.

Wtyczka jest obecnie dostępna w wersji zapoznawczej.

Wykonaj następujące kroki, aby skonfigurować serwer Grafana i tworzyć pulpity nawigacyjne na potrzeby metryk i dzienników z usługi Azure Monitor.

## <a name="set-up-a-grafana-server"></a>Konfigurowanie serwera narzędzia Grafana

### <a name="set-up-grafana-locally"></a>Konfigurowanie narzędzia Grafana lokalnie
Aby skonfigurować serwer lokalny Grafana [pobrać i zainstalować narzędzia Grafana w środowisku lokalnym](https://grafana.com/grafana/download). Aby korzystać z integracji usługi Azure Monitor wtyczki, należy zainstalować narzędzia Grafana w wersji 5.3 lub nowszej.

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Konfigurowanie narzędzia Grafana na platformie Azure za pośrednictwem portalu Azure Marketplace
1. Przejdź do portalu Azure Marketplace, a następnie wybierz Grafana przez Grafana Labs.

2. Podaj nazwy i szczegóły. Utwórz nową grupę zasobów. Zachowaj informacje o wartości, które możesz wybrać nazwę maszyny Wirtualnej użytkownika, hasło maszyny Wirtualnej i hasło administratora serwera Grafana.  

3. Wybierz rozmiar maszyny Wirtualnej i konta magazynu.

4. Skonfiguruj ustawienia konfiguracji sieci.

5. Wyświetlić podsumowanie, a następnie wybierz pozycję **Utwórz** po zaakceptowaniu warunków użytkowania.

6. Po zakończeniu wdrożenia wybierz **przejdź do grupy zasobów**. Zobaczysz listę nowo utworzonych zasobów.

    ![Obiekty grupy zasobów platformy Grafana](media/grafana-plugin/grafana1.png)

    Jeśli zostanie wybrana grupa zabezpieczeń sieci (*grafana-nsg* w tym przypadku), możesz zobaczyć, czy dostęp do narzędzia Grafana serwera jest używany port 3000.

7. Uzyskaj publiczny adres IP serwera Grafana — wróć do listy zasobów, a następnie wybierz pozycję **publiczny adres IP**.

## <a name="sign-in-to-grafana"></a>Zaloguj się do narzędzia Grafana

1. Za pomocą adresu IP serwera, otwórz stronę logowania w *http://\<adresu IP\>: 3000* lub  *\<DNSName >\:3000* w przeglądarce. 3000 jest domyślny port, należy pamiętać, że być może wybrano innego portu podczas instalacji. Powinieneś widzieć stronę logowania dla serwera platformy Grafana tworzonej.

    ![Ekran logowania narzędzia Grafana](./media/grafana-plugin/grafana-login-screen.png)

2. Zaloguj się przy użyciu nazwy użytkownika *administratora* i Grafana hasło administratora serwera, została utworzona wcześniej. Jeśli używasz lokalnej instalacji domyślnego hasła będzie *administratora*, i będzie można żądać Zmień ją na potrzeby pierwszego logowania.

## <a name="configure-data-source-plugin"></a>Skonfiguruj dodatek źródła danych

Po pomyślnym zalogowaniu powinien pojawić się, że dodatek źródła danych usługi Azure Monitor jest już dołączona.

![Grafana zawiera wtyczki usługi Azure Monitor](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. Wybierz **Dodaj źródło danych** Aby dodać i skonfigurować źródło danych usługi Azure Monitor.

2. Wybierz nazwę dla źródła danych, a następnie wybierz pozycję **usługi Azure Monitor** jako typ z listy rozwijanej.

3. Tworzenie jednostki usługi — Grafana używa nazwy głównej usługi Azure Active Directory, aby nawiązać połączenie z interfejsów API usługi Azure Monitor i zbieranie danych. Należy utworzyć lub użyć istniejącej nazwy głównej usługi do zarządzania dostępem do zasobów platformy Azure.
    * Zobacz [w instrukcjach](../../azure-resource-manager/resource-group-create-service-principal-portal.md) do utworzenia jednostki usługi. Skopiuj i Zapisz dzierżawy, identyfikator (identyfikator katalogu), identyfikator klienta (identyfikator aplikacji) oraz klucz tajny klienta (wartość klucza aplikacji).
    * Zobacz [przypisywanie aplikacji do roli](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) do przypisywania roli Czytelnik do aplikacji Azure Active Directory w ramach subskrypcji, grupy zasobów lub zasobu mają być monitorowane. 
    Interfejsu API programu Log Analytics wymaga [roli Czytelnik usługi Log Analytics](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader), który obejmuje uprawnienia roli Czytelnik i dodaje do niego.

4. Podaj szczegóły połączenia z interfejsami API, czy chcesz użyć. Możesz połączyć wszystkie lub niektóre z nich. 
    * Jeśli łączysz się metryk i dzienników w usłudze Azure Monitor możesz ponownie użyć tych samych poświadczeń, wybierając **szczegółów tego samego interfejsu API usługi Azure Monitor**.
    * Podczas konfigurowania wtyczki, można wskazać, jaka chmura Azure chcesz wtyczki do monitora (publiczne, dla administracji USA, Azure (Niemcy) lub chińska wersja platformy Azure).
    * Jeśli korzystasz z usługi Application Insights, można także dodać interfejs API usługi Application Insights oraz Identyfikatora aplikacji, aby zbieranie metryk usługi Application Insights na podstawie. Aby uzyskać więcej informacji, zobacz [uzyskiwanie Twojego klucza interfejsu API i identyfikator aplikacji](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

        > [!NOTE]
        > Niektóre pola źródła danych są nazwane w różny sposób niż ich skorelowany ustawień platformy Azure:
        > * Identyfikator dzierżawy jest identyfikator katalogu platformy Azure
        > * Identyfikator klienta jest identyfikator aplikacji Active Directory Azure
        > * Klucz tajny klienta jest wartość klucza aplikacji usługi Azure Active Directory

5. Jeśli korzystasz z usługi Application Insights, można także dodać interfejs API usługi Application Insights oraz Identyfikatora aplikacji, aby zbieranie metryk usługi Application Insights na podstawie. Aby uzyskać więcej informacji, zobacz [uzyskiwanie Twojego klucza interfejsu API i identyfikator aplikacji](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

6. Wybierz **Zapisz**, i Grafana przetestuje poświadczeń dla każdego interfejsu API. Powinien zostać wyświetlony komunikat podobny do poniższego.  
    ![Zatwierdzone konfiguracji źródła danych narzędzia Grafana](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>Tworzenie pulpitu nawigacyjnego do narzędzia Grafana

1. Przejdź do strony głównej platformy Grafana, a następnie wybierz pozycję **nowy pulpit nawigacyjny**.

2. Nowy pulpit nawigacyjny, wybierz **wykresu**. Możesz wypróbować inne opcje tworzenia wykresów, ale w tym artykule wykorzystano *wykres* jako przykład.

3. Pusty wykres wyświetlane na pulpicie nawigacyjnym. Kliknij tytuł panelu i wybierz pozycję **Edytuj** o podanie szczegółowych danych, do wykreślenia na tym wykresie wykresu.
    ![Nowy wykres narzędzia Grafana](./media/grafana-plugin/grafana-new-graph-dark.png)

4. Wybierz źródło danych usługi Azure Monitor, które zostały skonfigurowane.
   * Wybierz metryki — zbierania usługi Azure Monitor **usługi Azure Monitor** na liście rozwijanej usługi. Lista pokazuje selektory w górę, którym można wybrać zasoby i metrykę do monitorowania na tym wykresie. Aby zbierać metryki z maszyny Wirtualnej, należy użyć przestrzeni nazw **Microsoft.Compute/VirtualMachines**. Po wybraniu maszyn wirtualnych i metryk, możesz rozpocząć wyświetlanie swoje dane na pulpicie nawigacyjnym.
     ![Grafana konfiguracji programu graph dla usługi Azure Monitor](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
   * Zbieranie usługi Azure Monitor możesz rejestrować dane — wybieranie **usługi Azure Log Analytics** na liście rozwijanej usługi. Wybierz obszar roboczy, który chcesz zbadać i Ustaw tekst zapytania. W tym miejscu można skopiować dowolne zapytanie dziennika istniejącego konta lub utworzyć nową. Podczas wpisywania w zapytaniu funkcji IntelliSense będą wyświetlane, a także sugerują opcje Autouzupełniania. Wybierz typ wizualizacji **czas serii** **tabeli**, i uruchom zapytanie.
    
     > [!NOTE]
     >
     > Domyślne zapytanie dostarczane za pomocą wtyczki wykorzystuje dwa makra: "__interval $__timeFilter() i $. 
     > Te makra umożliwiają Grafana dynamicznie obliczyć zakres czasu i ziarno czasu, po przejściu na części wykresu. Można usunąć tych makr i użyj filtru (czas standardowy), takie jak *TimeGenerated > ago(1h)* , ale ta oznacza, że wykres będzie obsługuje powiększenia w funkcji.
    
     ![Grafana konfiguracji programu graph dla usługi Azure Log Analytics](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. Poniżej przedstawiono dwa wykresy na prostym pulpicie nawigacyjnym. Po lewej stronie zawiera procent użycia procesora CPU, dwie maszyny wirtualne. Wykres po prawej stronie pokazuje transakcji na koncie usługi Azure Storage, podzielone według typu transakcji interfejsu API.
    ![Przykładowe wykresy dwa narzędzia Grafana](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Opcjonalnie: Monitoruj metryki niestandardowe w tym samym serwerze narzędzia Grafana

Można także zainstalować Telegraf i InfluxDB do zbierania i wykresu metryki niestandardowe i oparte na agentach tego samego wystąpienia Grafana. Istnieje wiele wtyczek źródła danych korzystające ze sobą te metryki na pulpicie nawigacyjnym.

Możesz także ponownie użyć tej konfiguracji, aby uwzględnić metryki z serwera Prometheus. Użyj wtyczki źródła danych Prometheus w galerii wtyczki Grafana firmy.

Poniżej przedstawiono wartościowa dokumentacja ułatwiająca artykuły dotyczące sposobu używania Telegraf, InfluxDB, Prometheus i platformy Docker
 - [Jak monitorować metryki systemowe z stosem ZNACZNIKÓW w systemie Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Rozwiązanie monitorowania dla hostów platformy Docker, kontenery i usług konteneryzowanych](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Oto obraz pełny pulpit nawigacyjny Grafana, który zawiera metryki z usługi Azure Monitor i Application Insights.
![Przykład Grafana metryki](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Zaawansowane funkcje platformy Grafana

### <a name="variables"></a>Zmienne
Niektóre wartości zapytania można wybrany za pomocą interfejsu użytkownika w menu rozwijanych i aktualizowane w zapytaniu. Na przykład, należy wziąć pod uwagę następujące zapytanie:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

Można skonfigurować zmienną, która zostanie wyświetlona lista wszystkich dostępnych **rozwiązania** wartości, a następnie zaktualizuj zapytanie z niego korzystać.
Aby utworzyć nowej zmiennej, kliknij przycisk na pulpicie nawigacyjnym ustawienia przycisk w prawym górnym obszarze, wybierz **zmienne**, a następnie **New**.
Na stronie zmiennej zdefiniować źródło danych i zapytania w celu uruchomienia w celu pobrania listy wartości.
![Grafana skonfigurować zmienną](./media/grafana-plugin/grafana-configure-variable-dark.png)

Po utworzeniu dostosować zapytania, aby użyć wybranej wartości i odpowiednio odpowie wykresy:
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Zmiennych można używać narzędzia Grafana](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>Tworzenie list odtwarzania pulpitu nawigacyjnego

Jedną z wielu przydatnych funkcji Grafana jest lista odtwarzania pulpitu nawigacyjnego. Można tworzyć wiele pulpitów nawigacyjnych i dodać je do listy odtwarzania, konfigurowanie interwał każdy pulpit nawigacyjny pokazać. Wybierz **Odtwórz** Aby wyświetlić pulpity nawigacyjne cyklicznie. Można je wyświetlić w monitorze dużej tablicy, zapewnienie tablicy stan dla tej grupy.

![Przykład listy odtwarzania narzędzia Grafana](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli konfigurujesz środowiska Grafana na platformie Azure, opłaty są naliczane, gdy maszyny wirtualne są uruchomione, czy używasz ich lub nie. Aby uniknąć naliczania dodatkowych opłat, należy wyczyścić grupy zasobów utworzonej w tym artykule.

1. W menu po lewej stronie w witrynie Azure portal, kliknij **grup zasobów** a następnie kliknij przycisk **Grafana**.
2. Na stronie grupy zasobów, kliknij przycisk **Usuń**, typ **Grafana** w polu tekstowym, a następnie kliknij przycisk **Usuń**.

## <a name="next-steps"></a>Kolejne kroki
* [Przegląd metryk usługi Azure Monitor](data-platform.md)

