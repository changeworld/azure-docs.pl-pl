---
title: Wdrażanie topologii Apache Storm w usłudze Azure HDInsight i zarządzanie nimi
description: Informacje na temat wdrażania i monitorowania topologii Apache Storm oraz zarządzania nimi przy użyciu pulpitu nawigacyjnego burzy w usłudze HDInsight opartej na systemie Linux. Użyj narzędzi Hadoop dla programu Visual Studio.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/22/2018
ms.openlocfilehash: 1c219c85836eb4730fa90918385555c433a12449
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70915086"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Wdrażanie topologii Apache Storm w usłudze Azure HDInsight i zarządzanie nimi 

W tym dokumencie przedstawiono podstawowe informacje dotyczące zarządzania i monitorowania topologii [Apache Storm](https://storm.apache.org/) działających w ramach burzy w klastrach usługi HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Storm w usłudze HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybierz pozycję **burza** dla **typu klastra**.


* Obowiązkowe Znajomość protokołów SSH i SCP: Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

* Obowiązkowe Program Visual Studio: Zestaw Azure SDK 2.5.1 lub nowszy oraz Data Lake Tools for Visual Studio. Aby uzyskać więcej informacji, zobacz [Rozpoczynanie pracy przy użyciu narzędzi Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    Jedna z następujących wersji programu Visual Studio:

  * Visual Studio 2012 z aktualizacją Update 4

  * Visual Studio 2013 z aktualizacją Update 4 lub [Visual Studio 2013 Community](https://go.microsoft.com/fwlink/?LinkId=517284)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/)

  * Visual Studio 2015 (dowolna wersja)

  * Program Visual Studio 2017 (dowolna wersja). Narzędzia Data Lake Tools for Visual Studio 2017 są instalowane w ramach obciążeń platformy Azure.

## <a name="submit-a-topology-visual-studio"></a>Przesyłanie topologii: Visual Studio

Narzędzia usługi HDInsight mogą służyć do przesyłania C# lub topologii hybrydowej do klastra burzy. W poniższych krokach użyto przykładowej aplikacji. Aby uzyskać informacje na temat tworzenia przy użyciu narzędzi HDInsight, [Zobacz C# tworzenie topologii przy użyciu narzędzi HDInsight Tools for Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

1. Jeśli nie zainstalowano jeszcze najnowszej wersji narzędzi Data Lake Tools for Visual Studio, zobacz [Rozpoczynanie pracy przy użyciu narzędzi Data Lake Tools for Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

    > [!NOTE]  
    > Narzędzia Data Lake Tools for Visual Studio były wcześniej nazywane narzędziami HDInsight Tools for Visual Studio.
    >
    > Narzędzia Data Lake Tools for Visual Studio są zawarte w __obciążeniu platformy Azure__ dla programu visual Studio 2017.

2. Otwórz program Visual Studio, wybierz pozycję **plik** > **Nowy** > **projekt**.

3. W oknie dialogowym **Nowy projekt** rozwiń węzeł **zainstalowane** > **Szablony**, a następnie wybierz pozycję **HDInsight**. Z listy szablonów wybierz pozycję **przykład burzy**. W dolnej części okna dialogowego wpisz nazwę aplikacji.

    ![image](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

4. W **Eksplorator rozwiązań**kliknij prawym przyciskiem myszy projekt, a następnie wybierz pozycję **Prześlij do burzy w usłudze HDInsight**.

   > [!NOTE]  
   > Jeśli zostanie wyświetlony monit, wprowadź poświadczenia logowania dla subskrypcji platformy Azure. Jeśli masz więcej niż jedną subskrypcję, zaloguj się do niej, która zawiera swoją burzę w klastrze usługi HDInsight.

5. Wybierz pozycję burza w klastrze usługi HDInsight z listy rozwijanej **klaster burzy** , a następnie wybierz pozycję **Prześlij**. Możesz monitorować, czy przesyłanie zakończy się pomyślnie przy użyciu okna **danych wyjściowych** .

## <a name="submit-a-topology-ssh-and-the-storm-command"></a>Przesyłanie topologii: SSH i burza — polecenie

1. Połącz się z klastrem usługi HDInsight przy użyciu protokołu SSH. Zastąp nazwę **username** nazwą logowania SSH. Zastąp ciąg **ClusterName** nazwą klastra usługi HDInsight:

        ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

    Aby uzyskać więcej informacji na temat łączenia się z klastrem usługi HDInsight przy użyciu protokołu SSH, zobacz [Używanie protokołu SSH z usługą HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Użyj następującego polecenia, aby uruchomić przykładową topologię:

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount

    To polecenie uruchamia w klastrze przykładową topologię WordCount. Ta topologia losowo generuje zdania, a następnie zlicza wystąpienia każdego wyrazu w zdaniach.

   > [!NOTE]  
   > Podczas przesyłania topologii do klastra przed użyciem polecenia `storm` należy skopiować plik JAR zawierający klaster. Aby skopiować plik do klastra, można użyć `scp` polecenia. Na przykład: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
   >
   > Przykład WordCount i inne przykłady z projektu Storm Starter znajdują się już w klastrze w lokalizacji `/usr/hdp/current/storm-client/contrib/storm-starter/`.

## <a name="submit-a-topology-programmatically"></a>Przesyłanie topologii: programowe

Można programowo wdrożyć topologię przy użyciu usługi Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology)zawiera przykładową aplikację Java, która demonstruje sposób wdrażania i uruchamiania topologii za pomocą usługi Nimbus.

## <a name="monitor-and-manage-visual-studio"></a>Monitorowanie i zarządzanie: Visual Studio

Gdy topologia zostanie przesłana przy użyciu programu Visual Studio, zostanie wyświetlony widok **topologie burzy** . Wybierz topologię z listy, aby wyświetlić informacje o uruchomionej topologii.

![Monitor programu Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> Można również wyświetlić **topologie burzy** w **Eksplorator serwera** , rozszerzając **usługę Azure** > **HDInsight**, a następnie klikając prawym przyciskiem myszy burzę w klastrze usługi HDInsight i wybierając pozycję **Wyświetl topologie burzy**.

Wybierz kształt elementy Spout lub piorunów, aby wyświetlić informacje o tych składnikach. Zostanie otwarte nowe okno dla każdego wybranego elementu.

### <a name="deactivate-and-reactivate"></a>Dezaktywuj i aktywuj ponownie

Dezaktywacja topologii wstrzymuje ją do momentu jego zabicia lub ponownej aktywacji. Aby wykonać te operacje, użyj przycisków __Dezaktywuj__ i __Uaktywnij ponownie__ w górnej części __podsumowania topologii__.

### <a name="rebalance"></a>Zbilansuj ponownie

Ponowne równoważenie topologii umożliwia systemowi skorygowanie równoległości topologii. Na przykład jeśli Zmieniono rozmiar klastra w celu dodania kolejnych notatek, ponowne zrównoważenie umożliwia topologii wyświetlanie nowych węzłów.

Aby ponownie zrównoważyć topologię, użyj przycisku __Zrównoważ__ w górnej części __podsumowania topologii__.

> [!WARNING]  
> Ponowne równoważenie topologii najpierw dezaktywuje topologię, a następnie ponownie dystrybuuje procesy robocze w klastrze, a następnie zwraca topologię do stanu, w którym znajdowała się przed ponownym zrównoważeniem. Tak więc, jeśli topologia była aktywna, zostanie ponownie uaktywniona. Jeśli została zdezaktywowana, pozostanie zdezaktywowana.

### <a name="kill-a-topology"></a>Kasuj topologię

Topologie burzy są nadal uruchomione, dopóki nie zostaną zatrzymane lub klaster zostanie usunięty. Aby zatrzymać topologię, użyj przycisku __zabicia__ w górnej części __podsumowania topologii__.

## <a name="monitor-and-manage-ssh-and-the-storm-command"></a>Monitorowanie i zarządzanie: SSH i burza — polecenie

`storm` Narzędzie pozwala pracować z uruchamianiem topologii z poziomu wiersza polecenia. Użyj `storm -h` , aby uzyskać pełną listę poleceń.

### <a name="list-topologies"></a>Wyświetl topologie

Użyj następującego polecenia, aby wyświetlić listę wszystkich uruchomionych topologii:

    storm list

To polecenie zwraca informacje podobne do następującego tekstu:

    Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
    -------------------------------------------------------------------
    WordCount            ACTIVE     29         2            263

### <a name="deactivate-and-reactivate"></a>Dezaktywuj i aktywuj ponownie

Dezaktywacja topologii wstrzymuje ją do momentu jego zabicia lub ponownej aktywacji. Użyj następującego polecenia, aby dezaktywować i ponownie aktywować:

    storm Deactivate TOPOLOGYNAME

    storm Activate TOPOLOGYNAME

### <a name="kill-a-running-topology"></a>Kasuj uruchomioną topologię

Topologie burzy, po uruchomieniu, kontynuują działanie do momentu zatrzymania. Aby zatrzymać topologię, użyj następującego polecenia:

    storm kill TOPOLOGYNAME

### <a name="rebalance"></a>Zbilansuj ponownie

Ponowne równoważenie topologii umożliwia systemowi skorygowanie równoległości topologii. Na przykład jeśli Zmieniono rozmiar klastra w celu dodania kolejnych notatek, ponowne zrównoważenie umożliwia topologii wyświetlanie nowych węzłów.

> [!WARNING]  
> Ponowne równoważenie topologii najpierw dezaktywuje topologię, a następnie ponownie dystrybuuje procesy robocze w klastrze, a następnie zwraca topologię do stanu, w którym znajdowała się przed ponownym zrównoważeniem. Tak więc, jeśli topologia była aktywna, zostanie ponownie uaktywniona. Jeśli została zdezaktywowana, pozostanie zdezaktywowana.

    storm rebalance TOPOLOGYNAME

## <a name="monitor-and-manage-storm-ui"></a>Monitorowanie i zarządzanie: Interfejs użytkownika burzy

Interfejs użytkownika platformy Storm udostępnia interfejs sieci Web do pracy z uruchomionymi topologiami i jest zawarty w klastrze usługi HDInsight. Aby wyświetlić interfejs użytkownika burzy, użyj przeglądarki **https://CLUSTERNAME.azurehdinsight.net/stormui** sieci Web, gdzie **ClusterName** jest nazwą klastra.

> [!NOTE]  
> Jeśli zostanie wyświetlony monit o podanie nazwy użytkownika i hasła, wprowadź nazwę administratora klastra (admin) i hasło użyte podczas tworzenia klastra.

### <a name="main-page"></a>Strona główna

Na stronie głównej interfejsu użytkownika burzy są dostępne następujące informacje:

* **Podsumowanie klastra**: Podstawowe informacje o klastrze burzowym.
* **Podsumowanie topologii**: Lista uruchomionych topologii. Skorzystaj z linków w tej sekcji, aby wyświetlić więcej informacji na temat określonych topologii.
* **Podsumowanie opiekuna**: Informacje o Inspektorze burzy.
* **Konfiguracja Nimbus**: Nimbus konfigurację klastra.

### <a name="topology-summary"></a>Podsumowanie topologii

Wybranie linku z sekcji **Podsumowanie topologii** zawiera następujące informacje o topologii:

* **Podsumowanie topologii**: Podstawowe informacje o topologii.
* **Akcje topologii**: Akcje zarządzania, które można wykonać dla topologii.

  * **Aktywuj**: Wznawia przetwarzanie zdezaktywowanej topologii.
  * **Dezaktywuj**: Wstrzymuje uruchomioną topologię.
  * Ponowne **równoważenie**: Dostosowuje równoległość topologii. Po zmianie liczby węzłów w klastrze należy przeprowadzić ponowne równoważenie uruchomionych topologii. Ta operacja umożliwia topologii dostosowanie równoległości w celu zrekompensowania zwiększonej lub zmniejszonej liczby węzłów w klastrze.

    Aby uzyskać więcej informacji, zobacz <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Omówienie równoległości topologii Apache Storm</a>.
  * **Kasuj**: Kończy topologię burzy po określonym limicie czasu.
* **Statystyka topologii**: Statystyka topologii. Aby ustawić ramy czasowe dla pozostałych wpisów na stronie, użyj linków w kolumnie **okno** .
* **Elementy Spout**: Elementy Spout używany przez topologię. Skorzystaj z linków w tej sekcji, aby wyświetlić więcej informacji na temat określonych elementy Spout.
* **Pioruny**: Pioruny używane przez topologię. Skorzystaj z linków w tej sekcji, aby wyświetlić więcej informacji na temat określonych piorunów.
* **Konfiguracja topologii**: Konfiguracja wybranej topologii.

### <a name="spout-and-bolt-summary"></a>Elementu Spout i piorun — podsumowanie

Wybranie elementu Spout z sekcji **elementy Spout** lub **pioruns** wyświetla następujące informacje dotyczące wybranego elementu:

* **Podsumowanie składnika**: Podstawowe informacje na temat elementu Spout lub pioruna.
* **Statystyka elementu Spout/piorun**: Statystyka elementu Spout lub pioruna. Aby ustawić ramy czasowe dla pozostałych wpisów na stronie, użyj linków w kolumnie **okno** .
* **Statystyki wejściowe** (tylko Piorun): Informacje o strumieniach wejściowych używanych przez pioruna.
* **Statystyka wyjściowa**: Informacje o strumieniach emitowanych przez elementu Spout lub piorun.
* **Wykonawcy**: Informacje o wystąpieniach elementu Spout lub piorunów. Wybierz wpis **portu** dla określonego wykonawcy, aby wyświetlić dziennik informacji diagnostycznych tworzonych dla tego wystąpienia.
* **Błędy**: Wszystkie informacje o błędzie dla elementu Spout lub pioruna.

## <a name="monitor-and-manage-rest-api"></a>Monitorowanie i zarządzanie: Interfejs API REST

Interfejs użytkownika burzy jest oparty na interfejsie API REST, dzięki czemu można wykonywać podobne funkcje zarządzania i monitorowania za pomocą interfejsu API REST. Interfejsu API REST można użyć do tworzenia niestandardowych narzędzi do zarządzania i monitorowania topologii burzy.

Aby uzyskać więcej informacji, zobacz Interfejs [API REST interfejsu użytkownika Apache Storm](https://storm.apache.org/releases/current/STORM-UI-REST-API.html). Poniższe informacje dotyczą korzystania z interfejsu API REST z usługą Apache Storm w usłudze HDInsight.

> [!IMPORTANT]  
> Interfejs API REST burzy nie jest publicznie dostępny przez Internet i musi być dostępny przy użyciu tunelu SSH do węzła głównego klastra usługi HDInsight. Aby uzyskać informacje na temat tworzenia i używania tunelu SSH, zobacz [Używanie tunelowania SSH do uzyskiwania dostępu do interfejsu użytkownika usługi Apache Ambari Web, ResourceManager, JobHistory, NameNode, Apache Oozie i innych interfejsów użytkownika sieci Web](../hdinsight-linux-ambari-ssh-tunnel.md).

### <a name="base-uri"></a>Podstawowy identyfikator URI

Podstawowy identyfikator URI interfejsu API REST w klastrach usługi HDInsight opartych na systemie Linux jest dostępny w węźle głównym pod adresem **https\/:/HEADNODEFQDN: 8744/API/V1/** . Nazwa domeny węzła głównego jest generowana podczas tworzenia klastra i nie jest statyczna.

W pełni kwalifikowaną nazwę domeny (FQDN) węzła głównego klastra można znaleźć na kilka różnych sposobów:

* **Z poziomu sesji SSH**: Użyj polecenia `headnode -f` z sesji SSH do klastra.
* **Z witryny sieci Web Ambari**: Wybierz pozycję **usługi** w górnej części strony, a następnie wybierz pozycję **burza**. Na karcie **Podsumowanie** wybierz pozycję **serwer interfejsu użytkownika burzy**. W górnej części strony jest wyświetlana nazwa FQDN węzła, który hostuje interfejs użytkownika burzy i interfejs API REST.
* **Z interfejsu API REST usługi Ambari**: Użyj polecenia `curl -u admin -G "https:\//CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` , aby pobrać informacje o węźle, w którym działają interfejs użytkownika burzy i interfejs API REST. Zastąp wartość **ClusterName** nazwą klastra. Po wyświetleniu monitu wprowadź hasło dla konta logowania (administratora). W odpowiedzi wpis "HOST_NAME" zawiera nazwę FQDN węzła.

### <a name="authentication"></a>Authentication

Żądania kierowane do interfejsu API REST muszą używać **uwierzytelniania podstawowego**, dlatego należy użyć nazwy i hasła administratora klastra usługi HDInsight.

> [!NOTE]  
> Ponieważ uwierzytelnianie podstawowe jest wysyłane przy użyciu czystego tekstu, należy **zawsze** używać protokołu HTTPS do zabezpieczania komunikacji z klastrem.

### <a name="return-values"></a>Zwracane wartości

Informacje zwracane z interfejsu API REST mogą być używane tylko w ramach klastra. Na przykład w pełni kwalifikowana nazwa domeny (FQDN) zwracana dla serwerów [Apache ZooKeeper](https://zookeeper.apache.org/) nie jest dostępna z Internetu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [opracowywać topologie oparte na języku Java za pomocą platformy Apache Maven](apache-storm-develop-java-topology.md).

Aby uzyskać listę więcej przykładowych topologii, zobacz [przykładowe topologie dla Apache Storm w usłudze HDInsight](apache-storm-example-topology.md).
