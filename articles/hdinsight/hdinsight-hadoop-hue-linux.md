---
title: Barwa z Hadoopem w klastrach opartych na systemie HDInsight opartych na systemie Linux — Azure
description: Dowiedz się, jak zainstalować program Hue w klastrach HDInsight i użyć tunelowania do kierowania żądań do hue. Użyj funkcji Barwa, aby przeglądać pamięć masową i uruchamiać hive lub pig.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 11/28/2019
ms.openlocfilehash: 69acfd4f2edab9be1b1dcfbb52eafbd00aec712f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934563"
---
# <a name="install-and-use-hue-on-hdinsight-hadoop-clusters"></a>Instalowanie i używanie funkcji Hue w klastrach programu HDInsight Hadoop

Dowiedz się, jak zainstalować program Hue w klastrach HDInsight i użyć tunelowania do kierowania żądań do hue.

## <a name="what-is-hue"></a>Co to jest Hue?

Hue to zestaw aplikacji sieci Web używanych do interakcji z klastrem Apache Hadoop. Za pomocą funkcji Hue można przeglądać magazyn skojarzony z klastrem Hadoop (WASB, w przypadku klastrów HDInsight), uruchamiać zadania hive i skrypty Pig itd. Następujące składniki są dostępne w instalacjach Hue w klastrze HDInsight Hadoop.

* Edytor ulów wosku pszczelego
* Świnia Apache
* Kierownik sklepu metastore
* Apache Oozie
* FileBrowser (który mówi do domyślnego kontenera WASB)
* Przeglądarka zadań

> [!WARNING]  
> Składniki dostarczane z klastrem HDInsight są w pełni obsługiwane, a pomoc techniczna firmy Microsoft pomaga wyizolowaniu i rozwiązywaniu problemów związanych z tymi składnikami.
>
> Składniki niestandardowe otrzymują komercyjnie uzasadnione wsparcie, które pomoże Ci w dalszym rozwiązywaniu problemu. Może to spowodować rozwiązanie problemu lub z prośbą o zaangażowanie dostępnych kanałów dla technologii open source, w których znajduje się głęboka wiedza specjalistyczna w zakresie tej technologii. Na przykład istnieje wiele witryn społecznościowych, z których można korzystać, [https://stackoverflow.com](https://stackoverflow.com)takich jak: [forum MSDN dla HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), . Również projekty Apache mają [https://apache.org](https://apache.org)miejsca projektu na , na przykład: [Hadoop](https://hadoop.apache.org/).

## <a name="install-hue-using-script-actions"></a>Instalowanie barwy przy użyciu akcji skryptu

Użyj informacji w poniższej tabeli dla akcji skryptu. Aby uzyskać szczegółowe instrukcje dotyczące używania akcji skryptów, zobacz [Dostosowywanie klastrów usługi HDInsight](hdinsight-hadoop-customize-cluster-linux.md) za pomocą akcji skryptów.

> [!NOTE]  
> Aby zainstalować barwę w klastrach HDInsight, zalecany rozmiar węzła głównego to co najmniej A4 (8 rdzeni, 14 GB pamięci).

|Właściwość |Wartość |
|---|---|
|Typ skryptu:|- Niestandardowe|
|Nazwa|Zainstaluj barwę|
|Identyfikator URI skryptu bash|`https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh`|
|Typ(-y) węzła:|Head|

## <a name="use-hue-with-hdinsight-clusters"></a>Używanie funkcji Barwa z klastrami HDInsight

Tunelowanie SSH jest jedynym sposobem uzyskania dostępu do hue w klastrze po jego uruchomieniu. Tunelowanie przez SSH umożliwia ruch, aby przejść bezpośrednio do headnode klastra, w którym hue jest uruchomiony. Po zakończeniu inicjowania obsługi administracyjnej klastra należy wykonać następujące kroki, aby użyć funkcji Hue w klastrze HDInsight.

> [!NOTE]  
> Zalecamy korzystanie z przeglądarki Firefox, aby postępować zgodnie z poniższymi instrukcjami.

1. Użyj informacji w [użyciu SSH Tunelowanie, aby uzyskać dostęp do interfejsu użytkownika sieci Web Apache Ambari, ResourceManager, JobHistory, NameNode, Oozie i innych interfejsów internetowych,](hdinsight-linux-ambari-ssh-tunnel.md) aby utworzyć tunel SSH z systemu klienckiego do klastra HDInsight, a następnie skonfigurować przeglądarkę sieci Web do używania tunelu SSH jako serwera proxy.

1. Użyj [polecenia ssh,](./hdinsight-hadoop-linux-use-ssh-unix.md) aby połączyć się z klastrem. Edytuj poniższe polecenie, zastępując clustername nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Po nawiązaniu połączenia użyj następującego polecenia, aby uzyskać w pełni kwalifikowaną nazwę domeny głównego klucza głównego:

    ```bash
    hostname -f
    ```

    Spowoduje to zwrócenie nazwy podobnej do następującej:

        myhdi-nfebtpfdv1nubcidphpap2eq2b.ex.internal.cloudapp.net

    Jest to nazwa hosta głównego pliku głównego, w którym znajduje się strona internetowa Hue.

1. Użyj przeglądarki, aby otworzyć portal `http://HOSTNAME:8888`Hue w . Zamień nazwę HOSTNAME na nazwę uzyskaną w poprzednim kroku.

   > [!NOTE]  
   > Po zalogowaniu się po raz pierwszy zostanie wyświetlony monit o utworzenie konta w celu zalogowania się do portalu Hue. Poświadczenia określone w tym miejscu będą ograniczone do portalu i nie są powiązane z poświadczeniami administratora lub SSH użytkownika określonymi podczas inicjowania obsługi administracyjnej klastra.

    ![Okno logowania portalu HDInsight hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-login.png "Określanie poświadczeń dla portalu barwy")

### <a name="run-a-hive-query"></a>Uruchomienie zapytania programu Hive

1. W portalu Hue wybierz pozycję **Edytory zapytań**, a następnie wybierz pozycję **Gałąź,** aby otworzyć edytor hive.

    ![Portal hdinsight hue używa edytora gałęzi](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-use-hive.png "Użyj gałęzi")

2. Na karcie **Asysta** w obszarze **Baza danych**powinna zostać **wyświetlona liczba rozmropnych**. Jest to przykładowa tabela, która jest dostarczana ze wszystkimi klastrami Hadoop w programie HDInsight. Wprowadź przykładową kwerendę w prawym okienku i zobacz dane wyjściowe na karcie **Wyniki** w okienku poniżej, jak pokazano na ekranie przechwytywania.

    ![Zapytanie gałęzi portalu HDInsight hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-hive-query.png "Uruchamianie kwerendy gałęzi")

    Można również użyć karty **Wykres,** aby wyświetlić wizualną reprezentację wyniku.

### <a name="browse-the-cluster-storage"></a>Przeglądanie magazynu klastra

1. W portalu Barwy wybierz **pozycję Przeglądarka plików** w prawym górnym rogu paska menu.
2. Domyślnie przeglądarka plików zostanie otwarta w katalogu **/user/myuser.** Wybierz ukośnik do przodu tuż przed katalogiem użytkownika w ścieżce, aby przejść do katalogu głównego kontenera magazynu platformy Azure skojarzonego z klastrem.

    ![Przeglądarka plików portalu HDInsight hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-file-browser.png "Korzystanie z przeglądarki plików")

3. Kliknij prawym przyciskiem myszy plik lub folder, aby wyświetlić dostępne operacje. Użyj przycisku **Przekaż** w prawym rogu, aby przekazać pliki do bieżącego katalogu. Użyj przycisku **Nowy,** aby utworzyć nowe pliki lub katalogi.

> [!NOTE]  
> Przeglądarka plików Barwy może wyświetlać tylko zawartość domyślnego kontenera skojarzonego z klastrem HDInsight. Wszelkie dodatkowe konta/kontenery magazynu, które mogły być skojarzone z klastrem, nie będą dostępne za pomocą przeglądarki plików. Jednak dodatkowe kontenery skojarzone z klastrem będą zawsze dostępne dla zadań hive. Na przykład po wprowadzeniu `dfs -ls wasbs://newcontainer@mystore.blob.core.windows.net` polecenia w edytorze hive, można zobaczyć zawartość dodatkowych kontenerów, jak również. W tym poleceniu **newcontainer** nie jest domyślnym kontenerem skojarzonym z klastrem.

## <a name="important-considerations"></a>Istotne zagadnienia

1. Skrypt używany do zainstalowania hue instaluje go tylko na głównym kluczu klastra.

1. Podczas instalacji wiele usług Hadoop (HDFS, YARN, MR2, Oozie) jest ponownie uruchamianych w celu aktualizacji konfiguracji. Po zakończeniu instalacji skryptu może upłynąć trochę czasu, aby uruchomić inne usługi Hadoop. Może to początkowo wpłynąć na wydajność firmy Hue. Po uruchomieniu wszystkich usług, Hue będzie w pełni funkcjonalny.

1. Hue nie rozumie apache Tez zadań, który jest bieżący domyślnie hive. Jeśli chcesz użyć MapReduce jako aparat wykonywania hive, zaktualizuj skrypt, aby użyć następującego polecenia w skrypcie:

        set hive.execution.engine=mr;

1. W klastrach systemu Linux może istnieć scenariusz, w którym usługi są uruchomione na głównym niu głównym, podczas gdy Menedżer zasobów może być uruchomiony w pomocniczym. Taki scenariusz może spowodować błędy (pokazane poniżej) podczas korzystania z Hue, aby wyświetlić szczegóły uruchomionych zadań w klastrze. Można jednak wyświetlić szczegóły zadania po zakończeniu zadania.

   ![Przykładowy komunikat o błędzie portalu Hue](./media/hdinsight-hadoop-hue-linux/hdinsight-hue-portal-error.png "Błąd portalu Barwy")

   Jest to spowodowane znanym problemem. Aby obejść ten problem, zmodyfikuj ambari, aby aktywny Menedżer zasobów działał również na głównym kluczu głównym.

1. Hue rozumie usługę WebHDFS, podczas gdy klastry usługi HDInsight korzystają z usługi Azure Storage przy użyciu programu `wasbs://`. Tak więc niestandardowy skrypt używany z działaniem skryptu instaluje WebWasb, który jest usługą kompatybilną z WebHDFS do rozmowy z WASB. Tak więc, nawet jeśli portal Hue mówi HDFS w miejscach (jak podczas przesuwania myszą na **przeglądarkę plików),** należy go interpretować jako WASB.

## <a name="next-steps"></a>Następne kroki

[Zainstaluj R na klastrach HDInsight](hdinsight-hadoop-r-scripts-linux.md). Użyj dostosowywania klastra, aby zainstalować R na klastrach HDInsight Hadoop. R jest językiem open source i środowiskiem obliczeń statystycznych. Zapewnia setki wbudowanych funkcji statystycznych i własny język programowania, który łączy aspekty programowania funkcjonalnego i obiektowego. Zapewnia również szerokie możliwości graficzne.
