---
title: Odcień usługi Hadoop w klastrach opartych na systemie Linux w usłudze HDInsight — Azure
description: Dowiedz się, jak zainstalować odcień w klastrach usługi HDInsight i użyć tunelowania, aby skierować żądania do odcienia. Użyj odcieni, aby przeglądać magazyn i uruchamiać Hive lub świnie.
keywords: odcień usługi Hadoop
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: hrasheed
ms.openlocfilehash: 67f338b583ef428b8dd04e859a5204fd708ce434
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962003"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Instalowanie i używanie odcienia w klastrach usługi HDInsight Hadoop

Dowiedz się, jak zainstalować odcień w klastrach usługi HDInsight i użyć tunelowania, aby skierować żądania do odcienia.

## <a name="what-is-hue"></a>Co to jest odcień?
Odcień to zestaw aplikacji sieci Web służący do współdziałania z klastrem Apache Hadoop. Można użyć odcienia do przeglądania magazynu skojarzonego z klastrem usługi Hadoop (WASB, w przypadku klastrów usługi HDInsight), uruchamiania zadań Hive i skryptów świńskich itd. W przypadku instalacji odcienia w klastrze usługi HDInsight Hadoop dostępne są następujące składniki.

* Edytor Hive Beeswax
* Apache Pig
* Menedżer magazynu metadanych
* Apache Oozie
* FileBrowser (który nakomunikuje się z kontenerem domyślnym WASB)
* Przeglądarka zadań

> [!WARNING]  
> Składniki dostarczane z klastrem usługi HDInsight są w pełni obsługiwane, a pomoc techniczna firmy Microsoft ułatwiają izolowanie i rozwiązywanie problemów związanych z tymi składnikami.
>
> Składniki niestandardowe otrzymują komercyjnie uzasadnioną pomoc techniczną, która ułatwia dalsze Rozwiązywanie problemu. Może to skutkować rozwiązaniem problemu lub zapytaniem o zaangażowanie dostępnych kanałów dla technologii open source, w których znajduje się Szczegółowa wiedza dla tej technologii. Na przykład istnieje wiele witryn społeczności, które mogą być używane, takich jak: [Forum MSDN dotyczące](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight)usługi HDInsight [https://stackoverflow.com](https://stackoverflow.com),. Również projekty Apache mają witryny projektu, [https://apache.org](https://apache.org)na przykład: Usługa [Hadoop](https://hadoop.apache.org/).
>
>

## <a name="install-hue-using-script-actions"></a>Instalowanie odcienia przy użyciu akcji skryptu

Skrypt służący do instalowania odcienia w klastrze usługi HDInsight opartym na https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh systemie Linux jest dostępny pod adresem. Za pomocą tego skryptu można instalować odcienie w klastrach z obiektami BLOB usługi Azure Storage (WASB) lub Azure Data Lake Storage jako magazynem domyślnym.

Ta sekcja zawiera instrukcje dotyczące używania skryptu podczas aprowizacji klastra przy użyciu Azure Portal.

> [!NOTE]  
> Azure PowerShell można również użyć klasycznego interfejsu wiersza polecenia platformy Azure, zestawu .NET SDK usługi HDInsight lub szablonów Azure Resource Manager, aby zastosować akcje skryptu. Można również zastosować akcje skryptu do już uruchomionych klastrów. Aby uzyskać więcej informacji, zobacz [Dostosowywanie klastrów usługi HDInsight za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Rozpocznij Inicjowanie obsługi klastra, wykonując czynności opisane w temacie [Inicjowanie obsługi klastrów usługi HDInsight w systemie Linux](hdinsight-hadoop-provision-linux-clusters.md), ale nie Ukończ aprowizacji.

   > [!NOTE]  
   > Aby można było zainstalować odcień w klastrach usługi HDInsight, zalecanym rozmiarem węzła głównego jest co najmniej A4 (8 rdzeni, 14 GB pamięci).
   >
   >
2. W bloku **Konfiguracja opcjonalna** wybierz pozycję **Akcje skryptu**, a następnie podaj informacje, jak pokazano poniżej:

    ![Podaj parametry akcji skryptu dla odcienia](./media/hdinsight-hadoop-hue-linux/hdi-hue-script-action.png "Podaj parametry akcji skryptu dla odcienia")

   * **NAZWA**: Wprowadź przyjazną nazwę akcji skryptu.
   * **IDENTYFIKATOR URI SKRYPTU**: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
   * **NAGŁÓWEK**: Zaznacz tę opcję.
   * **PROCES ROBOCZY**: Pozostaw to pole puste.
   * **DOZORCY**: Pozostaw to pole puste.
   * **PARAMETRY**: Pozostaw to pole puste.
3. W dolnej części **akcji skryptu**Użyj przycisku **Wybierz** , aby zapisać konfigurację. Na koniec użyj przycisku **Wybierz** znajdującego się u dołu bloku **Konfiguracja opcjonalna** , aby zapisać opcjonalne informacje o konfiguracji.
4. Kontynuuj Inicjowanie obsługi klastra zgodnie z opisem w temacie [Inicjowanie obsługi klastrów usługi HDInsight w systemie Linux](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="use-hue-with-hdinsight-clusters"></a>Używanie odcienia z klastrami usługi HDInsight

Tunelowanie SSH jest jedynym sposobem, aby uzyskać dostęp do odcienia w klastrze po jego uruchomieniu. Tunelowanie za pośrednictwem protokołu SSH umożliwia przechodzenie ruchu bezpośrednio do węzła głównego klastra, w którym jest uruchomione. Po zakończeniu aprowizacji klastra wykonaj następujące kroki, aby użyć odcienia w klastrze usługi HDInsight w systemie Linux.

> [!NOTE]  
> Zalecamy używanie przeglądarki Firefox sieci Web, aby postępować zgodnie z poniższymi instrukcjami.
>
>

1. Korzystając z informacji w temacie [Używanie tunelowania SSH do uzyskiwania dostępu do interfejsu użytkownika usługi Apache Ambari Web, ResourceManager, JobHistory, NameNode, Oozie i innych interfejsów użytkownika sieci Web](hdinsight-linux-ambari-ssh-tunnel.md) , można utworzyć tunel SSH od systemu klienckiego do klastra usługi HDInsight, a następnie skonfigurować przeglądarkę sieci Web tak, aby korzystała z Tunel SSH jako serwer proxy.

2. Po utworzeniu tunelu SSH i skonfigurowaniu dla niego przeglądarki do ruchu serwera proxy należy znaleźć nazwę hosta podstawowego węzła głównego. Można to zrobić, łącząc się z klastrem przy użyciu protokołu SSH na porcie 22. Na przykład, `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` gdzie **username** to nazwa użytkownika ssh i **ClusterName** to nazwa klastra.

    Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Po nawiązaniu połączenia użyj następującego polecenia, aby uzyskać w pełni kwalifikowaną nazwę domeny węzła głównego podstawowego:

        hostname -f

    Spowoduje to zwrócenie nazwy podobnej do następującej:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Jest to nazwa hosta głównego węzła głównego, w którym znajduje się witryna internetowa odcienia.
4. Użyj przeglądarki, aby otworzyć Portal odcienia pod adresem\/http:/hostname: 8888. Zastąp nazwę hosta nazwą uzyskaną w poprzednim kroku.

   > [!NOTE]  
   > Gdy logujesz się po raz pierwszy, zostanie wyświetlony monit o utworzenie konta w celu zalogowania się do portalu odcień. Poświadczenia określone w tym miejscu będą ograniczone do portalu i nie są powiązane z poświadczeniami administratora lub użytkownika SSH określonymi podczas aprowizacji klastra.
   >
   >

    ![Zaloguj się do portalu odcienia](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Określ poświadczenia dla portalu odcienia")

### <a name="run-a-hive-query"></a>Uruchomienie zapytania programu Hive
1. W portalu odcienia kliknij pozycję **edytory zapytań**, a następnie kliknij pozycję **Hive** , aby otworzyć Edytor Hive.

    ![Korzystanie] z programu Hive (./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Korzystanie") z programu Hive
2. Na karcie **Pomoc** w obszarze **baza danych**powinna zostać wyświetlona wartość **hivesampletable**. Jest to Przykładowa tabela, która jest dostarczana ze wszystkimi klastrami Hadoop w usłudze HDInsight. Wprowadź przykładowe zapytanie w okienku po prawej stronie i zobacz dane wyjściowe na karcie **wyniki** w okienku poniżej, jak pokazano na przechwytywaniu ekranu.

    ![Uruchom zapytanie programu Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Uruchom zapytanie programu Hive")

    Możesz również użyć karty **Wykres** , aby zobaczyć wizualną reprezentację wyniku.

### <a name="browse-the-cluster-storage"></a>Przeglądanie magazynu klastra
1. W portalu odcienia kliknij pozycję **przeglądarka plików** w prawym górnym rogu paska menu.
2. Domyślnie przeglądarka plików otwiera się w katalogu **/User/Myuser** . Kliknij prawym przyciskiem myszy ukośnik przed katalogiem użytkownika w ścieżce, aby przejść do katalogu głównego kontenera usługi Azure Storage skojarzonego z klastrem.

    ![Użyj przeglądarki plików](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Użyj przeglądarki plików")
3. Kliknij prawym przyciskiem myszy plik lub folder, aby wyświetlić dostępne operacje. Użyj przycisku **Przekaż** w prawym górnym rogu, aby przekazać pliki do bieżącego katalogu. Użyj przycisku **Nowy** , aby utworzyć nowe pliki lub katalogi.

> [!NOTE]  
> W przeglądarce plików odcienia można wyświetlić tylko zawartość domyślnego kontenera skojarzonego z klastrem usługi HDInsight. Wszystkie dodatkowe konta magazynu/kontenery, które mogą być skojarzone z klastrem, nie będą dostępne przy użyciu przeglądarki plików. Jednak dodatkowe kontenery skojarzone z klastrem będą zawsze dostępne dla zadań Hive. Na przykład, jeśli wprowadzisz polecenie `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` w edytorze Hive, zobaczysz również zawartość dodatkowych kontenerów. W tym poleceniu **newcontainer** nie jest domyślnym kontenerem skojarzonym z klastrem.
>
>

## <a name="important-considerations"></a>Ważne zagadnienia
1. Skrypt służący do instalowania odcienia instaluje go tylko w podstawowym węzła głównego klastra.

2. Podczas instalacji usługi Hadoop (HDFS, PRZĘDZy, MR2, Oozie) są ponownie uruchamiane w celu zaktualizowania konfiguracji. Po zakończeniu instalowania przez skrypt odcienia może upłynąć trochę czasu, zanim inne usługi Hadoop będą mogły zostać uruchomione. Może to mieć wpływ na wydajność odcienia. Po uruchomieniu wszystkich usług odcień będzie w pełni funkcjonalny.
3. Odcień nie rozumie Apache Tez zadań, co jest bieżącym ustawieniem domyślnym dla programu Hive. Jeśli chcesz użyć MapReduce jako aparatu wykonywania programu Hive, zaktualizuj skrypt tak, aby używał następującego polecenia w skrypcie:

        set hive.execution.engine=mr;

4. W przypadku klastrów systemu Linux możesz mieć scenariusz, w którym usługi działają w podstawowym węzła głównego, podczas gdy Menedżer zasobów może być uruchomiona na serwerze pomocniczym. Taki scenariusz może spowodować błędy (pokazane poniżej) podczas korzystania z odcienia, aby wyświetlić szczegóły uruchamiania zadań w klastrze. Można jednak wyświetlić szczegóły zadania, gdy zadanie zostało zakończone.

   ![Błąd portalu odcienia](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Błąd portalu odcienia")

   Jest to spowodowane znanym problemem. W ramach tego problemu należy zmodyfikować Ambari tak, aby aktywne Menedżer zasobów również działały na podstawowym węzła głównego.
5. Odcienie rozumie WebHDFS, podczas gdy klastry HDInsight `wasb://`używają usługi Azure Storage za pomocą programu. Tak więc skrypt niestandardowy używany z akcją skryptu instaluje WebWasb, która jest usługą zgodną z WebHDFS na potrzeby rozmowy z WASB. W związku z tym, mimo że portal odcienia mówi system plików HDFS w miejscu (na przykład gdy przenosisz wskaźnik myszy nad **przeglądarką pliku**), powinien on być interpretowany jako WASB.

## <a name="next-steps"></a>Następne kroki
* [Zainstaluj program Apache Giraph w klastrach usługi HDInsight](hdinsight-hadoop-giraph-install-linux.md). Użyj dostosowywania klastra, aby zainstalować Giraph w klastrach usługi HDInsight Hadoop. Giraph umożliwia przetwarzanie grafów przy użyciu usługi Hadoop i może być używane z usługą Azure HDInsight.
* [Zainstaluj język R w klastrach usługi HDInsight](hdinsight-hadoop-r-scripts-linux.md). Użyj dostosowywania klastra, aby zainstalować język R w klastrach Hadoop usługi HDInsight. R to język i środowisko "open source" do obliczeń statystycznych. Zapewnia setki wbudowanych funkcji statystycznych i własnego języka programowania, który łączy aspekty funkcjonalne i programowanie zorientowane obiektowo. Zapewnia również szerokie możliwości graficzne.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
