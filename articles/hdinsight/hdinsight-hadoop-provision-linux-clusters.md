---
title: Klaster Instalatora dla technologii Apache Hadoop, Apache Spark, Apache Kafka, bazy danych Apache HBase lub R Server — HDInsight Azure
description: Konfigurowanie klastrów Hadoop, Kafka, Spark, HBase, R Server i Storm dla HDInsight z przeglądarki, klasycznego wiersza polecenia platformy Azure, programu Azure PowerShell, REST lub zestawu SDK.
keywords: Konfiguracja klastra usługi hadoop, klastra kafka instalacji, konfiguracji klastrów platformy spark, co to jest klaster na platformie hadoop
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017,seodec18
ms.topic: conceptual
ms.date: 05/28/2019
ms.openlocfilehash: 351b6a8e056d22fa8f2d695a2722b39b9771c8b0
ms.sourcegitcommit: 8c49df11910a8ed8259f377217a9ffcd892ae0ae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/29/2019
ms.locfileid: "66299386"
---
# <a name="set-up-clusters-in-hdinsight-with-apache-hadoop-apache-spark-apache-kafka-and-more"></a>Konfigurowanie klastrów w HDInsight przy użyciu technologii Apache Hadoop, Apache Spark, Apache Kafka i więcej

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Informacje o sposobie instalowania i konfigurowania klastrów w HDInsight przy użyciu technologii Apache Hadoop, Apache Spark, Apache Kafka, Interactive Query, bazy danych Apache HBase, usługi ML i Apache Storm. Ponadto Dowiedz się, jak dostosowywać klastry i zwiększyć bezpieczeństwo, je przyłączania do domeny.

Klaster Hadoop składa się z kilku maszyn wirtualnych (węzłów), które są używane do przetwarzania rozproszonego zadań. Usługa Azure HDInsight obsługuje szczegóły implementacji instalacji i konfiguracji poszczególnych węzłów, więc musisz podać informacje o konfiguracji ogólnych.

> [!IMPORTANT]  
> Naliczanie opłat rozpoczyna się w momencie utworzenia klastra usługi HDInsight i kończy się wraz z jego usunięciem. Opłaty są naliczane za minutę, więc jeśli klaster nie jest używany, należy go usunąć. Dowiedz się, jak [usunięcia klastra.](hdinsight-delete-cluster.md)

## <a name="cluster-setup-methods"></a>Metody instalacji klastra
W poniższej tabeli przedstawiono różne metody, których można użyć do skonfigurowania klastra usługi HDInsight.

| Klastry utworzone za pomocą | Przeglądarka sieci Web | Wiersz polecenia | Interfejs API REST | SDK | 
| --- |:---:|:---:|:---:|:---:|
| [Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Interfejs wiersza polecenia platformy Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Zestaw SDK platformy .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |
| [Szablony usługi Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

## <a name="quick-create-basic-cluster-setup"></a>Szybkie tworzenie: Konfiguracja podstawowa klastra
Ten artykuł przeprowadzi Cię przez Instalatora w [witryny Azure portal](https://portal.azure.com), w którym można utworzyć klaster HDInsight przy użyciu *szybkie tworzenie* lub *niestandardowe*. 

![Usługa hdinsight tworzenie opcji niestandardowych szybkie tworzenie](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-creation-options.png)

Wykonaj instrukcje na ekranie, aby zrobić Konfiguracja klastra podstawowego. Szczegółowe informacje są przedstawione poniżej dla:

* [Nazwa grupy zasobów](#resource-group-name)
* [Typy klastrów i konfiguracji](#cluster-types) 
* Logowania do klastra i nazwę użytkownika SSH
* [Lokalizacja](#location)

## <a name="resource-group-name"></a>Nazwa grupy zasobów

[Usługa Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) ułatwia pracę z zasobami w aplikacji jako grupa, określane jako grupę zasobów platformy Azure. Można wdrożyć, zaktualizować, monitorować lub usunąć wszystkie zasoby dla aplikacji w ramach jednej skoordynowanej operacji.

## <a name="cluster-types"></a> Typy klastrów i konfiguracji
Usługa Azure HDInsight aktualnie obsługuje następujące typy klastrów, każdy zestaw składników, aby zapewnić pewnych funkcji.

> [!IMPORTANT]  
> Klastry HDInsight są dostępne w różnych typów, z których każdy pojedyncze obciążenie lub technologii. Nie istnieje obsługiwana metoda do tworzenia klastra, który łączy wiele typów, takich jak Storm i bazy danych HBase w jednym klastrze. Jeśli rozwiązanie wymaga technologii, które są dystrybuowane między wieloma typy klastrów HDInsight, [sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-network) można połączyć typy wymagane klastra. 

| Typ klastra | Funkcja |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Zapytanie usługi Batch i analizy przechowywanych danych |
| [HBase](hbase/apache-hbase-overview.md) |Przetwarzanie dużych ilości danych NoSQL ze schematów, |
| [Zapytanie interaktywne](./interactive-query/apache-interactive-query-get-started.md) |Buforowanie w pamięci umożliwiające interaktywne i szybsze zapytania programu Hive |
| [Kafka](kafka/apache-kafka-introduction.md) | Jest to rozproszona platforma przesyłania strumieniowego, który może służyć do tworzenia potoków danych przesyłania strumieniowego w czasie rzeczywistym i aplikacji |
| [Usługi ML](r-server/r-server-overview.md) |Różne statystyki danych big data, modelowanie predykcyjne i możliwości uczenia maszynowego |
| [Spark](spark/apache-spark-overview.md) |Przetwarzanie w pamięci, zapytania interakcyjne, przetwarzanie wsadowe micro strumienia |
| [System STORM](storm/apache-storm-overview.md) |Przetwarzanie zdarzeń w czasie rzeczywistym |


### <a name="hdinsight-version"></a>HDInsight w wersji
Wybierz wersję HDInsight dla tego klastra. Aby uzyskać więcej informacji, zobacz [HDInsight obsługiwane wersje](hdinsight-component-versioning.md#supported-hdinsight-versions).


## <a name="cluster-login-and-ssh-username"></a>Logowania do klastra i nazwę użytkownika SSH
Przy użyciu klastrów HDInsight można skonfigurować dwa konta użytkownika podczas tworzenia klastra:

* Użytkownik HTTP: Domyślna nazwa użytkownika to *admin*. Używa konfiguracji podstawowej w witrynie Azure portal. Czasami jest to "Klaster użytkownika".
* Użytkownika SSH: Używane do łączenia z klastrem za pośrednictwem protokołu SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Pakiet Enterprise security umożliwia integrację HDInsight przy użyciu usługi Active Directory oraz struktury Apache Ranger. Wielu użytkowników mogą być tworzone przy użyciu pakiet Enterprise security.

## <a name="location"></a>Lokalizacji (regionów) dla klastrów i magazynu

Nie trzeba jawnie określić lokalizację klastra: Klaster znajduje się w tej samej lokalizacji co magazyn domyślny. Aby uzyskać listę obsługiwanych regionów, kliknij przycisk **Region** listy rozwijanej na [ceny HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

## <a name="storage-endpoints-for-clusters"></a>Punkty końcowe usługi Storage dla klastrów

Mimo że instalacji lokalnej usługi Hadoop używa pliku System (HDFS, Hadoop Distributed) do magazynu w klastrze, w chmurze można użyć punktów końcowych magazynu podłączone do klastra. Klastry HDInsight użyć [usługi Azure Data Lake Storage](hdinsight-hadoop-use-data-lake-store.md) lub [obiektów blob w usłudze Azure Storage](hdinsight-hadoop-use-blob-storage.md). Za pomocą usługi Azure Storage lub magazynu usługi Data Lake oznacza, że możesz bezpiecznie usunąć klastry HDInsight używane do obliczeń przy jednoczesnym zachowaniu danych. 

> [!WARNING]  
> Używanie dodatkowe konto magazynu w innej lokalizacji z klastra HDInsight nie jest obsługiwane.

Podczas konfigurowania dla domyślnego punktu końcowego magazynu należy określić kontener obiektów blob z konta usługi Azure Storage lub magazynu usługi Data Lake. Domyślny magazyn zawiera aplikacji i systemu dzienniki. Opcjonalnie można określić dodatkowe połączone konta usługi Azure Storage i kont usługi Data Lake Storage, które mogą uzyskiwać dostęp do klastra. Klaster HDInsight i kont magazynu zależne muszą być w tej samej lokalizacji platformy Azure.

![Ustawienia magazynu klastra: Punkty końcowe usługi storage zgodnego systemem plików HDFS](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-creation-storage.png)

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


### <a name="optional-metastores"></a>Opcjonalne magazyny metadanych
Można utworzyć opcjonalne magazyny metadanych programu Hive lub Apache Oozie. Jednak nie wszystkie typy klastrów obsługują magazyny metadanych i Azure SQL Data Warehouse nie jest zgodny z magazyny metadanych. 

Aby uzyskać więcej informacji, zobacz [używać zewnętrznych magazynów metadanych w usłudze Azure HDInsight](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]  
> Kiedy tworzysz niestandardowy Magazyn metadanych, nie używaj kresek, łączników ani spacji, nazwę bazy danych. Może to spowodować niepowodzenie procesu tworzenia klastra.

### <a name="use-hiveoozie-metastore"></a>Magazyn metadanych hive

Jeśli chcesz zachować tabele programu Hive, po usunięciu klastra usługi HDInsight, należy użyć niestandardowy Magazyn metadanych. Następnie można dołączyć Magazyn metadanych do innego klastra HDInsight.

HDInsight Magazyn metadanych, które są tworzone dla jednej wersji klastra HDInsight nie mogą być współużytkowane przez różnych wersji klastra HDInsight. Aby uzyskać listę wersji HDInsight, zobacz [HDInsight obsługiwane wersje](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="oozie-metastore"></a>Magazyn metadanych programu Oozie

Aby zwiększyć wydajność, korzystając z technologii Oozie, należy użyć niestandardowy Magazyn metadanych. Metadanych oferuje również dostęp do danych zadania programu Oozie, po usunięciu klastra. 

> [!IMPORTANT]  
> Nie można ponownie użyć niestandardowy Magazyn metadanych programu Oozie. Aby użyć niestandardowy Magazyn metadanych programu Oozie, musisz podać pustej bazy danych SQL Azure, podczas tworzenia klastra HDInsight.


## <a name="custom-cluster-setup"></a>Konfiguracja klastra niestandardowe
Kompilacje instalatora niestandardowego klastra na szybkie tworzenie ustawień i dodaje następujące opcje:
- [Pakiet Enterprise security](#enterprise-security-package)
- [Aplikacji HDInsight](#install-hdinsight-applications-on-clusters)
- [Rozmiar klastra](#configure-cluster-size)
- [Akcje skryptu](#advanced-settings-script-actions)
- [Sieć wirtualna](#advanced-settings-extend-clusters-with-a-virtual-network)
 
## <a name="enterprise-security-package"></a>Pakiet Enterprise security

Typy klastrów Hadoop, Spark, HBase, Kafka i interakcyjnych zapytań można włączyć **pakiet Enterprise Security**. Ten pakiet zawiera opcję, aby bezpieczniejszego ustawienia klastra przy użyciu struktury Apache Ranger i integracji z usługą Azure Active Directory. Aby uzyskać więcej informacji, zobacz [pakiet Enterprise Security w usłudze Azure HDInsight](./domain-joined/apache-domain-joined-introduction.md).

![Opcje tworzenia usługi hdinsight wybierz pakiet enterprise security](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-creation-enterprise-security-package.png)

Aby uzyskać więcej informacji na temat tworzenia HDInsight przyłączone do domeny klastra, zobacz [środowisku piaskownicy HDInsight przyłączone do domeny utwórz](./domain-joined/apache-domain-joined-configure.md). 

## <a name="install-hdinsight-applications-on-clusters"></a>Instalowanie aplikacji HDInsight w klastrach

Aplikacja usługi HDInsight to aplikacja, którą użytkownicy mogą zainstalować w klastrze usługi HDInsight opartym na systemie Linux. Możesz użyć aplikacji, dostarczone przez firmę Microsoft, innych firm lub który tworzyć samodzielnie. Aby uzyskać więcej informacji, zobacz [instalowanie aplikacji Apache Hadoop innych firm w usłudze Azure HDInsight](hdinsight-apps-install-applications.md).

Większość aplikacji HDInsight są instalowane na pustego węzła krawędzi.  Pustego węzła krawędzi jest maszyny wirtualnej z systemem Linux przy użyciu tych samych narzędzi klienckich, które są zainstalowane i skonfigurowane tak jak węzła głównego. W węźle brzegowym służy do uzyskiwania dostępu do klastra, testowania aplikację kliencką i hosting aplikacji klienckich. Aby uzyskać więcej informacji, zobacz [używanie pustych węzłów brzegowych w HDInsight](hdinsight-apps-use-edge-node.md).

## <a name="configure-cluster-size"></a>Skonfiguruj rozmiar klastra

Tak długo, jak istnieje klastra stosowana jest stawka za użycie węzła dla. Naliczanie opłat rozpoczyna się, gdy klaster zostanie utworzona i zatrzymuje, gdy klaster jest usuwany. Klastrów nie można cofać przydziału ani wstrzymania.

### <a name="number-of-nodes-for-each-cluster-type"></a>Liczba węzłów dla każdego typu klastra
Każdy typ klastra ma swój własny liczbę węzłów, terminologii dla węzłów i domyślny rozmiar maszyny Wirtualnej. W poniższej tabeli liczba węzłów dla każdego typu węzła jest w nawiasach.

| Type | Węzły | Diagram |
| --- | --- | --- |
| Hadoop |Węzeł główny (2), węzeł procesu roboczego (1 +) |![Węzły klastra usługi HDInsight Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |Główny serwer (2), region (1 +), węzeł główne/dozorcy (3) |![Węzły klastra HDInsight HBase](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Węzeł nimbus (2), serwer nadzorcy (1 +), węzłem dozorcy (3) |![Węzły klastra HDInsight Storm](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| platforma Spark |Węzeł główny (2), węzeł procesu roboczego (1 +), węzłem dozorcy (3) (wersja bezpłatna dla rozmiaru maszyny Wirtualnej dozorcy A1) |![Węzły klastra HDInsight Spark](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

Aby uzyskać więcej informacji, zobacz [domyślne rozmiary maszyn wirtualnych i konfiguracja węzła klastrów](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) w "Co to są składniki i wersje w HDInsight?"

Koszt klastrów HDInsight jest określany na podstawie liczby węzłów i rozmiary maszyn wirtualnych dla węzłów. 

Różne typy klastrów mieć różnych typów węzłów, liczby węzłów i rozmiary węzłów:
* Wartość domyślna typu klastra Hadoop: 
    * Dwa *węzłami głównymi*  
    * Cztery *węzłów procesu roboczego*
* Domyślny typ klastra STORM: 
    * Dwa *węzły Nimbus*
    * Trzy *węzłów dozorcy*
    * Cztery *węzły nadzorcy* 

Jeśli tylko testujesz HDInsight, firma Microsoft zaleca, że używasz jednego węzła procesu roboczego. Aby uzyskać więcej informacji na temat cen HDInsight, zobacz [ceny HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]  
> Limit rozmiaru klastra waha się między subskrypcjami platformy Azure. Skontaktuj się z pomocą [Azure pomocy technicznej dotyczącej rozliczeń](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) do zwiększenia limitu.

Jeśli używasz witryny Azure portal, aby skonfigurować klaster, rozmiar węzła jest dostępna za pośrednictwem **warstw cenowych węzła** bloku. W portalu widać również koszt związany z rozmiarów innego węzła. 

![Rozmiary węzłów HDInsight maszyny Wirtualnej](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-node-sizes.png)

### <a name="virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych 
Podczas wdrażania klastrów, należy wybrać zasoby obliczeniowe, w oparciu o rozwiązania, które planujesz wdrożyć. Następujące maszyny wirtualne są używane w przypadku klastrów HDInsight:
* A i maszyny wirtualne z serii D1 – 4: [Ogólnego przeznaczenia rozmiarów maszyn wirtualnych systemu Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* Seria D11 – 14 maszyn wirtualnych: [Zoptymalizowane pod kątem pamięci rozmiarów maszyn wirtualnych systemu Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Aby dowiedzieć się, jaka wartość należy używać do określenia rozmiaru maszyny Wirtualnej podczas tworzenia klastra za pomocą różnych zestawów SDK lub podczas korzystania z programu Azure PowerShell, zobacz [rozmiarów maszyn wirtualnych do użycia w przypadku klastrów HDInsight](../cloud-services/cloud-services-sizes-specs.md#size-tables). Z tego połączonego artykułu, użyj wartości w **rozmiar** kolumny tabel.

> [!IMPORTANT]  
> Jeśli potrzebujesz więcej niż 32 węzły procesu roboczego w klastrze, należy wybrać rozmiar węzła głównego z co najmniej 8 rdzeniami i 14 GB pamięci RAM.

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](../virtual-machines/windows/sizes.md). Aby uzyskać informacje o cenach poszczególnych rozmiarów, zobacz [ceny HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).   

## <a name="advanced-settings-script-actions"></a>Ustawienia zaawansowane: Akcje skryptu

Można zainstalować dodatkowe składniki lub dostosowywanie konfiguracji klastra za pomocą skryptów tworzenia. Te skrypty są wywoływane za pośrednictwem **akcji skryptu**, czyli opcja konfiguracji, która może być stosowane w witrynie Azure portal, poleceń cmdlet programu PowerShell Windows HDInsight lub zestawu .NET SDK HDInsight. Aby uzyskać więcej informacji, zobacz [klastra HDInsight dostosować za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

Niektóre składniki natywnego języka Java, takich jak Apache Mahout i usuwania kaskadowego, można uruchomić w klastrze jako pliki archiwum Java (JAR). Te pliki JAR można dystrybuować do usługi Azure Storage i przesłane do klastrów HDInsight za pomocą mechanizmów przesyłanie zadań Hadoop. Aby uzyskać więcej informacji, zobacz [przesłać Apache Hadoop zadań programowo](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]  
> Jeśli występują problemy, wdrażanie plików JAR, korzystając z klastrami HDInsight lub wywoływania pliki JAR w klastrach HDInsight, skontaktuj się z pomocą [Microsoft Support](https://azure.microsoft.com/support/options/).
>
> Kaskadowe nie jest obsługiwana przez HDInsight i nie kwalifikuje się do firmy Microsoft Support. Aby uzyskać listę obsługiwanych składników, zobacz [nowości w wersjach klastra, dostarczone przez HDInsight](hdinsight-component-versioning.md).

Czasami którą chcesz skonfigurować następujące pliki konfiguracji w trakcie procesu tworzenia:

* clusterIdentity.xml
* core-site.xml
* gateway.xml
* hbase-env.xml
* hbase-site.xml
* hdfs-site.xml
* hive-env.xml
* hive-site.xml
* mapred-site
* oozie-site.xml
* oozie-env.xml
* storm-site.xml
* tez-site.xml
* webhcat-site.xml
* yarn-site.xml

Aby uzyskać więcej informacji, zobacz [HDInsight Dostosowywanie klastrów za pomocą narzędzia Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="advanced-settings-extend-clusters-with-a-virtual-network"></a>Ustawienia zaawansowane: Rozszerzanie klastrów z siecią wirtualną
Jeśli rozwiązanie wymaga technologii, które są dystrybuowane między wieloma typy klastrów HDInsight, [sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-network) można połączyć typy wymagane klastra. Ta konfiguracja umożliwia klastrów i wszelkie kod, który można wdrożyć do nich, może komunikować się bezpośrednio ze sobą.

Aby uzyskać więcej informacji dotyczących korzystania z siecią wirtualną platformy Azure z usługą HDInsight, zobacz [rozszerzyć HDInsight z usługą Azure virtual networks](hdinsight-extend-hadoop-virtual-network.md).

Aby uzyskać przykład użycia dwa typy klastrów w ramach sieci wirtualnej platformy Azure, zobacz [Użyj Apache Spark przesyłanie strumieniowe ze strukturą za pomocą platformy Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). Aby uzyskać więcej informacji o używaniu HDInsight z siecią wirtualną, łącznie z określonymi wymaganiami konfiguracji sieci wirtualnej, zobacz [HDInsight rozszerzanie możliwości za pomocą usługi Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).


## <a name="next-steps"></a>Kolejne kroki

- [Co to są HDInsight, ten ekosystem Apache Hadoop i klastrów Hadoop?](hadoop/apache-hadoop-introduction.md)
- [Rozpoczynanie pracy przy użyciu technologii Apache Hadoop w HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
- [Praca w technologii Apache Hadoop w HDInsight z Windows PC](hdinsight-hadoop-windows-tools.md)
