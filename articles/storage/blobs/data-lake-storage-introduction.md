---
title: Usługa Azure Data Lake Storage Gen2 — wprowadzenie
description: Zawiera omówienie usługi Azure Data Lake Storage Gen2
services: storage
author: jamesbak
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 8777a7504c48b22d0e670dd9f0d28016ac8918db
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2019
ms.locfileid: "60009467"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Wprowadzenie do usługi Azure Data Lake Storage Gen2

Usługi Azure Data Lake magazynu Gen2 to zbiór funkcji przeznaczonych do analizy danych big data, w oparciu o [usługi Azure Blob storage](storage-blobs-introduction.md). Data Lake Storage Gen2 jest wynikiem zbieżności możliwości naszych dwa istniejące usługi storage, Azure Blob storage i Azure Data Lake Storage Gen1. Funkcje z [usługi Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), takie jak semantyki systemu plików, katalogu i zabezpieczenia na poziomie pliku i skalowania, które są połączone z niskie koszty i warstwowego magazynu, wysokiej dostępności/zastosowanie funkcji z [Usługi azure Blob storage](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Przeznaczona dla przedsiębiorstw analizy danych big data

Data Lake Storage Gen2 sprawia, że usługi Azure Storage jako podstawa tworzenia jeziora danych przedsiębiorstwa na platformie Azure. Zaprojektowany od początku do obsługi wielu petabajtów informacji podczas zatwierdzeniu setki gigabity przepływności, Data Lake Storage Gen2 umożliwia łatwe zarządzanie ogromnych ilości danych.

Podstawową część Data Lake Storage Gen2, to dodanie [hierarchicznej przestrzeni nazw](data-lake-storage-namespace.md) do magazynu obiektów Blob. Hierarchicznej przestrzeni nazw organizuje pliki/obiekty w hierarchii katalogów dla dostępu do danych wydajne. Typowych konwencji nazewnictwa magazynu obiektów używa ukośników w nazwie, aby mógł naśladować strukturę katalogów hierarchicznych. Ta struktura staje się rzeczywistych za pomocą Data Lake Storage Gen2. Operacje, takie jak zmiana nazwy lub usunięcie katalogu stają się operacje dotyczące metadanych atomic pojedynczego katalogu zamiast wyliczanie i przetwarzanie wszystkich obiektów, które mają prefiks nazwy katalogu.

W przeszłości funkcje analizy chmurowej — było naruszenia bezpieczeństwa w zakresie wydajności, zarządzania i zabezpieczeń. Data Lake Storage Gen2 dotyczy każdego z tych aspektów w następujący sposób:

-   **Wydajność** jest zoptymalizowany, ponieważ nie ma potrzeby kopiowania lub przekształcić dane jako warunek wstępny do analizy. Hierarchicznej przestrzeni nazw znacznie poprawia wydajność operacji zarządzania katalogu, co zwiększa ogólną wydajność zadania.

-   **Zarządzanie** jest łatwiejsze, ponieważ można organizować i manipulowania plików, katalogów i podkatalogów.

-   **Zabezpieczenia** obowiązuje, ponieważ możesz zdefiniować uprawnienia POSIX w katalogach lub poszczególne pliki.

-   **Przystępność cenową** jest możliwe, ponieważ Data Lake Storage Gen2 bazuje na usłudze niskie koszty [usługi Azure Blob storage](storage-blobs-introduction.md). Dodatkowe funkcje dodatkowo obniżyć całkowity koszt posiadania, uruchamiania analizy danych big data na platformie Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Najważniejsze funkcje Data Lake Storage Gen2

-   **Dostęp zgodny z usługi Hadoop**: Data Lake Storage Gen2 pozwala na zarządzanie i dostęp do danych, tak samo, jak za pomocą [pliku System (HDFS, Hadoop Distributed)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Nowy [sterownika ABFS](data-lake-storage-abfs-driver.md) jest dostępna w ramach wszystkich środowisk technologii Apache Hadoop, w tym [Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index)*,* [usługi Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index), i [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) dostępu do danych przechowywanych w Data Lake Storage Gen2.

-   **Nadzbiór uprawnień POSIX**: Model zabezpieczeń Data Lake Gen2 obsługuje listy ACL i POSIX uprawnienia oraz niektórych dodatkowy poziom szczegółowości specyficzne dla Data Lake Storage Gen2. Ustawienia można skonfigurować za pomocą Eksploratora usługi Storage lub środowisk, takich jak Hive, jak i platformy Spark.

-   **Niskie koszty**: Data Lake Storage Gen2 oferuje ekonomiczne pojemności i transakcji. Jako przejścia danych za pośrednictwem jego pełny cykl życia, stawki rozliczeniowe zmienia zachowanie kosztów do minimum, za pomocą wbudowanych funkcji takich jak [cykl życia magazynu obiektów Blob platformy Azure](storage-lifecycle-management-concepts.md).

-   **Sterownik zoptymalizowane**: Sterownik ABFS [specjalnie zoptymalizowane pod kątem](data-lake-storage-abfs-driver.md) do analizy danych big data. Odpowiednie interfejsy API REST są udostępniane za pośrednictwem punktu końcowego `dfs.core.windows.net`.

### <a name="scalability"></a>Skalowalność

Usługa Azure Storage jest skalowalna, zgodnie z projektem, czy możesz uzyskać dostęp za pośrednictwem interfejsu Data Lake Storage Gen2 lub usługi Blob storage. Jest w stanie przechowywać i obsługiwać *wiele eksabajtów danych*. Ta ilość miejsca jest dostępna z przepływnością mierzoną w gigabitach na sekundę (GB/s) na dużą liczbą operacji wejścia/wyjścia na sekundę (IOPS). Poza trwałości po prostu przetwarzania jest wykonywana na opóźnienia w pobliżu stałą na żądanie, są mierzone w poziomie usługi, konta i plików.

### <a name="cost-effectiveness"></a>Ekonomiczność

Jedną z wielu zalet tworzenia Data Lake Storage Gen2 na podstawie usługi Azure Blob storage to ekonomiczne pojemności i transakcji. W przeciwieństwie do innych usług magazynu w chmurze dane przechowywane w Data Lake Storage Gen2 nie jest wymagany do przeniesienia lub przekształcane przed przeprowadzeniem analizy. Aby uzyskać więcej informacji o cenach, zobacz [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage).

Ponadto funkcje, takie jak [hierarchicznej przestrzeni nazw](data-lake-storage-namespace.md) znacznie zwiększyć ogólną wydajność wielu zadań usługi analytics. Ta poprawa wydajności oznacza, że wymagają mniej mocy obliczeniowej do przetwarzania tej samej ilości danych, co spowodowało niższy całkowity koszt posiadania (TCO) dla zadania analiza end-to-end.

### <a name="one-service-multiple-concepts"></a>Jedna usługa, wiele pojęć

Data Lake Storage Gen2 zapewnia dodatkową funkcję analizy danych big data, korzystających z usługi Azure Blob storage. Dostępnych jest wiele korzyści dzięki wykorzystaniu istniejących składników platformy obiektów blob, aby tworzyć i obsługiwać jeziora danych do analizy, to prowadzić do wielu koncepcji zawierająca tego samego, udostępnionych.

Poniżej przedstawiono równoważne jednostek, zgodnie z opisem w różnych pojęcia. Chyba że określono inaczej, te jednostki są bezpośrednio równoznaczny:

| Pojęcie                                | Najwyższego poziomu organizacji | Obniż poziom organizacji                                            | Kontener danych |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Obiekty BLOB — magazyn obiektów ogólnego przeznaczenia | Kontener              | Katalog wirtualny (zestaw SDK — nie zapewniają tylko manipulowania atomic) | Obiekt blob           |
| Azure Data Lake Store Gen2 — Analytics Storage          | System plików             | Katalog                                                           | Plik           |

## <a name="supported-open-source-platforms"></a>Obsługiwane platformy typu open source

Wiele platform typu open source obsługi Gen2 magazynu programu Data Lake. Tych platform są wyświetlane w poniższej tabeli.

> [!NOTE]
> Obsługiwane są tylko wersje, które pojawiają się w tej tabeli.

| Platforma |  Obsługiwane wersje | Więcej informacji |
| --- | --- | --- |
| [HDInsight](https://azure.microsoft.com/services/hdinsight/) | 3.6 + | [Jakie są składniki platformy Apache Hadoop i wersje dostępne z HDInsight?](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning?toc=%2Fen-us%2Fazure%2Fhdinsight%2Fstorm%2FTOC.json&bc=%2Fen-us%2Fazure%2Fbread%2Ftoc.json)
| [Hadoop](https://hadoop.apache.org/) | 3.2+ | [Apache Hadoop zwalnia archiwum](https://hadoop.apache.org/release.html) |
| [Platformy Cloudera](https://www.cloudera.com/) | 6.1 + | [Informacje o wersji 6.x platformy Cloudera Enterprise](https://www.cloudera.com/documentation/enterprise/6/release-notes/topics/rg_cdh_6_release_notes.html) |
| [Azure Databricks](https://azure.microsoft.com/services/databricks/) | 5.1+ | [Wersje środowiska uruchomieniowego usługi Databricks](https://docs.databricks.com/release-notes/runtime/databricks-runtime-ver.html) |
|[Hortonworks](https://hortonworks.com/)| 3.1.x++ | [Konfigurowanie dostępu do danych w chmurze](https://docs.hortonworks.com/HDPDocuments/Cloudbreak/Cloudbreak-2.9.0/cloud-data-access/content/cb_configuring-access-to-adls2.html) |

## <a name="next-steps"></a>Kolejne kroki

Następujące artykuły opisano niektóre główne pojęcia Data Lake Storage Gen2 i szczegółów do przechowywania, uzyskać dostęp, zarządzać i uzyskiwać wgląd w dane:

-   [Hierarchicznej przestrzeni nazw](data-lake-storage-namespace.md)
-   [Tworzenie konta magazynu](data-lake-storage-quickstart-create-account.md)
-   [Użyj konta Data Lake Storage Gen2 w usłudze Azure Databricks](data-lake-storage-quickstart-create-databricks-account.md)
