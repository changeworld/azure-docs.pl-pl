---
title: Klaster Instalatora dla platformy Hadoop, Spark, Kafka, HBase lub R Server - Azure HDInsight | Dokumentacja firmy Microsoft
description: Skonfigurowanie usługi Hadoop, Kafka, Spark, HBase, R Server lub klastry Storm dla usługi HDInsight z przeglądarki, interfejsu wiersza polecenia Azure, programu Azure PowerShell, REST lub zestawu SDK.
keywords: Konfiguracja klastra usługi hadoop, kafka klastra Instalatora, konfiguracja klastra spark, co to jest klaster usługi hadoop
services: hdinsight
documentationcenter: ''
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 23a01938-3fe5-4e2e-8e8b-3368e1bbe2ca
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.devlang: na
ms.topic: conceptual
ms.date: 05/14/2018
ms.author: jgao
ms.openlocfilehash: 2afe1e91465c92c6f6cba00c4383456ef78d225c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
ms.locfileid: "34200201"
---
# <a name="set-up-clusters-in-hdinsight-with-hadoop-spark-kafka-and-more"></a>Ustawianie klastrów w usłudze HDInsight Hadoop, Spark, Kafka i

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Informacje o sposobie instalowania i konfigurowania klastrów w usłudze HDInsight Hadoop, Spark, Kafka, interakcyjne zapytania, HBase, R Server lub Storm. Ponadto Dowiedz się, jak dostosować klastry i Dodaj zabezpieczeń przez ich dołączania do domeny.

Klastra usługi Hadoop składa się z kilku maszyn wirtualnych (węzłów), które są używane do przetwarzania rozproszonego zadań. Usługa Azure HDInsight obsługuje szczegóły implementacji instalacji i konfiguracji poszczególnych węzłach, musisz podać informacje ogólne konfiguracji. 

> [!IMPORTANT]
>Naliczanie opłat rozpoczyna się w momencie utworzenia klastra usługi HDInsight i kończy się wraz z jego usunięciem. Opłaty są naliczane za minutę, więc jeśli klaster nie jest używany, należy go usunąć. Dowiedz się, jak [usunąć klaster.](hdinsight-delete-cluster.md)
>

## <a name="cluster-setup-methods"></a>Metody instalacji klastra
W poniższej tabeli przedstawiono różne metody, których można użyć do skonfigurowania klastra usługi HDInsight.

| Klastry utworzone za pomocą | Przeglądarki sieci Web | Wiersz polecenia | Interfejs API REST | SDK | 
| --- |:---:|:---:|:---:|:---:|
| [Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md) |✔ |&nbsp; |&nbsp; |&nbsp; |
| [Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md) |✔ |✔ |✔ |✔ |
| [Interfejs wiersza polecenia platformy Azure (wersja 1.0)](hdinsight-hadoop-create-linux-clusters-azure-cli.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |&nbsp; |✔ |&nbsp; |&nbsp; |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) |&nbsp; |✔ |✔ |&nbsp; |
| [Zestaw SDK platformy .NET](hdinsight-hadoop-create-linux-clusters-dotnet-sdk.md) |&nbsp; |&nbsp; |&nbsp; |✔ |
| [Szablony usługi Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md) |&nbsp; |✔ |&nbsp; |&nbsp; |

## <a name="quick-create-basic-cluster-setup"></a>Szybkie tworzenie: Konfiguracja podstawowa klastra
W tym artykule przedstawiono w za pośrednictwem ustawień [portalu Azure](https://portal.azure.com), w którym można utworzyć klastra usługi HDInsight przy użyciu *szybkie tworzenie* lub *niestandardowy*. 

![hdinsight tworzenie opcji niestandardowych szybkie tworzenie](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-creation-options.png)

Wykonaj instrukcje wyświetlane na ekranie przeprowadzenie instalacji podstawowy klaster. Szczegółowe informacje znajdują się poniżej dla:

* [Nazwa grupy zasobów](#resource-group-name)
* [Typy klastrów i konfiguracji](#cluster-types) 
* [Logowania do klastra i nazwy użytkownika SSH](#cluster-login-and-ssh-username)
* [Lokalizacja](#location)

> [!IMPORTANT]
> Linux jest jedynym systemem operacyjnym używanym w połączeniu z usługą HDInsight w wersji 3.4 lub nowszą. Aby uzyskać więcej informacji, zobacz [wycofanie HDInsight 3.3](hdinsight-component-versioning.md#hdinsight-windows-retirement).
>

## <a name="resource-group-name"></a>Nazwa grupy zasobów 

[Usługa Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) pomaga w pracy z zasobami w aplikacji w grupie, określany jako grupy zasobów platformy Azure. Można wdrożyć, zaktualizować, monitorować lub usunąć wszystkie zasoby aplikacji w ramach jednej skoordynowanej operacji.

## <a name="cluster-types"></a> Typy klastrów i konfiguracji
Usługa Azure HDInsight aktualnie zawiera następujące typy klastrów, każde z nich zestaw składników, aby zapewnić użycie pewnych funkcji.

> [!IMPORTANT]
> Klastry HDInsight są dostępne w różnych typów, z których każdy pojedyncze obciążenie lub technologii. Nie istnieje metoda obsługiwanych do utworzenia klastra, który łączy wiele typów, takie jak Storm i bazy danych HBase w jednym klastrze. Jeśli rozwiązanie wymaga technologie, które są rozkładane między wieloma typy klastrów usługi HDInsight, [sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-network) można połączyć typy klastrów wymagane. 
>
>

| Typ klastra | Funkcjonalność |
| --- | --- |
| [Hadoop](hadoop/apache-hadoop-introduction.md) |Partii zapytań i analizy przechowywanych danych |
| [HBase](hbase/apache-hbase-overview.md) |Przetwarzanie dużych ilości danych NoSQL schematu, |
| [Interakcyjne zapytania](./interactive-query/apache-interactive-query-get-started.md) |Buforowanie w pamięci dla zapytań programu Hive interakcyjne i szybsze |
| [Kafka](kafka/apache-kafka-introduction.md) | Rozproszonej platformy przesyłania strumieniowego, który może służyć do tworzenia w czasie rzeczywistym przesyłania strumieniowego potoki danych i aplikacji |
| [R Server](r-server/r-server-overview.md) |Różne statystyki danych big data, modelowania predykcyjnego i możliwości uczenia maszynowego |
| [Spark](spark/apache-spark-overview.md) |Przetwarzanie w pamięci, interakcyjnych zapytań przetwarzania wsadowego micro strumienia |
| [STORM](storm/apache-storm-overview.md) |Przetwarzanie zdarzeń w czasie rzeczywistym |


### <a name="hdinsight-version"></a>Wersja usługi HDInsight
Wybierz wersję HDInsight dla tego klastra. Aby uzyskać więcej informacji, zobacz [HDInsight obsługiwane wersje](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="enterprise-security-package"></a>Pakiet zabezpieczeń przedsiębiorstwa

Dla typów klastra usługi Hadoop, Spark i interaktywne zapytania, możesz włączyć **pakiet zabezpieczeń Enterprise**. Ten pakiet zawiera opcję, aby bezpieczniejsza Konfiguracja klastra przy użyciu zakres Apache i integracja z usługi Azure Active Directory. Aby uzyskać więcej informacji, zobacz [pakiet zabezpieczeń przedsiębiorstwa w usłudze Azure HDInsight](./domain-joined/apache-domain-joined-introduction.md).

![Opcje tworzenia hdinsight wybierz pakiet zabezpieczeń przedsiębiorstwa](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-creation-enterprise-security-package.png)

Aby uzyskać więcej informacji na temat tworzenia domeny w usłudze HDInsight klastra, zobacz [środowisko piaskownicy HDInsight przyłączonych do domeny utwórz](./domain-joined/apache-domain-joined-configure.md).


## <a name="cluster-login-and-ssh-user-name"></a>Logowania do klastra i nazwy użytkownika SSH
Z klastrami usługi HDInsight można skonfigurować dwa konta użytkownika, podczas tworzenia klastra:

* Użytkownik HTTP: domyślna nazwa użytkownika jest *admin*. Używa konfiguracji podstawowej w portalu Azure. Czasem jest nazywana "Klastra użytkownika".
* Użytkownik SSH (klastry z systemem Linux): używane do łączenia się z klastrem za pośrednictwem protokołu SSH. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Pakiet zabezpieczeń Enterprise umożliwia integrację HDInsight z usługą Active Directory i Apache zakres. Wielu użytkowników mogą być tworzone przy użyciu pakietu zabezpieczeń wpisywanie.

## <a name="location"></a>Lokalizacja (regiony) dla klastrów i magazynu

Nie trzeba jawnie określić lokalizację klastra: klastra znajduje się w tej samej lokalizacji co magazyn domyślny. Aby uzyskać listę obsługiwanych regionów, kliknij przycisk **Region** listy rozwijanej na [cennik usługi HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

## <a name="storage-endpoints-for-clusters"></a>Punkty końcowe magazynu dla klastrów

Mimo że instalacji lokalnej platformy Hadoop używa systemu Distributed plików Hadoop (HDFS) do magazynu w klastrze, w chmurze można użyć punkty końcowe magazynu podłączonego do klastra. Klastry HDInsight użyć [Azure Data Lake Store](hdinsight-hadoop-use-data-lake-store.md) lub [obiekty BLOB w usłudze Azure Storage](hdinsight-hadoop-use-blob-storage.md). Użycie magazynu Azure lub usługi Data Lake Store oznacza, że można bezpiecznie usunąć klastrów usługi HDInsight używane do obliczeń przy jednoczesnym zachowaniu danych. 

> [!WARNING]
> Przy użyciu konta, dodatkowe miejsce do magazynowania w innej lokalizacji z klastrem usługi HDInsight nie jest obsługiwane.

Podczas konfigurowania dla domyślnego punktu końcowego magazynu należy określić kontener obiektów blob z konta magazynu Azure lub usługi Data Lake Store. Magazyn domyślny zawiera aplikacji i systemu dzienników. Opcjonalnie można określić dodatkowe połączonych kontach magazynu Azure i konta usługi Data Lake Store, które mogą uzyskiwać dostęp do klastra. Klaster usługi HDInsight i kont magazynu zależnego musi być w tej samej lokalizacji platformy Azure.

![Ustawienia magazynu klastra: zgodnego systemem plików HDFS punkty końcowe usługi storage](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-cluster-creation-storage.png)

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]


### <a name="optional-metastores"></a>Magazyny opcjonalne
Można utworzyć opcjonalne magazyny Oozie lub gałąź. Jednak nie wszystkie typy klastrów obsługują magazyny i magazyn danych SQL Azure nie jest zgodna z magazynów metadanych. 

Aby uzyskać więcej informacji, zobacz [używać zewnętrznych metadanych magazynów w usłudze Azure HDInsight](./hdinsight-use-external-metadata-stores.md).

> [!IMPORTANT]
> Podczas tworzenia niestandardowego potrzeby magazynu metadanych, nie używaj łączniki, łączniki i spacje w nazwie bazy danych. Może to spowodować niepowodzenie procesu tworzenia klastra.

### <a name="use-hiveoozie-metastore"></a>Potrzeby magazynu metadanych hive

Jeśli chcesz zachować tabele programu Hive, po usunięciu klastra usługi HDInsight, należy użyć niestandardowego potrzeby magazynu metadanych. Następnie możesz dołączyć potrzeby magazynu metadanych do innego klastra usługi HDInsight.

Nie można udostępnić potrzeby magazynu metadanych usługi HDInsight, utworzonego dla jednego wersja klastra usługi HDInsight w różnych wersjach klastra usługi HDInsight. Aby uzyskać listę wersji usługi HDInsight, zobacz [HDInsight obsługiwane wersje](hdinsight-component-versioning.md#supported-hdinsight-versions).

### <a name="oozie-metastore"></a>Potrzeby magazynu metadanych Oozie

Aby zwiększyć wydajność, korzystając z Oozie, użyć niestandardowego potrzeby magazynu metadanych. Potrzeby magazynu metadanych oferuje również dostęp do danych zadania Oozie, po usunięciu klastra. 

> [!IMPORTANT]
> Nie można ponownie użyć niestandardowego potrzeby magazynu metadanych Oozie. Aby użyć niestandardowej potrzeby magazynu metadanych Oozie, musisz podać pustej bazy danych SQL Azure, podczas tworzenia klastra usługi HDInsight.

## <a name="configure-cluster-size"></a>Skonfiguruj rozmiar klastra

Rozliczenie jest użycie węzła tak długo, jak istnieje w klastrze. Rozliczanie zaczyna się po utworzeniu klastra i zatrzymuje po usunięciu klastra. Klastrów nie można deserializować przydzielone lub wstrzymane.


### <a name="number-of-nodes-for-each-cluster-type"></a>Liczba węzłów dla każdego typu klastra
Każdy typ klastra ma własną liczba węzłów, terminologii dla węzłów i domyślny rozmiar maszyny Wirtualnej. W poniższej tabeli liczba węzłów dla każdego typu węzła jest w nawiasach.

| Typ | Węzły | Diagram |
| --- | --- | --- |
| Hadoop |Nagłówek węzła (2), węzeł danych (1 +) |![Węzły klastra usługi HDInsight Hadoop](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hadoop-cluster-type-nodes.png) |
| HBase |HEAD serwer (2), region (1 +), węzeł główny/dozorcy (3) |![Węzły klastra HDInsight HBase](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-hbase-cluster-type-setup.png) |
| Storm |Węzeł nimbus (2), serwer przełożonego (1 +), dozorcy węzła (3) |![Węzły klastra Storm w usłudze HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-storm-cluster-type-setup.png) |
| platforma Spark |HEAD węzła (2), węzłem procesu roboczego (1 +), węzeł dozorcy (3) (bezpłatnie dla rozmiaru maszyna wirtualna A1 dozorcy) |![Węzły klastra Spark w usłudze HDInsight](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-spark-cluster-type-setup.png) |

Aby uzyskać więcej informacji, zobacz [domyślne rozmiary maszyn wirtualnych i konfiguracja węzła klastrów](hdinsight-component-versioning.md#default-node-configuration-and-virtual-machine-sizes-for-clusters) w "Jakie są składniki platformy Hadoop i wersji w usłudze HDInsight?"

Koszt klastrów usługi HDInsight zależy od liczby węzłów i rozmiarów maszyn wirtualnych dla węzłów. 

Różne typy klastrów ma inny węzeł typów, liczby węzłów i rozmiary węzła:
* Domyślny typ klastra usługi Hadoop: 
    * Dwa *węzły główne*  
    * Cztery *węzły danych*
* Domyślny typ klastra STORM: 
    * Dwa *węzłów Nimbus*
    * Trzy *węzły dozorcy*
    * Cztery *przełożonego węzłów* 

Jeśli właśnie próbujesz się HDInsight, firma Microsoft zaleca korzystanie z jednego węzła danych. Aby uzyskać więcej informacji na temat cen usługi HDInsight, zobacz [cennik usługi HDInsight](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

> [!NOTE]
> Limit rozmiaru klastra jest zależny od używanej subskrypcji platformy Azure. Skontaktuj się z [Azure pomocy dotyczącej rozliczeń](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) celu zwiększenia limitu.
>

Jeśli używasz portalu Azure, aby skonfigurować klaster, rozmiaru węzła jest dostępna za pośrednictwem **warstw cenowych węzłów** bloku. W portalu można również sprawdzić koszt związany z rozmiary innego węzła. 

![Rozmiary węzła HDInsight maszyn wirtualnych](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-node-sizes.png)

### <a name="virtual-machine-sizes"></a>Rozmiary maszyn wirtualnych 
Podczas wdrażania klastrów, wybierz zasoby obliczeniowe oparte na rozwiązanie, które planujesz wdrożyć. Następujące maszyny wirtualne są używane w klastrach usługi HDInsight:
* A i maszyny wirtualne z serii D1 4: [rozmiary General-purpose maszyny Wirtualnej systemu Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general)
* Seria D11 14 maszyny Wirtualnej: [rozmiary zoptymalizowanych pod kątem pamięci maszyny Wirtualnej systemu Linux](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)

Aby dowiedzieć się, jaka wartość powinna być używana do określenia rozmiaru maszyny Wirtualnej podczas tworzenia klastra przy użyciu różnych zestawów SDK lub przy użyciu programu Azure PowerShell, zobacz [rozmiarów maszyn wirtualnych na potrzeby klastrów usługi HDInsight](../cloud-services/cloud-services-sizes-specs.md#size-tables). Z tego artykułu połączony, użyj wartości w **rozmiar** kolumny tabel.

> [!IMPORTANT]
> Jeśli potrzebujesz więcej niż 32 węzłami procesu roboczego w klastrze, należy wybrać rozmiar węzła głównego z co najmniej 8 rdzeniami i 14 GB pamięci RAM.
>
>

Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](../virtual-machines/windows/sizes.md). Aby uzyskać informacje o cenach o różnych rozmiarach, zobacz [cennik usługi HDInsight](https://azure.microsoft.com/pricing/details/hdinsight).   

## <a name="custom-cluster-setup"></a>Konfiguracja klastra niestandardowych
Niestandardowe klastra konfiguracji kompilacji na szybki utworzyć ustawienia i dodaje następujące opcje:
- [Aplikacje usługi HDInsight](#hdinsight-applications)
- [Rozmiar klastra](#cluster-size)
- Ustawienia zaawansowane
  - [Akcje skryptu](#customize-clusters-using-script-action)
  - [Sieć wirtualna](#use-virtual-network)

## <a name="install-hdinsight-applications-on-clusters"></a>Instalowanie aplikacji HDInsight w klastrach

Aplikacja usługi HDInsight to aplikacja, którą użytkownicy mogą zainstalować w klastrze usługi HDInsight opartym na systemie Linux. Dostarczony przez firmę Microsoft, stron trzecich lub który opracowanie samodzielnie, możesz użyć aplikacji. Aby uzyskać więcej informacji, zobacz [instalacji aplikacji innych firm Hadoop w usłudze Azure HDInsight](hdinsight-apps-install-applications.md).

Większość aplikacji usługi HDInsight są zainstalowane na węzeł krawędzi puste.  Węzeł krawędzi pusty jest maszyny wirtualnej systemu Linux przy użyciu tego samego klienta narzędzi zainstalowane i skonfigurowane tak jak węzła głównego. Za pomocą węzła krawędzi dostęp do klastra, testowanie aplikacji klienckich i hosting aplikacji klienta. Aby uzyskać więcej informacji, zobacz [użycia węzłów pusty krawędzi w usłudze HDInsight](hdinsight-apps-use-edge-node.md).

## <a name="advanced-settings-script-actions"></a>Ustawienia zaawansowane: działania skryptu

Można zainstalować dodatkowe składniki lub dostosowanie konfiguracji klastra za pomocą skryptów tworzenia. Te skrypty są wywoływane za pośrednictwem **akcji skryptu**, która jest opcja konfiguracji, która może być stosowane w portalu Azure, poleceń cmdlet programu Windows PowerShell dla usługi HDInsight lub zestawu .NET SDK usługi HDInsight. Aby uzyskać więcej informacji, zobacz [dostosować klastra usługi HDInsight przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

Niektóre składniki natywnego języka Java, takich jak Mahout i usuwania kaskadowego, można uruchomić w klastrze jako pliki archiwum Java (JAR). Te pliki JAR można dystrybuować do magazynu Azure i przesłane do klastrów usługi HDInsight mechanizmy przesyłania zadania usługi Hadoop. Aby uzyskać więcej informacji, zobacz [Hadoop przesyłania zadania programowo](hadoop/submit-apache-hadoop-jobs-programmatically.md).

> [!NOTE]
> Jeśli masz problemy z wdrożeniem pliki JAR do klastrów usługi HDInsight, lub wywoływania pliki JAR w klastrach HDInsight, skontaktuj się z [Microsoft Support](https://azure.microsoft.com/support/options/).
>
> Kaskadowe nie jest obsługiwany przez usługi HDInsight i nie jest uprawniony do firmy Microsoft Support. Aby uzyskać listę obsługiwanych składników, zobacz [nowości w wersjach klastra dostarczanych z usługą HDInsight](hdinsight-component-versioning.md).
>
>

Czasami użytkownik chce skonfigurować następujące pliki konfiguracji w trakcie procesu tworzenia:

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

Aby uzyskać więcej informacji, zobacz [HDInsight dostosować klastry za pomocą początkowego](hdinsight-hadoop-customize-cluster-bootstrap.md).

## <a name="advanced-settings-extend-clusters-with-a-virtual-network"></a>Ustawienia zaawansowane: rozszerzenie klastrów z sieci wirtualnej
Jeśli rozwiązanie wymaga technologie, które są rozkładane między wieloma typy klastrów usługi HDInsight, [sieci wirtualnej platformy Azure](https://docs.microsoft.com/azure/virtual-network) można połączyć typy klastrów wymagane. Ta konfiguracja pozwala klastrów, a każdy kod, którą wdrożyć je, aby bezpośrednio komunikować się ze sobą.

Aby uzyskać więcej informacji na temat używania sieci wirtualnej platformy Azure z usługą HDInsight, zobacz [rozszerzenie usługi HDInsight za pomocą sieci wirtualnych platformy Azure](hdinsight-extend-hadoop-virtual-network.md).

Na przykład za pomocą dwóch typów klastra w ramach sieci wirtualnej platformy Azure, zobacz [Użyj Spark strukturalnych przesyłania strumieniowego z Kafka](hdinsight-apache-kafka-spark-structured-streaming.md). Aby uzyskać więcej informacji o korzystaniu z usługi HDInsight z sieci wirtualnej, łącznie z określonymi wymaganiami konfiguracji dla sieci wirtualnej, zobacz [możliwości rozszerzania HDInsight przy użyciu usługi Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).

## <a name="troubleshoot-access-control-issues"></a>Rozwiązywanie problemów z kontrolą dostępu

W razie problemów podczas tworzenia klastrów usługi HDInsight zapoznaj się z [wymaganiami dotyczącymi kontroli dostępu](hdinsight-administer-use-portal-linux.md#create-clusters).

## <a name="next-steps"></a>Kolejne kroki

- [Co to są HDInsight, ekosystemu Hadoop i klastrów Hadoop?](hadoop/apache-hadoop-introduction.md)
- [Rozpoczęcie korzystania z usługi Hadoop w usłudze HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
- [Praca w Hadoop w usłudze HDInsight z Komputerami z systemem Windows](hdinsight-hadoop-windows-tools.md)
