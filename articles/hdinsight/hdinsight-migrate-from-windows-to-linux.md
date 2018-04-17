---
title: Migrację z usługi HDInsight opartej na systemie Windows do usługi HDInsight opartej na systemie Linux - Azure | Dokumentacja firmy Microsoft
description: Informacje o migracji z klastra usługi HDInsight opartej na systemie Windows do klastra usługi HDInsight opartej na systemie Linux.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: ff35be59-bae3-42fd-9edc-77f0041bab93
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 6d4b537834b8a70f0015fc9c6316dae20af5dd5a
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Migracji z klastra usługi HDInsight opartej na systemie Windows do klastra z systemem Linux

Ten dokument zawiera szczegółowe informacje na temat różnic między HDInsight w systemie Windows i Linux. Zawiera również wskazówki dotyczące sposobu przeprowadzenia migracji do klastra z systemem Linux istniejących obciążeń.

HDInsight opartych na systemie Windows zapewnia prosty sposób użycia platformy Hadoop w chmurze, jednocześnie może być konieczne migracji do klastra z systemem Linux. Na przykład, aby korzystać z narzędzia oparte na systemie Linux i technologie, które są wymagane do rozwiązania. Wiele rzeczy w ekosystemie Hadoop są tworzone w systemach opartych na systemie Linux, a nie mogą być dostępne do użycia z usługą HDInsight z systemu Windows. Wiele książek, wideo i innych materiałów szkoleniowych założono, że korzystasz z systemu Linux podczas pracy z platformą Hadoop.

> [!NOTE]
> Klastry HDInsight użyć funkcji długoterminowej Ubuntu (LTS) jako systemu operacyjnego węzłów w klastrze. Aby uzyskać informacje na wersję Ubuntu dostępne z usługą HDInsight, oraz inne informacje na temat wersji składnika, zobacz [wersji składnika usługi HDInsight](hdinsight-component-versioning.md).

## <a name="migration-tasks"></a>Zadania migracji

Poniżej przedstawiono ogólny przepływ pracy do migracji.

![Diagram przepływu pracy migracji](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1. Przeczytaj każdej sekcji tego dokumentu, aby zrozumieć zmiany, które mogą być wymagane podczas migracji.

2. Tworzenie klastra opartych na systemie Linux jako środowiska gwarancji testu/jakości. Aby uzyskać więcej informacji o tworzeniu klastra opartych na systemie Linux, zobacz [opartych na systemie Linux z tworzenia klastrów w usłudze HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

3. Skopiuj zadania istniejące źródła danych i wychwytywanie do nowego środowiska.

4. Należy przeprowadzić testy weryfikacji, aby upewnić się, że zadaniach działać zgodnie z oczekiwaniami w nowym klastrze.

Po upewnieniu się, że wszystko działa zgodnie z oczekiwaniami, należy zaplanować przestój do migracji. Podczas tego przestojów wykonaj następujące czynności:

1. Utwórz kopię zapasową przejściowej danych przechowywanych lokalnie w węzłach klastra. Jeśli na przykład dane przechowywane bezpośrednio na węzła głównego.

2. Usuwanie klastra z systemem Windows.

3. Tworzenie klastra opartych na systemie Linux przy użyciu tego samego magazynu danych domyślny, który był używany przez klaster systemu Windows. Opartych na systemie Linux klastrów mogą kontynuować pracę z istniejących danych produkcyjnych.

4. Importuj wszystkie przejściowej dane kopii zapasowej.

5. Uruchom zadania/Kontynuuj przetwarzanie przy użyciu nowego klastra.

### <a name="copy-data-to-the-test-environment"></a>Kopiowanie danych do środowiska testowego

Istnieje wiele metod, aby skopiować dane i zadania, jednak omówione w tej sekcji są najprostszym metody służące do bezpośredniego przenoszenia plików do klastra testowego.

#### <a name="hdfs-copy"></a>System plików HDFS kopiowania

Wykonaj następujące kroki, aby skopiować dane z klastra produkcyjnego do klastra testowego. Użyj tych kroków `hdfs dfs` narzędzia, która jest zawarta w usłudze HDInsight.

1. Informacje magazynu konto i domyślny kontener dla istniejącego klastra. W poniższym przykładzie użyto programu PowerShell, aby pobrać te informacje:

    ```powershell
    $clusterName="Your existing HDInsight cluster name"
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
    write-host "Default container: $clusterInfo.DefaultStorageContainer"
    ```

2. Aby utworzyć środowisko testowe, wykonaj kroki w klastrach opartych na systemie Linux z utworzyć w dokumencie usługi HDInsight. Przed rozpoczęciem tworzenia klastra, a zamiast tego wybrać **konfiguracji opcjonalnej**.

3. Sekcja konfiguracji opcjonalnej zaznacz **połączonych kontach magazynu**.

4. Wybierz **Dodaj klucz magazynu**i po wyświetleniu monitu wybierz konto magazynu, który został zwrócony przez skrypt programu PowerShell w kroku 1. Kliknij przycisk **wybierz** w każdej sekcji. Na koniec Utwórz klaster.

5. Po utworzeniu klastra nawiązać z nią za pomocą **SSH.** Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

6. W sesji SSH Użyj następującego polecenia, aby skopiować pliki z połączonym koncie magazynu do nowego domyślnego konta magazynu. Zastąp kontenera informacje o kontenerze zwrócony przez programu PowerShell. Zastąp __konta__ o takiej nazwie. Ścieżka do danych Zamień na ścieżkę do pliku danych.

    ```bash
    hdfs dfs -cp wasb://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location
    ```

    > [!NOTE]
    > Jeśli struktura katalogów, która zawiera dane, nie istnieje w środowisku testowym, można utworzyć za pomocą następującego polecenia:

    ```bash
    hdfs dfs -mkdir -p /new/path/to/create
    ```

    `-p` Przełącznik umożliwia tworzenie katalogów w ścieżce.

#### <a name="direct-copy-between-blobs-in-azure-storage"></a>Bezpośrednie kopiowania między obiekty BLOB w usłudze Azure Storage

Alternatywnie można użyć `Start-AzureStorageBlobCopy` polecenia cmdlet programu Azure PowerShell do kopiowania obiektów blob między kontami magazynu poza usługą HDInsight. Aby uzyskać więcej informacji, zobacz temat jak zarządzać sekcji obiektów blob Azure przy użyciu programu Azure PowerShell z usługą Azure Storage.

## <a name="client-side-technologies"></a>Technologie po stronie klienta

Technologie po stronie klienta, takich jak [poleceń cmdlet programu Azure PowerShell](/powershell/azureps-cmdlets-docs), [interfejsu wiersza polecenia Azure](../cli-install-nodejs.md), lub [zestawu .NET SDK dla platformy Hadoop](https://hadoopsdk.codeplex.com/) kontynuowanie pracy opartych na systemie Linux klastrów. Te technologie korzystają z interfejsów API REST, które są takie same w obu typów klastra systemu operacyjnego.

## <a name="server-side-technologies"></a>Technologie serwerowe

Poniższa tabela zawiera wskazówki dotyczące migrowania składniki po stronie serwera, które są specyficzne dla systemu Windows.

| Jeśli używasz tej technologii... | Wykonanie tej czynności... |
| --- | --- |
| **PowerShell** (skrypty po stronie serwera, w tym akcji skryptu używane podczas tworzenia klastra) |Ponownego zapisywania jako skrypty Bash. Dla akcji skryptu, zobacz [systemem Linux dostosować usługi HDInsight za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md) i [skryptu programowanie akcji dla usługi HDInsight opartej na systemie Linux](hdinsight-hadoop-script-actions-linux.md). |
| **Azure CLI** (skrypty po stronie serwera) |Chociaż wiersza polecenia platformy Azure jest dostępna w systemie Linux, go nie są zainstalowane w głównymi węzłami klastra usługi HDInsight. Aby uzyskać więcej informacji na temat instalowania interfejsu wiersza polecenia Azure, zobacz [wprowadzenie Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli). |
| **Składniki platformy .NET** |.NET jest obsługiwana w HDInsight opartych na systemie Linux za pomocą [Mono](https://mono-project.com). Aby uzyskać więcej informacji, zobacz [migracji .NET rozwiązań opartych na systemie Linux usługi HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md). |
| **Składniki Win32 lub innych technologii systemu Windows** |Wskazówki dotyczące zależy od składnika lub technologii. Dzięki temu można znaleźć wersji, która jest zgodna z systemem Linux. Jeśli nie, należy znaleźć rozwiązania alternatywne lub przepisywania tego składnika. |

> [!IMPORTANT]
> Zarządzanie HDInsight SDK nie jest całkowicie zgodnej z Mono. Nie należy używać go jako część rozwiązania, które są wdrażane w klastrze usługi HDInsight.

## <a name="cluster-creation"></a>Tworzenie klastra

Ta sekcja zawiera informacje na temat różnic w tworzenia klastra.

### <a name="ssh-user"></a>SSH użytkownika

Opartych na systemie Linux klastrów usługi HDInsight użyj **Secure Shell (SSH)** protokołu umożliwia zdalny dostęp do węzłów klastra. W przeciwieństwie do klastrów z systemem Windows dla pulpitu zdalnego większość klientów SSH nie udostępniają graficznego użytkowników. Zamiast tego klientów SSH podać wiersz polecenia, który służy do uruchamiania poleceń w klastrze. Niektórzy klienci (takich jak [MobaXterm](http://mobaxterm.mobatek.net/)) podaj przeglądarki systemu plików graficznych, oprócz zdalnego wiersza polecenia.

Podczas tworzenia klastra, należy określić użytkownika SSH i albo **hasło** lub **certyfikatu klucza publicznego** do uwierzytelniania.

Zalecamy używanie certyfikatu klucza publicznego, ponieważ jest bezpieczniejsza niż używanie hasła. Uwierzytelnianie certyfikatu polega na generowanie podpisem pary kluczy publiczny/prywatny, a następnie podając klucza publicznego, podczas tworzenia klastra. Podczas nawiązywania połączenia z serwerem przy użyciu protokołu SSH, klucza prywatnego na komputerze klienckim zapewnia uwierzytelnianie dla połączenia.

Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="cluster-customization"></a>Dostosowywanie klastra

**Akcje skryptu** używane z opartą na systemie Linux klastrów musi być napisana w skrypcie Bash. Opartych na systemie Linux klastrów za pomocą akcji skryptów podczas lub po utworzeniu klastra. Aby uzyskać więcej informacji, zobacz [systemem Linux dostosować usługi HDInsight za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md) i [skryptu programowanie akcji dla usługi HDInsight opartej na systemie Linux](hdinsight-hadoop-script-actions-linux.md).

Jest inna funkcja dostosowywania **bootstrap**. W przypadku klastrów systemu Windows ta funkcja umożliwia określenie lokalizacji dodatkowe biblioteki do użycia z gałęzi. Po utworzeniu klastra te biblioteki są automatycznie dostępne do użycia z zapytań programu Hive bez konieczności użycia `ADD JAR`.

Funkcję inicjowania opartych na systemie Linux klastrów nie zapewnia tę funkcję. Zamiast tego należy użyć akcji skryptu w [dodać Hive bibliotek podczas tworzenia klastra](hdinsight-hadoop-add-hive-libraries.md).

### <a name="virtual-networks"></a>Sieci wirtualne

HDInsight opartych na systemie Windows klastrów działać tylko w przypadku klasycznych sieci wirtualnych, podczas gdy klastrów usługi HDInsight opartych na systemie Linux wymagają sieci wirtualnych Menedżera zasobów. Jeśli masz zasobów w sieci wirtualnej klasycznego klastra usługi HDInsight Linux należy nawiązać połączenie, zobacz [łączenie klasycznych sieci wirtualnych do sieci wirtualnych Menedżera zasobów](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

Aby uzyskać więcej informacji dotyczących wymagań dotyczących konfiguracji, zobacz [możliwości rozszerzania HDInsight przy użyciu sieci wirtualnej](hdinsight-extend-hadoop-virtual-network.md) dokumentu.

## <a name="management-and-monitoring"></a>Zarządzanie i monitorowanie

Wiele sieci web UI użyto z HDInsight opartych na systemie Windows, takich jak Historia zadań lub Yarn interfejsu użytkownika, są dostępne za pośrednictwem narzędzia Ambari. Ponadto widoku Hive narzędzia Ambari umożliwia uruchamianie zapytań Hive przy użyciu przeglądarki sieci web. Interfejs sieci Web Ambari jest dostępna w klastrach opartych na systemie Linux na https://CLUSTERNAME.azurehdinsight.net.

Aby uzyskać więcej informacji na temat pracy z narzędzia Ambari można znaleźć w następujących dokumentach:

* [Ambari Web](hdinsight-hadoop-manage-ambari.md)
* [Interfejs API REST Ambari](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Alerty Ambari

Ambari ma system alertów można ustalić o potencjalnych problemach z klastrem. Alerty są wyświetlane jako czerwony lub żółty wpisów w interfejsie użytkownika sieci Web Ambari, jednak może również pobierać za pośrednictwem interfejsu API REST.

> [!IMPORTANT]
> Alerty Ambari wskazują, że istnieje *może* to stanowić problem, nie czy nie *jest* problem. Na przykład otrzymasz alert, że nie można uzyskać dostępu do serwera HiveServer2, mimo że można do niego dostęp normalnie.
>
> Wiele alertów są zaimplementowane jako określonych odstępach czasu zapytań dotyczących usługi i oczekują odpowiedzi w określonym przedziale czasu. Aby alert nie musi oznaczać, że usługa nie działa, ale nie zwraca wyników w oczekiwanym czasie.

## <a name="file-system-locations"></a>Lokalizacje w systemie plików

System Linux klaster plików jest rozmieszczona inaczej niż klastrów usługi HDInsight opartych na systemie Windows. Znajdź najczęściej używanych plików, skorzystaj z poniższej tabeli.

| Chcę znaleźć... | Jest on umieszczony... |
| --- | --- |
| Konfigurowanie |`/etc`. Na przykład: `/etc/hadoop/conf/core-site.xml` |
| Pliki dziennika |`/var/logs` |
| Hortonworks Data Platform (HDP) |`/usr/hdp`. Istnieją dwa katalogi znajduje się w tym miejscu jest bieżąca wersja HDP i `current`. `current` Katalog zawiera linki symboliczne do plików i katalogów znajduje się w katalogu numeru wersji. `current` Katalogu została podana jako wygodny sposób uzyskiwania dostępu do plików HDP od zmiany numeru wersji jako HDP wersja jest aktualizowana. |
| hadoop streaming.jar |`/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

Ogólnie rzecz biorąc Jeśli znasz nazwę pliku, służy następujące polecenie w sesji SSH można znaleźć ścieżki pliku:

    find / -name FILENAME 2>/dev/null

Można również użyć symboli wieloznacznych, z nazwą pliku. Na przykład `find / -name *streaming*.jar 2>/dev/null` zwraca ścieżkę do plików jar zawierające słowo strumienia, jako część nazwy pliku.

## <a name="hive-pig-and-mapreduce"></a>Hive, Pig i MapReduce

Pig i MapReduce obciążeń są podobne w klastrach opartych na systemie Linux. Jednak klastrów usługi HDInsight opartych na systemie Linux mogą być tworzone przy użyciu nowszej wersji platformy Hadoop, Hive i Pig. Te różnice wersji może spowodować zmiany w sposób istniejących funkcji rozwiązania. Aby uzyskać więcej informacji o wersji składników zawartych z usługą HDInsight, zobacz [przechowywanie wersji składnika usługi HDInsight](hdinsight-component-versioning.md).

HDInsight opartych na systemie Linux nie zapewnia funkcje pulpitu zdalnego. Zamiast tego można użyć SSH zdalne połączenia z głównymi węzłami klastra. Aby uzyskać więcej informacji można znaleźć w następujących dokumentach:

* [Korzystanie z programu Hive przy użyciu protokołu SSH](hdinsight-hadoop-use-hive-ssh.md)
* [Korzystanie z języka Pig przy użyciu protokołu SSH](hadoop/apache-hadoop-use-pig-ssh.md)
* [Korzystać z usługi MapReduce przy użyciu protokołu SSH](hadoop/apache-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive

> [!IMPORTANT]
> Jeśli używasz zewnętrznego potrzeby magazynu metadanych Hive, przed użyciem go z usługi HDInsight opartej na systemie Linux należy kopię zapasową potrzeby magazynu metadanych. HDInsight opartych na systemie Linux jest dostępna z nowszymi wersjami programu Hive, który może mieć niezgodności z magazyny utworzone we wcześniejszych wersjach.

Poniżej znajdują się wskazówki dotyczące migracji obciążeń Hive.

| Na podstawie systemu Windows, używać... | Na opartych na systemie Linux... |
| --- | --- |
| **Edytor hive** |[Widok hive narzędzia Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;` Aby umożliwić aplikacji Tez |Tez jest domyślny aparat wykonywania opartych na systemie Linux klastrów, więc instrukcji set nie jest już potrzebne. |
| C# zdefiniowane przez użytkownika funkcji | Aby uzyskać informacje na weryfikacji składniki C# z opartą na systemie Linux usługą HDInsight, zobacz [rozwiązań .NET migracji do usługi HDInsight opartej na systemie Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| Pliki CMD lub skryptów na serwerze, który został wywołany jako część zadania Hive |Użyj skrypty powłoki systemowej |
| `hive` polecenie z pulpitu zdalnego |Użyj [Beeline](hadoop/apache-hadoop-use-hive-beeline.md) lub [Hive w sesji SSH](hdinsight-hadoop-use-hive-ssh.md) |

### <a name="pig"></a>Pig

| Na podstawie systemu Windows, używać... | Na opartych na systemie Linux... |
| --- | --- |
| C# zdefiniowane przez użytkownika funkcji | Aby uzyskać informacje na weryfikacji składniki C# z opartą na systemie Linux usługą HDInsight, zobacz [rozwiązań .NET migracji do usługi HDInsight opartej na systemie Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| Pliki CMD lub skryptów na serwerze, który został wywołany jako część zadania Pig |Użyj skrypty powłoki systemowej |

### <a name="mapreduce"></a>MapReduce

| Na podstawie systemu Windows, używać... | Na opartych na systemie Linux... |
| --- | --- |
| C# mapowania i reduktor składników | Aby uzyskać informacje na weryfikacji składniki C# z opartą na systemie Linux usługą HDInsight, zobacz [rozwiązań .NET migracji do usługi HDInsight opartej na systemie Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| Pliki CMD lub skryptów na serwerze, który został wywołany jako część zadania Hive |Użyj skrypty powłoki systemowej |

## <a name="oozie"></a>Oozie

> [!IMPORTANT]
> Jeśli używasz zewnętrznego potrzeby magazynu metadanych Oozie, przed użyciem go z usługi HDInsight opartej na systemie Linux należy kopię zapasową potrzeby magazynu metadanych. HDInsight opartych na systemie Linux jest dostępna z nowszymi wersjami programu Oozie, który może mieć niezgodności z magazyny utworzone we wcześniejszych wersjach.

Przepływy pracy Oozie umożliwiają akcji powłoki. Działania powłoki przy użyciu domyślną powłokę systemu operacyjnego do uruchomienia poleceń wiersza polecenia. Jeśli masz Oozie przepływy pracy, które opierają się na powłoce systemu Windows muszą zmodyfikować przepływy pracy, aby polegać na środowisku powłoki systemu Linux (Bash). Aby uzyskać więcej informacji o używaniu akcje powłoki z Oozie zobacz [rozszerzenie akcji powłoki Oozie](http://oozie.apache.org/docs/3.3.0/DG_ShellActionExtension.html).

Jeśli przepływ pracy, który korzysta z aplikacji C#, sprawdź poprawność tych aplikacji w środowisku systemu Linux. Aby uzyskać więcej informacji, zobacz [migracji .NET rozwiązań opartych na systemie Linux usługi HDInsight](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="storm"></a>Storm

| Na podstawie systemu Windows, używać... | Na opartych na systemie Linux... |
| --- | --- |
| Pulpit nawigacyjny STORM |Pulpit nawigacyjny Storm jest niedostępna. Zobacz [topologie wdrażania i zarządzania Storm w usłudze HDInsight z systemem Linux](storm/apache-storm-deploy-monitor-topology-linux.md) sposobów przesyłania topologii |
| STORM interfejsu użytkownika |Interfejs użytkownika platformy Storm znajduje się w temacie https://CLUSTERNAME.azurehdinsight.net/stormui |
| Visual Studio umożliwia tworzenie, wdrażanie i zarządzanie C# i hybrydowych topologii |Program Visual Studio umożliwia tworzenie, wdrażanie i zarządzanie języka C# (SCP.NET) lub hybrydowe topologie na opartych na systemie Linux Storm w usłudze HDInsight. Można można używać tylko z klastrami utworzone po 10/28/2016. |

## <a name="hbase"></a>HBase

W klastrach opartych na systemie Linux, nadrzędny znode bazy danych hbase jest `/hbase-unsecure`. Ustaw tę wartość w konfiguracji dla dowolnego klienta Java aplikacji, które używają natywnego interfejsu API języka Java HBase.

Zobacz [tworzenia aplikacji opartych na języku Java HBase](hdinsight-hbase-build-java-maven.md) dla klienta przykład ustawiający tę wartość.

## <a name="spark"></a>platforma Spark

Klastry Spark były dostępne w klastrach z systemem Windows w wersji zapoznawczej. Spark GA jest dostępna tylko w klastrach opartych na systemie Linux. Brak nie ścieżki migracji z klastra z systemem Windows Spark w wersji zapoznawczej do klastra Spark opartych na systemie Linux wersji.

## <a name="known-issues"></a>Znane problemy

### <a name="azure-data-factory-custom-net-activities"></a>Azure fabryki danych niestandardowych działań platformy .NET

Azure fabryki danych niestandardowych działań platformy .NET nie są obecnie obsługiwane w klastrach HDInsight opartych na systemie Linux. Zamiast tego należy jedną z następujących metod używać do implementowania niestandardowych działań w ramach planowaną ADF.

* Wykonanie działań platformy .NET w puli partii zadań Azure. Zobacz partii zadań Azure Użyj połączone usługi części [skorzystać z działań niestandardowych w potoku fabryki danych Azure](../data-factory/transform-data-using-dotnet-custom-activity.md)
* Wykonuje działanie jako działania MapReduce. Aby uzyskać więcej informacji, zobacz [wywołania programy MapReduce z fabryki danych](../data-factory/transform-data-using-hadoop-map-reduce.md).

### <a name="line-endings"></a>Zakończenia wierszy

Ogólnie rzecz biorąc zakończenia wierszy na komputerach z systemem Windows użyj CRLF, podczas gdy LF systemów opartych na systemie Linux. Należy zmodyfikować istniejący producentów danych i konsumentów do pracy z LF.

Na przykład usługi HDInsight w klastrze z systemem Windows przy użyciu programu Azure PowerShell zapytanie zwraca dane z CRLF. Samo zapytanie z klastrem opartych na systemie Linux zwraca wysuwu wiersza. Sprawdź, czy Zakończenie wiersza powoduje, że problem z rozwiązania przed przeprowadzeniem migracji do klastra z systemem Linux.

Zawsze używaj LF jako wiersz Kończenie dla skryptów uruchamianych w węzłach klastra. Jeśli używasz CRLF, mogą zostać wyświetlone błędy podczas uruchamiania skryptów w klastrze z systemem Linux.

Jeśli skrypty nie zawierają ciągi zawierające osadzonych znaków powrotu Karetki, można zbiorczo Zmień zakończenia wierszy przy użyciu jednej z następujących metod:

* **Przed przekazaniem do klastra**: zmiany zakończenia wierszy ze CRLF LF przed przekazaniem skrypt do klastra należy zastosować następujące polecenia programu PowerShell.

    ```powershell
    $original_file ='c:\path\to\script.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)
    ```

* **Po przekazaniu do klastra**: Użyj następującego polecenia w sesji SSH opartych na systemie Linux klastrem można zmodyfikować skrypt.

    ```bash
    hdfs dfs -get wasb:///path/to/script.py oldscript.py
    tr -d '\r' < oldscript.py > script.py
    hdfs dfs -put -f script.py wasb:///path/to/script.py
    ```

## <a name="next-steps"></a>Następne kroki

* [Informacje o sposobie tworzenia klastrów usługi HDInsight opartej na systemie Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Używanie protokołu SSH do nawiązania połączenia usługi HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Zarządzanie klastrem opartych na systemie Linux przy użyciu narzędzia Ambari](hdinsight-hadoop-manage-ambari.md)
