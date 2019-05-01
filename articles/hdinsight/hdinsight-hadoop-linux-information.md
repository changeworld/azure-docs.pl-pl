---
title: Porady dotyczące korzystania z usługi Hadoop w HDInsight opartych na systemie Linux — Azure
description: Implementacja porady dotyczące korzystania z klastrów opartych na systemie Linux HDInsight (Hadoop) w znajomym środowisku systemu Linux działające w chmurze platformy Azure.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 03/20/2019
ms.openlocfilehash: 2a7af59495966c76a47ea84311ab073eb594f82e
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64707570"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Informacje dotyczące korzystania z usługi HDInsight w systemie Linux

Usługa Azure klastry HDInsight zapewniają Apache Hadoop w znajomym środowisku systemu Linux działające w chmurze platformy Azure. W przypadku większości elementów powinny działać dokładnie tak jak w innych instalacji usługi Hadoop w systemie Linux. W tym dokumencie wywołuje konkretne różnice, które należy wiedzieć.

> [!IMPORTANT]  
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a name="prerequisites"></a>Wymagania wstępne

Wiele z tych kroków w tym dokumencie, użyj następujących narzędzi, które mogą wymagać do zainstalowania w systemie.

* [cURL](https://curl.haxx.se/) — używany do komunikowania się z usług sieci web.
* **jq**, wiersza polecenia procesora w formacie JSON.  Zobacz [ https://stedolan.github.io/jq/ ](https://stedolan.github.io/jq/).
* [Interfejs wiersza polecenia Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) — umożliwia zdalne zarządzanie usługami platformy Azure.
* **Klient SSH**. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="users"></a>Użytkownicy

Chyba że [przyłączone do domeny](./domain-joined/apache-domain-joined-introduction.md), należy rozważyć HDInsight **pojedynczego użytkownika** systemu. Jednego konta użytkownika SSH jest tworzone z klastrem, przy użyciu uprawnień na poziomie administratora. Można tworzyć dodatkowe konta SSH, ale mają dostęp administratora do klastra.

HDInsight przyłączone do domeny, obsługuje wielu użytkowników i bardziej szczegółowe ustawienia uprawnień i ról. Aby uzyskać więcej informacji, zobacz [klastry HDInsight przyłączone do domeny zarządzania](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Nazwy domen

Jest w pełni kwalifikowana nazwa domeny (FQDN) do użycia podczas łączenia się z klastrem z Internetu `CLUSTERNAME.azurehdinsight.net` lub `CLUSTERNAME-ssh.azurehdinsight.net` (dla tylko SSH).

Wewnętrznie każdy węzeł w klastrze ma nazwę, która jest przypisywana podczas konfiguracji klastra. Aby znaleźć nazwy klastra, zobacz **hosty** strony w Interfejsie użytkownika sieci Web Ambari. Aby zwrócić listę hostów z interfejsu API REST Ambari umożliwia także następujące czynności:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Zastąp ciąg `CLUSTERNAME` nazwą klastra. Po wyświetleniu monitu wprowadź hasło dla konta administratora. To polecenie zwraca dokument JSON, który zawiera listę hostów w klastrze. [jq](https://stedolan.github.io/jq/) służy do wyodrębniania `host_name` wartość elementu dla każdego hosta.

Jeśli potrzebujesz znaleźć nazwę węzła dla określonej usługi, można tworzyć zapytania Ambari danego składnika. Na przykład aby znaleźć hosty węzła Nazwa systemu plików HDFS, należy użyć następującego polecenia:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

To polecenie zwraca dokument JSON z opisem usługi, a następnie [jq](https://stedolan.github.io/jq/) stosuje tylko `host_name` wartości dla hostów.

## <a name="remote-access-to-services"></a>Zdalny dostęp do usług

* **Ambari (web)** - https://CLUSTERNAME.azurehdinsight.net

    Uwierzytelnianie przy użyciu użytkownika administratora klastra i hasło, a następnie zaloguj się do systemu Ambari.

    Uwierzytelnianie to zwykły tekst — zawsze używaj protokołu HTTPS, aby mieć pewność, że połączenie jest bezpieczne.

    > [!IMPORTANT]  
    > Niektóre z interfejsów użytkownika dostępnych za pomocą systemu Ambari w sieci web dostęp do węzłów przy użyciu nazwy domeny wewnętrznej. Nazwy domen wewnętrznych nie są publicznie dostępne za pośrednictwem Internetu. Możesz otrzymać błędy "nie można odnaleźć serwera" podczas próby niektóre funkcje są dostępne za pośrednictwem Internetu.
    >
    > Aby użyć pełnej funkcjonalności interfejsu użytkownika sieci web Ambari, użyj tunelu SSH, aby ruch internetowy serwer proxy do węzła głównego klastra. Zobacz [użycie tunelowania SSH do uzyskania dostępu do systemu Apache Ambari web UI, ResourceManager, JobHistory, NameNode, Oozie i innych web UI](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** - https://CLUSTERNAME.azurehdinsight.net/ambari

    > [!NOTE]  
    > Uwierzytelnianie za pomocą użytkownika administratora klastra i hasło.
    >
    > Uwierzytelnianie to zwykły tekst — zawsze używaj protokołu HTTPS, aby mieć pewność, że połączenie jest bezpieczne.

* **WebHCat (Templeton)** - https://CLUSTERNAME.azurehdinsight.net/templeton

    > [!NOTE]  
    > Uwierzytelnianie za pomocą użytkownika administratora klastra i hasło.
    >
    > Uwierzytelnianie to zwykły tekst — zawsze używaj protokołu HTTPS, aby mieć pewność, że połączenie jest bezpieczne.

* **SSH** -NAZWA_KLASTRA ssh.azurehdinsight.net na porcie 22 i 23. Port 22 jest używany do łączenia z podstawowym węzłem głównym, podczas gdy 23 jest używany do łączenia do regionu pomocniczego. Aby uzyskać więcej informacji o węzłach głównych, zobacz [dostępność i niezawodność usługi Apache Hadoop clusters w HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]  
    > Dostępne tylko do węzłów głównych klastra za pośrednictwem protokołu SSH z komputera klienckiego. Po nawiązaniu połączenia można następnie dostęp do węzłów procesu roboczego, przy użyciu protokołu SSH z węzłem głównym.

Aby uzyskać więcej informacji, zobacz [porty używane przez usługi Apache Hadoop w HDInsight](hdinsight-hadoop-port-settings-for-services.md) dokumentu.

## <a name="file-locations"></a>Lokalizacje plików

Pliki związane z usługą Hadoop znajdują się w węzłach klastra w `/usr/hdp`. Ten katalog zawiera podkatalogi następujące:

* **2.6.5.3006-29**: Nazwa katalogu jest wersja Hortonworks Data Platform, używane przez HDInsight. Liczba w klastrze może być inny niż ten, wymienione w tym miejscu.
* **bieżący**: Ten katalog zawiera łącza do podkatalogów w ramach **2.6.5.3006-29** katalogu. Ten katalog istnieje, dzięki czemu nie trzeba pamiętać numer wersji.

Przykładowe dane i pliki JAR znajduje się na rozproszony System plików Hadoop na `/example` i `/HdiSamples`.

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>System plików HDFS, usługa Azure Storage i usługi Data Lake Storage

W większości dystrybucji platformy Hadoop dane są przechowywane w systemie plików HDFS, która jest wspierana przez lokalny magazyn na komputerach w klastrze. Przy użyciu lokalnego magazynu może być kosztowne rozwiązanie oparte na chmurze której opłaty są naliczane co godzinę lub minutowym za zasoby obliczeniowe.

Korzystając z HDInsight, pliki danych są przechowywane w sposób skalowalności i odporności w chmurze przy użyciu usługi Azure Blob Storage i opcjonalnie usługi Azure Data Lake Storage. Usługi te oferują następujące korzyści:

* Tania długoterminowego przechowywania.
* Ułatwienia dostępu z usług zewnętrznych, takich jak witryny sieci Web, narzędzia do pobierania/przekazywania pliku, różnych zestawów SDK języka i przeglądarki sieci web.
* Pojemność dużych plików i dużych skalowalności magazynu.

Aby uzyskać więcej informacji, zobacz [obiektów blob opis](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) i [usługi Data Lake Storage](https://azure.microsoft.com/services/storage/data-lake-storage/).

Korzystając z usługi Azure Storage lub usługi Data Lake Storage, nie trzeba wykonywać żadnych specjalnych czynności z HDInsight dostępu do danych. Na przykład następujące polecenie wyświetla listę plików w `/example/data` folderu niezależnie od tego, czy są przechowywane w usłudze Azure Storage lub magazynu usługi Data Lake:

    hdfs dfs -ls /example/data

W HDInsight zasoby magazynu danych (magazyn obiektów Blob platformy Azure i usługi Azure Data Lake Storage) są całkowicie niezależni od zasobów obliczeniowych. W związku z tym można utworzyć klastry HDInsight w celu obliczeń, zgodnie z potrzebami i później usunąć klaster, po zakończeniu pracy, w międzyczasie przechowywanie plików danych bezpiecznie utrwalone w magazynie w chmurze tak długo, jak należy.


### <a name="URI-and-scheme"></a>Identyfikator URI i schematu

Niektóre polecenia mogą wymagać Określ schemat identyfikatora URI w ramach podczas uzyskiwania dostępu do pliku. Na przykład składnik systemu plików Storm-HDFS wymaga Określ schemat. Korzystając z innych niż domyślne magazynu (dodane do klastra jako "dodatkowego" miejsca do magazynowania), należy zawsze używać systemu jako część identyfikatora URI.

Korzystając z __usługi Azure Storage__, użyj jednej z następujących schematów identyfikator URI:

* `wasb:///`: Domyślny magazyn przy użyciu nieszyfrowanego komunikacji.

* `wasbs:///`: Dostęp do magazynu domyślnego za pomocą komunikacji szyfrowanej.  Schemat wasbs są obsługiwane tylko z HDInsight w wersji 3.6 lub nowszym.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Używany podczas komunikacji z kontem magazynu innego niż domyślny. Na przykład, jeśli masz dodatkowe konto magazynu lub podczas uzyskiwania dostępu do danych przechowywanych na koncie magazynu dostępny publicznie.

Korzystając z __usługi Azure Data Lake Storage Gen2__, użyj jednej z następujących schematów identyfikator URI:

* `abfs:///`: Domyślny magazyn przy użyciu nieszyfrowanego komunikacji.

* `abfss:///`: Dostęp do magazynu domyślnego za pomocą komunikacji szyfrowanej.  Schemat abfss jest obsługiwany tylko z HDInsight w wersji 3.6 lub nowszym.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: Używany podczas komunikacji z kontem magazynu innego niż domyślny. Na przykład, jeśli masz dodatkowe konto magazynu lub podczas uzyskiwania dostępu do danych przechowywanych na koncie magazynu dostępny publicznie.

Korzystając z __usługi Azure Data Lake Storage Gen1__, użyj jednej z następujących schematów identyfikator URI:

* `adl:///`: Dostęp do domyślnej usługi Data Lake Storage dla klastra.

* `adl://<storage-name>.azuredatalakestore.net/`: Używany podczas komunikacji z usługi Data Lake Storage innych niż domyślne. Umożliwia również dostęp do danych znajdujących się poza katalogiem głównym klastra usługi HDInsight.

> [!IMPORTANT]  
> Korzystając z usługi Data Lake Storage jako magazynu domyślnego dla HDInsight, należy określić ścieżkę w magazynie do użycia jako katalogu głównego magazynu HDInsight. Domyślna ścieżka to `/clusters/<cluster-name>/`.
>
> Korzystając z `/` lub `adl:///` dostępu do danych, możesz tylko dostęp do danych przechowywanych w katalogu głównym (na przykład `/clusters/<cluster-name>/`) klastra. Aby uzyskać dostęp do danych w dowolnym miejscu w magazynie, użyj `adl://<storage-name>.azuredatalakestore.net/` formatu.

### <a name="what-storage-is-the-cluster-using"></a>Magazynu klastra używa

Ambari służy do pobierania domyślnej konfiguracji magazynu dla klastra. Użyj następującego polecenia, aby pobrać informacje o konfiguracji systemu plików HDFS przy użyciu programu curl i filtrowanie ich przy użyciu [jq](https://stedolan.github.io/jq/):

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> To polecenie zwraca to pierwsza Konfiguracja stosowany na serwerze (`service_config_version=1`), który zawiera te informacje. Może być konieczne wszystkie wersje konfiguracji można znaleźć najnowsze listy.

To polecenie zwraca wartość podobne do następujących identyfikatorów URI:

* `wasb://<container-name>@<account-name>.blob.core.windows.net` Jeśli przy użyciu konta usługi Azure Storage.

    Nazwa konta jest nazwa konta usługi Azure Storage. Nazwa kontenera jest kontener obiektów blob, który jest elementem głównym magazynie klastra.

* `adl://home` Jeśli przy użyciu usługi Azure Data Lake Storage. Aby uzyskać nazwę usługi Data Lake Storage, należy użyć następującego wywołania REST:

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    To polecenie zwraca następujące nazwy hosta: `<data-lake-store-account-name>.azuredatalakestore.net`.

    Aby uzyskać katalog, w magazynie, który jest główną HDInsight, użyj następującego wywołania REST:

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    To polecenie zwraca ścieżkę podobna do następującej ścieżki: `/clusters/<hdinsight-cluster-name>/`.

Można również znaleźć informacje o magazynu przy użyciu witryny Azure portal wykonując następujące kroki:

1. Z [witryny Azure portal](https://portal.azure.com/), wybierz klaster usługi HDInsight.

2. Z **właściwości** zaznacz **kont magazynu**. Zostaną wyświetlone informacje magazynu dla klastra.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Jak dostęp do plików z poza HDInsight

Istnieją różne sposoby dostępu do danych poza klastrem HDInsight. Poniżej przedstawiono kilka linki do narzędzi i zestawów SDK, których można użyć do pracy z danymi:

Jeśli przy użyciu __usługi Azure Storage__, zobacz poniższe linki, aby sposób, że mogą uzyskiwać dostęp do danych:

* [Interfejs wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-az-cli2): Polecenia interfejsu wiersza polecenia do pracy z platformą Azure. Po zainstalowaniu należy użyć `az storage` polecenie, aby uzyskać pomoc na temat korzystania z magazynu lub `az storage blob` poleceń specyficznych dla obiektów blob.
* [blobxfer.PY](https://github.com/Azure/blobxfer): Skrypt języka python do pracy z obiektami BLOB w usłudze Azure Storage.
* Różnych zestawów SDK:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [Interfejs API REST magazynu](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Jeśli przy użyciu __usługi Azure Data Lake Storage__, zobacz poniższe linki, aby sposób, że mogą uzyskiwać dostęp do danych:

* [Przeglądarki sieci Web](../data-lake-store/data-lake-store-get-started-portal.md)
* [Program PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Interfejs wiersza polecenia platformy Azure](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [Interfejs API REST WebHDFS](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Narzędzia Data Lake Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>Skalowanie klastra

Skalowanie funkcji klastra umożliwia dynamiczną zmianę liczby węzłów danych używane przez klaster. Można wykonać operacji skalowania podczas inne zadania lub procesy są uruchomione w klastrze.  Zobacz też [klastrów HDInsight skalowania](./hdinsight-scaling-best-practices.md)

Różne typy klastrów ma wpływ skalowania w następujący sposób:

* **Hadoop**: Podczas skalowania w dół liczbę węzłów w klastrze zostaną ponownie uruchomione niektóre z tych usług w klastrze. Operacje skalowania może spowodować zadania pracy lub w stanie oczekiwania na zakończenie operacji skalowania. Po zakończeniu operacji, można ponownie przesłać zadania.
* **HBase**: Serwery regionalne automatycznie są równoważone w ciągu kilku minut, po zakończeniu operacji skalowania. Ręcznie równoważyć serwerów regionalnych, wykonaj następujące kroki:

    1. Łączenie z klastrem HDInsight przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    2. Uruchom powłokę HBase należy wykonać następujące kroki:

            hbase shell

    3. Po załadowaniu powłoki HBase ręcznie równoważyć regionalnych serwerów należy wykonać następujące kroki:

            balancer

* **STORM**: Po wykonaniu operacji skalowania należy przeprowadzić ponowne równoważenie wszystkie działające topologie Storm. Ponowne równoważenie umożliwia topologii, dostosowywać ustawienia równoległości na podstawie nowej liczby węzłów w klastrze. Aby przeprowadzić ponowne równoważenie uruchomionych topologii, należy użyć jednej z następujących opcji:

    * **SSH**: Połączenie z serwerem i użyj następującego polecenia, aby ponowne zrównoważenie topologii:

            storm rebalance TOPOLOGYNAME

        Można również określić parametry, aby zastąpić wskazówek równoległości pierwotnie dostarczana przez topologię. Na przykład `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` ponownie konfiguruje topologię 5 procesów roboczych, 3 executors składnika niebieski spout i 10 executors składnika elementu bolt na żółty.

    * **System STORM UI**: Wykonaj następujące kroki, aby ponowne zrównoważenie topologii przy użyciu interfejsu użytkownika platformy Storm.

        1. Otwórz `https://CLUSTERNAME.azurehdinsight.net/stormui` w przeglądarce sieci web, gdzie `CLUSTERNAME` jest nazwą klastra Storm. Jeśli zostanie wyświetlony monit, wprowadź nazwę administratora (Administrator) klastra HDInsight i hasło określone podczas tworzenia klastra.
        2. Wybierz topologię chcesz przeprowadzić ponowne równoważenie, a następnie wybierz **ponowne zrównoważenie** przycisku. Wprowadź opóźnienie przed wykonaniem operacji ponownego równoważenia.

* **Platforma Kafka**: Po operacji skalowania należy przeprowadzić ponowne równoważenie replik partycji. Aby uzyskać więcej informacji, zobacz [zapewnić wysoką dostępność danych dzięki platformie Apache Kafka w HDInsight](./kafka/apache-kafka-high-availability.md) dokumentu.

Aby uzyskać szczegółowe informacje na temat skalowania klastra usługi HDInsight zobacz:

* [Zarządzanie klastrami Apache Hadoop w HDInsight przy użyciu witryny Azure portal](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Zarządzanie klastrami Apache Hadoop w HDInsight przy użyciu programu Azure PowerShell](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Jak instalowanie aplikacji Hue (lub innych składników usługi Hadoop)

HDInsight to zarządzana usługa. Jeśli Azure wykryje problem z klastrem, jego może usunąć węzeł niepowodzeniem i Utwórz węzeł, aby go zastąpić. Po zainstalowaniu ręczne czynności w klastrze, nie zostaną utrwalone po wystąpieniu tej operacji. Zamiast tego należy użyć [akcji skryptu HDInsight](hdinsight-hadoop-customize-cluster-linux.md). Akcja skryptu, można wprowadzić następujące zmiany:

* Instalowanie i konfigurowanie usługi lub witryny sieci web.
* Zainstaluj i skonfiguruj składnik, który wymaga zmian konfiguracji na wielu węzłach w klastrze.

Akcje skryptu to skrypty powłoki Bash. Skrypty są uruchamiane podczas tworzenia klastra i są używane do instalowania i konfigurowania dodatkowych składników. Przykładowe skrypty są dostępne do zainstalowania następujących składników:

* [Apache Giraph](hdinsight-hadoop-giraph-install-linux.md)

Informacje na temat tworzenia własnych akcji skryptu można znaleźć w temacie [Script Action development with HDInsight](hdinsight-hadoop-script-actions-linux.md) (Tworzenie akcji skryptu za pomocą usługi HDInsight).

### <a name="jar-files"></a>Pliki JAR

Niektóre technologie Hadoop znajdują się pliki jar niezależna, które zawierają funkcje używane jako część zadania MapReduce lub od wewnątrz Pig i Hive. Często nie wymaga żadnej konfiguracji i mogą być przekazywane do klastra, po utworzeniu i bezpośredniego użycia. Jeśli chcesz upewnić się, że składnik przeżyje, odtwarzanie z obrazu klastra, można przechowywać plik jar w domyślnego magazynu klastra (WASB lub ADL).

Na przykład, jeśli chcesz używać najnowszej wersji [Apache DataFu](https://datafu.incubator.apache.org/), możesz pobrać plik jar zawierający projekt i przekaż go do klastra HDInsight. Następnie postępuj zgodnie z dokumentacją DataFu o tym, jak z niego korzystać z języka Pig i Hive.

> [!IMPORTANT]  
> Niektóre składniki są autonomiczne pliki JAR używane są dostarczane z HDInsight, ale nie znajdują się w ścieżce. Jeśli szukasz dla określonego składnika, można użyć działaniami do wyszukania w klastrze:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> To polecenie zwraca ścieżkę żadnych pasujących plików jar.

Aby użyć innej wersji składnika, Przekaż wersji, potrzebujesz i używać go w zadaniach.

> [!IMPORTANT]
> Składniki dostarczony z klastrem usługi HDInsight są w pełni obsługiwane i Microsoft Support pomaga wyizolować i rozwiązać problemy związane z tych składników.
>
> Składniki niestandardowe otrzymują uzasadnioną komercyjnie pomoc techniczną, aby pomóc rozwiązać ten problem. Może to spowodować rozwiązuje problem lub pytaniem, dzięki którym można zaangażować dostępne kanały dla technologii "open source", gdzie znajduje się specjalistyczna dla tej technologii. Na przykład istnieje wiele witryn społeczności, które mogą być używane, takie jak: [Forum MSDN dotyczące HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ https://stackoverflow.com ](https://stackoverflow.com). Projektów Apache mieć witryny projektu na [ https://apache.org ](https://apache.org), na przykład: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>Kolejne kroki

* [Migracja z systemem Windows HDInsight do opartych na systemie Linux](hdinsight-migrate-from-windows-to-linux.md)
* [Zarządzanie klastrami HDInsight przy użyciu interfejsu API Apache Ambari REST](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [Use Apache Hive z HDInsight](hadoop/hdinsight-use-hive.md)
* [Apache Pig za pomocą HDInsight](hadoop/hdinsight-use-pig.md)
* [Korzystanie z zadań MapReduce z usługą HDInsight](hadoop/hdinsight-use-mapreduce.md)
