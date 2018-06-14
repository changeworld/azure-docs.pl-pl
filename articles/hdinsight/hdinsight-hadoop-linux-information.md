---
title: Wskazówki dotyczące korzystania z platformy Hadoop w usłudze HDInsight opartych na systemie Linux - Azure | Dokumentacja firmy Microsoft
description: Implementacja porady dotyczące korzystania z klastrów opartych na systemie Linux usługą HDInsight (Hadoop) w znanym środowisku Linux działającą w chmurze Azure.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: c41c611c-5798-4c14-81cc-bed1e26b5609
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: larryfr
ms.openlocfilehash: 3ad7aa01200bf2bf4a63a380b2b883983c8622d6
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
ms.locfileid: "31405395"
---
# <a name="information-about-using-hdinsight-on-linux"></a>Informacje dotyczące korzystania z usługi HDInsight w systemie Linux

Azure klastry HDInsight zapewniają Hadoop w znanym środowisku Linux działającą w chmurze Azure. Dla większości zadań powinny działać dokładnie w żadnej instalacji platformy Hadoop w systemie Linux. Ten dokument uwidacznia poznać konkretne różnice, które należy zwrócić uwagę.

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz sekcję [HDInsight retirement on Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement) (Wycofanie usługi HDInsight w systemie Windows).

## <a name="prerequisites"></a>Wymagania wstępne

Wiele z tych kroków w tym dokumencie, użyj następujących narzędzi, które mogą muszą być zainstalowane w systemie.

* [cURL](https://curl.haxx.se/) — używanych do komunikowania się z usługami sieci web
* [jq](https://stedolan.github.io/jq/) — używane do analizowania dokumentów JSON
* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2) — umożliwia zdalne zarządzanie usługami platformy Azure

## <a name="users"></a>Użytkownicy

O ile [przyłączonych do domeny](./domain-joined/apache-domain-joined-introduction.md), należy rozważyć HDInsight **pojedynczego użytkownika** systemu. Utworzono pojedynczego konto użytkownika SSH z klastrem, z poziomu uprawnień administratora. Można utworzyć dodatkowych kont SSH, ale mają także dostęp administratora do klastra.

Domeny w usłudze HDInsight obsługuje wielu użytkowników i bardziej szczegółowe ustawienia uprawnień i ról. Aby uzyskać więcej informacji, zobacz [przyłączonych do domeny zarządzania w usłudze hdinsight](./domain-joined/apache-domain-joined-manage.md).

## <a name="domain-names"></a>Nazwy domen

Jest w pełni kwalifikowaną nazwę (FQDN) do użycia podczas połączenia z klastrem z Internetu  **&lt;clustername >. azurehdinsight.net** lub (tylko SSH)  **&lt;clustername-ssh >. azurehdinsight.net**.

Wewnętrznie każdy węzeł w klastrze ma nazwę, która jest przypisywana podczas konfiguracji klastra. Aby znaleźć nazwy klastra, zobacz **hostów** strony w interfejsie użytkownika sieci Web Ambari. Można także użyć następujących aby powrócić do listy hostów z interfejsu API REST Ambari:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Zastąp ciąg **CLUSTERNAME** nazwą klastra. Po wyświetleniu monitu wprowadź hasło dla konta administratora. To polecenie zwraca dokumentem JSON, który zawiera listę hostów w klastrze. Jq jest używany do wyodrębniania `host_name` wartość elementu dla każdego hosta.

Jeśli potrzebujesz do znajdowania nazwy węzła dla określonej usługi, można badać Ambari dla tego składnika. Na przykład aby znaleźć hosty węzła nazw systemu plików HDFS, należy użyć następującego polecenia:

    curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

To polecenie zwraca dokument JSON opisujący usługę, a następnie jq wysunięcie tylko `host_name` wartości dla hostów.

## <a name="remote-access-to-services"></a>Zdalny dostęp do usług

* **Ambari (sieć web)** -https://&lt;clustername >. azurehdinsight.net

    Uwierzytelnianie przy użyciu klastra administrator użytkownika i hasło, a następnie zaloguj się do narzędzia Ambari.

    Uwierzytelnianie w postaci zwykłego tekstu — zawsze pomocy, upewnij się, że połączenie jest zabezpieczone przy użyciu protokołu HTTPS.

    > [!IMPORTANT]
    > Część sieci web UI dostępne za pośrednictwem narzędzia Ambari uzyskiwać dostęp do węzłów przy użyciu nazwy domeny wewnętrznej. Nazwy domeny wewnętrznej nie są publicznie w Internecie. Błędy "nie można odnaleźć serwera" może zostać wyświetlony podczas próby uzyskania dostępu przez Internet niektórych funkcji.
    >
    > Aby korzystać z pełnej funkcjonalności interfejsu użytkownika sieci web Ambari, użyj tunelu SSH do serwera proxy ruchu w sieci web do węzła głównego klastra. Zobacz [Użyj SSH Tunneling można uzyskać dostępu do interfejsu użytkownika sieci web Ambari, ResourceManager, JobHistory, NameNode, Oozie i innych sieci web UI](hdinsight-linux-ambari-ssh-tunnel.md)

* **Ambari (REST)** -https://&lt;clustername >.azurehdinsight.net/ambari

    > [!NOTE]
    > Uwierzytelnianie przy użyciu klastra administrator użytkownika i hasła.
    >
    > Uwierzytelnianie w postaci zwykłego tekstu — zawsze pomocy, upewnij się, że połączenie jest zabezpieczone przy użyciu protokołu HTTPS.

* **WebHCat (Templeton)** -https://&lt;clustername >.azurehdinsight.net/templeton

    > [!NOTE]
    > Uwierzytelnianie przy użyciu klastra administrator użytkownika i hasła.
    >
    > Uwierzytelnianie w postaci zwykłego tekstu — zawsze pomocy, upewnij się, że połączenie jest zabezpieczone przy użyciu protokołu HTTPS.

* **SSH** - &lt;clustername >-ssh.azurehdinsight.net port 22 i 23. Port 22 jest używany do nawiązania połączenia headnode podstawowego, podczas gdy 23 jest używany do nawiązania połączenia pomocniczej. Aby uzyskać więcej informacji o węzłach głównych, zobacz artykuł [Availability and reliability of Hadoop clusters in HDInsight](hdinsight-high-availability-linux.md) (Dostępność i niezawodność klastrów Hadoop w usłudze HDInsight).

    > [!NOTE]
    > Dostępne są tylko głównymi węzłami klastra za pośrednictwem protokołu SSH z komputera klienta. Po nawiązaniu połączenia można następnie dostęp węzłów procesu roboczego przy użyciu protokołu SSH z headnode.

Aby uzyskać więcej informacji, zobacz [porty używane przez usługi Hadoop w usłudze HDInsight](hdinsight-hadoop-port-settings-for-services.md) dokumentu.

## <a name="file-locations"></a>Lokalizacje plików

Pliki związane z Hadoop znajdują się w węzłach klastra w `/usr/hdp`. Ten katalog, który zawiera podkatalogi następujące:

* **2.2.4.9-1**: Nazwa katalogu jest wersja platformie Hortonworks Data Platform używane przez usługi HDInsight. Numer w klastrze mogą być inne niż wymienione w tym miejscu.
* **bieżący**: ten katalog zawiera łącza do podkatalogów w obszarze **2.2.4.9-1** katalogu. Ten katalog istnieje, dzięki czemu nie trzeba pamiętać o numer wersji.

Przykładowe dane i pliki JAR znajduje się na rozproszonego systemu plików Hadoop w `/example` i `/HdiSamples`

## <a name="hdfs-azure-storage-and-data-lake-store"></a>System plików HDFS, magazynu Azure i usługi Data Lake Store

W większości dystrybucji Hadoop system plików HDFS nie jest obsługiwana przez lokalny magazyn na komputerach w klastrze. Korzystanie z magazynu lokalnego może być kosztowne rozwiązania oparte na chmurze gdzie są naliczane co godzinę lub za minutę dla zasobów obliczeniowych.

HDInsight używa obiekty BLOB w magazynie Azure albo usługi Azure Data Lake Store jako domyślnego magazynu. Te usługi zapewniają następujące korzyści:

* Tanie magazynu długoterminowego
* Ułatwienia dostępu z usług zewnętrznych, takich jak witryny sieci Web, plik przekazywania/Pobierz narzędzia różnych zestawów SDK języka i przeglądarki sieci web

Konto magazynu Azure mogą przechowywać 4,75 TB, że poszczególne obiekty BLOB (lub pliki z punktu widzenia HDInsight) mogą postępować tylko do 195 GB. Azure Data Lake Store może zwiększyć się dynamicznie do przechowywania plików z poszczególnych plików przekracza petabajt bilionów. Aby uzyskać więcej informacji, zobacz [obiekty BLOB opis](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs) i [usługi Data Lake Store](https://azure.microsoft.com/services/data-lake-store/).

Korzystając z usługi Azure Storage lub Data Lake Store, nie trzeba wykonywać żadnych czynności specjalnych, z usługi HDInsight w celu uzyskania dostępu do danych. Na przykład następujące polecenie wyświetla listę plików w `/example/data` folderu niezależnie od tego, czy jest on przechowywany w magazynie Azure lub usługi Data Lake Store:

    hdfs dfs -ls /example/data

### <a name="uri-and-scheme"></a>Identyfikator URI i schematu

Niektóre polecenia może wymagać Określ schemat identyfikatora URI w ramach podczas uzyskiwania dostępu do pliku. Na przykład składnik systemu plików HDFS Storm wymaga Określ schemat. Korzystając z innych niż domyślne magazynu dodany jako "dodatkowego" magazynu do klastra, należy zawsze używać systemu jako część identyfikatora URI.

Korzystając z __usługi Azure Storage__, użyj jednej z następujących systemów identyfikatora URI:

* `wasb:///`: Dostęp do magazynu domyślnego przy użyciu nieszyfrowanego komunikacji.

* `wasbs:///`: Dostęp do magazynu domyślnego za pomocą szyfrowaną komunikację.  Schemat wasbs jest obsługiwany tylko z usługi HDInsight w wersji 3,6 i jego nowszych wersjach.

* `wasb://<container-name>@<account-name>.blob.core.windows.net/`: Używany podczas komunikacji z kontem magazynu innych niż domyślne. Na przykład, jeśli użytkownik ma konto dodatkowego miejsca do magazynowania lub podczas uzyskiwania dostępu do danych przechowywane na koncie magazynu publicznie.

Korzystając z __usługi Data Lake Store__, użyj jednej z następujących systemów identyfikatora URI:

* `adl:///`: Dostęp do domyślnego Data Lake Store dla klastra.

* `adl://<storage-name>.azuredatalakestore.net/`: Używany podczas komunikacji z innych niż domyślne Data Lake Store. Umożliwia również dostęp do danych znajdujących się poza katalogiem głównym z klastrem usługi HDInsight.

> [!IMPORTANT]
> Podczas korzystania z usługi Data Lake Store jako domyślny magazyn dla usługi HDInsight, należy określić ścieżkę w ramach magazynu do użycia jako katalogu głównego magazynu usługi HDInsight. Domyślna ścieżka to `/clusters/<cluster-name>/`.
>
> Korzystając z `/` lub `adl:///` dostępu do danych, należy tylko dostęp do danych przechowywanych w katalogu głównym (na przykład `/clusters/<cluster-name>/`) klastra. Aby uzyskać dostęp do danych w dowolnym miejscu w magazynie, użyj `adl://<storage-name>.azuredatalakestore.net/` format.

### <a name="what-storage-is-the-cluster-using"></a>Jakie magazynu klastra używa

Ambari służy do pobierania domyślnej konfiguracji magazynu klastra. Użyj następującego polecenia można pobrać informacji o konfiguracji systemu plików HDFS przy użyciu programu curl i filtrować za pomocą [jq](https://stedolan.github.io/jq/):

```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'```

> [!NOTE]
> To polecenie zwraca pierwszy konfiguracji stosowany na serwerze (`service_config_version=1`), który zawiera te informacje. Konieczne może być Wyświetl listę wszystkich wersji konfiguracji można znaleźć najnowszy numer.

To polecenie zwraca wartość podobne do następujących identyfikatorów URI:

* `wasb://<container-name>@<account-name>.blob.core.windows.net` Jeśli przy użyciu konta usługi Azure Storage.

    Nazwa konta jest nazwa konta usługi Magazyn Azure. Nazwa kontenera jest kontenera obiektów blob, który jest elementem głównym magazynie klastra.

* `adl://home` Jeśli przy użyciu usługi Azure Data Lake Store. Aby uzyskać nazwę usługi Data Lake Store, użyj następujące wywołanie REST:

    ```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.hostname"] | select(. != null)'```

    To polecenie zwraca następujące nazwy hosta: `<data-lake-store-account-name>.azuredatalakestore.net`.

    Aby uzyskać katalogu w magazynie, który jest elementem głównym dla usługi HDInsight, należy użyć następujących wywołania REST:

    ```curl -u admin -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["dfs.adls.home.mountpoint"] | select(. != null)'```

    To polecenie zwraca ścieżkę podobną do następującej ścieżki: `/clusters/<hdinsight-cluster-name>/`.

Można również znaleźć informacje o magazynu przy użyciu portalu Azure, korzystając z następujących czynności:

1. W [portalu Azure](https://portal.azure.com/), wybierz z klastrem usługi HDInsight.

2. Z **właściwości** zaznacz **kont magazynu**. Zostaną wyświetlone informacje magazynu dla klastra.

### <a name="how-do-i-access-files-from-outside-hdinsight"></a>Jak dostęp do plików z poza HDInsight

Istnieją różne sposoby dostępu do danych poza klastrem usługi HDInsight. Poniżej przedstawiono kilka łącza do narzędzia i zestawy SDK, które mogą służyć do pracy z danymi:

Jeśli przy użyciu __usługi Azure Storage__, zobacz następujące linki dotyczące metod, że można uzyskać dostępu do danych:

* [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-az-cli2): polecenia interfejsu wiersza polecenia do pracy z platformą Azure. Po zainstalowaniu należy użyć `az storage` polecenia, aby uzyskać pomoc na temat używania magazynu, lub `az storage blob` poleceń specyficznych dla obiekt blob.
* [blobxfer.PY](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): skryptu python do pracy z obiektami BLOB w usłudze Azure Storage.
* Różne zestawy SDK:

    * [Java](https://github.com/Azure/azure-sdk-for-java)
    * [Node.js](https://github.com/Azure/azure-sdk-for-node)
    * [PHP](https://github.com/Azure/azure-sdk-for-php)
    * [Python](https://github.com/Azure/azure-sdk-for-python)
    * [Ruby](https://github.com/Azure/azure-sdk-for-ruby)
    * [.NET](https://github.com/Azure/azure-sdk-for-net)
    * [Interfejs API REST magazynu](https://msdn.microsoft.com/library/azure/dd135733.aspx)

Jeśli przy użyciu __Azure Data Lake Store__, zobacz następujące linki dotyczące metod, że można uzyskać dostępu do danych:

* [Przeglądarki sieci Web](../data-lake-store/data-lake-store-get-started-portal.md)
* [Program PowerShell](../data-lake-store/data-lake-store-get-started-powershell.md)
* [Interfejs wiersza polecenia platformy Azure 2.0](../data-lake-store/data-lake-store-get-started-cli-2.0.md)
* [Interfejsu API REST WebHDFS](../data-lake-store/data-lake-store-get-started-rest-api.md)
* [Narzędzia Data Lake Tools dla programu Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504)
* [.NET](../data-lake-store/data-lake-store-get-started-net-sdk.md)
* [Java](../data-lake-store/data-lake-store-get-started-java-sdk.md)
* [Python](../data-lake-store/data-lake-store-get-started-python.md)

## <a name="scaling"></a>Skalowanie klastra

Skalowanie funkcji klastra umożliwia dynamiczną zmianę liczby węzłów danych używane przez klaster. Można wykonywać operacje skalowania podczas innych zadań lub procesów są uruchomione w klastrze.

Różne typy klastrów dotyczy skalowanie w następujący sposób:

* **Hadoop**: podczas skalowania liczby węzłów w klastrze, niektóre z tych usług w klastrze ponownego uruchomienia. Skalowanie operacji może spowodować zadania uruchomione lub oczekujące się niepowodzeniem po zakończeniu operacji skalowania. Po zakończeniu operacji można ponownie przesłać zadania.
* **HBase**: serwery regionalne są automatycznie rozmieszczana za kilka minut, po zakończeniu operacji skalowania. Aby ręcznie równoważyć serwerów regionalnych, wykonaj następujące kroki:

    1. Połącz z klastrem usługi HDInsight przy użyciu protokołu SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

    2. Aby uruchomić z poziomu powłoki HBase, skorzystaj z następujących:

            hbase shell

    3. Po załadowaniu powłoki HBase, ręcznie saldo serwery regionalne należy wykonać następujące kroki:

            balancer

* **STORM**: należy przeprowadzić ponowne równoważenie wszelkie uruchomione topologie Storm, po wykonaniu operacji skalowania. Ponowne równoważenie umożliwia topologii Dopasuj ustawienia równoległości na podstawie nowych liczby węzłów w klastrze. Aby przeprowadzić ponowne równoważenie uruchomionych topologii, należy użyć jednej z następujących opcji:

    * **SSH**: nawiąż połączenie z serwerem i użyj następującego polecenia, aby ponowne zrównoważenie topologii:

            storm rebalance TOPOLOGYNAME

        Można również określić parametry, aby zastąpić wskazówek równoległości pierwotnie pochodzącymi z topologią. Na przykład `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` ponownie konfiguruje topologię procesów roboczych 5, 3 z modułów wykonujących dla składnika niebieski spout i 10 z modułów wykonujących dla składnika żółty bolt.

    * **Interfejsu użytkownika STORM**: ponowne zrównoważenie topologii przy użyciu interfejsu użytkownika platformy Storm, wykonaj następujące kroki.

        1. Otwórz **https://CLUSTERNAME.azurehdinsight.net/stormui** w przeglądarce sieci web, gdzie CLUSTERNAME jest nazwą klastra Storm. Jeśli zostanie wyświetlony monit, wprowadź nazwę administratora (Administrator) klastra usługi HDInsight i hasło określone podczas tworzenia klastra.
        2. Wybierz topologię chcesz wyrównać, a następnie wybierz **Rebalance** przycisku. Wprowadź opóźnienie przed wykonaniem operacji Zrównoważ.

* **Kafka**: replik partycji powinien rebalance po operacji skalowania. Aby uzyskać więcej informacji, zobacz [wysoką dostępność danych za pomocą Kafka w usłudze HDInsight](./kafka/apache-kafka-high-availability.md) dokumentu.

Aby uzyskać szczegółowe informacje na temat skalowania z klastrem usługi HDInsight zobacz:

* [Zarządzanie klastrami Hadoop w usłudze HDInsight przy użyciu portalu Azure](hdinsight-administer-use-portal-linux.md#scale-clusters)
* [Zarządzanie klastrami Hadoop w usłudze HDInsight przy użyciu programu Azure PowerShell](hdinsight-administer-use-command-line.md#scale-clusters)

## <a name="how-do-i-install-hue-or-other-hadoop-component"></a>Jak zainstalować Hue (lub innych składników Hadoop)?

HDInsight to zarządzana usługa. Jeśli Azure wykryje problem z klastrem, jego może usunąć węzeł się niepowodzeniem i Utwórz węzeł, aby go zastąpić. Jeśli musisz ręcznie zainstalować rzeczy w klastrze, nie zostają one zachowane, gdy ta operacja odbywa się. Zamiast tego należy użyć [akcji skryptu HDInsight](hdinsight-hadoop-customize-cluster.md). Akcja skryptu można dokonać następujących zmian:

* Instalowanie i konfigurowanie usługi lub witryny sieci web.
* Zainstaluj i skonfiguruj składnik, który wymaga zmian konfiguracji na wielu węzłach w klastrze.

Akcje skryptu to skrypty Bash. Skrypty uruchamiane podczas tworzenia klastra i są używane do instalowania i konfigurowania dodatkowe składniki. Przykładowe skrypty są dostępne do zainstalowania następujących składników:

* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)

Informacje na temat tworzenia własnych akcji skryptu można znaleźć w temacie [Script Action development with HDInsight](hdinsight-hadoop-script-actions-linux.md) (Tworzenie akcji skryptu za pomocą usługi HDInsight).

### <a name="jar-files"></a>Pliki JAR

Niektóre technologie Hadoop znajdują się pliki jar niezależne, które zawierają funkcje używane w ramach zadania MapReduce, lub z wewnątrz Pig lub Hive. Często nie wymaga żadnej konfiguracji i mogą być przekazywane do klastra po utworzeniu i bezpośredniego użycia. Jeśli chcesz upewnij się, że składnik przeżyje ponownym klastra, można przechowywać plik jar w magazynie domyślny dla klastra (WASB lub ADL).

Na przykład, jeśli chcesz używać najnowszej wersji [DataFu](http://datafu.incubator.apache.org/), możesz pobrać jar zawierający projekt i przekaż go do klastra usługi HDInsight. Następnie postępuj zgodnie z dokumentacją DataFu na jak z niego korzystać z języka Pig lub Hive.

> [!IMPORTANT]
> Niektóre składniki są pliki jar autonomiczny znajdują się z usługą HDInsight, ale nie znajdują się w ścieżce. Jeśli szukasz określonego składnika umożliwia wykonaj wyszukiwanie w klastrze:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> To polecenie zwraca ścieżkę pasujące pliki jar.

Aby użyć innej wersji składnika, Przekaż wersji, należy go i użyć go w zadaniach.

> [!WARNING]
> Składniki dostarczony z klastrem usługi HDInsight są w pełni obsługiwane i Microsoft Support pomaga wyizolować i rozwiązać problemy związane z tych składników.
>
> Niestandardowe składniki otrzymywanie pomocy uzasadnione ekonomicznie ułatwiające aby dalej rozwiązywać ten problem. Może to spowodować w rozwiązaniu problemu lub monitem o Uwzględnij dostępnych kanałów dla technologiach typu open source wykryto głębokie doświadczenia z tej technologii. Na przykład istnieje wiele witryn społeczności, które mogą być używane, takie jak: [forum MSDN dla usługi HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [ http://stackoverflow.com ](http://stackoverflow.com). Projekty Apache mieć witryny projektu na [ http://apache.org ](http://apache.org), na przykład: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/).

## <a name="next-steps"></a>Kolejne kroki

* [Migracja z usługi HDInsight opartej na systemie Windows do opartych na systemie Linux](hdinsight-migrate-from-windows-to-linux.md)
* [Korzystanie z programu Hive z usługą HDInsight](hadoop/hdinsight-use-hive.md)
* [Korzystanie z języka Pig z usługą HDInsight](hadoop/hdinsight-use-pig.md)
* [Korzystanie z zadań MapReduce z usługą HDInsight](hadoop/hdinsight-use-mapreduce.md)
