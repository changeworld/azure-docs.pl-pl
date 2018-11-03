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
ms.component: ''
ms.openlocfilehash: 533fbfc42939299f99699a13513cd3b55c89cabc
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/02/2018
ms.locfileid: "50957288"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Monitorowanie usług platformy Azure w narzędzia Grafana
Usługi platformy Azure i aplikacje od teraz można również monitorować [Grafana](https://grafana.com/) przy użyciu [wtyczki źródła danych usługi Azure Monitor](https://grafana.com/plugins/grafana-azure-monitor-datasource). Wtyczka zbiera dane dotyczące wydajności aplikacji zbierane przez zestaw SDK usługi Application Insights, a także dane infrastruktury udostępniane przez usługi Azure Monitor. Następnie można wyświetlić te dane na pulpicie nawigacyjnym Grafana.

Wtyczka jest obecnie dostępna w wersji zapoznawczej.

Wykonaj następujące kroki konfiguracji serwera Grafana z witryny Azure Marketplace i tworzyć pulpity nawigacyjne umożliwiające metryki z usługi Azure Monitor i Application Insights.

## <a name="set-up-a-grafana-instance"></a>Skonfiguruj wystąpienie narzędzia Grafana
1. Przejdź do portalu Azure Marketplace, a następnie wybierz Grafana przez Grafana Labs.

2. Podaj nazwy i szczegóły. Utwórz nową grupę zasobów. Zachowaj informacje o wartości, które możesz wybrać nazwę maszyny Wirtualnej użytkownika, hasło maszyny Wirtualnej i hasło administratora serwera Grafana.  

3. Wybierz rozmiar maszyny Wirtualnej i konta magazynu.

4. Skonfiguruj ustawienia konfiguracji sieci.

5. Wyświetlić podsumowanie, a następnie wybierz pozycję **Utwórz** po zaakceptowaniu warunków użytkowania.

## <a name="log-in-to-grafana"></a>Zaloguj się do narzędzia Grafana
1. Po zakończeniu wdrożenia wybierz **przejdź do grupy zasobów**. Zobaczysz listę nowo utworzonych zasobów.

    ![Obiekty grupy zasobów platformy Grafana](media/monitor-send-to-grafana/grafana1.png)

    Jeśli zostanie wybrana grupa zabezpieczeń sieci (*grafana-nsg* w tym przypadku), możesz zobaczyć, czy dostęp do narzędzia Grafana serwera jest używany port 3000.

2. Wróć do listy zasobów, a następnie wybierz pozycję **publiczny adres IP**. Korzystając z na wartości znajdujące się na tym ekranie, wpisz *http://<IP address>: 3000* lub  *<DNSName>: 3000* w przeglądarce. Powinieneś widzieć stronę logowania dla serwera platformy Grafana, który właśnie zbudowany.

    ![Ekran logowania narzędzia Grafana](media/monitor-send-to-grafana/grafana2.png)

3. Zaloguj się przy użyciu nazwy użytkownika jako *administratora* i Grafana hasło administratora serwera, została utworzona wcześniej.

## <a name="configure-data-source-plugin"></a>Skonfiguruj dodatek źródła danych

Po pomyślnym zalogowaniu powinien pojawić się, że dodatek źródła danych usługi Azure Monitor jest już dołączona.

![Grafana pokazuje wtyczkę usługi Azure Monitor](media/monitor-send-to-grafana/grafana3.png)

1. Wybierz **Dodaj źródło danych** do konfigurowania usługi Azure Monitor i Application Insights.

2. Wybierz nazwę dla źródła danych, a następnie wybierz pozycję **usługi Azure Monitor** jako źródło danych z listy rozwijanej.


## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Grafana używa nazwy głównej usługi Azure Active Directory, aby nawiązać połączenie z interfejsów API usługi Azure Monitor i zbieranie danych metryk. Użytkownik musi utworzyć jednostkę usługi do zarządzania dostępem do zasobów platformy Azure.

1. Zobacz [w instrukcjach](../active-directory/develop/howto-create-service-principal-portal.md) do utworzenia jednostki usługi. Skopiuj i Zapisz dzierżawy, identyfikator, identyfikator klienta oraz klucz tajny klienta.

2. Zobacz [przypisywanie aplikacji do roli](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role) przypisać rolę czytelnika do aplikacji usługi Azure Active Directory.     

3. Jeśli korzystasz z usługi Application Insights, można także dodać interfejs API usługi Application Insights oraz Identyfikatora aplikacji, aby zbieranie metryk usługi Application Insights na podstawie. Aby uzyskać więcej informacji, zobacz [uzyskiwanie Twojego klucza interfejsu API i identyfikator aplikacji](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

4. Po wprowadzeniu wszystkich tych informacji, wybierz **Zapisz** i Grafana testuje interfejsu API. Powinien zostać wyświetlony komunikat podobny do poniższego.  

    ![Grafana pokazuje wtyczkę usługi Azure Monitor](media/monitor-send-to-grafana/grafana4-1.png)

> [!NOTE]
> Podczas konfigurowania wtyczki można określić, jaka chmura platformy Azure (publiczne, dla administracji USA, Azure (Niemcy) lub Azure — Chiny) ma się wtyczki, należy skonfigurować przed.
>
>

## <a name="build-a-grafana-dashboard"></a>Tworzenie pulpitu nawigacyjnego do narzędzia Grafana

1. Przejdź do strony głównej i wybierz pozycję **nowy pulpit nawigacyjny**.

2. Nowy pulpit nawigacyjny, wybierz **wykresu**. Możesz wypróbować inne opcje tworzenia wykresów, ale w tym artykule wykorzystano *wykres* jako przykład.

    ![Nowy pulpit nawigacyjny platformy Grafana](media/monitor-send-to-grafana/grafana5.png)

3. Pusty wykres wyświetlane na pulpicie nawigacyjnym.

4. Kliknij tytuł panelu i wybierz pozycję **Edytuj** o podanie szczegółowych danych, do wykreślenia na tym wykresie wykresu.

5. Po wybraniu wszystkich maszyn wirtualnych prawym możesz rozpocząć wyświetlanie metryk na pulpicie nawigacyjnym.

Poniżej przedstawiono dwa wykresy na prostym pulpicie nawigacyjnym. Po lewej stronie zawiera procent użycia procesora CPU, dwie maszyny wirtualne. Wykres po prawej stronie pokazuje transakcji na koncie usługi Azure Storage, podzielone według typu transakcji interfejsu API.

![Przykładowe wykresy dwa narzędzia Grafana](media/monitor-send-to-grafana/grafana6.png)


## <a name="optional-create-dashboard-playlists"></a>Opcjonalnie: Tworzenie list odtwarzania pulpitu nawigacyjnego

Jedną z wielu przydatnych funkcji Grafana jest lista odtwarzania pulpitu nawigacyjnego. Można tworzyć wiele pulpitów nawigacyjnych i dodać je do listy odtwarzania, konfigurowanie interwał każdy pulpit nawigacyjny pokazać. Wybierz **Odtwórz** Aby wyświetlić pulpity nawigacyjne cyklicznie. Można je wyświetlić na monitorze dużej tablicy zapewnienie "board stan" dla tej grupy.

![Przykład listy odtwarzania narzędzia Grafana](media/monitor-send-to-grafana/grafana7.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Opcjonalnie: Monitoruj metryki niestandardowe w tym samym serwerze narzędzia Grafana

Można także zainstalować Telegraf i InfluxDB do zbierania i wykresu metryki niestandardowe i oparte na agentach, w tym samym wystąpieniu platformy Grafana. Istnieje wiele wtyczek źródła danych korzystające ze sobą te metryki na pulpicie nawigacyjnym.

Możesz także ponownie użyć tej konfiguracji, aby uwzględnić metryki z serwera Prometheus. Użyj wtyczki źródła danych Prometheus w galerii wtyczki Grafana firmy.

Poniżej przedstawiono wartościowa dokumentacja ułatwiająca artykuły dotyczące sposobu używania Telegraf, InfluxDB, Prometheus i platformy Docker
 - [Jak monitorować metryki systemowe z stosem ZNACZNIKÓW w systemie Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Monitoruj metryki zasobu platformy Docker przy użyciu narzędzia Grafana, InfluxDB i Telegraf](https://blog.vpetkov.net/2016/08/04/monitor-docker-resource-metrics-with-grafana-influxdb-and-telegraf/)

 - [Rozwiązanie monitorowania dla hostów platformy Docker, kontenery i usług konteneryzowanych](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Oto obraz pełny pulpit nawigacyjny Grafana, który zawiera metryki z usługi Azure Monitor i Application Insights.
![Przykład Grafana metryki](media/monitor-send-to-grafana/grafana8.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Opłaty są naliczane, gdy maszyny wirtualne są uruchomione, czy używasz ich lub nie. Aby uniknąć naliczania dodatkowych opłat, należy wyczyścić grupy zasobów utworzonej w tym artykule.

1. W menu po lewej stronie w witrynie Azure portal, kliknij **grup zasobów** a następnie kliknij przycisk **Grafana**.
2. Na stronie grupy zasobów, kliknij przycisk **Usuń**, typ **Grafana** w polu tekstowym, a następnie kliknij przycisk **Usuń**.

## <a name="next-steps"></a>Kolejne kroki
* [Przegląd metryk usługi Azure Monitor](../monitoring/monitoring-data-collection.md)
