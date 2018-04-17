---
title: HUE z platformą Hadoop w klastrach opartych na systemie Linux usługi HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak instalowanie aplikacji Hue w klastrach HDInsight i Użyj tunelowania można przekierować żądania do aplikacji Hue. Odcienia, który umożliwia przeglądanie magazynu i uruchom Hive i Pig.
keywords: HUE hadoop
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 9e57fcca-e26c-479d-a745-7b80a9290447
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 12/11/2017
ms.author: nitinme
ms.openlocfilehash: 829f309fa66bc548b9f74cca8112c244cc88775e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Zainstalować i używać Hue w klastrach HDInsight Hadoop

Dowiedz się, jak instalowanie aplikacji Hue w klastrach HDInsight i Użyj tunelowania można przekierować żądania do aplikacji Hue.

> [!IMPORTANT]
> Kroki opisane w tym dokumencie wymagają klastra usługi HDInsight, który używa systemu Linux. Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a name="what-is-hue"></a>Co to jest Hue?
HUE to zestaw aplikacji sieci Web umożliwiający interakcję z klastrem usługi Hadoop. Możesz przeglądać magazynem skojarzonym z klastra usługi Hadoop (w przypadku klastrów usługi HDInsight WASB) za pomocą aplikacji Hue, uruchamiania zadań Hive i skryptów usługi Pig i tak dalej. Następujące składniki są dostępne z instalacjami aplikacji Hue w klastrze usługi HDInsight Hadoop.

* Edytor Hive wosk pszczeli
* Pig
* Menedżer na potrzeby magazynu metadanych
* Oozie
* FileBrowser (który komunikuje się kontener domyślny WASB)
* Przeglądarka zadań

> [!WARNING]
> Składniki dostarczony z klastrem usługi HDInsight są w pełni obsługiwane, a Microsoft Support pomoże w celu odizolowania i rozwiązać problemy związane z tych składników.
>
> Niestandardowe składniki otrzymywanie pomocy uzasadnione ekonomicznie ułatwiające aby dalej rozwiązywać ten problem. Może to spowodować w rozwiązaniu problemu lub monitem o Uwzględnij dostępnych kanałów dla technologiach typu open source wykryto głębokie doświadczenia z tej technologii. Na przykład istnieje wiele witryn społeczności, które mogą być używane, takie jak: [forum MSDN dla usługi HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Projekty Apache mieć witryny projektu na [ http://apache.org ](http://apache.org), na przykład: [Hadoop](http://hadoop.apache.org/).
>
>

## <a name="install-hue-using-script-actions"></a>Instalowanie aplikacji Hue za pomocą akcji skryptu

Skrypt instalowanie aplikacji Hue w klastrze usługi HDInsight opartej na systemie Linux jest dostępna na https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh. Ten skrypt umożliwia instalowanie aplikacji Hue w klastrach z obiektów blob magazynu Azure (WASB) lub usługi Azure Data Lake Store jako domyślnego magazynu.

Ta sekcja zawiera instrukcje dotyczące sposobu użycia skryptu w przypadku inicjowania obsługi klastra przy użyciu portalu Azure.

> [!NOTE]
> Program Azure PowerShell, interfejsu wiersza polecenia Azure, zestawu .NET SDK usługi HDInsight lub szablonów usługi Azure Resource Manager można również zastosować akcji skryptu. Akcje skryptu można również dotyczą już działające klastry. Aby uzyskać więcej informacji, zobacz [Dostosowywanie klastrów usługi HDInsight z akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).
>
>

1. Uruchom inicjowania obsługi klastra, wykonując kroki opisane w [Obsługa administracyjna klastrów HDInsight w systemie Linux](hdinsight-hadoop-provision-linux-clusters.md), kroków inicjowania obsługi administracyjnej.

   > [!NOTE]
   > Instalowanie aplikacji Hue w klastrach HDInsight, rozmiar headnode zalecane jest co najmniej A4 (8 rdzenie, 14 GB pamięci RAM).
   >
   >
2. Na **konfiguracji opcjonalnej** bloku, wybierz opcję **akcji skryptu**i podaj informacje, jak pokazano poniżej:

    ![Podaj parametry akcji skryptu dla Hue](./media/hdinsight-hadoop-hue-linux/hue-script-action.png "Podaj parametry akcji skryptu dla Hue")

   * **Nazwa**: Wprowadź przyjazną nazwę dla akcji skryptu.
   * **IDENTYFIKATOR URI SKRYPTU**: https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
   * **HEAD**: Zaznaczenie tego pola wyboru
   * **Proces ROBOCZY**: to pole pozostanie puste.
   * **DOZORCY**: to pole pozostanie puste.
   * **Parametry**: to pole pozostanie puste.
3. W dolnej części **akcji skryptu**, użyj **wybierz** przycisk, aby zapisać konfigurację. Na koniec użyj **wybierz** przycisk w dolnej części **konfiguracji opcjonalnej** bloku, aby zapisać informacje o konfiguracji opcjonalnej.
4. Kontynuuj, inicjowania obsługi klastra, zgodnie z opisem w [Obsługa administracyjna klastrów HDInsight w systemie Linux](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="use-hue-with-hdinsight-clusters"></a>Użyj Hue z klastrami usługi HDInsight

Tunelowanie SSH jest jedynym sposobem na dostęp do aplikacji Hue w klastrze po jej uruchomieniu. Tunelowania za pośrednictwem SSH zezwala na ruch przejść bezpośrednio do headnode klastra, w którym jest uruchomiona Hue. Po zakończeniu klastra podczas inicjowania obsługi, wykonaj następujące kroki do używania aplikacji Hue w klastrze HDInsight Linux.

> [!NOTE]
> Zaleca się za pomocą przeglądarki Firefox, postępuj zgodnie z instrukcjami poniżej.
>
>

1. Skorzystaj z informacji w [Użyj SSH Tunneling można uzyskać dostępu do interfejsu użytkownika sieci web Ambari, ResourceManager, JobHistory, NameNode, Oozie i innych web UI's](hdinsight-linux-ambari-ssh-tunnel.md) tworzenia tunelu SSH z systemu klienta do klastra usługi HDInsight, a następnie skonfiguruj przeglądarkę sieci Web do używania tunelu SSH jako serwer proxy.

2. Po utworzeniu tunelu SSH i skonfigurowany przeglądarkę, aby ruch serwera proxy przy jego użyciu, należy znaleźć nazwy hosta podstawowego węzła głównego. Można to zrobić przez nawiązanie połączenia z klastrem przy użyciu protokołu SSH na port 22. Na przykład `ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net` gdzie **USERNAME** to nazwa użytkownika SSH i **CLUSTERNAME** jest nazwą klastra.

    Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

3. Po nawiązaniu połączenia użyj następującego polecenia można uzyskać w pełni kwalifikowaną nazwę głównej headnode:

        hostname -f

    Spowoduje to zwrócenie nazwę podobny do następującego:

        hn0-myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Jest nazwą hosta headnode głównej, gdzie znajduje się Hue witryny sieci Web.
4. Umożliwia otwieranie portalu Hue w przeglądarce http://HOSTNAME:8888. Zastąp nazwę hosta nazwą uzyskanego w poprzednim kroku.

   > [!NOTE]
   > Po zalogowaniu się po raz pierwszy, pojawi się monit Aby utworzyć konto, aby zalogować się do portalu Hue. Poświadczenia określone w tym miejscu będzie ograniczony do portalu i nie są związane z administratora lub poświadczeń użytkownika SSH określony podczas udostępniania klastra.
   >
   >

    ![Zaloguj się do portalu Hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Określ poświadczenia dla portalu Hue")

### <a name="run-a-hive-query"></a>Uruchomienie zapytania programu Hive
1. Z portalu Hue kliknij **edytory zapytania**, a następnie kliknij przycisk **Hive** aby otworzyć Edytor Hive.

    ![Korzystanie z programu Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "korzystanie z programu Hive")
2. Na **Assist** , w obszarze **bazy danych**, powinny pojawić się **hivesampletable**. Jest to Przykładowa tabela, który jest dostarczany z wszystkich klastrów Hadoop w usłudze HDInsight. Wprowadź przykładowe zapytanie w okienku po prawej stronie i wyświetlić dane wyjściowe na **wyniki** karcie w okienku poniżej, jak pokazano na zrzucie ekranu.

    ![Uruchamianie zapytań Hive](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "zapytanie Hive Uruchom")

    Można również użyć **wykresu** kartę, aby wyświetlić graficzną reprezentację wynik.

### <a name="browse-the-cluster-storage"></a>Przejdź do magazynu klastra
1. W portalu Hue kliknij **przeglądarka plików** w prawym górnym rogu paska menu.
2. Domyślnie przeglądarka plików zostanie otwarty w **/użytkownik/Myuser.pds** katalogu. Kliknij przycisk ukośnik bezpośrednio poprzedzający katalogu użytkownika w ścieżce, aby przejść do katalogu głównego kontenera magazynu systemu Azure skojarzony z klastrem.

    ![Użyj pliku przeglądarki](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "używana przeglądarka plików")
3. Kliknij prawym przyciskiem myszy plik lub folder, aby wyświetlić dostępne operacje. Użyj **przekazać** przycisk w prawym rogu, aby przekazać pliki do bieżącego katalogu. Użyj **nowy** przycisk, aby utworzyć nowe pliki lub katalogi.

> [!NOTE]
> Przeglądarka plików odcienia, który można wyświetlić tylko zawartość domyślnego kontenera skojarzonego z klastrem usługi HDInsight. Wszelkie dodatkowe miejsce do magazynowania kont/kontenerów, które mogą być skojarzone z klastra nie będzie dostępna przy użyciu przeglądarki plików. Jednak dodatkowe kontenery skojarzony z klastrem zawsze będzie dostępna dla zadań Hive. Na przykład wprowadź polecenie `dfs -ls wasb://newcontainer@mystore.blob.core.windows.net` w edytorze Hive, można zobaczyć zawartość również dodatkowe kontenerów. W tym poleceniu **newcontainer** nie jest skojarzony z klastrem domyślnego kontenera.
>
>

## <a name="important-considerations"></a>Ważne uwagi
1. Skrypt umożliwia instalowanie aplikacji Hue instaluje je tylko na podstawowym headnode klastra.

2. Podczas instalacji aktualizacji konfiguracji ponownego uruchomienia wielu usług Hadoop (HDFS, YARN, MR2, Oozie). Po zakończeniu działania skryptu, instalowanie aplikacji Hue, może upłynąć trochę czasu, zanim inne usługi Hadoop mogła się uruchomić. To może wpłynąć na wydajność aplikacji Hue w początkowo. Po uruchomić wszystkie usługi odcienia, który będzie pełną funkcjonalność.
3. HUE nie rozpoznaje Tez zadania, co jest ustawieniem domyślnym bieżącej gałęzi. Jeśli chcesz korzystać z usługi MapReduce jako gałąź aparat wykonywania aktualizacji skrypt, którego chcesz użyć następującego polecenia w skrypcie:

        set hive.execution.engine=mr;

4. Dzięki klastry z systemem Linux mogą mieć scenariusz, w którym usługi są uruchomione na headnode głównej podczas Resource Manager mogą być wykonywane na serwerze pomocniczym. Taki scenariusz może powodować błędy, (pokazana poniżej) podczas przy użyciu aplikacji Hue, aby wyświetlić szczegóły zadań działa w klastrze. Jednak po ukończeniu zadania można przeglądać szczegóły zadania.

   ![Błąd portalu Hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "błąd portalu Hue")

   Jest to spowodowane znanym problemem. Jako obejście należy zmodyfikować Ambari, tak aby active Resource Manager również działa na headnode podstawowego.
5. HUE rozumie WebHDFS, podczas gdy klastry usługi HDInsight przy użyciu usługi Azure Storage `wasb://`. Tak niestandardowego skryptu używany z akcji skryptu instaluje WebWasb, który jest zgodny z WebHDFS usługi do rozmowy WASB. Tak, mimo że portalu Hue mówi systemu plików HDFS w miejscach (takich jak, gdy mysz przesuwa się nad **przeglądarka plików**), powinny być rozumiane jako WASB.

## <a name="next-steps"></a>Kolejne kroki
* [Zainstaluj Giraph w klastrach HDInsight](hdinsight-hadoop-giraph-install-linux.md). Dostosowywanie klastra należy zainstalować Giraph w klastrach HDInsight Hadoop. Giraph umożliwia wykonywanie przetwarzania wykresu przy użyciu platformy Hadoop i może służyć z usługą Azure HDInsight.
* [Zainstaluj Solr w klastrach HDInsight](hdinsight-hadoop-solr-install-linux.md). Dostosowywanie klastra należy zainstalować Solr w klastrach HDInsight Hadoop. Solr umożliwia wykonywanie operacji wyszukiwania zaawansowanego w przechowywanych danych.
* [Zainstalować język R w klastrach HDInsight](hdinsight-hadoop-r-scripts-linux.md). Dostosowywanie klastra należy zainstalować R w klastrach HDInsight Hadoop. R jest open source języka i środowiska do statystycznego przetwarzania danych. Zapewnia on setki wbudowanych funkcji statystycznych i własnej język programowania, łączącą aspekty funkcjonalne i zorientowanym obiektowo programowania. Umożliwia także rozbudowane funkcje graficzne.

[powershell-install-configure]: install-configure-powershell-linux.md
[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
