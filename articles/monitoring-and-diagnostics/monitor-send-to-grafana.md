---
title: Monitorowanie usług platformy Azure i aplikacji przy użyciu Grafana
description: Dane trasy Monitor Azure i usługi Application Insights, aby je wyświetlić w Grafana.
services: azure-monitor
keywords: ''
author: rboucher
ms.author: robb
ms.date: 11/06/2017
ms.topic: conceptual
ms.service: azure-monitor
ms.component: ''
ms.openlocfilehash: de2c57949cb2087e41b79a225963225d340f12af
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/11/2018
ms.locfileid: "35263086"
---
# <a name="monitor-your-azure-services-in-grafana"></a>Monitorowanie usług systemu Azure w Grafana
Azure usługi i aplikacje od teraz można również monitorować [Grafana](https://grafana.com/) przy użyciu [wtyczki źródła danych monitora Azure](https://grafana.com/plugins/grafana-azure-monitor-datasource). Wtyczka zbiera informacje zebrane przez zestaw SDK usługi Application Insights danych dotyczących wydajności aplikacji, a także infrastruktury dane dostarczone przez Azure Monitor. Następnie można wyświetlić te dane na pulpicie nawigacyjnym Grafana.

Wtyczka jest obecnie w przeglądzie.

Wykonaj następujące kroki konfiguracji serwera Grafana z portalu Azure Marketplace i tworzenie pulpitów nawigacyjnych dla metryki z monitora Azure i usługi Application Insights.

## <a name="set-up-a-grafana-instance"></a>Skonfiguruj wystąpienie Grafana
1. Przejdź do portalu Azure Marketplace i wybierz Grafana przez laboratoria Grafana.

2. Wprowadź nazwy i szczegóły. Utwórz nową grupę zasobów. Zachowaj informacje o wybranej dla maszyny Wirtualnej nazwy użytkownika, hasło maszyny Wirtualnej i hasło administratora serwera Grafana wartości.  

3. Wybierz rozmiar maszyny Wirtualnej i konto magazynu.

4. Skonfiguruj ustawienia konfiguracji sieci.

5. Wyświetlanie podsumowania i wybierz **Utwórz** po zaakceptowaniu warunków użytkowania.

## <a name="log-in-to-grafana"></a>Zaloguj się do Grafana
1. Po zakończeniu wdrożenia, wybierz **przejdź do grupy zasobów**. Możesz wyświetlić listę nowo utworzone zasoby.

    ![Obiekty grupy zasobów Grafana](.\media\monitor-how-to-grafana\grafana1.png)

    Jeśli zostanie wybrana grupa zabezpieczeń sieci (*grafana nsg* w takim przypadku), można zobaczyć, czy dostęp do serwera Grafana jest używany port 3000.

2. Wróć do listy zasobów, a następnie wybierz **publicznego adresu IP**. Użyj wartości znajdujące się na tym ekranie, wpisz *http://<IP address>: 3000* lub  *<DNSName>: 3000* w przeglądarce. Powinna zostać wyświetlona strona logowania do serwera Grafana, które zostały utworzone.

    ![Ekran logowania Grafana](.\media\monitor-how-to-grafana\grafana2.png)

3. Zaloguj się przy użyciu nazwy użytkownika jako *admin* i hasło administratora serwera Grafana utworzony wcześniej.

## <a name="configure-data-source-plugin"></a>Skonfiguruj dodatek źródła danych

Po pomyślnym zalogowaniu, powinna zostać wyświetlona dodatek źródła danych monitora Azure jest już dołączona.

![Grafana zawiera wtyczki Azure Monitor](.\media\monitor-how-to-grafana\grafana3.png)

1. Wybierz **Dodaj źródło danych** skonfigurować Azure Monitor i usługi Application Insights.

2. Wybierz nazwę dla źródła danych, a następnie wybierz **Azure Monitor** jako źródło danych z listy rozwijanej.


## <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Grafana używa nazwy głównej usługi Azure Active Directory, aby nawiązać połączenia z interfejsów API usługi Azure monitora i zbieranie danych metryki. Należy utworzyć usługę podmiotu zabezpieczeń, aby zarządzać dostępem do zasobów platformy Azure.

1. Zobacz [tych instrukcji](../azure-resource-manager/resource-group-create-service-principal-portal.md) można utworzyć nazwy głównej usługi. Skopiuj i Zapisz dzierżawy ID, identyfikator klienta i klucz tajny klienta.

2. Zobacz [przypisywanie aplikacji do roli](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal#assign-application-to-role) można przypisać rolę czytelnika do aplikacji usługi Azure Active Directory.     

3. Jeśli używasz usługi Application Insights, możesz również uwzględnić interfejsu API usługi Application Insights i identyfikator aplikacji na potrzeby zbierania miar na podstawie usługi Application Insights. Aby uzyskać więcej informacji, zobacz [Twojego klucza interfejsu API i identyfikator aplikacji](https://dev.applicationinsights.io/documentation/Authorization/API-key-and-App-ID).

4. Po wprowadzeniu wszystkich tych informacji, wybierz **zapisać** i Grafana testy interfejsu API. Powinien zostać wyświetlony komunikat podobny do następującego.  

    ![Grafana zawiera wtyczki Azure Monitor](.\media\monitor-how-to-grafana\grafana4-1.png)

> [!NOTE]
> Podczas konfigurowania można określić, które w chmurze Azure (publiczne, Azure instytucji rządowych Stanów Zjednoczonych, platformy Azure w Niemczech lub chińskiej wersji platformy Azure) ma dodatek plug-in, aby skonfigurować przed.
>
>

## <a name="build-a-grafana-dashboard"></a>Tworzenie pulpitu nawigacyjnego Grafana

1. Przejdź do strony głównej i wybierz **nowego pulpitu nawigacyjnego**.

2. Wybierz nowy pulpit nawigacyjny **wykresu**. Możesz spróbować innych opcji wykresów, ale w tym artykule wykorzystano *wykres* jako przykład.

    ![Grafana nowego pulpitu nawigacyjnego](.\media\monitor-how-to-grafana\grafana5.png)

3. Wykres puste są wyświetlane na pulpicie nawigacyjnym.

4. Kliknij panel tytuł i wybierz pozycję **Edytuj** do wprowadź szczegóły danych do wykreślenia na tym wykresie wykresu.

5. Po wybraniu wszystkich prawo maszyn wirtualnych, można uruchomić wyświetlanie metryki na pulpicie nawigacyjnym.

Poniżej przedstawiono prosty pulpit nawigacyjny z dwa wykresy. Na lewo przedstawia procent użycia procesora CPU dwóch maszyn wirtualnych. Wykres po prawej stronie pokazuje transakcji konta usługi Azure Storage w rozbiciu na typ interfejsu API transakcji.

![Przykład wykresy Grafana dwa](.\media\monitor-how-to-grafana\grafana6.png)


## <a name="optional-create-dashboard-playlists"></a>Opcjonalnie: Utwórz pulpit nawigacyjny listy odtwarzania

Jedną z wielu przydatnych funkcji Grafana jest odtwarzania pulpitu nawigacyjnego. Można tworzyć wiele pulpitów nawigacyjnych i dodać je do listy odtwarzania Konfigurowanie interwału dla każdego pulpitu nawigacyjnego do wyświetlenia. Wybierz **odtwarzanie** użytkownikom przechodzenie między pulpitów nawigacyjnych. Można je wyświetlić na monitorze dużej tablicy zapewnienie tablicę"status" dla tej grupy.

![Przykład listy odtwarzania Grafana](.\media\monitor-how-to-grafana\grafana7.png)


## <a name="optional-monitor-your-custom-metrics-in-the-same-grafana-server"></a>Opcjonalnie: Monitorowanie niestandardowych metryk w tym samym serwerze Grafana

Można także zainstalować Telegraf i InfluxDB do gromadzenia i metryki niestandardowe i opartej o agentów w tym samym wystąpieniu Grafana kreślenia. Istnieje wiele wtyczek źródła danych korzystających ze sobą te metryki na pulpicie nawigacyjnym.

Można również wykorzystać to ustawić, aby uwzględnić metryki z serwera Prometheus. Użyj dodatek źródła danych Prometheus w galerii wtyczki Grafana firmy.

Poniżej przedstawiono dobrej odwołanie artykuły dotyczące sposobu używania Telegraf, InfluxDB Prometheus i Docker
 - [Jak monitorować System metryk ze stosu ZNACZNIKÓW na Ubuntu 16.04](https://www.digitalocean.com/community/tutorials/how-to-monitor-system-metrics-with-the-tick-stack-on-ubuntu-16-04)

 - [Monitorowanie Docker zasobu metryki z Grafana, InfluxDB i Telegraf](https://blog.vpetkov.net/2016/08/04/monitor-docker-resource-metrics-with-grafana-influxdb-and-telegraf/)

 - [Rozwiązanie monitorowania hostów Docker, kontenery i konteneryzowanych usług](https://stefanprodan.com/2016/a-monitoring-solution-for-docker-hosts-containers-and-containerized-services/)

Oto obraz pełny pulpit nawigacyjny Grafana, który zawiera metryki z monitora Azure i usługi Application Insights.
![Przykład Grafana metryk](.\media\monitor-how-to-grafana\grafana8.png)


## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Są naliczane, gdy maszyny wirtualne są uruchomione, czy są używane lub nie. Aby uniknąć ponoszenia dodatkowych kosztów, wyczyść grupy zasobów utworzonej w tym artykule.

1. Z menu po lewej stronie w portalu Azure kliknij **grup zasobów** , a następnie kliknij przycisk **Grafana**.
2. Na stronie grupy zasobów, kliknij przycisk **usunąć**, typ **Grafana** w polu tekstowym, a następnie kliknij przycisk **usunąć**.

## <a name="next-steps"></a>Następne kroki
* [Omówienie metryki Azure monitora](monitoring-overview-metrics.md)
