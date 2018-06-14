---
title: Monitorowanie i zarządzanie nimi za pomocą interfejsu użytkownika sieci Web Ambari w usłudze Azure HDInsight | Dokumentacja firmy Microsoft
description: Dowiedz się, jak używać narzędzia Ambari do monitorowania i zarządzania klastrami usługi HDInsight opartej na systemie Linux. W tym dokumencie Dowiedz się jak używać Interfejsu sieci Web Ambari, które są dołączone do klastrów usługi HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 4787f3cc-a650-4dc3-9d96-a19a67aad046
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 03/23/2018
ms.author: larryfr
ms.openlocfilehash: 8c58607e191d58d1cb6706569b909a4d709b14b3
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31405270"
---
# <a name="manage-hdinsight-clusters-by-using-the-ambari-web-ui"></a>Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Ambari

[!INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari upraszcza zarządzanie i monitorowanie klastra usługi Hadoop zapewniając łatwy do interfejsu użytkownika sieci web i interfejsu API REST. Ambari znajduje się w klastrach HDInsight opartych na systemie Linux i służy do monitorowania klastra i wprowadzania zmian w konfiguracji.

W tym dokumencie Dowiedz się jak używać interfejsu użytkownika sieci Web Ambari z klastra usługi HDInsight.

## <a id="whatis"></a>Co to jest Ambari?

[Apache Ambari](http://ambari.apache.org) ułatwia zarządzanie Hadoop, zapewniając łatwy w użyciu interfejsu użytkownika sieci web. Ambari służy do zarządzania i monitorowania klastrów platformy Hadoop. Deweloperzy mogą integrować te możliwości w swoich aplikacjach przy użyciu [interfejsów API REST Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

Interfejs sieci Web Ambari jest udostępniana domyślnie z klastrami usługi HDInsight, które używają systemu operacyjnego Linux.

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows). 

## <a name="connectivity"></a>Łączność

Interfejs sieci Web Ambari jest dostępna w klastrze usługi HDInsight w HTTPS://CLUSTERNAME.azurehdinsight.net, gdzie **CLUSTERNAME** jest nazwą klastra.

> [!IMPORTANT]
> Połączenie z Ambari w usłudze HDInsight wymaga protokołu HTTPS. Po wyświetleniu monitu na potrzeby uwierzytelniania, użyj nazwy konta administratora i hasło podane podczas tworzenia klastra.

## <a name="ssh-tunnel-proxy"></a>Tunel SSH (proxy)

Gdy Ambari dla klastra jest dostępny bezpośrednio przez Internet, niektóre łącza w interfejsie użytkownika sieci Web Ambari, (np. JobTracker) nie są widoczne w Internecie. Aby uzyskać dostęp do tych usług, należy utworzyć tunelu SSH. Aby uzyskać więcej informacji, zobacz [Użyj SSH Tunneling z usługą HDInsight](hdinsight-linux-ambari-ssh-tunnel.md).

## <a name="ambari-web-ui"></a>Interfejs użytkownika sieci Web Ambari

> [!WARNING]
> Nie wszystkie funkcje interfejsu użytkownika sieci Web Ambari są obsługiwane w usłudze HDInsight. Aby uzyskać więcej informacji, zobacz [obsługiwane operacje](#unsupported-operations) sekcji tego dokumentu.

Podczas łączenia z interfejsu użytkownika sieci Web Ambari, zostanie wyświetlony monit uwierzytelniać się na stronie. Użyj użytkownika Administrator klastra (ustawienie domyślne Admin) i hasło użyte podczas tworzenia klastra.

Po otwarciu strony, należy zwrócić uwagę na pasku u góry. Ten pasek zawiera następujące informacje i kontrolki:

![Nawiguj do ambari](./media/hdinsight-hadoop-manage-ambari/ambari-nav.png)

* **Logo narzędzia Ambari** — powoduje otwarcie pulpitu nawigacyjnego, który może służyć do monitorowania klastra.

* **Nazwa # ops klastra** -Wyświetla liczbę przeprowadzanych operacjach Ambari. Wybieranie nazwy klastra lub **# ops** zostanie wyświetlona lista operacje w tle.

* **alerty #** -wyświetla ostrzeżenia lub alerty krytyczne dla klastra.

* **Pulpit nawigacyjny** -Wyświetla pulpitu nawigacyjnego.

* **Usługi** — informacje oraz jej konfigurację ustawień dla usług w klastrze.

* **Hosty** -informacji i ustawień konfiguracji dla węzłów w klastrze.

* **Alerty** -dziennika informacje, ostrzeżenia i alerty krytyczne.

* **Administrator** -oprogramowania stosu/usług, które są zainstalowane w klastrze, informacje o koncie usługi i zabezpieczeń protokołu Kerberos.

* **Przycisk Admin** -Ambari zarządzania, ustawienia użytkownika i wylogowania.

## <a name="monitoring"></a>Monitorowanie

### <a name="alerts"></a>Alerty

Poniższa lista zawiera typowe stany alertów używana przez narzędzia Ambari:

* **OK**
* **Ostrzeżenie**
* **KRYTYCZNE**
* **UNKNOWN**

Alerty innych niż **OK** spowodować **alerty #** wpis w górnej części strony, aby wyświetlić liczbę alertów. Wybranie tego wpisu powoduje wyświetlenie alertów i ich stan.

Alerty są podzielone na kilka domyślnych grup, które mogą być wyświetlane z **alerty** strony.

![Strona alertów](./media/hdinsight-hadoop-manage-ambari/alerts.png)

Grupy można zarządzać za pomocą **akcje** menu i wybierając **Zarządzaj grupami alertu**.

![Zarządzanie grupami alertu w oknie dialogowym](./media/hdinsight-hadoop-manage-ambari/manage-alerts.png)

Można również zarządzać metody alertów i utworzyć powiadomienia o alertach z **akcje** menu, wybierając __Zarządzanie powiadomienia Alert__. Bieżący powiadomienia są wyświetlane. Powiadomienia można również utworzyć w tym miejscu. Powiadomienia mogą być wysyłane za pośrednictwem **E-mail** lub **SNMP** przypadku wystąpienia określonych ważności alertu/kombinacji. Na przykład możesz wysłać wiadomość e-mail, gdy żadnych alertów w **domyślne YARN** ustawiono grupy **krytyczny**.

![Tworzenie okna dialogowego alertu](./media/hdinsight-hadoop-manage-ambari/create-alert-notification.png)

Ponadto wybierając __Zarządzaj ustawieniami Alert__ z __akcje__ menu umożliwia określenie, ile razy alertu musi wystąpić, aby wysłać powiadomienia. To ustawienie umożliwia zapobieganie powiadomienia w przypadku błędów przejściowych.

### <a name="cluster"></a>Klaster

**Metryki** pulpitu nawigacyjnego zawiera szereg elementy widget, które ułatwiają monitorowanie stanu klastra jeden rzut oka. Kilka elementów widget, takich jak **użycie procesora CPU**, znajdują się dodatkowe informacje po kliknięciu.

![pulpit nawigacyjny z metryk](./media/hdinsight-hadoop-manage-ambari/metrics.png)

**Heatmaps** karcie są wyświetlane jako kolorowe heatmaps, przechodząc od zielony czerwony metryki.

![pulpit nawigacyjny z heatmaps](./media/hdinsight-hadoop-manage-ambari/heatmap.png)

Aby uzyskać więcej informacji na węzłach w klastrze, wybierz **hostów**. Następnie wybierz określonego węzła, który chcesz.

![Szczegóły hosta](./media/hdinsight-hadoop-manage-ambari/host-details.png)

### <a name="services"></a>Usługi

**Usług** paska bocznego na pulpicie nawigacyjnym zapewnia szybki wgląd w stan usługi działające w klastrze. Różne ikony są używane do wskazania stanu lub akcje, które należy podjąć. Na przykład symbol żółty odtworzenia jest wyświetlana, jeśli usługa musi zostać odtworzona.

![pasek boczny usług](./media/hdinsight-hadoop-manage-ambari/service-bar.png)

> [!NOTE]
> Usługi wyświetlane różnią się typy klastrów usługi HDInsight i wersje. Usługi wyświetlane w tym miejscu mogą różnić się od usługi wyświetlane dla klastra.

Wybranie usługi powoduje wyświetlenie bardziej szczegółowych informacji w usłudze.

![Podsumowanie usługi](./media/hdinsight-hadoop-manage-ambari/service-details.png)

#### <a name="quick-links"></a>Szybkie łącza

Wyświetlanie niektórych usług **szybkie linki** łącze w górnej części strony. Może to służyć do dostępu specyficzne dla usługi sieci web UI, takich jak:

* **Historia zadań** -historii zadań MapReduce.
* **Menedżer zasobów** -YARN ResourceManager UI.
* **NameNode** -Hadoop Distributed Interfejsu NameNode (HDFS) systemu plików.
* **Interfejs użytkownika sieci Web Oozie** -Oozie interfejsu użytkownika.

Wybranie dowolnego z tych łączy otwiera nową kartę w przeglądarce, która wyświetla wybranej strony.

> [!NOTE]
> Wybieranie **szybkie linki** wpis dla usługi mogą zwracać błąd "nie można odnaleźć serwera". Jeśli wystąpi ten błąd, należy użyć tunelu SSH przy użyciu **szybkie linki** wpis dla tej usługi. Aby uzyskać informacje, zobacz [Użyj SSH Tunneling z usługą HDInsight](hdinsight-linux-ambari-ssh-tunnel.md)

## <a name="management"></a>Zarządzanie

### <a name="ambari-users-groups-and-permissions"></a>Ambari użytkowników, grup i uprawnień

Praca z użytkownikami, grupami i uprawnienia są obsługiwane w przypadku korzystania z [przyłączony do domeny](./domain-joined/apache-domain-joined-introduction.md) klastra usługi HDInsight. Uzyskać przy użyciu interfejsu użytkownika narzędzia Ambari zarządzania w klastrze przyłączonych do domeny, zobacz [zarządzania klastrami HDInsight przyłączonych do domeny](./domain-joined/apache-domain-joined-introduction.md).

> [!WARNING]
> Nie należy zmieniać hasła strażnika Ambari (hdinsightwatchdog) w klastrze usługi HDInsight opartej na systemie Linux. Zmiana hasła dzieli możliwość akcje skryptu lub operacji skalowania z klastrem.

### <a name="hosts"></a>Hosts

**Hostów** strona zawiera listę wszystkich hostów w klastrze. Aby zarządzać hostami, wykonaj następujące kroki.

![strony hosty](./media/hdinsight-hadoop-manage-ambari/hosts.png)

> [!NOTE]
> Dodawanie, wycofywanie z eksploatacji i recommissioning hosta nie można używać z klastrami usługi HDInsight.

1. Wybierz hosta, który ma być zarządzana.

2. Użyj **akcje** menu wybierz czynność, którą chcesz wykonać:

   * **Uruchom wszystkie składniki** -Start wszystkich składników na hoście.

   * **Zatrzymaj wszystkie składniki** -zatrzymania wszystkich składników na hoście.

   * **Uruchom ponownie wszystkie składniki** — zatrzymano i uruchomić wszystkie składniki na hoście.

   * **Włącz tryb konserwacji** -pomija alerty dla hosta. W tym trybie powinien być włączony, jeśli przeprowadzasz akcje, które generują alerty. Na przykład zatrzymywania i uruchamiania usługi.

   * **Wyłącz tryb konserwacji** — zwraca hosta do normalnej alerty.

   * **Zatrzymaj** -zatrzymuje DataNode lub NodeManagers na hoście.

   * **Uruchom** — uruchamia DataNode lub NodeManagers na hoście.

   * **Uruchom ponownie** -przerywa działanie i uruchamia DataNode lub NodeManagers na hoście.

   * **Likwidowanie** — usuwa hosta z klastra.

     > [!NOTE]
     > Nie należy używać tej akcji w klastrach usługi HDInsight.

   * **Recommission** -dodaje wcześniej zlikwidowana hosta do klastra.

     > [!NOTE]
     > Nie należy używać tej akcji w klastrach usługi HDInsight.

### <a id="service"></a>Usługi

Z **pulpitu nawigacyjnego** lub **usług** użyj **akcje** przycisk w dolnej części listy usług, aby zatrzymać i uruchomić wszystkie usługi.

![Akcje usługi](./media/hdinsight-hadoop-manage-ambari/service-actions.png)

> [!WARNING]
> Gdy **Dodaj usługę** znajduje się w tym menu, nie można stosować nad dodaniem usług do klastra usługi HDInsight. Należy dodać nowe usługi za pomocą akcji skryptu podczas inicjowania obsługi klastra. Aby uzyskać więcej informacji dotyczących za pomocą akcji skryptu, zobacz [HDInsight dostosować klastry za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

Gdy **akcje** przycisku można uruchomić ponownie wszystkie usługi, często chcesz uruchomić, zatrzymać lub ponowne uruchamianie określonej usługi. Poniższe kroki umożliwiają wykonywania działań na poszczególnych usług:

1. Z **pulpitu nawigacyjnego** lub **usług** wybierz opcję usługi.

2. Z góry **Podsumowanie** użyj **akcji usługi** przycisk i wybierz akcję do wykonania. Spowoduje to ponowne uruchomienie usługi na wszystkich węzłach.

    ![działanie usługi](./media/hdinsight-hadoop-manage-ambari/individual-service-actions.png)

   > [!NOTE]
   > Ponowne uruchamianie niektórych usług uruchomionej klastra może generować alerty. Aby uniknąć alertów, można użyć **akcji usługi** przycisk, aby włączyć **trybu konserwacji** przed wykonaniem operacji ponownego uruchomienia usługi.

3. Po wybraniu akcji **# op** wpis na górze przyrostem strony, aby pokazać, że operacji w tle ma miejsce. Jeśli skonfigurowana, aby wyświetlić, zostanie wyświetlona lista operacje w tle.

   > [!NOTE]
   > Jeśli włączono **trybu konserwacji** dla usługi, pamiętaj, aby ją wyłączyć za pomocą **akcji usługi** przycisku po zakończeniu operacji.

Aby skonfigurować usługę, należy użyć następujące czynności:

1. Z **pulpitu nawigacyjnego** lub **usług** wybierz opcję usługi.

2. Wybierz **Configs** kartę. Wyświetlana jest bieżąca konfiguracja. Wyświetlana jest również lista poprzedniej konfiguracji.

    ![Konfiguracje](./media/hdinsight-hadoop-manage-ambari/service-configs.png)

3. Użyj pól wyświetlane, aby zmodyfikować konfigurację, a następnie wybierz **zapisać**. Lub wybierz poprzednią konfigurację, a następnie wybierz **stać się bieżącym** do przywrócenia poprzedniego ustawienia.

## <a name="ambari-views"></a>Widoki Ambari

Widoki Ambari umożliwiają deweloperom Podłącz elementy interfejsu użytkownika do przy użyciu interfejsu użytkownika sieci Web Ambari [Framework widoków Ambari](https://cwiki.apache.org/confluence/display/AMBARI/Views). HDInsight udostępnia następujące widoki z typami klastra usługi Hadoop:

* Yarn menedżera kolejek: menedżera kolejek udostępnia prosty interfejs do wyświetlanie i modyfikowanie YARN kolejek.

* Widok hive: Hive View umożliwia uruchamianie zapytań Hive bezpośrednio z przeglądarki sieci web. Można zapisać zapytania, wyniki, Zapisz wyniki do magazynu klastra lub pobrać wyniki do systemu lokalnego. Aby uzyskać więcej informacji na korzystanie z widoków Hive, zobacz [Użyj widoki Hive z usługą HDInsight](hadoop/apache-hadoop-use-hive-ambari-view.md).

* Widok tez: Widok Tez umożliwia do lepszego zrozumienia i zoptymalizować zadania. Można wyświetlać informacje w sposób Tez zadania są wykonywane, i jakie zasoby są używane.

## <a name="unsupported-operations"></a>Nieobsługiwane operacje

Następujące operacje Ambari nie są obsługiwane w usłudze HDInsight:

* __Przenoszenie Usługa modułu zbierającego metryki__. Podczas wyświetlania informacji w usłudze modułu zbierającego metryki jest jedną z akcji, które są dostępne w menu Akcje usługi __modułu zbierającego Przenieś metryki__. To nie jest obsługiwane z usługą HDInsight.

## <a name="next-steps"></a>Kolejne kroki

Dowiedz się, jak używać [interfejsu API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md) z usługą HDInsight.
