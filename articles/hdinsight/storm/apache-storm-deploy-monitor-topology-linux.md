---
title: Wdrażanie topologii usługi Apache Storm i zarządzanie nimi w usłudze Azure HDInsight
description: Dowiedz się, jak wdrażać, monitorować i zarządzać topologią Apache Storm za pomocą pulpitu nawigacyjnego Storm na bazie Linux HDInsight. Użyj narzędzi Hadoop dla programu Visual Studio.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: e890289230b3215bd102d8c5a78dca4f1b7b90f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271904"
---
# <a name="deploy-and-manage-apache-storm-topologies-on-azure-hdinsight"></a>Wdrażanie topologii usługi Apache Storm i zarządzanie nimi w usłudze Azure HDInsight

W tym dokumencie poznaj podstawy zarządzania i monitorowania topologii [Apache Storm](https://storm.apache.org/) działającej w storm na klastrach HDInsight.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Burzy Apache w programie HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu portalu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybierz opcję **Burza** dla **typu klastra**.

* (Opcjonalnie) Znajomość secure shell (SSH) i secure copy (SCP). Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* (Opcjonalnie) Visual Studio, azure SDK 2.5.1 lub nowsze i Narzędzia usługi Data Lake dla programu Visual Studio. Aby uzyskać więcej informacji, zobacz [Apache Hadoop & Narzędzia programu Visual Studio Data Lake Tools](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).

## <a name="submit-a-topology-using-visual-studio"></a>Przesyłanie topologii przy użyciu programu Visual Studio

Za pomocą narzędzia Usługi Data Lake Tools for Visual Studio można przesłać topologie języka C# lub hybrydowe do klastra Storm. W poniższych krokach użyto przykładowej aplikacji. Aby uzyskać informacje na temat tworzenia topologii przy użyciu narzędzi usługi Data Lake Tools, zobacz [Topologie burzy apyche w programie Visual Studio i języku C#](apache-storm-develop-csharp-visual-studio-topology.md).

1. Jeśli nie zainstalowano jeszcze najnowszej wersji narzędzi usługi Data Lake dla programu Visual Studio, zobacz [Korzystanie z narzędzi usługi Data Lake tools dla programu Visual Studio.](../hadoop/apache-hadoop-visual-studio-tools-get-started.md)

    > [!NOTE]  
    > Narzędzia usługi Azure Data Lake i Stream Analytics Tools były wcześniej nazywane narzędziami HDInsight tools for Visual Studio.
    >
    > Narzędzia Azure Data Lake i Stream Analytics Tools for Visual Studio są uwzględnione w obciążeniu **deweloperskim platformy Azure** dla programu Visual Studio 2019.

1. Uruchom program Visual Studio.

1. W oknie **Start** wybierz pozycję **Utwórz nowy projekt**.

1. W oknie **Utwórz nowy projekt** zaznacz pole `Storm`wyszukiwania i wprowadź . Następnie wybierz z listy wyników **pozycję Próbka burzy** i wybierz pozycję **Dalej**.

1. W oknie **Konfigurowanie nowego projektu** wprowadź **nazwę projektu**i przejdź do lub utwórz **lokalizację,** w którym zostanie zapisane nowy projekt. Następnie wybierz pozycję **Utwórz**.

    ![Konfigurowanie nowego okna projektu, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-sample1.png)

1. Z **Eksploratora serwera**kliknij prawym przyciskiem myszy **pozycję Azure** i wybierz pozycję **Połącz z subskrypcją platformy Microsoft Azure...** i zakończ proces logowania.

1. W **eksploratorze rozwiązań**kliknij prawym przyciskiem myszy projekt i wybierz polecenie **Prześlij do burzy w programie HDInsight**.

    > [!NOTE]  
    > Jeśli zostanie wyświetlony monit, wprowadź poświadczenia logowania dla subskrypcji platformy Azure. Jeśli masz więcej niż jedną subskrypcję, zaloguj się do tej, która zawiera pakiet Storm w klastrze HDInsight.

1. W oknie dialogowym **Przesyłanie topologii** w obszarze listy rozwijanej **Klaster burzowy** wybierz klaster Burza w programie HDInsight, a następnie wybierz pozycję **Prześlij**. Można monitorować, czy przesyłanie zakończyło się pomyślnie, przeglądając okienko **Dane wyjściowe.**

## <a name="submit-a-topology-using-ssh-and-the-storm-command"></a>Prześlij topologię za pomocą funkcji SSH i polecenia Burza

1. Użyj [polecenia ssh,](../hdinsight-hadoop-linux-use-ssh-unix.md) aby połączyć się z klastrem. Edytuj poniższe polecenie, zastępując clustername nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. W sesji ssh użyj następującego polecenia, aby uruchomić przykładową topologię **wordcount:**

    ```bash
    storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology WordCount
    ```

    To polecenie uruchamia w klastrze przykładową topologię WordCount. Ta topologia losowo generuje zdania, a następnie zlicza wystąpienie każdego wyrazu w zdaniach.

    > [!NOTE]  
    > Podczas przesyłania topologii do klastra należy najpierw skopiować plik jar zawierający klaster przed użyciem `storm` polecenia. Aby skopiować plik do klastra, `scp` można użyć polecenia. Na przykład wprowadź wartość `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`.
    >
    > Przykład *WordCount* i inne przykłady startowe burzy są już `/usr/hdp/current/storm-client/contrib/storm-starter/`uwzględnione w klastrze w programie .

## <a name="submit-a-topology-programmatically"></a>Przesyłanie topologii programowo

Topologię można programowo wdrożyć za pomocą usługi Nimbus. [https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology](https://github.com/Azure-Samples/hdinsight-java-deploy-storm-topology)zawiera przykładową aplikację Java, która pokazuje, jak wdrożyć i uruchomić topologię za pośrednictwem usługi Nimbus.

## <a name="monitor-and-manage-a-topology-in-visual-studio"></a>Monitorowanie topologii w programie Visual Studio i zarządzanie nią

Po przesłaniu topologii za pomocą programu Visual Studio zostanie wyświetle się okno **Widok topologii burzy.** Wybierz topologię z listy, aby wyświetlić informacje o topologii uruchamiania.

![Topologia monitora, okno Widok topologii burzy, Visual Studio](./media/apache-storm-deploy-monitor-topology-linux/visual-studio-monitor.png)

> [!NOTE]  
> Topologie **burzy** można również wyświetlić w **Eksploratorze serwera**. Rozwiń pozycję **Azure** > **HDInsight**, kliknij prawym przyciskiem myszy burzę w klastrze HDInsight, a następnie wybierz pozycję **Wyświetl topologie burzy**.

Wybierz kształt wylewek lub śrub, aby wyświetlić informacje o tych komponentach. Dla wybranego elementu zostanie wyświetlona etykietka narzędzia z informacjami o komponencie.

### <a name="deactivate-and-reactivate-a-topology"></a>Dezaktywowanie i ponowne aktywowanie topologii

Dezaktywacja topologii wstrzymuje ją do momentu zabicia lub ponownego uaktywnienia topologii. Aby wykonać te operacje, użyj przycisków **Dezaktywuj** i **aktywuj ponownie** w obszarze **Akcje** u góry okna **Widok topologii burzy.**

### <a name="rebalance-a-topology"></a>Zrównoważyć topologię

Ponowne zrównoważenie topologii umożliwia systemowi zmianę równoległości topologii. Na przykład jeśli został przebudowany na numer klastra, aby dodać więcej notatek, ponowne równoważenie umożliwia topologii, aby zobaczyć nowe węzły.

Aby zrównoważyć topologię, użyj przycisku **Równoważ ponownie** w obszarze **Akcje** okna **Widok topologii burzy.**

> [!WARNING]  
> Ponowne równoważenie topologii dezaktywuje topologię, rozprowadza pracowników równomiernie w klastrze, a następnie zwraca topologię do stanu, w jakiej się znajdowała przed ponownym równoważeniem. Jeśli topologia była aktywna, staje się ponownie aktywna. Jeśli topologia została dezaktywowana, pozostaje dezaktywowana.

### <a name="kill-a-running-topology"></a>Zabij topologię biegu

Topologie burzowe będą nadal działać, dopóki nie zostaną zatrzymane lub klaster zostanie usunięty. Aby zatrzymać topologię, użyj przycisku **Zbij** w obszarze **Akcje.**

## <a name="monitor-and-manage-a-topology-using-ssh-and-the-storm-command"></a>Monitorowanie topologii i zarządzanie nią za pomocą funkcji SSH i polecenia Burza

Narzędzie `storm` umożliwia pracę z uruchamianiem topologii z wiersza polecenia. Użyj `storm -h` pełnej listy poleceń.

### <a name="list-topologies"></a>Topologie listy

Użyj następującego polecenia, aby wyświetlić listę wszystkich uruchomionych topologii:

```shell
storm list
```

To polecenie zwraca informacje podobne do następującego tekstu:

```shell
Topology_name        Status     Num_tasks  Num_workers  Uptime_secs
-------------------------------------------------------------------
WordCount            ACTIVE     29         2            263
```

### <a name="deactivate-and-reactivate-a-topology"></a>Dezaktywowanie i ponowne aktywowanie topologii

Dezaktywacja topologii wstrzymuje ją do momentu zabicia lub ponownego uaktywnienia topologii. Użyj następujących poleceń, aby dezaktywować lub ponownie aktywować:

```shell
storm Deactivate TOPOLOGYNAME
```

```shell
storm Activate TOPOLOGYNAME
```

### <a name="kill-a-running-topology"></a>Zabij topologię biegu

Topologie burzowe, po uruchomieniu, nadal działają aż do zatrzymania. Aby zatrzymać topologię, użyj następującego polecenia:

```shell
storm kill TOPOLOGYNAME
```

### <a name="rebalance-a-topology"></a>Zrównoważyć topologię

Ponowne zrównoważenie topologii umożliwia systemowi zmianę równoległości topologii. Na przykład jeśli został przebudowany na numer klastra, aby dodać więcej notatek, ponowne równoważenie umożliwia topologii, aby zobaczyć nowe węzły.

> [!WARNING]  
> Ponowne równoważenie topologii dezaktywuje topologię, rozprowadza pracowników równomiernie w klastrze, a następnie zwraca topologię do stanu, w jakiej się znajdowała przed ponownym równoważeniem. Jeśli topologia była aktywna, staje się ponownie aktywna. Jeśli został dezaktywowany, pozostaje wyłączony.

```shell
storm rebalance TOPOLOGYNAME
```

## <a name="monitor-and-manage-a-topology-using-the-storm-ui"></a>Monitorowanie topologii i zarządzanie nią przy użyciu interfejsu użytkownika burzy

Interfejs użytkownika burzy zapewnia interfejs internetowy do pracy z topologią uruchamiania i jest dołączony do klastra HDInsight. Aby wyświetlić interfejs użytkownika burzy, użyj `https://CLUSTERNAME.azurehdinsight.net/stormui`przeglądarki sieci web, aby otworzyć , gdzie *CLUSTERNAME* jest nazwą klastra.

> [!NOTE]  
> Jeśli zostaniesz poproszony o podanie nazwy użytkownika i hasła, wprowadź nazwę użytkownika i hasło administratora klastra używane podczas tworzenia klastra.

### <a name="storm-ui-main-page"></a>Strona główna interfejsu użytkownika burzy

Strona główna interfejsu użytkownika burzy zawiera następujące informacje:

| Sekcja | Opis |
| --- | --- |
| Podsumowanie klastra| Podstawowe informacje o klastrze Burza. |
| Podsumowanie Nimbus | Lista podstawowych informacji nimbus. |
| Podsumowanie topologii | Lista topologii uruchamiania. Aby wyświetlić więcej informacji o określonej topologii, zaznacz jej łącze w kolumnie **Nazwa.** |
| Podsumowanie przełożonego | Informacje o nadzorcy burzy. Aby wyświetlić zasoby pracownika skojarzone z określonym przełożonym, wybierz jego **łącze** w kolumnie Host lub **Id.** |
| Konfiguracja nimbusa | Konfiguracja nimbusa dla klastra. |

Strona główna interfejsu użytkownika burzy wygląda podobnie do tej strony internetowej:

![Strona główna, Interfejs burzy, Topologie Burzy Apache, Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-main-page.png)

#### <a name="topology-summary"></a>Podsumowanie topologii

Wybranie łącza z sekcji **Podsumowanie topologii** powoduje wyświetlenie następujących informacji o topologii:

| Sekcja | Opis |
| --- | --- |
| Podsumowanie topologii | Podstawowe informacje o topologii. |
| Działania topologii| Akcje zarządzania, które można wykonać dla topologii. Dostępne akcje są opisane w dalszej części tej sekcji. |
| Statystyki topologii | Statystyki dotyczące topologii. Aby ustawić przedział czasu wpisu w tej sekcji, zaznacz jego łącze w kolumnie **Okno.** |
| Wylewki *(przedział czasowy)* | Dziobki używane przez topologię. Aby wyświetlić więcej informacji o określonym wylewce, wybierz jego łącze w kolumnie **Identyfikator.** |
| Śruby *(ramy czasowe)* | Śruby używane przez topologię. Aby wyświetlić więcej informacji o określonej śrubie, wybierz jej łącze w kolumnie **Identyfikator.** |
| Zasoby pracowników | Lista zasobów pracowników. Aby wyświetlić więcej informacji o określonym zasobie pracownika, zaznacz jego łącze w kolumnie **Host.** |
| Wizualizacja topologii | Przycisk **Pokaż wizualizację,** który wyświetla wizualizację topologii. |
| Konfiguracja topologii | Konfiguracja wybranej topologii. |

Strona podsumowania topologii burzy wygląda podobnie do tej strony internetowej:

![Strona podsumowania topologii, interfejs użytkownika burzy, burza apache, platforma Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-topology-summary.png)

W sekcji **Akcje topologii** można wybrać następujące przyciski, aby wykonać akcję:

| Button | Opis |
| --- | --- |
| Activate | Wznawia przetwarzanie dezaktywacji topologii. |
| Dezaktywuj | Wstrzymuje topologię biegu. |
| Równowaga | Dostosowuje równoległość topologii. Po zmianie liczby węzłów w klastrze należy ponownie zrównoważyć uruchamianie topologii. Ta operacja umożliwia topologii dostosować równoległości, aby zrekompensować dodatkową lub zmniejszoną liczbę węzłów w klastrze.<br/><br/>Aby uzyskać więcej informacji, zobacz <a href="https://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Opis równoległości topologii burzy Apache</a>.
| Zabić | Kończy topologię burzy po upływie określonego limitu czasu. |
| Debugowanie | Rozpoczyna sesję debugowania dla uruchamiania topologii. |
| Zatrzymaj debugowanie | Kończy sesję debugowania dla uruchamiania topologii. |
| Zmień poziom dziennika | Modyfikuje poziom dziennika debugowania. |

##### <a name="spout-and-bolt-summary"></a>Podsumowanie wylewki i śruby

Wybranie wylewki z sekcji **Wylewki** lub **Śruby** wyświetla następujące informacje o wybranym elemencie:

| Sekcja | Opis |
| --- | --- |
| Podsumowanie komponentów | Podstawowe informacje o wylewce lub śrubie. |
| Akcje komponentu | **Przyciski debugowania** i **zatrzymywania debugowania.** |
| Statystyki wylewki lub statystyki śrub | Statystyki dotyczące wylewki lub śruby. Aby ustawić przedział czasu wpisu w tej sekcji, zaznacz jego łącze w kolumnie **Okno.** |
| (Tylko przy śrubach)<br/>Statystyki wejściowe *(przedział czasu)* | Informacje o strumieniach wejściowych zużywanych przez śrubę. |
| Statystyki wyjściowe *(ramy czasowe)* | Informacje o strumieniach emitowanych przez wylewka lub śrubę. |
| Profilowanie i debugowanie | Formanty profilowania i debugowania składników na tej stronie. Można ustawić wartość **Stan / Limit czasu (minuty)** i wybrać przyciski **JStack**, **Restart Worker**i **Heap**. |
| Executors *(ramy czasowe)* | Informacje o wystąpieniach wylewki lub śruby. Aby wyświetlić dziennik informacji diagnostycznych wyprodukowanych dla tego wystąpienia, wybierz **port** wpis dla określonego wykonawcy. Można również wyświetlić zasoby robocze skojarzone z określonym wykonawcą, wybierając jego łącze w kolumnie **Host.** |
| Errors | Wszelkie informacje o błędzie wylewki lub śruby. |

Strona podsumowania śruby burzy wygląda podobnie do tej strony internetowej:

![Strona podsumowania śruby, interfejs użytkownika burzy, burza apache, platforma Azure](./media/apache-storm-deploy-monitor-topology-linux/apache-storm-web-ui-bolt-summary.png)

## <a name="monitor-and-manage-the-topology-using-the-rest-api"></a>Monitorowanie topologii i zarządzanie nią za pomocą interfejsu API REST

Interfejs użytkownika burzy jest zbudowany na podstawie interfejsu API REST, dzięki czemu można wykonać podobne zadania zarządzania i monitorowania przy użyciu interfejsu API REST. Za pomocą interfejsu API REST można tworzyć niestandardowe narzędzia do zarządzania topologią burzy i monitorowania ich.

Aby uzyskać więcej informacji, zobacz [Interfejs API interfejsu użytkownika usługi Apache Storm .](https://storm.apache.org/releases/current/STORM-UI-REST-API.html) Następujące informacje są specyficzne dla korzystania z interfejsu API REST z Apache Storm na HDInsight.

> [!IMPORTANT]  
> Interfejs API Storm REST nie jest publicznie dostępny w Internecie. Musi być dostępny za pomocą tunelu SSH do węzła głównego klastra HDInsight. Aby uzyskać informacje na temat tworzenia i używania tunelu SSH, zobacz [Uzyskiwanie dostępu do usługi Azure HDInsight za pomocą tunelowania SSH.](../hdinsight-linux-ambari-ssh-tunnel.md)

### <a name="base-uri"></a>Podstawowy identyfikator URI

Podstawowy identyfikator URI dla interfejsu API REST w klastrach HDInsight opartych na systemie Linux jest dostępny pod adresem `https://HEADNODEFQDN:8744/api/v1/`URL , gdzie zastępujesz *HEADNODEFQDN* węzłem głównym. Nazwa domeny węzła głównego jest generowana podczas tworzenia klastra i nie jest statyczna.

W pełni kwalifikowaną nazwę domeny (FQDN) dla węzła głównego klastra można znaleźć na kilka sposobów:

| Metoda odnajdowania FQDN | Opis |
| --- | --- |
| Sesja SSH | Użyj polecenia `headnode -f` z sesji SSH do klastra. |
| Ambari Web | Na stronie internetowej klastra`https://CLUSTERNAME.azurehdinsight.net`Ambari ( ), wybierz **pozycję Usługi** z góry strony, a następnie wybierz pozycję **Burza**. Na karcie **Podsumowanie** wybierz pozycję **Serwer interfejsu użytkownika burzy**. Numer FQDN węzła obsługującego interfejs użytkownika burzy i interfejs API REST jest wyświetlany w górnej części strony. |
| Ambari REST API | Użyj polecenia, `curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/STORM/components/STORM_UI_SERVER"` aby pobrać informacje o węźle, na który są uruchomione interfejs użytkownika burzy i interfejs API REST. Zastąp dwa wystąpienia *CLUSTERNAME* nazwą klastra. Po wyświetleniu monitu wprowadź hasło do konta użytkownika (administratora). W odpowiedzi wpis "host_name" danych wyjściowych JSON zawiera numer FQDN węzła. |

### <a name="authentication"></a>Uwierzytelnianie

Żądania do interfejsu API REST muszą używać *uwierzytelniania podstawowego,* dlatego należy użyć nazwy administratora i hasła dla klastra HDInsight.

> [!NOTE]  
> Ponieważ uwierzytelnianie podstawowe jest wysyłane przy użyciu zwykłego tekstu, należy *zawsze* używać protokołu HTTPS do zabezpieczania komunikacji z klastrem.

### <a name="return-values"></a>Zwracane wartości

Informacje zwracane z interfejsu API REST mogą być dostępne tylko z poziomu klastra. Na przykład w pełni kwalifikowana nazwa domeny (FQDN) zwrócona dla [serwerów Apache ZooKeeper](https://zookeeper.apache.org/) nie jest dostępna z Internetu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [rozwijać topologie oparte na javie za pomocą Apache Maven](apache-storm-develop-java-topology.md).

Aby uzyskać listę więcej przykładowych topologii, zobacz [Przykładowe topologie burzy apache w usłudze Azure HDInsight](apache-storm-example-topology.md).
