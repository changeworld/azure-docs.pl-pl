---
title: Klastrze konfiguracji w usłudze Hadoop, Spark, Kafka, HBase i R Server — HDInsight platformy Azure
description: Konfigurowanie usługi Hadoop, platformy Kafka, Spark, HBase, R Server lub klastry Storm dla HDInsight z przeglądarki, wiersza polecenia platformy Azure, programu Azure PowerShell, REST lub zestawu SDK.
services: storage
author: jamesbak
ms.component: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.openlocfilehash: f1c42a3e091bc4b3804edfe835e521a99fcee880
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2018
ms.locfileid: "52975067"
---
# <a name="quickstart-set-up-clusters-in-hdinsight"></a>Szybki Start: Konfigurowanie klastrów w HDInsight

W tym przewodniku Szybki Start dowiesz się, jak instalowanie i konfigurowanie klastrów w HDInsight przy użyciu Hadoop, Spark, Kafka, Interactive Query, HBase, R Server i Storm. Dowiesz się też dostosowywać klastry, dołącz je do domeny i dołącz je do konta magazynu przy użyciu [wersji zapoznawczej usługi Azure Data Lake Storage Gen2](data-lake-storage-introduction.md) włączone.

Klaster Hadoop składa się z kilku maszyn wirtualnych (węzłów), które są używane do przetwarzania rozproszonego zadań. Usługa Azure HDInsight obsługuje szczegóły implementacji instalacji i konfiguracji poszczególnych węzłów, więc musisz podać informacje o konfiguracji ogólnych.

> [!IMPORTANT]
>Naliczanie opłat rozpoczyna się w momencie utworzenia klastra usługi HDInsight i kończy się wraz z jego usunięciem. Opłaty są naliczane za minutę, więc jeśli klaster nie jest używany, należy go usunąć. Dowiedz się, jak [usunięcia klastra.](../../hdinsight/hdinsight-delete-cluster.md)

Konto magazynu z możliwościami Data Lake Storage Gen2 służy jako warstwa danych, w tym przewodniku Szybki Start. Ze swoją usługą hierarchicznej przestrzeni nazw i [sterownika Hadoop](data-lake-storage-abfs-driver.md), Data Lake Storage Gen2 jest zoptymalizowany do przetwarzania rozproszonego i analizy. Dane przechowywane na koncie magazynu ma włączone Gen2 Data Lake magazynu — będzie nadal występować, nawet w przypadku, po usunięciu klastra usługi HDInsight.

## <a name="cluster-setup-methods"></a>Metody instalacji klastra

W poniższej tabeli przedstawiono różne metody, których można użyć do skonfigurowania klastra usługi HDInsight.

| Klastry utworzone za pomocą | Przeglądarki sieci Web | Wiersz polecenia | Interfejs API REST | SDK | 
| --- |:---:|:---:|:---:|:---:|
| [Azure Portal](../../hdinsight/hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](../../hdinsight/hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Interfejs wiersza polecenia platformy Azure (wersja 1.0)](../../hdinsight/hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](../../hdinsight/hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](../../hdinsight/hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Szablony usługi Azure Resource Manager](../../hdinsight/hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

## <a name="quick-create-basic-cluster-setup"></a>Szybkie tworzenie: Konfiguracja podstawowa klastra

Ten artykuł przeprowadzi Cię przez Instalatora w [witryny Azure portal](https://portal.azure.com), w którym można utworzyć klaster HDInsight przy użyciu *szybkie tworzenie* lub *niestandardowe*.

![Usługa hdinsight tworzenie opcji niestandardowych szybkie tworzenie](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-creation-options.png)

Wykonaj instrukcje na ekranie, aby zrobić Konfiguracja klastra podstawowego. Szczegółowe informacje są przedstawione poniżej dla:

* [Nazwa grupy zasobów](#resource-group-name)
* [Typy klastrów i konfiguracji](#cluster-types) 
* [Logowania do klastra i nazwę użytkownika SSH](#cluster-login-and-ssh-user-name)
* [Lokalizacja](#location)

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz [wycofanie HDInsight 3.3](../../hdinsight/hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a name="resource-group-name"></a>Nazwa grupy zasobów

[Usługa Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) ułatwia pracę z zasobami w aplikacji jako grupa, określane jako grupę zasobów platformy Azure. Można wdrożyć, zaktualizować, monitorować lub usunąć wszystkie zasoby dla aplikacji w ramach jednej skoordynowanej operacji.

## <a name="cluster-types"></a> Typy klastrów i konfiguracji

Usługa Azure HDInsight aktualnie obsługuje następujące typy klastrów, każdy zestaw składników, aby zapewnić pewnych funkcji.

> [!IMPORTANT]
> Klastry HDInsight są dostępne w różnych typów, z których każdy pojedyncze obciążenie lub technologii. Nie istnieje obsługiwana metoda do tworzenia klastra, który łączy wiele typów, takich jak Storm i bazy danych HBase w jednym klastrze. Jeśli rozwiązanie wymaga technologii, które są dystrybuowane między wieloma typy klastrów HDInsight, [sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-network) można połączyć typy wymagane klastra.

| Typ klastra | Funkcjonalność |
| --- | --- |
| [Hadoop](../../hdinsight/hadoop/apache-hadoop-introduction.md) |Zapytanie usługi Batch i analizy przechowywanych danych |
| [HBase](../../hdinsight/hbase/apache-hbase-overview.md) |Przetwarzanie dużych ilości danych NoSQL ze schematów, |
| [Zapytanie interaktywne](../../hdinsight/interactive-query/apache-interactive-query-get-started.md) |Buforowanie w pamięci umożliwiające interaktywne i szybsze zapytania programu Hive |
| [Kafka](../../hdinsight/kafka/apache-kafka-introduction.md) | Jest to rozproszona platforma przesyłania strumieniowego, który może służyć do tworzenia potoków danych przesyłania strumieniowego w czasie rzeczywistym i aplikacji |
| [R Server](../../hdinsight/r-server/r-server-overview.md) |Różne statystyki danych big data, modelowanie predykcyjne i możliwości uczenia maszynowego |
| [Spark](../../hdinsight/spark/apache-spark-overview.md) |Przetwarzanie w pamięci, zapytania interakcyjne, przetwarzanie wsadowe micro strumienia |
| [System STORM](../../hdinsight/storm/apache-storm-overview.md) |Przetwarzanie zdarzeń w czasie rzeczywistym |

### <a name="hdinsight-version"></a>HDInsight w wersji

Wybierz wersję HDInsight dla tego klastra. Aby uzyskać więcej informacji, zobacz [HDInsight obsługiwane wersje](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

Aby uzyskać więcej informacji na temat tworzenia HDInsight przyłączone do domeny klastra, zobacz [środowisku piaskownicy HDInsight przyłączone do domeny utwórz](../../hdinsight/domain-joined/apache-domain-joined-configure.md).

## <a name="cluster-login-and-ssh-user-name"></a>Logowania do klastra i nazwę użytkownika SSH

Przy użyciu klastrów HDInsight można skonfigurować dwa konta użytkownika podczas tworzenia klastra:

* Użytkownika HTTP: domyślna nazwa użytkownika jest *administratora*. Używa konfiguracji podstawowej w witrynie Azure portal. Czasami jest to "Klaster użytkownika".
* Użytkownika SSH (klastry systemu Linux): używane do łączenia z klastrem za pośrednictwem protokołu SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="location"></a>Lokalizacji (regionów) dla klastrów i magazynu

Nie trzeba jawnie; określ lokalizację klastra klaster znajduje się w tej samej lokalizacji co magazyn domyślny. Aby uzyskać listę obsługiwanych regionów, kliknij przycisk **Region** listy rozwijanej na [ceny HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

## <a name="storage-endpoints-for-clusters"></a>Punkty końcowe usługi Storage dla klastrów

Mimo że instalacji lokalnej usługi Hadoop używa pliku System (HDFS, Hadoop Distributed) do magazynu w klastrze, w chmurze można użyć punktów końcowych magazynu podłączone do klastra. Klastry HDInsight użyć [Data Lake Storage Gen2](data-lake-storage-abfs-driver.md) lub [obiektów blob w usłudze Azure Storage](../../hdinsight/hdinsight-hadoop-use-blob-storage.md). Za pomocą usługi Azure Storage lub magazynu usługi Data Lake oznacza, że możesz bezpiecznie usunąć klastry HDInsight używane do obliczeń przy jednoczesnym zachowaniu danych.

> [!WARNING]
> Używanie dodatkowe konto magazynu w innej lokalizacji z klastra HDInsight nie jest obsługiwane.

Podczas konfigurowania dla domyślnego punktu końcowego magazynu należy określić usługi Data Lake Storage. Domyślny magazyn zawiera aplikacji i systemu dzienniki. Opcjonalnie można określić dodatkowe połączone konta magazynu mających Gen2 magazynu programu Data Lake włączone, czy dostęp do klastra. Klaster HDInsight i kont magazynu zależne muszą być w tej samej lokalizacji platformy Azure.

![Ustawienia magazynu klastra: punktów końcowych magazynu zgodnego systemem plików HDFS](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-cluster-creation-storage2.png)

> [!IMPORTANT]
> Pamiętaj, aby **wyłączyć dostęp Data Lake Store**. To ustawienie odnosi się do starych *Data Lake Store* funkcjonalność i ma być wyłączona, aby *usługi Data Lake Storage* funkcje, aby działać poprawnie.

[!INCLUDE [secure-transfer-enabled-storage-account](../../../includes/hdinsight-secure-transfer.md)]

### <a name="optional-metastores"></a>Opcjonalne magazyny metadanych

Można utworzyć opcjonalne magazyny metadanych programu Hive lub Oozie. Jednak nie wszystkie typy klastrów obsługują magazyny metadanych i Azure SQL Data Warehouse nie jest zgodny z magazyny metadanych.

Aby uzyskać więcej informacji, zobacz [używać zewnętrznych magazynów metadanych w usłudze Azure HDInsight](../../hdinsight/hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]
> Kiedy tworzysz niestandardowy Magazyn metadanych, nie używaj kresek, łączników ani spacji, nazwę bazy danych. Może to spowodować niepowodzenie procesu tworzenia klastra.

### <a name="use-hiveoozie-metastore"></a>Magazyn metadanych hive

Jeśli chcesz zachować tabele programu Hive, po usunięciu klastra usługi HDInsight, należy użyć niestandardowy Magazyn metadanych. Następnie można dołączyć Magazyn metadanych do innego klastra HDInsight.

HDInsight Magazyn metadanych, które są tworzone dla jednej wersji klastra HDInsight nie mogą być współużytkowane przez różnych wersji klastra HDInsight. Aby uzyskać listę wersji HDInsight, zobacz [HDInsight obsługiwane wersje](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="oozie-metastore"></a>Magazyn metadanych programu Oozie

Aby zwiększyć wydajność, korzystając z technologii Oozie, należy użyć niestandardowy Magazyn metadanych. Metadanych oferuje również dostęp do danych zadania programu Oozie, po usunięciu klastra.

> [!IMPORTANT]
> Nie można ponownie użyć niestandardowy Magazyn metadanych programu Oozie. Aby użyć niestandardowy Magazyn metadanych programu Oozie, musisz podać pustej bazy danych SQL Azure, podczas tworzenia klastra HDInsight.

## <a name="configure-cluster-size"></a>Skonfiguruj rozmiar klastra

Tak długo, jak istnieje klastra stosowana jest stawka za użycie węzła dla. Naliczanie opłat rozpoczyna się, gdy klaster zostanie utworzona i zatrzymuje, gdy klaster jest usuwany. Klastrów nie można cofać przydziału ani wstrzymania.

### <a name="number-of-nodes-for-each-cluster-type"></a>Liczba węzłów dla każdego typu klastra

Każdy typ klastra ma swój własny liczbę węzłów, terminologii dla węzłów i domyślny rozmiar maszyny Wirtualnej. W poniższej tabeli liczba węzłów dla każdego typu węzła jest w nawiasach.

| Typ | Węzły | Diagram |
| --- | --- | --- |
| Hadoop |Węzeł główny (2), węzeł danych (1 +) |![Węzły klastra usługi HDInsight Hadoop](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Główny serwer (2), region (1 +), węzeł główne/dozorcy (3) |![Węzły klastra HDInsight HBase](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Węzeł nimbus (2), serwer nadzorcy (1 +), węzłem dozorcy (3) |![Węzły klastra HDInsight Storm](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-storm-cluster-type-setup.png) |
| platforma Spark |Węzeł główny (2), węzeł procesu roboczego (1 +), węzłem dozorcy (3) (wersja bezpłatna dla rozmiaru maszyny Wirtualnej dozorcy A1) |![Węzły klastra HDInsight Spark](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-spark-cluster-type-setup.png) |

Aby uzyskać więcej informacji, zobacz [domyślne rozmiary maszyn wirtualnych i konfiguracja węzła klastrów](../../hdinsight/hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) w "Co to są składniki i wersje w HDInsight?"

Koszt klastrów HDInsight jest określany na podstawie liczby węzłów i rozmiary maszyn wirtualnych dla węzłów.

Różne typy klastrów mieć różnych typów węzłów, liczby węzłów i rozmiary węzłów:
* Wartość domyślna typu klastra Hadoop:
    * Dwa *węzłami głównymi*  
    * Cztery *węzły danych*
* Domyślny typ klastra STORM:
    * Dwa *węzły Nimbus*
    * Trzy *węzłów dozorcy*
    * Cztery *węzły nadzorcy*

Jeśli tylko testujesz HDInsight, firma Microsoft zaleca, że używasz jednego węzła danych. Aby uzyskać więcej informacji na temat cen HDInsight, zobacz [ceny HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]
> Limit rozmiaru klastra waha się między subskrypcjami platformy Azure. Skontaktuj się z pomocą [Azure pomocy technicznej dotyczącej rozliczeń](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) do zwiększenia limitu.

Jeśli używasz witryny Azure portal, aby skonfigurować klaster, rozmiar węzła jest dostępna za pośrednictwem **warstw cenowych węzła** bloku. W portalu widać również koszt związany z rozmiarów innego węzła.

![Rozmiary węzłów HDInsight maszyny Wirtualnej](media/data-lake-storage-quickstart-create-connect-hdi-cluster/hdinsight-node-sizes.png)

### <a name="virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych

Podczas wdrażania klastrów, należy wybrać zasoby obliczeniowe, w oparciu o rozwiązania, które planujesz wdrożyć. Następujące maszyny wirtualne są używane w przypadku klastrów HDInsight:

* A i maszyny wirtualne z serii D1 – 4: [rozmiarów maszyn wirtualnych systemu Linux General-purpose](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* Maszyny Wirtualnej serii D11 – 14: [rozmiarów zoptymalizowanych pod kątem pamięci maszyny Wirtualnej systemu Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Aby dowiedzieć się, jaka wartość należy używać do określenia rozmiaru maszyny Wirtualnej podczas tworzenia klastra za pomocą różnych zestawów SDK lub podczas korzystania z programu Azure PowerShell, zobacz [rozmiarów maszyn wirtualnych do użycia w przypadku klastrów HDInsight](../../cloud-services/cloud-services-sizes-specs.md#size-tables). Z tego połączonego artykułu, użyj wartości w **rozmiar** kolumny tabel.

> [!IMPORTANT]
> Jeśli potrzebujesz więcej niż 32 węzły procesu roboczego w klastrze, należy wybrać rozmiar węzła głównego z co najmniej 8 rdzeniami i 14 GB pamięci RAM.

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](../../virtual-machines/windows/sizes.md). Aby uzyskać informacje o cenach poszczególnych rozmiarów, zobacz [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).

## <a name="custom-cluster-setup"></a>Konfiguracja klastra niestandardowe

Kompilacje instalatora niestandardowego klastra na szybkie tworzenie ustawień i dodaje następujące opcje:

- [Aplikacji HDInsight](#hdinsight-applications)
- [Rozmiar klastra](#cluster-size)
- Ustawienia zaawansowane
  - [Akcje skryptu](#customize-clusters-using-script-action)
  - [Sieć wirtualna](#use-virtual-network)

## <a name="install-hdinsight-applications-on-clusters"></a>Instalowanie aplikacji HDInsight w klastrach

Aplikacja usługi HDInsight to aplikacja, którą użytkownicy mogą zainstalować w klastrze usługi HDInsight opartym na systemie Linux. Możesz użyć aplikacji, dostarczone przez firmę Microsoft, innych firm lub który tworzyć samodzielnie. Aby uzyskać więcej informacji, zobacz [instalowanie aplikacji platformy Hadoop innych firm w usłudze Azure HDInsight](../../hdinsight/hdinsight-apps-install-applications.md).

Większość aplikacji HDInsight są instalowane na pustego węzła krawędzi.  Pustego węzła krawędzi jest maszyny wirtualnej z systemem Linux przy użyciu tych samych narzędzi klienckich, które są zainstalowane i skonfigurowane tak jak węzła głównego. W węźle brzegowym służy do uzyskiwania dostępu do klastra, testowania aplikację kliencką i hosting aplikacji klienckich. Aby uzyskać więcej informacji, zobacz [używanie pustych węzłów brzegowych w HDInsight](../../hdinsight/hdinsight-apps-use-edge-node.md).

## <a name="advanced-settings-script-actions"></a>Ustawienia zaawansowane: akcji skryptu

Można zainstalować dodatkowe składniki lub dostosowywanie konfiguracji klastra za pomocą skryptów tworzenia. Te skrypty są wywoływane za pośrednictwem **akcji skryptu**, czyli opcja konfiguracji, która może być stosowane w witrynie Azure portal, poleceń cmdlet programu PowerShell Windows HDInsight lub zestawu .NET SDK HDInsight. Aby uzyskać więcej informacji, zobacz [klastra HDInsight dostosować za pomocą akcji skryptu](../../hdinsight/hdinsight-hadoop-customize-cluster-linux.md).

Niektóre składniki natywnego języka Java, takich jak Mahout i usuwania kaskadowego, można uruchomić w klastrze jako pliki archiwum Java (JAR). Te pliki JAR można dystrybuować do usługi Azure Storage lub Azure Data Lake Storage i przesłane do klastrów HDInsight za pomocą mechanizmów przesyłanie zadań Hadoop. Aby uzyskać więcej informacji, zobacz [Hadoop przesyłanie zadań programowo](../../hdinsight/hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]
> Jeśli występują problemy, wdrażanie plików JAR, korzystając z klastrami HDInsight lub wywoływania pliki JAR w klastrach HDInsight, skontaktuj się z pomocą [Microsoft Support](https://azure.microsoft.com/support/options/).
>
> Kaskadowe nie jest obsługiwana przez HDInsight i nie kwalifikuje się do firmy Microsoft Support. Aby uzyskać listę obsługiwanych składników, zobacz [nowości w wersjach klastra, dostarczone przez HDInsight](../../hdinsight/hdinsight-component-versioning.md).

Czasami którą chcesz skonfigurować następujące pliki konfiguracji w trakcie procesu tworzenia:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred lokacji
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Aby uzyskać więcej informacji, zobacz [HDInsight Dostosowywanie klastrów za pomocą narzędzia Bootstrap](../../hdinsight/hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="advanced-settings-extend-clusters-with-a-virtual-network"></a>Ustawienia zaawansowane: rozszerzanie klastrów z siecią wirtualną

Jeśli rozwiązanie wymaga technologii, które są dystrybuowane między wieloma typy klastrów HDInsight, [sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-network) można połączyć typy wymagane klastra. Ta konfiguracja umożliwia klastrów i wszelkie kod, który można wdrożyć do nich, może komunikować się bezpośrednio ze sobą.

Aby uzyskać więcej informacji dotyczących korzystania z siecią wirtualną platformy Azure z usługą HDInsight, zobacz [rozszerzyć HDInsight z usługą Azure virtual networks](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md).

Aby uzyskać przykład użycia dwa typy klastrów w ramach sieci wirtualnej platformy Azure, zobacz [korzystanie ze strukturą przesyłania strumieniowego platformy Spark z platformą Kafka](../../hdinsight/hdinsight-apache-kafka-spark-structured-streaming.md). Aby uzyskać więcej informacji o używaniu HDInsight z siecią wirtualną, łącznie z określonymi wymaganiami konfiguracji sieci wirtualnej, zobacz [HDInsight rozszerzanie możliwości za pomocą usługi Azure Virtual Network](../../hdinsight/hdinsight-extend-hadoop-virtual-network.md).

## <a name="troubleshoot-access-control-issues"></a>Rozwiązywanie problemów z kontrolą dostępu

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](../../hdinsight/hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Kolejne kroki

- [Sterownik systemu plików Hadoop ABFS dla usługi Azure Data Lake Storage Gen2](data-lake-storage-abfs-driver.md)
- [Samouczek: wyodrębnianie, przekształcanie i ładowanie danych przy użyciu oprogramowania Apache Hive w usłudze Azure HDInsight](data-lake-storage-tutorial-extract-transform-load-hive.md)
- [Co to są HDInsight i ekosystemu platformy Hadoop oraz klastrów Hadoop?](../../hdinsight/hadoop/apache-hadoop-introduction.md)
- [Rozpoczęcie korzystania z usługi Hadoop w usłudze HDInsight](../../hdinsight/hadoop/apache-hadoop-linux-tutorial-get-started.md)
- [Pracowanie z platformą Hadoop w HDInsight z Windows PC](../../hdinsight/hdinsight-hadoop-windows-tools.md)
