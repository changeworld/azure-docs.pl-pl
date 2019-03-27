---
title: Migracja z HDInsight z systemem Windows do HDInsight opartych na systemie Linux — platformy Azure
description: Dowiedz się, jak przeprowadzić migrację z klastra HDInsight z systemem Windows w klastrze HDInsight opartych na systemie Linux.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.author: hrasheed
ms.openlocfilehash: ec40c587ea4c609b1f8201e9860eb0e98e06ec62
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58448187"
---
# <a name="migrate-from-a-windows-based-hdinsight-cluster-to-a-linux-based-cluster"></a>Migracja z klastra HDInsight z systemem Windows do klastra opartego na systemie Linux

Ten dokument zawiera szczegółowe informacje na temat różnic między HDInsight na Windows i Linux. Zawiera także wskazówki dotyczące migrowania istniejących obciążeń do klastra opartego na systemie Linux.

HDInsight oparte na Windows zapewnia łatwy sposób korzystać z technologii Apache Hadoop w chmurze, jednocześnie może być konieczne migracji do klastra z systemem Linux. Na przykład, aby móc korzystać z narzędzi opartych na systemie Linux i technologii, które są wymagane do rozwiązania. Wiele elementów w ekosystemie usługi Hadoop są opracowywane w systemach opartych na systemie Linux i mogą nie być dostępne do użycia z usługą HDInsight z systemem Windows. Wiele książek, materiały wideo i inne materiały szkoleniowe przyjęto założenie, że używasz systemu Linux podczas pracy z usługą Hadoop.

> [!NOTE]  
> Klastry HDInsight użyć długoterminowe pomocy technicznej Ubuntu (LTS) jako systemu operacyjnego dla węzłów w klastrze. Aby uzyskać informacji na temat wersji ubuntu, które są dostępne z HDInsight, oraz inne informacje na temat wersji składnika, zobacz [wersje składników HDInsight](hdinsight-component-versioning.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="migration-tasks"></a>Zadania migracji

Ogólny przepływ pracy migracji jest następujący.

![Diagram przepływu pracy migracji](./media/hdinsight-migrate-from-windows-to-linux/workflow.png)

1. Przeczytaj sekcję tego dokumentu, aby zrozumieć zmiany, które mogą być wymagane w przypadku migracji.

2. Utwórz klaster oparty na systemie Linux jako środowisko assurance jakość/testu. Aby uzyskać więcej informacji o tworzeniu klastra opartego na systemie Linux, zobacz [opartych na systemie Linux z Tworzenie klastrów w HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

3. Kopiowanie istniejących zadań, danych źródła i ujścia, do nowego środowiska.

4. Należy przeprowadzić testy sprawdzania poprawności, aby upewnić się, że Twoje zadania działają zgodnie z oczekiwaniami w nowym klastrze.

Po upewnieniu się, że wszystko działa zgodnie z oczekiwaniami, należy zaplanować przestój w związku z migracją. Podczas tej awarii wykonaj następujące czynności:

1. Utwórz kopię zapasową wszelkich danych przejściowych przechowywane lokalnie w węzłach klastra. Na przykład, jeśli masz dane zapisane bezpośrednio na węzła głównego.

2. Usuwanie klastra z systemem Windows.

3. Utwórz klaster oparty na systemie Linux przy użyciu tego samego magazynu danych domyślne używany z klastrami systemu Windows. Klaster oparty na systemie Linux mogą kontynuować pracę, względem istniejących danych produkcyjnych.

4. Importowanie danych przejściowych, których kopię zapasową.

5. Rozpoczęcie zadania/kontynuować przetwarzanie za pomocą nowego klastra.

### <a name="copy-data-to-the-test-environment"></a>Kopiowanie danych do środowiska testowego

Istnieje wiele metod, aby skopiować dane i zadania, jednak omówione w tej sekcji są najprostsze sposoby bezpośrednio przenoszenie plików do klastra testowego.

#### <a name="hdfs-copy"></a>Kopiuj systemu plików HDFS

Wykonaj następujące kroki, aby skopiować dane z klastra produkcyjnego do klastra testowego. Te kroki odnoszą się `hdfs dfs` narzędzie, które są dołączone HDInsight.

1. Znajdź magazynu konta i domyślny kontener informacje dotyczące istniejącego klastra. W poniższym przykładzie użyto programu PowerShell, aby pobrać te informacje:

    ```powershell
    $clusterName="Your existing HDInsight cluster name"
    $clusterInfo = Get-AzHDInsightCluster -ClusterName $clusterName
    write-host "Storage account name: $clusterInfo.DefaultStorageAccount.split('.')[0]"
    write-host "Default container: $clusterInfo.DefaultStorageContainer"
    ```

2. Aby utworzyć środowisko testowe, wykonaj kroki w klastrach opartych na systemie Linux z tworzenia w dokumencie HDInsight. Przed rozpoczęciem tworzenia klastra, a zamiast tego wybierz **opcjonalna konfiguracja**.

3. Sekcja konfiguracji opcjonalnej zaznacz **połączonych kontach usługi Storage**.

4. Wybierz **Dodaj klucz magazynu**i po wyświetleniu monitu wybierz konto magazynu, który został zwrócony przez skrypt programu PowerShell w kroku 1. Kliknij przycisk **wybierz** w każdej sekcji. Na koniec Utwórz klaster.

5. Po utworzeniu klastra nawiązać z nim za pomocą **SSH.** Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

6. W sesji SSH Użyj następującego polecenia, aby skopiować pliki z na połączonym koncie magazynu do nowego domyślnego konta magazynu. Zastąp kontenera informacje o kontenerze, zwrócone przez programu PowerShell. Zastąp __konta__ nazwą konta. Ścieżka do danych należy zastąpić ścieżkę do pliku danych.

    ```bash
    hdfs dfs -cp wasb://CONTAINER@ACCOUNT.blob.core.windows.net/path/to/old/data /path/to/new/location
    ```

    > [!NOTE]  
    > Jeśli nie ma struktury katalogów, która zawiera dane w środowisku testowym, można utworzyć za pomocą następującego polecenia:

    ```bash
    hdfs dfs -mkdir -p /new/path/to/create
    ```

    `-p` Przełącznik umożliwia utworzenie wszystkich katalogów w ścieżce.

#### <a name="direct-copy-between-blobs-in-azure-storage"></a>Bezpośrednie kopiowanie między obiektami BLOB w usłudze Azure Storage

Alternatywnie, możesz chcieć użyć `Start-AzStorageBlobCopy` polecenia cmdlet programu Azure PowerShell, aby kopiować obiekty BLOB między kontami magazynu poza HDInsight. Aby uzyskać więcej informacji, zobacz, jak zarządzać sekcji obiektów blob platformy Azure przy użyciu programu Azure PowerShell z usługą Azure Storage.

## <a name="client-side-technologies"></a>Technologie po stronie klienta

Technologie po stronie klienta, takie jak [poleceń cmdlet programu Azure PowerShell](/powershell/azureps-cmdlets-docs), [klasyczny interfejs wiersza polecenia platformy Azure](../cli-install-nodejs.md), lub [zestawu SDK .NET dla usługi Apache Hadoop](https://hadoopsdk.codeplex.com/) nadal działały w klastrach opartych na systemie Linux. Technologie te opierają się na interfejsach API REST, które są takie same w przypadku obu typów klastra systemu operacyjnego.

## <a name="server-side-technologies"></a>Technologie serwerowe

Poniższa tabela zawiera wskazówki dotyczące migrowania składniki po stronie serwera, które są specyficzne dla Windows.

| Jeśli używasz tej technologii... | Wykonanie tej czynności... |
| --- | --- |
| **Program PowerShell** (skrypty po stronie serwera, w tym akcji skryptu, używane podczas tworzenia klastra) |Napisać ponownie jako skrypty powłoki Bash. Dla akcji skryptu, zobacz [HDInsight opartych na systemie Linux z dostosować za pomocą akcji skryptów](hdinsight-hadoop-customize-cluster-linux.md) i [opracowywanie akcji dla HDInsight opartych na systemie Linux skryptu](hdinsight-hadoop-script-actions-linux.md). |
| **Klasyczny interfejs wiersza polecenia Azure** (skrypty po stronie serwera) |Natomiast klasycznego wiersza polecenia platformy Azure jest dostępny w systemie Linux, go nie są zainstalowane w głównymi węzłami klastra HDInsight. Aby uzyskać więcej informacji na temat instalowania interfejsu wiersza polecenia Azure Classic zobacz [Rozpoczynanie pracy z usługą Azure klasyczny interfejs wiersza polecenia](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli). |
| **Składniki .NET** |.NET jest obsługiwany w HDInsight opartych na systemie Linux za pomocą [Mono](https://mono-project.com). Aby uzyskać więcej informacji, zobacz [.NET Migrowanie rozwiązania HDInsight opartych na systemie Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md). |
| **Składniki systemu Win32 lub inne technologie tylko Windows** |Wskazówki dotyczące zależy od tego, składnika lub technologii. Dzięki temu można znaleźć wersji, która jest zgodna z systemem Linux. W przeciwnym razie należy znaleźć rozwiązanie alternatywne lub napisz ponownie tego składnika. |

> [!IMPORTANT]  
> Zarządzanie HDInsight SDK nie jest w pełni zgodny z platformy Mono. Nie należy używać go jako część rozwiązania, które zostały wdrożone w klastrze HDInsight.

## <a name="cluster-creation"></a>Tworzenie klastra

Ta sekcja zawiera informacje na temat różnic w procesie tworzenia klastra.

### <a name="ssh-user"></a>SSH użytkownika

HDInsight opartych na systemie Linux klastrów użyj **Secure Shell (SSH)** protokołu zapewnienie dostępu zdalnego do węzłów klastra. W przeciwieństwie do klastrów z systemem Windows do usług pulpitu zdalnego większość klientów SSH nie zapewniają interfejs graficzny użytkownika. Zamiast tego klientów SSH Podaj wiersz polecenia, który umożliwia uruchamianie poleceń w klastrze. Niektórzy klienci (takie jak [MobaXterm](https://mobaxterm.mobatek.net/)) zapewniają przeglądarki systemu plików graficznych oprócz zdalne wiersza polecenia.

Podczas tworzenia klastra, należy podać użytkownika SSH, a następnie **hasło** lub **certyfikatu klucza publicznego** do uwierzytelniania.

Firma Microsoft zaleca, przy użyciu certyfikatu klucza publicznego, ponieważ jest bardziej bezpieczne niż korzystanie z hasła. Uwierzytelnianie certyfikatu polega na generowanie podpisane parą kluczy publiczny/prywatny, a następnie podając klucza publicznego podczas tworzenia klastra. Podczas nawiązywania połączenia z serwerem przy użyciu protokołu SSH, klucza prywatnego na komputerze klienckim zapewnia uwierzytelniania dla połączenia.

Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

### <a name="cluster-customization"></a>Dostosowywanie klastra

**Akcji skryptu** używane z opartą na systemie Linux klastrów muszą być napisane w skrypcie powłoki Bash. Klastrów opartych na systemie Linux za pomocą akcji skryptów podczas lub po utworzeniu klastra. Aby uzyskać więcej informacji, zobacz [HDInsight opartych na systemie Linux z dostosować za pomocą akcji skryptów](hdinsight-hadoop-customize-cluster-linux.md) i [opracowywanie akcji dla HDInsight opartych na systemie Linux skryptu](hdinsight-hadoop-script-actions-linux.md).

Kolejną funkcją dostosowywania jest **bootstrap**. W przypadku klastrów Windows ta funkcja umożliwia określenie lokalizacji dodatkowe biblioteki do użycia przy użyciu technologii Hive. Po utworzeniu klastra są automatycznie dostępne do użycia przy użyciu zapytań programu Hive, bez potrzeby używania tych bibliotek `ADD JAR`.

Funkcja ładowania początkowego dla klastrów opartych na systemie Linux nie zapewnia tę funkcję. Zamiast tego należy użyć akcji skryptu, które opisano w [biblioteki Dodaj Apache Hive, podczas tworzenia klastra](hdinsight-hadoop-add-hive-libraries.md).

### <a name="virtual-networks"></a>Sieci wirtualne

HDInsight oparte na Windows Klastry działają tylko w klasycznych sieci wirtualnych, gdy klastry HDInsight opartych na systemie Linux wymagają sieci wirtualnej usługi Resource Manager. Jeśli zasoby znajdują się w klastrze HDInsight Linux muszą połączyć się z klasyczną siecią wirtualną, zobacz [łączenie klasycznej sieci wirtualnej z sieci wirtualnej usługi Resource Manager](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

Aby uzyskać więcej informacji na temat wymagań dotyczących konfiguracji, zobacz [HDInsight rozszerzyć możliwości przy użyciu sieci wirtualnej](hdinsight-extend-hadoop-virtual-network.md) dokumentu.

## <a name="management-and-monitoring"></a>Zarządzanie i monitorowanie

Wiele sieci web UI, które zostało użyte z systemem Windows HDInsight, takie jak Historia zadania lub interfejsie użytkownika Yarn, są dostępne za pośrednictwem systemu Apache Ambari. Ponadto widoku Hive narzędzia Ambari umożliwia uruchamianie zapytań Hive przy użyciu przeglądarki sieci web. Interfejs użytkownika sieci Web Ambari jest dostępna w klastrach opartych na systemie Linux w https://CLUSTERNAME.azurehdinsight.net.

Aby uzyskać więcej informacji na temat pracy za pomocą systemu Ambari zobacz następujące dokumenty:

* [Apache Ambari Web](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari REST API](hdinsight-hadoop-manage-ambari-rest-api.md)

### <a name="ambari-alerts"></a>Alerty systemu Ambari

Ambari udostępnia system alertów, które mogą wskazać możliwość rozwiązania potencjalnych problemów z klastrem. Alerty są wyświetlane jako czerwone threshold lub Yellow threshold wpisów w interfejsie użytkownika sieci Web Ambari, jednak można również pobrać za pośrednictwem interfejsu API REST.

> [!IMPORTANT]  
> Alerty systemu Ambari wskazują, że istnieje *może* stanowić problem, nie w tym miejscu *jest* problem. Na przykład otrzymasz alert, nie można uzyskać dostępu do serwera HiveServer2, mimo że można go było uzyskanie normalnego dostępu.
>
> Wiele alertów są implementowane jako zapytania względem usługi oparte na interwał i oczekują odpowiedzi w określonym przedziale czasu. Aby alert nie musi oznaczać, że usługa nie działa, ale nie zwraca wyników w oczekiwanym czasie.

## <a name="file-system-locations"></a>Lokalizacje w systemie plików

System plików klastrów systemu Linux jest rozmieszczony inaczej niż klastry HDInsight oparte na Windows. Skorzystaj z poniższej tabeli, Znajdź najczęściej używanych plików.

| Musisz znaleźć... | Znajduje się... |
| --- | --- |
| Konfigurowanie |`/etc`. Na przykład: `/etc/hadoop/conf/core-site.xml` |
| Pliki dziennika |`/var/logs` |
| Hortonworks Data Platform (HDP) |`/usr/hdp`. Istnieją dwa katalogi znajduje się w tym miejscu, który stanowi bieżącą wersję HDP i `current`. `current` Katalog zawiera linki symboliczne do plików i katalogów znajdujących się w katalogu numeru wersji. `current` Katalogu zostanie podana jako wygodny sposób uzyskiwania dostępu do plików HDP od zmiany numeru wersji jako HDP wersja jest aktualizowana. |
| streaming.jar usługi hadoop |`/usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar` |

Ogólnie rzecz biorąc Jeśli znasz nazwę pliku, służy następujące polecenie w sesji SSH można znaleźć ścieżki pliku:

    find / -name FILENAME 2>/dev/null

Można również używać symboli wieloznacznych, z nazwą pliku. Na przykład `find / -name *streaming*.jar 2>/dev/null` zwraca ścieżkę do plików jar, które zawierają słowo, streaming, jako część nazwy pliku.

## <a name="apache-hive-apache-pig-and-mapreduce"></a>Apache Hive, Apache Pig i MapReduce

Obciążeń programów pig i MapReduce są podobne w klastrach opartych na systemie Linux. Jednak klastry HDInsight opartych na systemie Linux można tworzyć przy użyciu nowszej wersji usługi Hadoop, Hive i Pig. Te różnice wersji mogą wprowadzać zmiany w sposób istniejących funkcji rozwiązania. Aby uzyskać więcej informacji na wersje składników dołączone HDInsight, zobacz [przechowywanie wersji składnika HDInsight](hdinsight-component-versioning.md).

HDInsight opartych na systemie Linux nie udostępnia funkcje pulpitu zdalnego. Zamiast tego można użyć protokołu SSH do zdalnego łączenia z głównymi węzłami klastra. Więcej informacji na ten temat można znaleźć w następujących dokumentach:

* [Use Apache Hive przy użyciu protokołu SSH](hdinsight-hadoop-use-hive-ssh.md)
* [Use Apache Pig przy użyciu protokołu SSH](hadoop/apache-hadoop-use-pig-ssh.md)
* [Korzystanie z technologii MapReduce przy użyciu protokołu SSH](hadoop/apache-hadoop-use-mapreduce-ssh.md)

### <a name="hive"></a>Hive

> [!IMPORTANT]  
> Jeśli używasz zewnętrzny Magazyn metadanych Hive, możesz należy wykonać kopię zapasową magazynu metadanych przed użyciem HDInsight opartych na systemie Linux. HDInsight opartych na systemie Linux jest dostępna z nowszymi wersjami programu Hive, które mogą mieć niezgodności z magazyny metadanych utworzone we wcześniejszych wersjach.

Poniższej tabeli znajdują się wskazówki dotyczące migracji obciążenia Hive.

| Na podstawie Windows używam... | Na opartą na systemie Linux... |
| --- | --- |
| **Hive Editor** |[Apache Hive widoku systemu Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md) |
| `set hive.execution.engine=tez;` Aby umożliwić aplikacji Tez |Apache Tez jest domyślny aparat wykonywania dla klastrów opartych na systemie Linux, więc instrukcji set nie są już potrzebne. |
| C# funkcje zdefiniowane przez użytkownika | Aby uzyskać informacje na temat weryfikacji składników języka C# za pomocą HDInsight opartych na systemie Linux, zobacz [.NET Migrowanie rozwiązania HDInsight opartych na systemie Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| Pliki CMD lub skrypty na serwerze wywoływanych jako część zadania Hive |za pomocą skryptów powłoki Bash |
| `hive` polecenie z pulpitu zdalnego |Użyj [z usługi Beeline Apache Hive](hadoop/apache-hadoop-use-hive-beeline.md) lub [Apache Hive w sesji SSH](hdinsight-hadoop-use-hive-ssh.md) |

### <a name="pig"></a>Pig

| Na podstawie Windows używam... | Na opartą na systemie Linux... |
| --- | --- |
| C# funkcje zdefiniowane przez użytkownika | Aby uzyskać informacje na temat weryfikacji składników języka C# za pomocą HDInsight opartych na systemie Linux, zobacz [.NET Migrowanie rozwiązania HDInsight opartych na systemie Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| Pliki CMD lub skrypty na serwerze wywoływane jako część zadania Pig |za pomocą skryptów powłoki Bash |

### <a name="mapreduce"></a>MapReduce

| Na podstawie Windows używam... | Na opartą na systemie Linux... |
| --- | --- |
| C# mapowania reduktor składników i | Aby uzyskać informacje na temat weryfikacji składników języka C# za pomocą HDInsight opartych na systemie Linux, zobacz [.NET Migrowanie rozwiązania HDInsight opartych na systemie Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md) |
| Pliki CMD lub skrypty na serwerze wywoływanych jako część zadania Hive |za pomocą skryptów powłoki Bash |

## <a name="apache-oozie"></a>Apache Oozie

> [!IMPORTANT]  
> Jeśli używasz zewnętrzny Magazyn metadanych programu Oozie, możesz należy wykonać kopię zapasową magazynu metadanych przed użyciem HDInsight opartych na systemie Linux. HDInsight opartych na systemie Linux jest dostępna z nowszymi wersjami programu Oozie, który może mieć niezgodności z magazyny metadanych utworzone we wcześniejszych wersjach.

Przepływy pracy programu Oozie umożliwiają akcji powłoki. Powłoka akcje wykorzystują domyślnej powłoki systemu operacyjnego, aby uruchamiać polecenia wiersza polecenia. W przypadku przepływów pracy programu Oozie, które zależą od powłoki Windows należy przepisać przepływy pracy w celu zależą od środowiska powłoki systemu Linux (powłoki Bash). Aby uzyskać więcej informacji na temat korzystania z powłoki akcji przy użyciu programu Oozie, zobacz [rozszerzenie akcji powłoki programu Oozie](https://oozie.apache.org/docs/3.3.0/DG_ShellActionExtension.html).

Jeśli przepływ pracy, który korzysta z aplikacji w języku C#, sprawdzanie poprawności tych aplikacji w środowisku systemu Linux. Aby uzyskać więcej informacji, zobacz [.NET Migrowanie rozwiązania HDInsight opartych na systemie Linux](hdinsight-hadoop-migrate-dotnet-to-linux.md).

## <a name="storm"></a>Storm

| Na podstawie Windows używam... | Na opartą na systemie Linux... |
| --- | --- |
| Pulpit nawigacyjny STORM |Pulpit nawigacyjny platformy Storm jest niedostępna. Zobacz [topologii wdrażania i zarządzania platformy Apache Storm w HDInsight opartych na systemie Linux](storm/apache-storm-deploy-monitor-topology-linux.md) sposoby przesyłania topologii |
| Interfejs użytkownika platformy STORM |Interfejs użytkownika platformy Storm znajduje się w temacie https://CLUSTERNAME.azurehdinsight.net/stormui |
| Visual Studio na tworzenie, wdrażanie i zarządzanie topologii C# lub hybrydowe |Program Visual Studio umożliwia tworzenie, wdrażanie i zarządzanie języka C# (platformy SCP.NET) lub hybrydowe topologie w systemie w HDInsight Storm dla opartych na systemie Linux. Można można używać tylko z klastrami utworzonych po 10/28/2016. |

## <a name="apache-hbase"></a>Apache HBase

W klastrach opartych na systemie Linux, nadrzędny znode dla bazy danych HBase jest `/hbase-unsecure`. Aby ustawić tę wartość, należy w konfiguracji dla dowolnego klienta Java w aplikacji, które używają natywnych interfejsów API języka Java bazy danych HBase.

Zobacz [tworzenie aplikacji opartych na języku Java bazy danych Apache HBase](hbase/apache-hbase-build-java-maven-linux.md) klienta przykładu, który ustawia tę wartość.

## <a name="spark"></a>platforma Spark

Klastry Spark były dostępne w klastrach Windows w wersji zapoznawczej. Spark GA jest dostępna tylko w klastrach opartych na systemie Linux. Nie ma żadnych ścieżki migracji z klastra Spark oparty na Windows (wersja zapoznawcza) do klastra Spark oparty na systemie Linux wersji.

## <a name="known-issues"></a>Znane problemy

### <a name="azure-data-factory-custom-net-activities"></a>Usługa Azure Data Factory niestandardowe działania programu .NET

Usługa Azure Data Factory niestandardowe działania programu .NET nie są obecnie obsługiwane w klastrach HDInsight opartych na systemie Linux. Zamiast tego warto z nich korzystać z następujących metod do implementowania niestandardowych działań w ramach potoku usługi ADF.

* Wykonaj działania programu .NET w puli Azure Batch. Zobacz użycie usługi Azure Batch połączone usługi części [używanie niestandardowych działań w potoku usługi Azure Data Factory](../data-factory/transform-data-using-dotnet-custom-activity.md)
* Implementowanie działania działania technologii MapReduce. Aby uzyskać więcej informacji, zobacz [wywoływanie programów MapReduce z usługi Data Factory](../data-factory/transform-data-using-hadoop-map-reduce.md).

### <a name="line-endings"></a>Końce wierszy

Ogólnie rzecz biorąc końce wierszy na komputerach z systemem Windows użyj CRLF, podczas gdy LF systemów opartych na systemie Linux. Użytkownik może być konieczne zmodyfikowanie istniejących producentów danych i konsumentów, aby pracować z LF.

Na przykład HDInsight w klastrze z systemem Windows przy użyciu programu Azure PowerShell do wykonywania zapytań zwraca dane z CRLF. Tego samego zapytania z opartą na systemie Linux klastrem zwraca LF. Test, aby zobaczyć, jeśli koniec wiersza powoduje problem z rozwiązaniem przed przeprowadzeniem migracji do klastra opartego na systemie Linux.

Zawsze używaj LF jako skryptów uruchamianych w węzłach klastra na końcu wiersza. Jeśli używasz CRLF, mogą zostać wyświetlone błędy podczas uruchamiania skryptów w klastrze opartych na systemie Linux.

Jeśli skrypty nie zawierają ciągi z osadzonych znaków powrotu Karetki, można zbiorczo Zmień zakończenia wierszy przy użyciu jednej z następujących metod:

* **Przed przekazaniem do klastra**: Użyj następujące instrukcje programu PowerShell, aby zmienić końce wierszy z CRLF do LF przed przekazaniem skrypt do klastra.

    ```powershell
    $original_file ='c:\path\to\script.py'
    $text = [IO.File]::ReadAllText($original_file) -replace "`r`n", "`n"
    [IO.File]::WriteAllText($original_file, $text)
    ```

* **Po przekazaniu do klastra**: Użyj następującego polecenia w sesji SSH z opartą na systemie Linux klastrem celu zmodyfikowania skryptu.

    ```bash
    hdfs dfs -get wasb:///path/to/script.py oldscript.py
    tr -d '\r' < oldscript.py > script.py
    hdfs dfs -put -f script.py wasb:///path/to/script.py
    ```

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się, jak tworzyć klastry HDInsight opartych na systemie Linux](hdinsight-hadoop-provision-linux-clusters.md)
* [Używanie protokołu SSH, aby nawiązać połączenie HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Zarządzanie klastrem opartych na systemie Linux przy użyciu narzędzia Apache Ambari](hdinsight-hadoop-manage-ambari.md)
