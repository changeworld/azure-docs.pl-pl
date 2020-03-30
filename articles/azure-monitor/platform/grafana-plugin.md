---
title: Monitorowanie usług i aplikacji platformy Azure przy użyciu grawany
description: Rozsyłaj dane usługi Azure Monitor i usługa Application Insights, aby można je było wyświetlać w grafanie.
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/06/2017
ms.openlocfilehash: 142e3e19c13710963d239a75bc237b63713c29cc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672212"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Monitorowanie usług platformy Azure w Grafanie
Teraz możesz monitorować usługi i aplikacje platformy Azure z [graginany](https://grafana.com/) za pomocą [wtyczki źródła danych usługi Azure Monitor.](https://grafana.com/plugins/grafana-azure-monitor-datasource) Wtyczka zbiera dane dotyczące wydajności aplikacji zbierane przez usługę Azure Monitor, w tym różne dzienniki i metryki. Następnie można wyświetlić te dane na pulpicie nawigacyjnym Grafana.

Aby skonfigurować serwer Grafana i utworzyć pulpity nawigacyjne dla metryk i dzienników z usługi Azure Monitor, należy wykonać następujące kroki.

## <a name="set-up-a-grafana-server"></a>Konfigurowanie serwera Grafana

### <a name="set-up-grafana-locally"></a>Konfigurowanie granazy lokalnie
Aby skonfigurować lokalny serwer Grafana, [pobierz i zainstaluj gramontę w lokalnym środowisku.](https://grafana.com/grafana/download) Aby korzystać z integracji usługi Azure Monitor wtyczki, należy zainstalować grafana w wersji 5.3 lub nowszej.

### <a name="set-up-grafana-on-azure-through-the-azure-marketplace"></a>Konfigurowanie grawany na platformie Azure za pośrednictwem portalu Azure Marketplace
1. Przejdź do portalu Azure Marketplace i wybierz grafana firmy Grafana Labs.

2. Wpisz nazwy i szczegóły. Utwórz nową grupę zasobów. Śledź wartości, które wybierzesz dla nazwy użytkownika maszyny Wirtualnej, hasła maszyny Wirtualnej i hasła administratora serwera Grafana.  

3. Wybierz rozmiar maszyny Wirtualnej i konto magazynu.

4. Skonfiguruj ustawienia konfiguracji sieci.

5. Wyświetl podsumowanie i wybierz **pozycję Utwórz** po zaakceptowaniu warunków użytkowania.

6. Po zakończeniu wdrażania wybierz pozycję **Przejdź do grupy zasobów**. Widoczna jest lista nowo utworzonych zasobów.

    ![Obiekty grupy zasobów Grafana](media/grafana-plugin/grafana1.png)

    Jeśli w tym przypadku wybierzesz grupę zabezpieczeń sieci *(grafana-nsg),* zobaczysz, że port 3000 jest używany do uzyskiwania dostępu do serwera Grafana.

7. Uzyskaj publiczny adres IP serwera Grafana — wróć do listy zasobów i wybierz **publiczny adres IP**.

## <a name="sign-in-to-grafana"></a>Zaloguj się do grawany

1. Korzystając z adresu IP serwera, otwórz stronę Logowania pod *http:// adresem\<\>IP :3000* lub * \<DNSName>\:3000* w przeglądarce. Chociaż 3000 jest portem domyślnym, należy pamiętać, że podczas instalacji wybrano inny port. Powinna zostać wyświetlona strona logowania dla zbudowanego serwera Grafana.

    ![Ekran logowania grafana](./media/grafana-plugin/grafana-login-screen.png)

2. Zaloguj się przy tym przy tym przy tym przy tym przy tym *przy* tym przy tym przy tym przywieszce hasła administratora serwera Grafana. Jeśli używasz konfiguracji lokalnej, domyślnym hasłem będzie *administrator*i zostaniesz poproszony o zmianę go przy pierwszym logowaniu.

## <a name="configure-data-source-plugin"></a>Konfigurowanie wtyczki źródła danych

Po pomyślnym zalogowaniu należy zobaczyć, że wtyczka źródła danych usługi Azure Monitor jest już uwzględniona.

![Grafana zawiera wtyczkę Usługi Azure Monitor](./media/grafana-plugin/grafana-includes-azure-monitor-plugin-dark.png)

1. Wybierz **pozycję Dodaj źródło danych,** aby dodać i skonfigurować źródło danych usługi Azure Monitor.

2. Wybierz nazwę źródła danych i wybierz **usługę Azure Monitor** jako typ z listy rozwijanej.

3. Tworzenie jednostki usługi — Grafana używa jednostki usługi Azure Active Directory do łączenia się z interfejsami API usługi Azure Monitor i zbierania danych. Aby zarządzać dostępem do zasobów platformy Azure, należy utworzyć lub użyć istniejącego podmiotu usługi.
    * Zobacz [te instrukcje,](../../azure-resource-manager/resource-group-create-service-principal-portal.md) aby utworzyć jednostkę usługi. Skopiuj i zapisz identyfikator dzierżawy (identyfikator katalogu), identyfikator klienta (identyfikator aplikacji) i klucz tajny klienta (wartość klucza aplikacji).
    * Zobacz [Przypisywanie aplikacji do roli,](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal) aby przypisać rolę czytelnika do aplikacji usługi Azure Active Directory w ramach subskrypcji, grupy zasobów lub zasobu, który chcesz monitorować. 
    Interfejs API usługi Log Analytics wymaga [roli czytnika analizy dzienników,](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#log-analytics-reader)która zawiera uprawnienia roli czytelnika i dodaje do niej.

4. Podaj szczegóły połączenia do interfejsów API, których chcesz użyć. Możesz połączyć się ze wszystkimi lub z niektórymi z nich. 
    * Jeśli łączysz się zarówno z metrykami, jak i dziennikami w usłudze Azure Monitor, możesz ponownie użyć tych samych poświadczeń, wybierając **te same szczegóły co interfejs API usługi Azure Monitor.**
    * Podczas konfigurowania wtyczki można wskazać, którą usługę Azure Cloud chcesz monitorować wtyczkę (publiczne, usługi Azure US Government, Azure Germany lub Azure China).
    * Jeśli używasz usługi Application Insights, można również dołączyć interfejs API usługi Application Insights i identyfikator aplikacji do zbierania metryk opartych na usłudze Application Insights. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie klucza interfejsu API i identyfikator aplikacji](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

        > [!NOTE]
        > Niektóre pola źródła danych są nazywane inaczej niż ich skorelowane ustawienia platformy Azure:
        > * Identyfikator dzierżawy to identyfikator usługi Azure Directory ID
        > * Identyfikator klienta to identyfikator aplikacji usługi Azure Active Directory
        > * Klucz tajny klienta to wartość klucza aplikacji usługi Azure Active Directory

5. Jeśli używasz usługi Application Insights, można również dołączyć interfejs API usługi Application Insights i identyfikator aplikacji do zbierania metryk opartych na usłudze Application Insights. Aby uzyskać więcej informacji, zobacz [Uzyskiwanie klucza interfejsu API i identyfikator aplikacji](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

6. Wybierz **pozycję Zapisz**, a grana przetestuje poświadczenia dla każdego interfejsu API. Powinien zostać wyświetlony komunikat podobny do następującego.  
    ![Zatwierdzono konfigurgę źródła danych Grafana](./media/grafana-plugin/grafana-data-source-config-approved-dark.png)

## <a name="build-a-grafana-dashboard"></a>Tworzenie pulpitu nawigacyjnego Grafana

1. Przejdź do strony głównej grawany i wybierz pozycję **Nowy pulpit nawigacyjny**.

2. Na nowym pulpicie nawigacyjnym wybierz **pozycję Wykres**. Możesz wypróbować inne opcje tworzenia wykresów, ale w tym artykule użyto *wykresu* jako przykładu.

3. Na pulpicie nawigacyjnym pojawi się pusty wykres. Kliknij tytuł panelu i wybierz pozycję **Edytuj,** aby wprowadzić szczegóły danych, które chcesz wykreślić na tym wykresie.
    ![Grafana nowy wykres](./media/grafana-plugin/grafana-new-graph-dark.png)

4. Wybierz skonfigurowane źródło danych usługi Azure Monitor.
   * Zbieranie metryk usługi Azure Monitor — wybierz **usługę Azure Monitor** w rozwijanej usłudze. Zostanie wyświetlona lista selektorów, w których można wybrać zasoby i dane do monitorowania na tym wykresie. Aby zbierać metryki z maszyny Wirtualnej, użyj obszaru nazw **Microsoft.Compute/VirtualMachines**. Po wybraniu maszyn wirtualnych i metryk można rozpocząć wyświetlanie ich danych na pulpicie nawigacyjnym.
     ![Grafana graph config for Azure Monitor](./media/grafana-plugin/grafana-graph-config-for-azure-monitor-dark.png)
   * Zbieranie danych dziennika usługi Azure Monitor — wybierz **usługę Azure Log Analytics** w rozwijanej usłudze. Zaznacz obszar roboczy, który chcesz zbadać, i ustaw tekst kwerendy. W tym miejscu można skopiować dowolną kwerendę dziennika, którą już masz, lub utworzyć nową. Podczas wpisywalania zapytania program IntelliSense będzie wyświetlał się i sugerował opcje autouzupełniania. Wybierz typ wizualizacji, **Tabela szeregów** **czasowych**i uruchom kwerendę.
    
     > [!NOTE]
     >
     > Domyślna kwerenda dostarczona z wtyczką używa dwóch makr: "$__timeFilter() i $__interval. 
     > Te makra umożliwiają grafanie dynamiczne obliczanie zakresu czasu i ziarna czasu podczas powiększania części wykresu. Można usunąć te makra i użyć standardowego filtru czasu, takiego jak *TimeGenerated > temu(1h),* ale oznacza to, że wykres nie będzie obsługiwał funkcji powiększania.
    
     ![Grafana graph config for Azure Log Analytics](./media/grafana-plugin/grafana-graph-config-for-azure-log-analytics-dark.png)

5. Poniżej znajduje się prosty pulpit nawigacyjny z dwoma wykresami. Jeden po lewej stronie pokazuje procent procesora CPU dwóch maszyn wirtualnych. Wykres po prawej stronie przedstawia transakcje na koncie usługi Azure Storage w podziale według typu interfejsu API transakcji.
    ![Przykład dwóch wykresów Grafana](media/grafana-plugin/grafana6.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Opcjonalnie: monitorowanie niestandardowych danych na tym samym serwerze Grafana

Można również zainstalować Telegraf i InfluxDB do zbierania i wykreślenia zarówno niestandardowych, jak i opartych na agentach metryk tego samego wystąpienia Grafana. Istnieje wiele wtyczek źródła danych, których można użyć do zebrania tych metryk na pulpicie nawigacyjnym.

Można również użyć tej konfiguracji, aby uwzględnić metryki z serwera Prometheus. Użyj wtyczki źródła danych Prometheus w galerii wtyczek Grawy.

Oto dobre artykuły referencyjne na temat korzystania z Telegraf, InfluxDB, Prometeusz i Docker
 - [Jak monitorować metryki systemu ze stosem TICK na Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Rozwiązanie do monitorowania dla hostów platformy Docker, kontenerów i konteneryzowanych usług](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Oto obraz pełnego pulpitu nawigacyjnego Grafana, który ma metryki z usługi Azure Monitor i usługa Application Insights.
![Metryki przykładowe grafany](media/grafana-plugin/grafana8.png)

## <a name="advanced-grafana-features"></a>Zaawansowane funkcje grawany

### <a name="variables"></a>Zmienne
Niektóre wartości kwerend można wybrać za pośrednictwem listy rozwijanej interfejsu użytkownika i zaktualizować w kwerendzie. Rozważmy następujący przykład następującej kwerendy:
```
Usage 
| where $__timeFilter(TimeGenerated) 
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```

Można skonfigurować zmienną, która wyświetli listę wszystkich dostępnych wartości **rozwiązania,** a następnie zaktualizować kwerendę, aby ją użyć.
Aby utworzyć nową zmienną, kliknij przycisk Ustawienia pulpitu nawigacyjnego w prawym górnym rogu, wybierz pozycję **Zmienne**, a następnie **pozycję Nowy**.
Na stronie zmiennej zdefiniuj źródło danych i kwerendę do uruchomienia w celu uzyskania listy wartości.
![Grafana konfigurować zmienną](./media/grafana-plugin/grafana-configure-variable-dark.png)

Po utworzeniu dostosuj kwerendę, aby używała wybranych wartości, a wykresy będą odpowiednio reagować:
```
Usage 
| where $__timeFilter(TimeGenerated) and Solution in ($Solutions)
| summarize total_KBytes=sum(Quantity)*1024 by bin(TimeGenerated, $__interval) 
| sort by TimeGenerated
```
    
![Grafana używać zmiennych](./media/grafana-plugin/grafana-use-variables-dark.png)

### <a name="create-dashboard-playlists"></a>Tworzenie list odtwarzania pulpitu nawigacyjnego

Jedną z wielu przydatnych funkcji grawany jest lista odtwarzania na desce rozdzielczej. Można utworzyć wiele pulpitów nawigacyjnych i dodać je do listy odtwarzania, konfigurując interwał dla każdego pulpitu nawigacyjnego do wyświetlenia. Wybierz **przycisk Odtwórz,** aby wyświetlić przechodzenie między pulpitami nawigacyjnymi. Możesz wyświetlić je na dużym monitorze ściennym, aby zapewnić tablicę stanu dla grupy.

![Przykład listy odtwarzania Grafana](./media/grafana-plugin/grafana7.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli skonfigurowano środowisko Grafana na platformie Azure, opłaty są naliczane, gdy maszyny wirtualne są uruchomione, niezależnie od tego, czy ich używasz, czy nie. Aby uniknąć ponoszenia dodatkowych opłat, należy wyczyścić grupę zasobów utworzoną w tym artykule.

1. W menu po lewej stronie w witrynie Azure portal kliknij pozycję **Grupy zasobów,** a następnie kliknij pozycję **Grafana**.
2. Na stronie grupy zasobów kliknij pozycję **Usuń**, wpisz **Grafana** w polu tekstowym, a następnie kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki
* [Omówienie metryk usługi Azure Monitor](data-platform.md)

