---
title: Wskazówki dotyczące korzystania z usługi Hadoop w systemie HDInsight opartym na systemie Linux — Azure
description: Uzyskaj wskazówki dotyczące implementacji dotyczące korzystania z klastrów HDInsight (Hadoop) opartych na systemie Linux w znanym środowisku systemu Linux działającym w chmurze platformy Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/14/2019
ms.openlocfilehash: 3d9dec0065bb62821fcedcbc4f6e5b578c061caf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272463"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Informacje dotyczące korzystania z usługi HDInsight w systemie Linux

Klastry usługi Azure HDInsight zapewniają apache Hadoop w znanym środowisku systemu Linux, działającym w chmurze platformy Azure. W przypadku większości rzeczy powinien działać dokładnie tak, jak każda inna instalacja Hadoop-on-Linux. W tym dokumencie wywołują konkretne różnice, o których należy pamiętać.

## <a name="prerequisites"></a>Wymagania wstępne

Wiele kroków w tym dokumencie korzysta z następujących narzędzi, które mogą wymagać zainstalowania w systemie.

* [cURL](https://curl.haxx.se/) - używany do komunikowania się z usługami internetowymi.
* **jq**, procesor JSON wiersza polecenia.  Zobacz [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/).
* [Interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) — służy do zdalnego zarządzania usługami platformy Azure.
* **Klient SSH**. Aby uzyskać więcej informacji, zobacz [Łączenie się z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="users"></a>Użytkownicy

O ile [domena nie została przyłączona,](./domain-joined/hdinsight-security-overview.md)usługa HDInsight powinna być uważana za system **dla jednego użytkownika.** Z klastrem jest tworzone pojedyncze konto użytkownika SSH z uprawnieniami na poziomie administratora. Można utworzyć dodatkowe konta SSH, ale mają również dostęp administratora do klastra.

Usługa HDInsight przyłączona do domeny obsługuje wielu użytkowników oraz bardziej szczegółowe uprawnienia i ustawienia ról. Aby uzyskać więcej informacji, zobacz [Zarządzanie klastrami HDInsight przyłączonych do domeny](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Nazwy domen

W pełni kwalifikowana nazwa domeny (FQDN) do użycia `CLUSTERNAME.azurehdinsight.net` podczas `CLUSTERNAME-ssh.azurehdinsight.net` łączenia się z klastrem z Internetu jest lub (tylko dla SSH).

Wewnętrznie każdy węzeł w klastrze ma nazwę przypisaną podczas konfiguracji klastra. Aby znaleźć nazwy klastra, zobacz stronę **Hosts** w interfejsie użytkownika sieci Web Ambari. Możesz również użyć następujących opcji, aby zwrócić listę hostów z interfejsu API Ambari REST:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Zastąp ciąg `CLUSTERNAME` nazwą klastra. Po wyświetleniu monitu wprowadź hasło do konta administratora. To polecenie zwraca dokument JSON zawierający listę hostów w klastrze. [jq](https://stedolan.github.io/jq/) służy do `host_name` wyodrębniania wartości elementu dla każdego hosta.

Jeśli chcesz znaleźć nazwę węzła dla określonej usługi, można wykonać kwerendę Ambari dla tego składnika. Na przykład, aby znaleźć hosty dla węzła nazwy HDFS, użyj następującego polecenia:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

To polecenie zwraca dokument JSON opisujący usługę, a następnie `host_name` [jq](https://stedolan.github.io/jq/) wyciąga tylko wartość dla hostów.

## <a name="remote-access-to-services"></a>Zdalny dostęp do usług

* **Ambari (strona internetowa)** - `https://CLUSTERNAME.azurehdinsight.net`

    Uwierzytelnij się przy użyciu użytkownika i hasła administratora klastra, a następnie zaloguj się do Ambari.

    Uwierzytelnianie jest włókniną - zawsze używaj protokołu HTTPS, aby zapewnić bezpieczeństwo połączenia.

    > [!IMPORTANT]  
    > Niektóre internetowe interfejsy użytkownika dostępne za pośrednictwem węzłów dostępu Ambari przy użyciu wewnętrznej nazwy domeny. Wewnętrzne nazwy domen nie są publicznie dostępne za pośrednictwem Internetu. Podczas próby uzyskania dostępu do niektórych funkcji przez Internet mogą pojawić się błędy "nie znaleziono serwera".
    >
    > Aby użyć pełnej funkcjonalności interfejsu użytkownika sieci Web Ambari, użyj tunelu SSH, aby proxy ruchu sieciowego do węzła głównego klastra. Zobacz [Używanie tunelowania SSH w celu uzyskania dostępu do interfejsu użytkownika sieci Web Apache Ambari, ResourceManager, JobHistory, NameNode, Oozie i innych interfejsów użytkownika sieci Web](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (ODPOCZYNEK)** - `https://CLUSTERNAME.azurehdinsight.net/ambari`

    > [!NOTE]  
    > Uwierzytelnij się przy użyciu użytkownika i hasła administratora klastra.
    >
    > Uwierzytelnianie jest włókniną - zawsze używaj protokołu HTTPS, aby zapewnić bezpieczeństwo połączenia.

* **WebHCat (Templeton)** - `https://CLUSTERNAME.azurehdinsight.net/templeton`

    > [!NOTE]  
    > Uwierzytelnij się przy użyciu użytkownika i hasła administratora klastra.
    >
    > Uwierzytelnianie jest włókniną - zawsze używaj protokołu HTTPS, aby zapewnić bezpieczeństwo połączenia.

* **SSH** - CLUSTERNAME-ssh.azurehdinsight.net na porcie 22 lub 23. Port 22 służy do łączenia się z głównymi kluczami, podczas gdy 23 służy do łączenia się z pomocniczym. Aby uzyskać więcej informacji na temat węzłów głównego, zobacz [Dostępność i niezawodność klastrów Apache Hadoop w programie HDInsight](hdinsight-high-availability-linux.md).

    > [!NOTE]  
    > Dostęp do węzłów głównego klastra można uzyskać tylko za pośrednictwem pamięci SSH z komputera klienckiego. Po nawiązaniu połączenia można uzyskać dostęp do węzłów procesu roboczego przy użyciu funkcji SSH z węzła głównego.

Aby uzyskać więcej informacji, zobacz [porty używane przez usługi Apache Hadoop w dokumencie HDInsight.](hdinsight-hadoop-port-settings-for-services.md)

## <a name="file-locations"></a>Lokalizacje plików

Pliki związane z hadoopem można znaleźć `/usr/hdp`w węzłach klastra pod adresem . Ten katalog zawiera następujące podkatalogi:

* **2.6.5.3009-43**: Nazwa katalogu jest wersją platformy Hadoop używanej przez HDInsight. Numer w klastrze może być inny niż ten podany w tym miejscu.
* **current**: Ten katalog zawiera łącza do podkatalogów w katalogu **2.6.5.3009-43.** Ten katalog istnieje, dzięki czemu nie trzeba zapamiętywać numeru wersji.

Przykładowe dane i pliki JAR można znaleźć na `/example` Hadoop Distributed File System w i `/HdiSamples`.

## <a name="hdfs-azure-storage-and-data-lake-storage"></a>Usługi HDFS, usługa Azure Storage i magazyn usługi Data Lake

W większości dystrybucji Hadoop dane są przechowywane w systemie PLIKÓW HDFS, który jest wspierany przez lokalną pamięć masową na komputerach w klastrze. Korzystanie z magazynu lokalnego może być kosztowne dla rozwiązania chmurowego, w którym naliczane są opłaty godzinowe lub minutowe za zasoby obliczeniowe.

Podczas korzystania z usługi HDInsight pliki danych są przechowywane w skalowalny i odporny sposób w chmurze przy użyciu usługi Azure Blob Storage i opcjonalnie usługi Azure Data Lake Storage. Usługi te zapewniają następujące korzyści:

* Tanie długoterminowe przechowywanie.
* Ułatwienia dostępu z usług zewnętrznych, takich jak strony internetowe, narzędzia do przesyłania/pobierania plików, różne pliki SDK języka i przeglądarki internetowe.
* Duża pojemność pliku i duża skalowalna pamięć masowa.

Aby uzyskać więcej informacji, zobacz [Opis obiektów blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) i [magazynu usługi Data Lake](https://azure.microsoft.com/services/storage/data-lake-storage/).

Podczas korzystania z usługi Azure Storage lub Usługi Data Lake Storage, nie trzeba robić nic specjalnego z HDInsight, aby uzyskać dostęp do danych. Na przykład następujące polecenie wyświetla `/example/data` listę plików w folderze, niezależnie od tego, czy są one przechowywane w usłudze Azure Storage, czy w magazynie usługi Data Lake:

    hdfs dfs -ls /example/data

W usłudze HDInsight zasoby magazynu danych (usługa Azure Blob Storage i Usługa Azure Data Lake Storage) są oddzielone od zasobów obliczeniowych. W związku z tym można utworzyć klastry HDInsight do wykonywania obliczeń, zgodnie z potrzebami, a później usunąć klaster po zakończeniu pracy, w międzyczasie utrzymując pliki danych utrwalone bezpiecznie w magazynie w chmurze, tak długo, jak trzeba.

### <a name="uri-and-scheme"></a><a name="URI-and-scheme"></a>Identyfikator URI i schemat

Niektóre polecenia mogą wymagać określenia schematu jako części identyfikatora URI podczas uzyskiwania dostępu do pliku. Na przykład składnik Storm-HDFS wymaga określenia schematu. W przypadku korzystania z magazynu nie domyślnego (magazyn dodany jako "dodatkowy" magazyn do klastra) należy zawsze używać schematu jako części identyfikatora URI.

Korzystając z [**usługi Azure Storage,**](./hdinsight-hadoop-use-blob-storage.md)użyj jednego z następujących schematów identyfikatorów URI:

* `wasb:///`: Dostęp do magazynu domyślnego przy użyciu komunikacji niezaszyfrowanej.

* `wasbs:///`: Dostęp do domyślnej pamięci masowej przy użyciu komunikacji szyfrowanej.  Schemat wasbs jest obsługiwany tylko od wersji HDInsight 3.6.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Używany podczas komunikowania się z domyślnym kontem magazynu. Na przykład, gdy masz dodatkowe konto magazynu lub podczas uzyskiwania dostępu do danych przechowywanych na publicznie dostępnym koncie magazynu.

Korzystając z [**usługi Azure Data Lake Storage Gen2,**](./hdinsight-hadoop-use-data-lake-storage-gen2.md)użyj następującego schematu identyfikatorów URI:

* `abfs://`: Dostęp do domyślnej pamięci masowej przy użyciu komunikacji szyfrowanej.

* `abfs://<container-name>@<account-name>.dfs.core.windows.net/`: Używany podczas komunikowania się z domyślnym kontem magazynu. Na przykład, gdy masz dodatkowe konto magazynu lub podczas uzyskiwania dostępu do danych przechowywanych na publicznie dostępnym koncie magazynu.

Korzystając z [**usługi Azure Data Lake Storage Gen1,**](./hdinsight-hadoop-use-data-lake-store.md)należy użyć jednego z następujących schematów identyfikatorów URI:

* `adl:///`: Dostęp do domyślnego magazynu usługi Data Lake dla klastra.

* `adl://<storage-name>.azuredatalakestore.net/`: Używany podczas komunikowania się z nieobezwłasnym magazynem usługi Data Lake. Używany również do uzyskiwania dostępu do danych spoza katalogu głównego klastra HDInsight.

> [!IMPORTANT]  
> Korzystając z magazynu usługi Data Lake jako domyślnego magazynu dla usługi HDInsight, należy określić ścieżkę w magazynie, która będzie używana jako katalog główny magazynu HDInsight. Domyślną ścieżką jest `/clusters/<cluster-name>/`.
>
> Podczas `/` korzystania `adl:///` z danych lub dostępu do nich można uzyskać `/clusters/<cluster-name>/`dostęp tylko do danych przechowywanych w katalogu głównym (na przykład ) klastra. Aby uzyskać dostęp do danych `adl://<storage-name>.azuredatalakestore.net/` w dowolnym miejscu w magazynie, użyj formatu.

### <a name="what-storage-is-the-cluster-using"></a>Jaki magazyn jest klastrem przy użyciu

Za pomocą programu Ambari można pobrać domyślną konfigurację magazynu dla klastra. Użyj następującego polecenia, aby pobrać informacje o konfiguracji usługi HDFS przy użyciu curl, i filtrować je za pomocą [jq:](https://stedolan.github.io/jq/)

```bash
curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
```

> [!NOTE]  
> To polecenie zwraca pierwszą konfigurację`service_config_version=1`zastosowaną do serwera ( ), która zawiera te informacje. Może być konieczne wyświetlenie listy wszystkich wersji konfiguracyjnych, aby znaleźć najnowszą.

To polecenie zwraca wartość podobną do następujących identyfikatorów URI:

* `wasb://<container-name>@<account-name>.blob.core.windows.net`jeśli używa konta usługi Azure Storage.

    Nazwa konta to nazwa konta usługi Azure Storage. Nazwa kontenera jest kontenerem obiektów blob, który jest katalogiem głównym magazynu klastra.

* `adl://home`jeśli używa usługi Azure Data Lake Storage. Aby uzyskać nazwę magazynu usługi Data Lake, użyj następującego wywołania REST:

     ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'
    ```

    To polecenie zwraca następującą `<data-lake-store-account-name>.azuredatalakestore.net`nazwę hosta: .

    Aby uzyskać katalog w magazynie, który jest głównym dla HDInsight, użyj następującego wywołania REST:

    ```bash
    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'
    ```

    To polecenie zwraca ścieżkę podobną `/clusters/<hdinsight-cluster-name>/`do następującej ścieżki: .

Informacje o magazynie można również znaleźć za pomocą witryny Azure portal, wykonując następujące kroki:

1. Z [witryny Azure portal](https://portal.azure.com/)wybierz klaster HDInsight.

2. W sekcji **Właściwości** wybierz pozycję **Konta magazynu**. Wyświetlane są informacje o magazynie klastra.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Jak uzyskać dostęp do plików spoza programu HDInsight

Istnieje wiele sposobów uzyskiwania dostępu do danych spoza klastra HDInsight. Poniżej przedstawiono kilka łączy do narzędzi i zestawów SDK, które mogą służyć do pracy z danymi:

Jeśli korzystasz z __usługi Azure Storage,__ zobacz następujące łącza, aby dostrzec sposoby uzyskiwania dostępu do danych:

* [Interfejs wiersza polecenia platformy Azure:](https://docs.microsoft.com/cli/azure/install-az-cli2)polecenia interfejsu wiersza polecenia do pracy z platformą Azure. Po zainstalowaniu `az storage` użyj polecenia, aby uzyskać `az storage blob` pomoc dotyczącą używania magazynu lub poleceń specyficznych dla obiektów blob.
* [blobxfer.py:](https://github.com/Azure/blobxfer)Skrypt języka Python do pracy z obiektami blob w usłudze Azure Storage.
* Różne SDK:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [Interfejs API REST magazynu](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Jeśli korzystasz z __usługi Azure Data Lake Storage,__ zobacz następujące łącza, aby dostrzec sposoby uzyskiwania dostępu do danych:

* [Przeglądarka internetowa](../data-lake-store/data-lake-store-get-started-portal.md)
* [Powershell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Interfejs wiersza polecenia platformy Azure](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [WebHDFS REST API](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Narzędzia usługi Data Lake tools dla programu Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling-your-cluster"></a><a name="scaling"></a>Skalowanie klastra

Funkcja skalowania klastra umożliwia dynamiczną zmianę liczby węzłów danych używanych przez klaster. Operacje skalowania można wykonywać, gdy w klastrze są uruchomione inne zadania lub procesy.  Zobacz też [Skalowanie klastrów HDInsight](./hdinsight-scaling-best-practices.md)

Różne typy klastrów są objęte skalowaniem w następujący sposób:

* **Hadoop:** Podczas skalowania w dół liczby węzłów w klastrze, niektóre usługi w klastrze są ponownie uruchamiane. Skalowanie operacji może spowodować zadania uruchomione lub oczekujące zakończyć się niepowodzeniem po zakończeniu operacji skalowania. Po zakończeniu operacji można ponownie przesłać zadania.
* **HBase**: Serwery regionalne są automatycznie równoważące w ciągu kilku minut, po zakończeniu operacji skalowania. Aby ręcznie zrównoważyć serwery regionalne, należy wykonać następujące czynności:

    1. Połącz się z klastrem HDInsight za pomocą funkcji SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    2. Aby uruchomić powłokę HBase, użyj następujących czynności:

            hbase shell

    3. Po załadowaniu powłoki HBase użyj następujących czynności, aby ręcznie zrównoważyć serwery regionalne:

            balancer

* **Burza:** Należy zrównoważyć wszystkie uruchomione topologie burzy po wykonaniu operacji skalowania. Ponowne równoważenie umożliwia topologii do ponownego dostosowania ustawień równoległości na podstawie nowej liczby węzłów w klastrze. Aby zrównoważyć topologie, użyj jednej z następujących opcji:

    * **SSH**: Połącz się z serwerem i użyj następującego polecenia, aby zrównoważyć topologię:

            storm rebalance TOPOLOGYNAME

        Można również określić parametry, aby zastąpić wskazówki równoległości pierwotnie dostarczone przez topologię. Na przykład `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` ponownie konfiguruje topologię do 5 procesów roboczych, 3 executory dla składnika blue-spout i 10 executors dla składnika żółtej śruby.

    * **Interfejs użytkownika burzy:** Użyj następujących kroków, aby zrównoważyć topologię przy użyciu interfejsu użytkownika burzy.

        1. Otwórz `https://CLUSTERNAME.azurehdinsight.net/stormui` w przeglądarce `CLUSTERNAME` internetowej, gdzie jest nazwa klastra Storm. Jeśli zostanie wyświetlony monit, wprowadź nazwę administratora klastra (administratora) HDInsight i hasło określone podczas tworzenia klastra.
        2. Wybierz topologię, którą chcesz zrównoważyć, a następnie wybierz przycisk **Równoważ równowagę.** Wprowadź opóźnienie przed wykonaniem operacji równoważenia.

* **Kafka**: Należy ponownie zrównoważyć repliki partycji po operacji skalowania. Aby uzyskać więcej informacji, zobacz [Wysoka dostępność danych z Apache Kafka w dokumencie HDInsight.](./kafka/apache-kafka-high-availability.md)

Aby uzyskać szczegółowe informacje na temat skalowania klastra HDInsight, zobacz:

* [Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu portalu Azure](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Zarządzanie klastrami Apache Hadoop w usłudze HDInsight przy użyciu interfejsu wiersza polecenia platformy Azure](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Jak zainstalować hue (lub inny składnik Hadoop)?

HDInsight jest usługą zarządzaną. Jeśli platforma Azure wykryje problem z klastrem, może usunąć węzeł, który się nie powiódł, i utworzyć węzeł, aby go zastąpić. Jeśli ręcznie zainstalować rzeczy w klastrze, nie są one utrwalone, gdy ta operacja występuje. Zamiast tego należy użyć [akcji skryptów HDInsight](hdinsight-hadoop-customize-cluster-linux.md). Akcji skryptu można wprowadzić następujące zmiany:

* Zainstaluj i skonfiguruj usługę lub witrynę sieci Web.
* Zainstaluj i skonfiguruj składnik, który wymaga zmian konfiguracji w wielu węzłach w klastrze.

Akcje skryptów są skryptami Bash. Skrypty są uruchamiane podczas tworzenia klastra i są używane do instalowania i konfigurowania dodatkowych składników. Informacje na temat tworzenia własnych akcji skryptu można znaleźć w temacie [Script Action development with HDInsight](hdinsight-hadoop-script-actions-linux.md) (Tworzenie akcji skryptu za pomocą usługi HDInsight).

### <a name="jar-files"></a>Pliki jar

Niektóre technologie Hadoop są dostarczane w samodzielnych plikach jar, które zawierają funkcje używane jako część zadania MapReduce lub od wewnątrz Pig lub Hive. Często nie wymagają żadnej konfiguracji i mogą być przekazywane do klastra po utworzeniu i używane bezpośrednio. Jeśli chcesz upewnić się, że składnik przetrwa ponowne zagosgnalizowanie klastra, możesz zapisać plik jar w domyślnym magazynie dla klastra (WASB lub ADL).

Na przykład, jeśli chcesz użyć najnowszej wersji [Apache DataFu](https://datafu.incubator.apache.org/), możesz pobrać słoik zawierający projekt i przekazać go do klastra HDInsight. Następnie postępuj zgodnie z dokumentacją DataFu, jak go używać z Pig lub Hive.

> [!IMPORTANT]  
> Niektóre składniki, które są autonomiczne pliki jar są dostarczane z HDInsight, ale nie są w ścieżce. Jeśli szukasz określonego składnika, możesz użyć obserwowanego, aby wyszukać go w klastrze:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> To polecenie zwraca ścieżkę wszystkich pasujących plików jar.

Aby użyć innej wersji składnika, przekaż potrzebną wersję i użyj jej w zadaniach.

> [!IMPORTANT]
> Składniki dostarczane z klastrem HDInsight są w pełni obsługiwane, a pomoc techniczna firmy Microsoft pomaga wyizolować i rozwiązać problemy związane z tymi składnikami.
>
> Składniki niestandardowe otrzymują komercyjnie uzasadnione wsparcie, które pomoże Ci w dalszym rozwiązywaniu problemu. Może to spowodować rozwiązanie problemu lub z prośbą o zaangażowanie dostępnych kanałów dla technologii open source, w których znajduje się głęboka wiedza specjalistyczna w zakresie tej technologii. Na przykład istnieje wiele witryn społecznościowych, z których można korzystać, [https://stackoverflow.com](https://stackoverflow.com)takich jak: [forum MSDN dla HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), . Również projekty Apache mają [https://apache.org](https://apache.org)miejsca projektu na , na przykład: [Hadoop](https://hadoop.apache.org/), [Spark](https://spark.apache.org/).

## <a name="next-steps"></a>Następne kroki

* [Zarządzanie klastrami HDInsight przy użyciu interfejsu API Apache Ambari REST](./hdinsight-hadoop-manage-ambari-rest-api.md)
* [Korzystanie z programu Apache Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z zadań MapReduce z usługą HDInsight](hadoop/hdinsight-use-mapreduce.md)
