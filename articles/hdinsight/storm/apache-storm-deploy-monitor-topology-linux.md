---
title: Wdrażanie topologii Apache Storm w usłudze Azure HDInsight i zarządzanie
description: Dowiedz się, jak wdrażanie, monitorowanie i zarządzanie topologii usługi Apache Storm na HDInsight opartych na systemie Linux przy użyciu pulpitu nawigacyjnego Storm. Użyj narzędzi usługi Hadoop dla programu Visual Studio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: 8b27ad34bdc6fcbd7a1eb46515fbf33c96d02528
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125242"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Wdrażanie topologii Apache Storm w usłudze Azure HDInsight i zarządzanie 

W tym dokumencie przedstawiono podstawy monitorowania i zarządzania nim [Apache Storm](https://storm.apache.org/) topologii uruchamianych w systemie Storm w klastrach HDInsight.

> [!IMPORTANT]  
> Kroki opisane w tym artykule wymaga platformy Storm opartej na systemie Linux w klastrze HDInsight. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows). 
>


## <a name="prerequisites"></a>Wymagania wstępne

* **Platformy Storm opartej na systemie Linux w klastrze HDInsight**: zobacz [Rozpoczynanie pracy z usługą Apache Storm w HDInsight](apache-storm-tutorial-get-started-linux.md) Aby uzyskać instrukcje dotyczące tworzenia klastra

* (Opcjonalnie) **Znajomość protokołów SSH i SCP**: Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Opcjonalnie) **Programu visual Studio**: Zestaw Azure SDK 2.5.1 lub nowszej i narzędzi Data Lake Tools for Visual Studio. Aby uzyskać więcej informacji, zobacz [rozpoczęcie korzystania z narzędzi Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    Jeden z następujących wersji programu Visual Studio:

  * Program Visual Studio 2012 z aktualizacją Update 4

  * Visual Studio 2013 z aktualizacją Update 4 lub [Visual Studio 2013 Community](https://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Program Visual Studio 2015 (w każdej wersji)

  * Program Visual Studio 2017 (dowolna wersja). Narzędzia Data Lake Tools for Visual Studio 2017 są instalowane jako część obciążenia platformy Azure.

## <a name="submit-a-topology-visual-studio"></a>Przesyłanie topologii: Visual Studio

Narzędzia HDInsight może służyć do przesyłania topologii C# lub hybrydowe z klastrem Storm. Użyto przykładowej aplikacji. Aby uzyskać informacji o tworzeniu na temat korzystania z narzędzi HDInsight, zobacz [topologii opracowywanie języka C# za pomocą narzędzi HDInsight dla programu Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

1. Jeśli nie już zainstalowano najnowszą wersję narzędzi Data Lake tools for Visual Studio, zobacz [rozpoczęcie korzystania z narzędzi Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > Narzędzia Data Lake Tools for Visual Studio były dawniej nazywane narzędzi HDInsight Tools for Visual Studio.
    >
    > Narzędzia Data Lake Tools for Visual Studio są objęte __obciążenie platformy Azure__ dla programu Visual Studio 2017.

2. Otwórz program Visual Studio, wybierz **pliku** > **New** > **projektu**.

3. W **nowy projekt** okna dialogowego rozwiń **zainstalowane** > **szablony**, a następnie wybierz pozycję **HDInsight**. Z listy szablonów wybierz **przykładowe Storm**. W dolnej części okna dialogowego wpisz nazwę dla aplikacji.

    ![image](./media/apache-storm-deploy-monitor-topology-linux/sample.png)

4. W **Eksploratora rozwiązań**, kliknij prawym przyciskiem myszy projekt i wybierz **Prześlij do systemu Storm w HDInsight**.

   > [!NOTE]  
   > Po wyświetleniu monitu wprowadź poświadczenia logowania dla subskrypcji platformy Azure. Jeśli masz więcej niż jedną subskrypcję, zaloguj się do tego, który zawiera Storm w klastrze HDInsight.

5. Wybierz usługi Storm w klastrze HDInsight z **klastra Storm** listy rozwijanej, a następnie wybierz **przesyłania**. Można monitorować, czy przesyłanie zakończy się za pomocą **dane wyjściowe** okna.

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>Przesyłanie topologii: SSH i polecenie systemu Storm

1. Za pomocą protokołu SSH Połącz się z klastrem HDInsight. Zastąp **USERNAME** nazwę logowania użytkownika protokołu SSH. Zastąp **CLUSTERNAME** nazwą klastra usługi HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Aby uzyskać więcej informacji na temat nawiązywania połączenia z klastrem HDInsight przy użyciu protokołu SSH, zobacz [użycia protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Użyj następującego polecenia, aby uruchomić przykładową topologię:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    To polecenie uruchamia w klastrze przykładową topologię WordCount. Ta topologia przykład obejmuje losowe wygenerowanie zdań, a następnie zlicza wystąpienia wystąpień poszczególnych wyrazów w zdaniach.

   > [!NOTE]  
   > Podczas przesyłania topologii do klastra przed użyciem polecenia `storm` należy skopiować plik JAR zawierający klaster. Aby skopiować plik do klastra, można użyć `scp` polecenia. Na przykład: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   >
   > Przykład WordCount i inne przykłady z projektu Storm Starter znajdują się już w klastrze w lokalizacji `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Przesyłanie topologii: programowe

Można programowo wdrażanie topologii przy użyciu usługi Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology) przykład aplikacji Java, która pokazuje, jak wdrożyć i uruchomić topologię za pośrednictwem usługi Nimbus.

## <a name="monitor-and-manage-visual-studio"></a>Monitorowanie i zarządzanie nimi: Visual Studio

Podczas przesyłania topologii przy użyciu programu Visual Studio **topologii Storm** zostanie wyświetlony widok. Wybierz topologię z listy, aby wyświetlić informacje o uruchomionej topologii.

![visual studio monitor](./media/apache-storm-deploy-monitor-topology-linux/vsmonitor.png)

> [!NOTE]  
> Można również wyświetlić **topologii Storm** z **Eksploratora serwera** , rozwijając **Azure** > **HDInsight**, a następnie Kliknij prawym przyciskiem myszy platformy Storm w klastrze HDInsight, a następnie wybierając **wyświetl topologie Storm**.

Wybierz kształt elementy spout lub bolt, aby wyświetlić informacje o tych składników. Zostanie otwarte nowe okno, dla każdego wybranego elementu.

### <a name="deactivate-and-reactivate"></a>Dezaktywuj i ponownie aktywować

Dezaktywowanie topologii wstrzymuje jej dopóki zostanie zakończone, lub ponownie aktywować. Aby wykonać te operacje, użyj __Dezaktywuj__ i __ponownie uaktywnić__ przycisków w górnej części __podsumowanie topologii__.

### <a name="rebalance"></a>Zbilansuj ponownie

Ponowne równoważenie topologii umożliwia systemowi uzupełniać równoległości topologii. Umożliwia na przykład jeśli zmieniono rozmiar klastra, aby dodać więcej uwagi, ponowne równoważenie topologii zobaczyć nowe węzły.

Aby ponowne zrównoważenie topologii, należy użyć __ponowne zrównoważenie__ znajdujący się u góry __podsumowanie topologii__.

> [!WARNING]  
> Ponowne równoważenie topologii najpierw dezaktywuje topologii, dystrybuuje pracowników równomiernie w klastrze, a następnie wreszcie zwraca topologii do stanu, w jakim był, zanim ponowne równoważenie wystąpił. Dlatego jeśli topologia była aktywna, staje się aktywny ponownie. Jeśli Dezaktywowano, pozostaje dezaktywowane.

### <a name="kill-a-topology"></a>Zabić topologię

Topologie STORM nadal uruchomione, dopóki nie pozostaną one zatrzymane lub klaster jest usuwany. Aby zatrzymać topologii, użyj __Kill__ znajdujący się u góry __podsumowanie topologii__.

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>Monitorowanie i zarządzanie nimi: SSH i polecenie systemu Storm

`storm` Narzędzie umożliwia pracę z uruchomionymi topologiami z wiersza polecenia. Użyj `storm -h` pełną listę poleceń.

### <a name="list-topologies"></a>Topologie listy

Użyj następującego polecenia, aby wyświetlić listę wszystkich działające topologie:

    storm list

To polecenie zwraca informacje podobne do następującego tekstu:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Dezaktywuj i ponownie aktywować

Dezaktywowanie topologii wstrzymuje jej dopóki zostanie zakończone, lub ponownie aktywować. Aby dezaktywować i ponownie aktywować, użyj następującego polecenia:

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Kill uruchomionej topologii

System STORM topologies, po uruchomieniu, nadal uruchomione do czasu ich zatrzymania. Aby zatrzymać topologii, użyj następującego polecenia:

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>Zbilansuj ponownie

Ponowne równoważenie topologii umożliwia systemowi uzupełniać równoległości topologii. Umożliwia na przykład jeśli zmieniono rozmiar klastra, aby dodać więcej uwagi, ponowne równoważenie topologii zobaczyć nowe węzły.

> [!WARNING]  
> Ponowne równoważenie topologii najpierw dezaktywuje topologii, dystrybuuje pracowników równomiernie w klastrze, a następnie wreszcie zwraca topologii do stanu, w jakim był, zanim ponowne równoważenie wystąpił. Dlatego jeśli topologia była aktywna, staje się aktywny ponownie. Jeśli Dezaktywowano, pozostaje dezaktywowane.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>Monitorowanie i zarządzanie nimi: Interfejs użytkownika platformy STORM

Interfejs użytkownika platformy Storm udostępnia interfejs sieci Web do pracy z uruchomionymi topologiami i jest zawarty w klastrze usługi HDInsight. Aby wyświetlić interfejs użytkownika platformy Storm, użyj przeglądarki sieci web, aby otworzyć **https://CLUSTERNAME.azurehdinsight.net/stormui**, gdzie **CLUSTERNAME** jest nazwą klastra.

> [!NOTE]  
> Jeśli zostanie wyświetlony monit o podanie nazwy użytkownika i hasła, wprowadź nazwę administratora klastra (admin) i hasło użyte podczas tworzenia klastra.

### <a name="main-page"></a>Strona główna

Główna strona interfejsu użytkownika platformy Storm udostępnia następujące informacje:

* **Podsumowanie klastra**: Podstawowe informacje o klaster Storm.
* **Podsumowanie topologii**: Listy uruchomionych topologii. Użyj linków w tej sekcji, aby wyświetlić więcej informacji na temat określonych topologii.
* **Nadzorca podsumowania**: Informacje na temat nadzorcy systemu Storm.
* **Konfiguracja nimbus**: Nimbus konfigurację klastra.

### <a name="topology-summary"></a>Podsumowanie topologii

Link z wybraniu **podsumowanie topologii** sekcja wyświetla następujące informacje na temat topologii:

* **Podsumowanie topologii**: Podstawowe informacje na temat topologii.
* **Akcje topologii**: Akcje zarządzania, które można wykonywać w odniesieniu do topologii.

  * **Aktywuj**: Wznowienie przetwarzania dezaktywowanej topologii.
  * **Dezaktywuj**: Wstrzymanie uruchomionej topologii.
  * **Rebalance**: To dostosować równoległość topologii. Po zmianie liczby węzłów w klastrze należy przeprowadzić ponowne równoważenie uruchomionych topologii. Ta operacja umożliwia topologię, aby dostosować równoległość topologii w celu kompensacji zwiększenia lub zmniejszenia liczby węzłów w klastrze.

    Aby uzyskać więcej informacji, zobacz <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">pojęcie równoległości w topologii Apache Storm</a>.
  * **Kill**: Kończy topologii Storm po określonym czasie.
* **Topology stats**: Statystyka topologii. Aby ustawić przedział czasu dla pozostałych wpisów na stronie, użyj linków w **okna** kolumny.
* **Spouts**: Elementy spout używane przez topologię. Użyj linków w tej sekcji, aby wyświetlić więcej informacji na temat określonych elementów spout.
* **Bolts**: Elementy bolt używane przez topologię. Użyj linków w tej sekcji, aby wyświetlić więcej informacji na temat określonych elementów bolt.
* **Konfiguracja topologii**: Konfiguracja wybrana topologia.

### <a name="spout-and-bolt-summary"></a>Spout i Bolt — podsumowanie

Spout z wybraniu **Spouts** lub **Bolts** sekcje są wyświetlane następujące informacje dotyczące wybranej pozycji:

* **Podsumowanie składników**: Podstawowe informacje o spout lub bolt.
* **Spout/Bolt stats**: Statystyka spout lub bolt. Aby ustawić przedział czasu dla pozostałych wpisów na stronie, użyj linków w **okna** kolumny.
* **Dane wejściowe statystyki** (tylko dla elementów bolt): Informacje na temat strumienie wejściowe, używane przez element bolt.
* **OUTPUT stats**: Informacje na temat strumienie emitowane przez spout lub bolt.
* **Executors**: Informacje na temat wystąpień elementu spout lub bolt. Wybierz **portu** wpis dla określonych wykonawcy wyświetlić dziennik informacji diagnostycznych utworzone dla tego wystąpienia.
* **Błędy**: Wszelkie informacje o błędzie dla spout lub bolt.

## <a name="monitor-and-manage-rest-api"></a>Monitorowanie i zarządzanie nimi: Interfejs API REST

Interfejs użytkownika platformy Storm bazuje na usłudze interfejsu API REST, aby można było wykonywać podobne do zarządzania i monitorowania funkcjonalności za pomocą interfejsu API REST. Interfejs API REST umożliwia tworzenie niestandardowych narzędzi do zarządzania i monitorowania topologii Storm.

Aby uzyskać więcej informacji, zobacz [Apache Storm Interfejsu REST API](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). Następujące informacje są specyficzne dla przy użyciu platformy Apache Storm w HDInsight przy użyciu interfejsu API REST.

> [!IMPORTANT]  
> Interfejs API REST platformy Storm nie jest publicznie dostępna w Internecie i muszą być dostępne przy użyciu tunelu SSH z węzłem głównym klastra HDInsight. Aby uzyskać informacje na temat tworzenia i używania tunelu SSH, zobacz [użycie tunelowania SSH do uzyskania dostępu do systemu Apache Ambari web UI, ResourceManager, JobHistory, NameNode, Apache Oozie i innych web UI](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Podstawowy identyfikator URI

Podstawowy identyfikator URI dla interfejsu API REST w klastrach HDInsight opartych na systemie Linux jest dostępna w węźle głównym w **https:\//HEADNODEFQDN:8744/api/v1/**. Nazwa domeny węzła głównego jest generowany podczas tworzenia klastra i nie jest statyczne.

W pełni kwalifikowana nazwa domeny (FQDN) węzła głównego klastra można znaleźć na kilka różnych sposobów:

* **W sesji SSH**: Użyj polecenia `headnode -f` w sesji SSH do klastra.
* **Z sieci Web Ambari**: Wybierz **usług** w górnej części strony, następnie wybierz pozycję **Storm**. Z **Podsumowanie** zaznacz **Server interfejsu użytkownika Storm**. Nazwa FQDN węzła, który hostuje interfejs użytkownika platformy Storm i interfejsu API REST jest wyświetlany w górnej części strony.
* **Z interfejsu API REST Ambari**: Użyj polecenia `curl -u admin -G "https:\//CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` można pobrać informacji na temat węzeł, w którym są uruchomione interfejs użytkownika platformy Storm i interfejsu API REST. Zastąp **CLUSTERNAME** nazwą klastra. Po wyświetleniu monitu wprowadź hasło dla konta logowania (administratora). W odpowiedzi wpis "host_name" zawiera nazwę FQDN węzła.

### <a name="authentication"></a>Authentication

Żądania interfejsu API REST muszą używać **uwierzytelnianie podstawowe**, dlatego użyto nazwy administratora klastra HDInsight i hasło.

> [!NOTE]  
> Ponieważ uwierzytelnianie podstawowe są wysyłane przy użyciu zwykłego tekstu, należy **zawsze** bezpieczna komunikacja przy użyciu klastra przy użyciu protokołu HTTPS.

### <a name="return-values"></a>Zwracane wartości

Można używać z w ramach klastra może być tylko informacje zwrócone z interfejsu API REST. Na przykład w pełni kwalifikowana nazwa domeny (FQDN) dla [Apache ZooKeeper](https://zookeeper.apache.org/) serwery nie jest dostępny z Internetu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [opartych na języku Java z opracowywanie topologii przy użyciu narzędzia Apache Maven](apache-storm-develop-java-topology.md).

Aby uzyskać listę więcej przykładowe topologie, zobacz [przykładowe topologie dla Storm Apache na HDInsight](apache-storm-example-topology.md).
