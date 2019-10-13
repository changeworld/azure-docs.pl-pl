---
title: Azure Data Lake Storage Gen2 wprowadzenie
description: Zawiera omówienie Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 10/11/2019
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: d843e288297db656cca6e2a07f2e1f3322ebfa89
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299656"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Wprowadzenie do Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 to zestaw funkcji przeznaczonych do analizy danych Big Data, opartych na [usłudze Azure Blob Storage](storage-blobs-introduction.md). Data Lake Storage Gen2 jest wynikiem zbieżności możliwości naszych dwóch istniejących usług magazynu, magazynu obiektów blob platformy Azure i Azure Data Lake Storage Gen1. Funkcje z [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), takie jak semantyka systemu plików, katalog i zabezpieczenia na poziomie plików i skalowanie, są połączone z tanim magazynem warstwowym, wysoką dostępnością/odzyskiwaniem po awarii z [usługi Azure Blob Storage](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Zaprojektowana na potrzeby analizy danych Big Data w przedsiębiorstwie

Data Lake Storage Gen2 sprawia, że usługa Azure Storage jest podstawą do tworzenia jezior danych przedsiębiorstwa na platformie Azure. Zaprojektowana od początku do obsługi wielu petabajtów informacji przy zachowaniu setek gigabitowej przepływności, Data Lake Storage Gen2 umożliwia łatwe zarządzanie ogromnymi ilościami danych.

Podstawową częścią Data Lake Storage Gen2 jest dodanie [hierarchicznej przestrzeni nazw](data-lake-storage-namespace.md) do magazynu obiektów BLOB. Hierarchiczna przestrzeń nazw organizuje obiekty/pliki w hierarchię katalogów w celu zapewnienia wydajnego dostępu do danych. Wspólna konwencja nazewnictwa magazynu obiektów używa ukośników w nazwie do naśladowania hierarchicznej struktury katalogów. Ta struktura zacznie być prawdziwa z Data Lake Storage Gen2. Operacje, takie jak zmiana nazwy lub usuwanie katalogu, stają się jednocyfrowymi operacjami metadanych w katalogu zamiast wyliczania i przetwarzania wszystkich obiektów, które współużytkują prefiks nazwy katalogu.

W przeszłości Analiza oparta na chmurze musiała być naruszona w obszarach wydajności, zarządzania i zabezpieczeń. Data Lake Storage Gen2 dotyczy każdego z tych aspektów w następujący sposób:

-   **Wydajność** jest zoptymalizowana, ponieważ nie trzeba kopiować ani przekształcać danych jako wymaganie wstępne do analizy. Hierarchiczna przestrzeń nazw znacznie zwiększa wydajność operacji zarządzania katalogami, co zwiększa ogólną wydajność zadań.

-   **Zarządzanie** jest łatwiejsze, ponieważ pliki można organizować i manipulować nimi za poorednictwem katalogów i podkatalogów.

-   **Zabezpieczenia** są wymuszane, ponieważ można DEFINIOWAĆ uprawnienia POSIX do katalogów lub pojedynczych plików.

-   **Jest to** możliwe, ponieważ Data Lake Storage Gen2 jest tworzona w oparciu o niski koszt [magazynu obiektów blob platformy Azure](storage-blobs-introduction.md). Dodatkowe funkcje dodatkowo obniżają łączny koszt posiadania na potrzeby analizy danych Big Data na platformie Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Najważniejsze funkcje Data Lake Storage Gen2

-   **Dostęp zgodny**z usługą Hadoop: Data Lake Storage Gen2 umożliwia zarządzanie danymi i uzyskiwanie do nich dostępu tak samo jak w przypadku [rozproszony system plików Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Nowy [Sterownik ABFS](data-lake-storage-abfs-driver.md) jest dostępny w ramach wszystkich środowisk Apache Hadoop, w [tym usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index) *,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index)i [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) w celu uzyskania dostępu do danych przechowywanych w Data Lake Storage Gen2.

-   **Nadzbiór uprawnień systemu POSIX**: model zabezpieczeń dla Data Lake Gen2 obsługuje uprawnienia ACL i POSIX oraz niezbędny stopień szczegółowości Data Lake Storage Gen2. Ustawienia można skonfigurować za poorednictwem Eksplorator usługi Storage lub platform, takich jak Hive i Spark.

-   **Koszt ekonomiczny**: Data Lake Storage Gen2 oferuje niską wydajność magazynu i transakcji. W miarę jak dane są przenoszone przez pełny cykl życia, opłaty są naliczane przy użyciu wbudowanych funkcji, takich jak [cykl życia usługi Azure Blob Storage](storage-lifecycle-management-concepts.md).

-   **Zoptymalizowany sterownik**: Sterownik ABFS jest [zoptymalizowany pod](data-lake-storage-abfs-driver.md) kątem analizy danych Big Data. Odpowiednie interfejsy API REST są nadane przez punkt końcowy `dfs.core.windows.net`.

### <a name="scalability"></a>Skalowalność

Usługa Azure Storage jest skalowalna poprzez projektowanie, czy dostęp odbywa się za pośrednictwem interfejsów Data Lake Storage Gen2 lub BLOB Storage. Jest w stanie przechowywać i obsługiwać *wiele eksabajtowej danych*. Ta ilość miejsca w magazynie jest dostępna z przepływem wydajności mierzonym w gigabitach na sekundę (GB/s) na wysokim poziomie operacji wejścia/wyjścia na sekundę (IOPS). Oprócz tylko trwałości, przetwarzanie jest wykonywane przy opóźnieniu bliskich żądań na żądanie, które są mierzone na poziomach usługi, konta i pliku.

### <a name="cost-effectiveness"></a>Efektywność kosztów

Jedną z wielu korzyści związanych z tworzeniem Data Lake Storage Gen2 w usłudze Azure Blob Storage jest niski koszt pojemności magazynu i transakcji. W przeciwieństwie do innych usług magazynu w chmurze, dane przechowywane w Data Lake Storage Gen2 nie są wymagane do przeniesienia ani przekształcenia przed wykonaniem analizy. Aby uzyskać więcej informacji o cenach, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage).

Ponadto funkcje takie jak [hierarchiczna przestrzeń nazw](data-lake-storage-namespace.md) znacząco zwiększają ogólną wydajność wielu zadań analitycznych. To ulepszenie wydajności oznacza, że wymaga mniejszej mocy obliczeniowej, aby przetwarzać tę samą ilość danych, co skutkuje niższym całkowitym kosztem posiadania (TCO) dla kompleksowego zadania analizy.

### <a name="one-service-multiple-concepts"></a>Jedna usługa, wiele koncepcji

Data Lake Storage Gen2 to dodatkowa funkcja analizy danych Big Data, oparta na usłudze Azure Blob Storage. Istnieje wiele korzyści związanych z wykorzystywaniem istniejących składników platformy obiektów BLOB do tworzenia i obsługiwania jezior danych na potrzeby analiz. prowadzi to do wielu koncepcji opisujących te same, udostępnione elementy.

Poniżej przedstawiono jednostki równoważne, zgodnie z opisem różnych koncepcji. O ile nie określono inaczej, te jednostki są bezpośrednio synonimem:

| Pojęcie                                | Organizacja najwyższego poziomu | Organizacja niższego poziomu                                            | Kontener danych |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Obiekty blob — magazyn obiektów ogólnego przeznaczenia | Kontener              | Katalog wirtualny (tylko SDK — nie zapewnia manipulowania niepodzielnego) | Obiekt blob           |
| Azure Data Lake Storage Gen2 — magazyn analizy          | Kontener            | Katalog                                                           | Plik           |

## <a name="supported-open-source-platforms"></a>Obsługiwane platformy Open Source

Kilka platform Open Source obsługuje Data Lake Storage Gen2. Te platformy są wyświetlane w poniższej tabeli.

> [!NOTE]
> Obsługiwane są tylko wersje, które pojawiają się w tej tabeli.

| Platforma |  Obsługiwane wersje | Więcej informacji |
| --- | --- | --- |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | 3.6 + | [Jakie składniki Apache Hadoop i wersje są dostępne w usłudze HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning?toc=%2Fen-us%2Fazure%2Fhdinsight%2Fstorm%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
| [Hadoop](https://hadoop.apache.org/) | 3.2 + | [Archiwum wydań Apache Hadoop](https://hadoop.apache.org/release.html) |
| [Cloudera](https://www.cloudera.com/) | 6.1 + | [Cloudera Enterprise 6. x — informacje o wersji](https://www.cloudera.com/documentation/enterprise/6/release-notes/topics/rg_cdh_6_release_notes.html) |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) | 5.1 + | [Wersje Databricks Runtime](https://docs.databricks.com/release-notes/runtime/databricks-runtime-ver.html) |
|[Hortonworks](https://hortonworks.com/)| 3.1. x + + | [Konfigurowanie dostępu do danych w chmurze](https://docs.hortonworks.com/HDPDocuments/Cloudbreak/Cloudbreak-2.9.0/cloud-data-access/content/cb_configuring-access-to-adls2.html) |

## <a name="supported-azure-services"></a>Obsługiwane usługi platformy Azure

Data Lake Storage Gen2 obsługuje kilka usług platformy Azure, których można użyć do pozyskiwania danych, wykonywania analiz i tworzenia reprezentacji wizualnych. Aby zapoznać się z listą obsługiwanych usług platformy Azure, zobacz [integrowanie Azure Data Lake Storage z usługami platformy Azure](data-lake-store-integrate-with-azure-services.md).

## <a name="next-steps"></a>Następne kroki

W poniższych artykułach opisano niektóre główne koncepcje Data Lake Storage Gen2 i szczegółowo, jak przechowywać dane, uzyskiwać do nich dostęp, zarządzać nimi i uzyskiwać do nich szczegółowe informacje:

- [Hierarchiczna przestrzeń nazw](data-lake-storage-namespace.md)
- [Tworzenie konta magazynu](data-lake-storage-quickstart-create-account.md)
- [Dostęp z wieloprotokołem do Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)
- [Integruj Azure Data Lake Storage z usługami platformy Azure](data-lake-store-integrate-with-azure-services.md);
