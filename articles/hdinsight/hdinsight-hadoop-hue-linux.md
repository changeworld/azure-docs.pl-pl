---
title: HUE przy użyciu usługi Hadoop w klastrach opartych na systemie HDInsight Linux — Azure
description: Dowiedz się, jak instalowanie aplikacji Hue w klastrach HDInsight i korzystanie z tunelowania do kierowania żądań do aplikacji Hue. Korzystanie z rozwiązania Hue, Przeglądaj magazyn do uruchomienia programu Hive i Pig.
keywords: HUE hadoop
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: hrasheed
ms.openlocfilehash: b0354803a117e8e2c2382ae888bde94a502f24c6
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64724573"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Instalowanie i korzystanie z rozwiązania Hue w klastrach usługi HDInsight Hadoop

Dowiedz się, jak instalowanie aplikacji Hue w klastrach HDInsight i korzystanie z tunelowania do kierowania żądań do aplikacji Hue.

> [!IMPORTANT]  
> Procedura przedstawiona w tym dokumencie wymaga klastra usługi HDInsight używającego systemu Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a name="what-is-hue"></a>Co to jest Hue?
HUE to zestaw aplikacji sieci Web umożliwiający interakcję z klastrem usługi Apache Hadoop. Możesz przeglądać magazynu skojarzonego z klastrem usługi Hadoop (w przypadku klastrów HDInsight WASB) za pomocą aplikacji Hue, uruchamiania zadań Hive i Pig skryptów i tak dalej. Następujące składniki są dostępne z instalacjami aplikacji Hue w klastrze usługi HDInsight Hadoop.

* Edytor Hive beeswax
* Apache Pig
* Menedżer magazynu metadanych
* Apache Oozie
* FileBrowser (który rozmawia z WASB domyślnego kontenera)
* Przeglądarka zadań

> [!WARNING]  
> Składniki dostarczony z klastrem usługi HDInsight są w pełni obsługiwane i Microsoft Support pomoże wyizolować i rozwiązać problemy związane z tych składników.
>
> Składniki niestandardowe otrzymują uzasadnioną komercyjnie pomoc techniczną, aby pomóc rozwiązać ten problem. Może to spowodować rozwiązuje problem lub pytaniem, dzięki którym można zaangażować dostępne kanały dla technologii "open source", gdzie znajduje się specjalistyczna dla tej technologii. Na przykład istnieje wiele witryn społeczności, które mogą być używane, takie jak: [Forum MSDN dotyczące HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ https://stackoverflow.com ](https://stackoverflow.com). Projektów Apache mieć witryny projektu na [ https://apache.org ](https://apache.org), na przykład: [Hadoop](https://hadoop.apache.org/).
>
>

## <a name="install-hue-using-script-actions"></a>Instalowanie aplikacji Hue, za pomocą akcji skryptu

Skrypt instalowanie aplikacji Hue w klastrze HDInsight opartych na systemie Linux jest dostępna na https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Ten skrypt umożliwia instalowanie aplikacji Hue w klastrach za pomocą usługi Azure blob Storage (WASB) lub usługi Azure Data Lake Storage jako magazynu domyślnego.

Ta sekcja zawiera instrukcje dotyczące używania skryptu podczas aprowizowania klastra przy użyciu witryny Azure portal.

> [!NOTE]  
> Program Azure PowerShell, wiersza polecenia platformy Azure Classic, zestawu .NET SDK HDInsight lub szablonów usługi Azure Resource Manager można również zastosować akcji skryptu. Można również zastosować akcji skryptu na już działające klastry. Aby uzyskać więcej informacji, zobacz [HDInsight Dostosowywanie klastrów za pomocą akcji skryptów](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Uruchom aprowizację klastra wykonując kroki opisane w [Provision HDInsight clusters w systemie Linux](hdinsight-hadoop-provision-linux-clusters.md), ale inicjowania obsługi nie jest ukończona.

   > [!NOTE]  
   > Instalowanie aplikacji Hue w klastrach HDInsight, rozmiar zalecany węzła głównego jest co najmniej A4 (8 rdzeni, 14 GB pamięci).
   >
   >
2. Na **opcjonalna konfiguracja** bloku wybierz **akcji skryptu**i podaj informacje, jak pokazano poniżej:

    ![Podanie skryptu parametry akcji Hue](./media/hdinsight-hadoop-hue-linux/hue-script-action.png "dostarczają parametrów akcji skryptu aplikacji Hue")

   * **NAZWA**: Wprowadź przyjazną nazwę dla akcji skryptu.
   * **IDENTYFIKATOR URI SKRYPTU**: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
   * **HEAD**: Zaznacz tę opcję.
   * **PROCES ROBOCZY**: Pozostaw to pole puste.
   * **ZOOKEEPER**: Pozostaw to pole puste.
   * **PARAMETRY**: Pozostaw to pole puste.
3. W dolnej części **akcji skryptu**, użyj **wybierz** przycisk, aby zapisać konfigurację. Na koniec użyj **wybierz** znajdujący się u dołu **opcjonalna konfiguracja** bloku, aby zapisać informacje o konfiguracji opcjonalnej.
4. Kontynuuj, inicjowania obsługi klastra zgodnie z opisem w [Provision HDInsight clusters w systemie Linux](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="use-hue-with-hdinsight-clusters"></a>Korzystanie z rozwiązania Hue z klastrami HDInsight

Tunelowanie SSH jest jedynym sposobem na dostęp do aplikacji Hue w klastrze, gdy aplikacja działa. Tunelowanie za pośrednictwem protokołu SSH zezwala na ruch przechodzić bezpośrednio do węzła głównego klastra, w którym jest uruchomiona Hue. Po zakończeniu klastra podczas inicjowania obsługi, wykonaj następujące kroki, aby korzystanie z rozwiązania Hue w klastrze usługi HDInsight w systemie Linux.

> [!NOTE]  
> Firma Microsoft zaleca, za pomocą przeglądarki Firefox, postępuj zgodnie z instrukcjami poniżej.
>
>

1. Skorzystaj z informacji w [użycie tunelowania SSH w celu dostępu do systemu Apache Ambari web UI, ResourceManager, JobHistory, NameNode, Oozie i innych web UI's](hdinsight-linux-ambari-ssh-tunnel.md) do tworzenia tunelu SSH w systemie klienta do klastra HDInsight, a następnie skonfiguruj przeglądarki sieci Web, aby korzystał z tunelu SSH jako serwer proxy.

2. Po utworzeniu tunelu SSH i skonfigurowany przeglądarkę, aby przez nią ruchu serwera proxy, należy znaleźć nazwę hosta z podstawowym węzłem głównym. Można to zrobić, nawiązując połączenie z klastrem przy użyciu protokołu SSH na porcie 22. Na przykład `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` gdzie **USERNAME** to nazwa użytkownika SSH i **CLUSTERNAME** jest nazwą klastra.

    Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Po nawiązaniu połączenia użyj następującego polecenia, aby uzyskać w pełni kwalifikowaną nazwę domeny z podstawowym węzłem głównym:

        hostname -f

    Kod ten zwróci nazwę podobny do następującego:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Jest nazwą hosta z podstawowym węzłem głównym, gdzie znajduje się witryna sieci Web aplikacji Hue.
4. Otwieranie portalu Hue HTTP przy użyciu przeglądarki:\//HOSTNAME:8888. Zastąp nazwy hosta o nazwie uzyskanego w poprzednim kroku.

   > [!NOTE]  
   > Po zalogowaniu się po raz pierwszy, zostanie wyświetlony monit o utworzenie konta do logowania do portalu Hue. Poświadczenia określone w tym miejscu będzie ograniczona do portalu i nie są związane z administratora lub poświadczeń użytkownika SSH określone w podczas aprowizacji klastra.
   >
   >

    ![Zaloguj się do portalu Hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Określ poświadczenia dla portalu Hue")

### <a name="run-a-hive-query"></a>Uruchomienie zapytania programu Hive
1. W portalu Hue kliknij **edytory zapytania**, a następnie kliknij przycisk **Hive** aby otworzyć Edytor Hive.

    ![Korzystanie z programu Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "korzystanie z programu Hive")
2. Na **Assist** , w obszarze **bazy danych**, powinien zostać wyświetlony **hivesampletable**. Jest to Przykładowa tabela, który jest dostarczany ze wszystkich klastrów platformy Hadoop w HDInsight. Wprowadź przykładowe zapytanie w okienku po prawej stronie i wyświetlić dane wyjściowe na **wyniki** karcie w okienku poniżej, jak pokazano na zrzucie ekranu.

    ![Uruchomienie zapytania Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "zapytania programu Hive w uruchamiania")

    Można również użyć **wykresu** kartę, aby zobaczyć graficzną reprezentację wynik.

### <a name="browse-the-cluster-storage"></a>Przejdź do magazynu klastra
1. W portalu Hue kliknij **przeglądarka plików** w prawym górnym rogu paska menu.
2. Domyślnie zostanie otwarta przeglądarka plików, na **/użytkownik/myuser** katalogu. Kliknij przycisk ukośnik bezpośrednio poprzedzający katalogu użytkownika w ścieżce, aby przejść do katalogu głównym kontenera usługi Azure storage skojarzonym z klastrem.

    ![Korzystanie z przeglądarki plików](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "korzystanie z przeglądarki plików")
3. Kliknij prawym przyciskiem myszy plik lub folder, aby wyświetlić dostępne operacje. Użyj **przekazywanie** przycisk w prawym górnym rogu, aby przekazać pliki do bieżącego katalogu. Użyj **New** przycisk, aby utworzyć nowe pliki lub katalogi.

> [!NOTE]  
> Przeglądarka plików odcienia, który może tylko wyświetlać zawartość domyślnego kontenera skojarzonego z klastrem HDInsight. Dodatkowy magazyn kont/kontenerach, które mogą być skojarzone z klastrem, nie będzie dostępna przy użyciu przeglądarki plików. Jednak dodatkowe kontenery skojarzone z klastrem zawsze będzie dostępny dla zadań Hive. Na przykład, jeśli wprowadź polecenie `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` w edytorze Hive, można zobaczyć zawartość również dodatkowe kontenery. W tym poleceniu **newcontainer** nie jest domyślny kontener skojarzonego z klastrem.
>
>

## <a name="important-considerations"></a>Istotne zagadnienia
1. Skrypt umożliwia instalowanie aplikacji Hue instaluje go tylko na podstawowym węzłem głównym klastra.

2. Podczas instalacji wielu usług Hadoop (HDFS, YARN, MR2, Oozie) zostaną ponownie uruchomione, aktualizowania konfiguracji. Po zakończeniu działania skryptu instalowanie aplikacji Hue może potrwać trochę czasu, inne usługi Hadoop, aby uruchomić. To może mieć wpływ na wydajność aplikacji Hue w początkowym. Po wszystkich usług jest uruchomiona, odcienia, który będzie w pełni funkcjonalne.
3. Odcienia, który nie rozpoznaje Apache Tez zadania, co jest ustawieniem domyślnym bieżącej gałęzi. Jeśli chcesz używać MapReduce jako aparat Hive wykonywania aktualizacji skryptu Użyj następującego polecenia w skrypcie:

        set hive.execution.engine=mr;

4. Przy użyciu klastrów systemu Linux może mieć scenariusz, w których usługi są uruchomione na podstawowym węzłem głównym, a Resource Manager mogą być wykonywane na pomocniczym. Taki scenariusz może prowadzić do błędów (pokazana poniżej), korzystając z odcienia, który chcesz wyświetlić szczegóły zadania uruchomione w klastrze. Jednak po ukończeniu zadania można wyświetlić szczegóły zadania.

   ![Błąd portalu Hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "błąd portalu Hue")

   Jest to spowodowane znanym problemem. Jako obejście tego problemu należy zmodyfikować Ambari, aby active usługi Resource Manager również korzysta z podstawowym węzłem głównym.
5. HUE rozumie WebHDFS, podczas gdy klastrów HDInsight za pomocą usługi Azure Storage `wasb://`. Dlatego niestandardowego skryptu, używany przy użyciu akcji skryptu instaluje WebWasb, czyli usłudze zgodnych z WebHDFS do rozmowy WASB. Tak, nawet jeśli portalu Hue mówi systemu plików HDFS w miejscach (np. gdy przesuwa wskaźnik myszy nad **przeglądarka plików**), powinno być interpretowane jako WASB.

## <a name="next-steps"></a>Kolejne kroki
* [Instalowanie systemu Apache Giraph w klastrach HDInsight](hdinsight-hadoop-giraph-install-linux.md). Użyj dostosowywania klastra, aby zainstalować system Giraph w klastrach usługi HDInsight Hadoop. System Giraph umożliwia przetwarzanie wykresów za pomocą usługi Hadoop i mogą służyć za pomocą usługi Azure HDInsight.
* [Instalowanie języka R w klastrach HDInsight](hdinsight-hadoop-r-scripts-linux.md). Użyj dostosowywania klastra, aby zainstalować język R w klastrach usługi HDInsight Hadoop. R to język typu open source i środowisko do przeprowadzania obliczeń statystycznych. Zapewnia setek wbudowanych funkcji statystycznych i swój własny łączącą aspektów programowania funkcjonalności i zorientowane obiektowo języka programowania. Udostępnia również rozbudowane funkcje graficzne.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
