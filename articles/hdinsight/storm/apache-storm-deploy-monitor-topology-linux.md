---
title: Wdrażanie i zarządzanie topologiami Apache Storm na HDInsight opartych na systemie Linux | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrażanie, monitorowanie i zarządzanie topologiami Apache Storm na HDInsight opartych na systemie Linux przy użyciu pulpitu nawigacyjnego Storm. Narzędzia usługi Hadoop dla programu Visual Studio.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: 35086e62-d6d8-4ccf-8cae-00073464a1e1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/22/2018
ms.author: larryfr
ms.openlocfilehash: 9dd63e1f3ec381dd99495ebc6193198611c76c88
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="deploy-and-manage-apache-storm-topologies-on-hdinsight"></a>Wdrażanie i zarządzanie nimi topologii Apache Storm w usłudze HDInsight

W tym dokumencie przedstawiono podstawy zarządzania i monitorowania topologii Storm działających w Storm w klastrach usługi HDInsight.

> [!IMPORTANT]
> Kroki opisane w tym artykule wymagają platformy Storm opartej na systemie Linux w klastrze usługi HDInsight. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows). 
>
> Aby uzyskać informacje dotyczące wdrażania i monitorowania topologii w usłudze HDInsight z systemu Windows, zobacz [wdrażanie i zarządzanie topologiami Apache Storm na HDInsight opartych na systemie Windows](apache-storm-deploy-monitor-topology.md)


## <a name="prerequisites"></a>Wymagania wstępne

* **Platformy Storm opartej na systemie Linux w klastrze usługi HDInsight**: zobacz [wprowadzenie do Apache Storm w usłudze HDInsight](apache-storm-tutorial-get-started-linux.md) instrukcje dotyczące tworzenia klastra

* (Opcjonalnie) **Znajomość protokołów SSH i SCP**: Aby uzyskać więcej informacji, zobacz [używanie SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Opcjonalnie) **Programu visual Studio**: zestaw Azure SDK 2.5.1 lub nowszej i narzędzi Data Lake Tools dla programu Visual Studio. Aby uzyskać więcej informacji, zobacz [rozpoczęcie pracy przy użyciu narzędzi Data Lake Tools dla programu Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    Jedna z następujących wersji programu Visual Studio:

  * Program Visual Studio 2012 z [aktualizacja 4](http://www.microsoft.com/download/details.aspx?id=39305)

  * Visual Studio 2013 z [aktualizacja 4](http://www.microsoft.com/download/details.aspx?id=44921) lub [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015 (dowolna wersja)

  * Visual Studio 2017 r (dowolna wersja). Narzędzia Data Lake Tools dla programu Visual Studio 2017 są instalowane jako część obciążenia Azure.

## <a name="submit-a-topology-visual-studio"></a>Przedstawia topologię: Visual Studio

Narzędzia HDInsight Tools może służyć do przesyłania C# i hybrydowych topologii do klastra Storm. Poniższe kroki użyć przykładowej aplikacji. Informacji o tworzeniu przy użyciu narzędzi HDInsight, zobacz [topologii opracowywania C# za pomocą narzędzi HDInsight Tools for Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

1. Jeśli nie został już zainstalowany najnowszą wersję narzędzi Data Lake Tools dla programu Visual Studio, zobacz [rozpoczęcie pracy przy użyciu narzędzi Data Lake Tools dla programu Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]
    > Narzędzia Data Lake Tools dla programu Visual Studio były dawniej nazywane narzędzia HDInsight Tools for Visual Studio.
    >
    > Narzędzia Data Lake Tools dla programu Visual Studio znajdują się w __obciążenia Azure__ dla programu Visual Studio 2017 r.

2. Otwórz program Visual Studio, wybierz **pliku** > **nowy** > **projektu**.

3. W **nowy projekt** okna dialogowego rozwiń **zainstalowana** > **szablony**, a następnie wybierz **HDInsight**. Wybierz z listy szablonów **próbki Storm**. W dolnej części okna dialogowego wpisz nazwę aplikacji.

    ![Obraz](./media/apache-storm-deploy-monitor-topology-linux/sample.png)

4. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt i wybierz **przesyłania do systemu Storm w usłudze HDInsight**.

   > [!NOTE]
   > Jeśli zostanie wyświetlony monit, wprowadź poświadczenia logowania dla subskrypcji platformy Azure. Jeśli masz więcej niż jedną subskrypcję, zaloguj się za jedną, która zawiera Storm w klastrze usługi HDInsight.

5. Wybierz Storm w klastrze usługi HDInsight z **klaster Storm** listy rozwijanej, a następnie wybierz **przesyłania**. Można monitorować, czy przekazywania działa prawidłowo, używając **dane wyjściowe** okna.

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>Przedstawia topologię: SSH, a polecenie Storm

1. Używanie protokołu SSH, aby połączyć się z klastrem usługi HDInsight. Zastąp **USERNAME** nazwę użytkownika logowania SSH. Zastąp **CLUSTERNAME** z nazwą klastra usługi HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Aby uzyskać więcej informacji o korzystaniu z protokołu SSH do nawiązania połączenia z klastrem usługi HDInsight, zobacz [używanie SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Użyj następującego polecenia, aby uruchomić przykładową topologię:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    To polecenie uruchamia w klastrze przykładową topologię WordCount. Ta topologia losowo generuje zdania, a następnie liczby wystąpień poszczególnych wyrazów w zdaniach.

   > [!NOTE]
   > Podczas przesyłania topologii do klastra przed użyciem polecenia `storm` należy skopiować plik JAR zawierający klaster. Aby skopiować plik do klastra, można użyć `scp` polecenia. Na przykład: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   >
   > Przykład WordCount i inne przykłady z projektu Storm Starter znajdują się już w klastrze w lokalizacji `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Przedstawia topologię: programowe

Można programowo wdrożenia topologii przy użyciu usługi Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) przykład aplikacji w języku Java pokazano, jak wdrożyć i uruchomić topologii za pośrednictwem usługi Nimbus.

## <a name="monitor-and-manage-visual-studio"></a>Monitorowanie i zarządzanie nimi: Visual Studio

Podczas przesyłania topologii za pomocą programu Visual Studio, **topologii Storm** zostanie wyświetlony widok. Wybierz topologię z listy, aby wyświetlić informacje o uruchomionej topologii.

![monitor programu Visual studio](./media/apache-storm-deploy-monitor-topology-linux/vsmonitor.png)

> [!NOTE]
> Można również wyświetlić **topologii Storm** z **Eksploratora serwera** rozwijając **Azure** > **HDInsight**, a następnie prawym przyciskiem myszy klaster Storm w usłudze HDInsight i wybierając **topologii Storm widoku**.

Wybierz kształt na elementach spout lub elementów bolt, aby wyświetlić informacje dotyczące tych składników. Otwiera nowe okno dla każdego wybranego elementu.

### <a name="deactivate-and-reactivate"></a>Najpierw dezaktywować i ponowne uaktywnianie

Dezaktywowanie topologii wstrzymuje go dopóki zostanie przerwana lub ponownej aktywacji. Aby wykonać te operacje, należy użyć __Dezaktywuj__ i __ponownie uaktywnić__ przycisków w górnej części __podsumowanie topologii__.

### <a name="rebalance"></a>Zbilansuj ponownie

Ponowne równoważenie topologii umożliwi systemowi Popraw równoległości topologii. Na przykład jeśli zmieniono klastra, aby dodać więcej uwagi, ponowne równoważenie umożliwia topologii zobaczyć nowe węzły.

Aby ponowne zrównoważenie topologii, użyj __Rebalance__ przycisk w górnej części __podsumowanie topologii__.

> [!WARNING]
> Ponowne równoważenie topologii najpierw dezaktywuje topologii, ponownie dystrybuuje pracowników równomiernie w ramach klastra, a następnie na koniec zwraca topologii do stanu sprzed ponowne równoważenie wystąpił. Dlatego jeśli topologii był aktywny, staje się aktywny ponownie. Jeśli została ona zdezaktywowana, pozostaje dezaktywowane.

### <a name="kill-a-topology"></a>Kasowanie topologii

Topologii STORM nadal działać do chwili zostały zatrzymane lub usunąć klastra. Aby zatrzymać topologii, użyj __Kill__ przycisk w górnej części __podsumowanie topologii__.

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>Monitorowanie i zarządzanie nimi: SSH, a polecenie Storm

`storm` Narzędzie umożliwia pracę z uruchomionymi topologiami z wiersza polecenia. Użyj `storm -h` pełną listę poleceń.

### <a name="list-topologies"></a>Topologie listy

Użyj następującego polecenia, aby wyświetlić listę wszystkich uruchomionymi topologiami:

    storm list

To polecenie zwraca informacje podobne do następującego tekstu:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Najpierw dezaktywować i ponowne uaktywnianie

Dezaktywowanie topologii wstrzymuje go dopóki zostanie przerwana lub ponownej aktywacji. Aby zdezaktywować i ponownie uaktywnić, użyj następującego polecenia:

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Kasowanie uruchomionej topologii

STORM topologii po uruchomieniu, nadal uruchomione aż do zatrzymania. Aby zatrzymać topologii, użyj następującego polecenia:

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>Zbilansuj ponownie

Ponowne równoważenie topologii umożliwi systemowi Popraw równoległości topologii. Na przykład jeśli zmieniono klastra, aby dodać więcej uwagi, ponowne równoważenie umożliwia topologii zobaczyć nowe węzły.

> [!WARNING]
> Ponowne równoważenie topologii najpierw dezaktywuje topologii, ponownie dystrybuuje pracowników równomiernie w ramach klastra, a następnie na koniec zwraca topologii do stanu sprzed ponowne równoważenie wystąpił. Dlatego jeśli topologii był aktywny, staje się aktywny ponownie. Jeśli została ona zdezaktywowana, pozostaje dezaktywowane.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>Monitorowanie i zarządzanie nimi: Storm interfejsu użytkownika

Interfejs użytkownika platformy Storm udostępnia interfejs sieci Web do pracy z uruchomionymi topologiami i jest zawarty w klastrze usługi HDInsight. Aby wyświetlić interfejsu użytkownika platformy Storm, użyj przeglądarki sieci web, aby otworzyć **https://CLUSTERNAME.azurehdinsight.net/stormui**, gdzie **CLUSTERNAME** jest nazwą klastra.

> [!NOTE]
> Jeśli zostanie wyświetlony monit o podanie nazwy użytkownika i hasła, wprowadź nazwę administratora klastra (admin) i hasło użyte podczas tworzenia klastra.

### <a name="main-page"></a>Strona główna

Strona główna interfejsu użytkownika Storm udostępnia następujące informacje:

* **Podsumowanie klastra**: podstawowe informacje dotyczące klastra Storm.
* **Topologia podsumowania**: Lista uruchomionych topologii. Użyj linków w tej sekcji, aby wyświetlić więcej informacji na temat określonych topologii.
* **Podsumowanie przełożonego**: informacje o przełożonego Storm.
* **Konfiguracja nimbus**: Nimbus konfiguracji klastra.

### <a name="topology-summary"></a>Topologia podsumowania

Kliknąć łącze z **podsumowanie topologii** sekcja zawiera następujące informacje o topologii:

* **Topologia podsumowania**: podstawowe informacje o topologii.
* **Akcje topologii**: Akcje zarządzania, które można wykonywać w topologii.

  * **Aktywuj**: wznowienie przetwarzania dezaktywowanej topologii.
  * **Dezaktywuj**: wstrzymanie uruchomionej topologii.
  * **Rebalance**: dopasowanie równoległości topologii. Po zmianie liczby węzłów w klastrze należy przeprowadzić ponowne równoważenie uruchomionych topologii. Ta operacja pozwala topologii dostosować równoległość kompensacji zwiększenia lub zmniejszenia liczby węzłów w klastrze.

    Aby uzyskać więcej informacji, zobacz temat <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Pojęcie równoległości w topologii Storm</a>.
  * **Kasowanie**: kończy topologii Storm po określonym czasie.
* **Statystyka topologii**: Statystyka topologii. Aby ustawić zakres czasu dla pozostałe wpisy na stronie, użyj linków w **okna** kolumny.
* **Spouts**: elementach spout, używany przez topologii. Użyj łącza w tej sekcji, aby wyświetlić więcej informacji o elementach spout określonych.
* **Bolts**: elementów bolt, używany przez topologii. Użyj łącza w tej sekcji, aby wyświetlić więcej informacji na temat określonych elementów bolt.
* **Topologia konfiguracji**: konfiguracji wybranego topologii.

### <a name="spout-and-bolt-summary"></a>Spout i Bolt — podsumowanie

Wybieranie spout z **Spouts** lub **Bolts** sekcje zawiera następujące informacje dotyczące wybranej pozycji:

* **Składnik podsumowania**: podstawowe informacje o spout lub bolt.
* **Spout/Bolt stats**: Statystyka spout lub bolt. Aby ustawić zakres czasu dla pozostałe wpisy na stronie, użyj linków w **okna** kolumny.
* **Wprowadź statystykę** (tylko dla elementów bolt): informacje o strumienie wejściowe, używane przez elementy bolt.
* **OUTPUT stats**: informacje o strumieni emitowane przez elementy spout lub bolt.
* **Modułów**: informacje na temat wystąpień spout lub bolt. Wybierz **portu** utworzony wpis dla określonego modułu wykonującego wyświetlić dziennik informacje diagnostyczne dla tego wystąpienia.
* **Błędy**: informacje o błędzie spout lub bolt.

## <a name="monitor-and-manage-rest-api"></a>Monitorowanie i zarządzanie nimi: interfejsu API REST

Interfejsu użytkownika platformy Storm jest oparty na interfejsie API REST, dlatego można wykonać podobne do zarządzania i monitorowania funkcji za pomocą interfejsu API REST. Można użyć interfejsu API REST do tworzenia niestandardowych narzędzi do zarządzania i monitorowania topologii Storm.

Aby uzyskać więcej informacji, zobacz [interfejsu API REST interfejsu użytkownika Storm](http://storm.apache.org/releases/0.9.6/STORM-UI-REST-API.html). Następujące informacje są specyficzne dla z systemu Apache Storm w usłudze HDInsight przy użyciu interfejsu API REST.

> [!IMPORTANT]
> Interfejs API REST Storm nie jest publicznie dostępna przez internet i muszą być dostępne przy użyciu tunelu SSH do węzła głównego klastra usługi HDInsight. Aby uzyskać informacje na temat tworzenia i używania tunelu SSH, zobacz [Użyj SSH Tunneling można uzyskać dostępu do interfejsu użytkownika sieci web Ambari, ResourceManager, JobHistory, NameNode, Oozie i innych sieci web UI](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Podstawowy identyfikator URI

Podstawowy identyfikator URI dla interfejsu API REST w klastrach HDInsight opartych na systemie Linux jest dostępna w węźle głównym na **https://HEADNODEFQDN:8744/api/v1/**. Nazwa węzła głównego domeny jest generowany podczas tworzenia klastra i nie jest statyczne.

W pełni kwalifikowaną nazwę (FQDN) dla węzła głównego klastra można znaleźć na kilka różnych sposobów:

* **W sesji SSH**: Użyj polecenia `headnode -f` w sesji SSH do klastra.
* **Z sieci Ambari Web**: Wybierz **usług** w górnej części strony, następnie wybierz **Storm**. Z **Podsumowanie** wybierz opcję **serwera interfejsu użytkownika Storm**. Nazwa FQDN węzła, który jest hostem interfejsu użytkownika platformy Storm i interfejsu API REST jest wyświetlany w górnej części strony.
* **Z interfejsu API REST Ambari**: Użyj polecenia `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` można pobrać informacji na temat interfejsu użytkownika platformy Storm i interfejsu API REST są uruchomione na węzeł. Zastąp **CLUSTERNAME** z nazwą klastra. Po wyświetleniu monitu wprowadź hasło dla konta logowania (Administrator). W odpowiedzi wpis "host_name" zawiera nazwę FQDN węzła.

### <a name="authentication"></a>Authentication

Żądania do interfejsu API REST muszą używać **uwierzytelnianie podstawowe**, aby użyć nazwy administratora klastra usługi HDInsight i hasło.

> [!NOTE]
> Ponieważ uwierzytelnianie podstawowe jest wysyłany przy użyciu zwykłego tekstu, należy **zawsze** zabezpieczenia komunikacji z klastrem przy użyciu protokołu HTTPS.

### <a name="return-values"></a>Wartości zwracane

Można używać z w klastrze mogą być tylko informacje zwrócone z interfejsu API REST. Na przykład Pełna nazwa domeny (FQDN) dla serwerów dozorcy zwracane nie jest dostępny z Internetu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [topologie oparte na opracowanie Java za pomocą programu Maven](apache-storm-develop-java-topology.md).

Aby uzyskać listę więcej przykładowe topologie, zobacz [przykładowe topologie dla systemu Storm w usłudze HDInsight](apache-storm-example-topology.md).
