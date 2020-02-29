---
title: Azure Data Lake Storage Gen2 wprowadzenie
description: Zawiera omówienie Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 75bd27f0945c66b9757055c0777b43a050ba67d7
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/28/2020
ms.locfileid: "77920998"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Wprowadzenie do Azure Data Lake Storage Gen2

Azure Data Lake Storage Gen2 to zestaw funkcji przeznaczonych do analizy danych Big Data, opartych na [usłudze Azure Blob Storage](storage-blobs-introduction.md). Data Lake Storage Gen2 jest wynikiem zbieżności możliwości naszych dwa istniejące usługi storage, Azure Blob storage i Azure Data Lake Storage Gen1. Funkcje z [Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), takie jak semantyka systemu plików, katalog i zabezpieczenia na poziomie plików i skalowanie, są połączone z tanim magazynem warstwowym, wysoką dostępnością/odzyskiwaniem po awarii z [usługi Azure Blob Storage](storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Przeznaczona dla przedsiębiorstw analizy danych big data

Data Lake Storage Gen2 sprawia, że usługi Azure Storage jako podstawa tworzenia jeziora danych przedsiębiorstwa na platformie Azure. Zaprojektowany od początku do obsługi wielu petabajtów informacji podczas zatwierdzeniu setki gigabity przepływności, Data Lake Storage Gen2 umożliwia łatwe zarządzanie ogromnych ilości danych.

Podstawową częścią Data Lake Storage Gen2 jest dodanie [hierarchicznej przestrzeni nazw](data-lake-storage-namespace.md) do magazynu obiektów BLOB. Hierarchicznej przestrzeni nazw organizuje pliki/obiekty w hierarchii katalogów dla dostępu do danych wydajne. Typowych konwencji nazewnictwa magazynu obiektów używa ukośników w nazwie, aby mógł naśladować strukturę katalogów hierarchicznych. Ta struktura staje się rzeczywistych za pomocą Data Lake Storage Gen2. Operacje, takie jak zmiana nazwy lub usunięcie katalogu stają się operacje dotyczące metadanych atomic pojedynczego katalogu zamiast wyliczanie i przetwarzanie wszystkich obiektów, które mają prefiks nazwy katalogu.

W przeszłości funkcje analizy chmurowej — było naruszenia bezpieczeństwa w zakresie wydajności, zarządzania i zabezpieczeń. Data Lake Storage Gen2 dotyczy każdego z tych aspektów w następujący sposób:

-   **Wydajność** jest zoptymalizowana, ponieważ nie trzeba kopiować ani przekształcać danych jako wymaganie wstępne do analizy. Hierarchicznej przestrzeni nazw znacznie poprawia wydajność operacji zarządzania katalogu, co zwiększa ogólną wydajność zadania.

-   **Zarządzanie** jest łatwiejsze, ponieważ pliki można organizować i manipulować nimi za poorednictwem katalogów i podkatalogów.

-   **Zabezpieczenia** są wymuszane, ponieważ można DEFINIOWAĆ uprawnienia POSIX do katalogów lub pojedynczych plików.

-   **Jest to** możliwe, ponieważ Data Lake Storage Gen2 jest tworzona w oparciu o niski koszt [magazynu obiektów blob platformy Azure](storage-blobs-introduction.md). Dodatkowe funkcje dodatkowo obniżyć całkowity koszt posiadania, uruchamiania analizy danych big data na platformie Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Najważniejsze funkcje Data Lake Storage Gen2

-   **Dostęp zgodny**z usługą Hadoop: Data Lake Storage Gen2 umożliwia zarządzanie danymi i uzyskiwanie do nich dostępu tak samo jak w przypadku [rozproszony system plików Hadoop (HDFS)](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Nowy [Sterownik ABFS](data-lake-storage-abfs-driver.md) jest dostępny w ramach wszystkich środowisk Apache Hadoop, w [tym usługi Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/index) *,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index)i [SQL Data Warehouse](https://docs.microsoft.com/azure/sql-data-warehouse/) w celu uzyskania dostępu do danych przechowywanych w Data Lake Storage Gen2.

-   **Nadzbiór uprawnień systemu POSIX**: model zabezpieczeń dla Data Lake Gen2 obsługuje uprawnienia ACL i POSIX oraz niezbędny stopień szczegółowości Data Lake Storage Gen2. Ustawienia można skonfigurować za pomocą Eksploratora usługi Storage lub środowisk, takich jak Hive, jak i platformy Spark.

-   **Koszt ekonomiczny**: Data Lake Storage Gen2 oferuje niską wydajność magazynu i transakcji. W miarę jak dane są przenoszone przez pełny cykl życia, opłaty są naliczane przy użyciu wbudowanych funkcji, takich jak [cykl życia usługi Azure Blob Storage](storage-lifecycle-management-concepts.md).

-   **Zoptymalizowany sterownik**: Sterownik ABFS jest [zoptymalizowany pod](data-lake-storage-abfs-driver.md) kątem analizy danych Big Data. Odpowiednie interfejsy API REST są przyłączone do `dfs.core.windows.net`punktu końcowego.

### <a name="scalability"></a>Skalowalność

Usługa Azure Storage jest skalowalna, zgodnie z projektem, czy możesz uzyskać dostęp za pośrednictwem interfejsu Data Lake Storage Gen2 lub usługi Blob storage. Jest w stanie przechowywać i obsługiwać *wiele eksabajtowej danych*. Ta ilość miejsca jest dostępna z przepływnością mierzoną w gigabitach na sekundę (GB/s) na dużą liczbą operacji wejścia/wyjścia na sekundę (IOPS). Poza trwałości po prostu przetwarzania jest wykonywana na opóźnienia w pobliżu stałą na żądanie, są mierzone w poziomie usługi, konta i plików.

### <a name="cost-effectiveness"></a>Ekonomiczność

Jedną z wielu zalet tworzenia Data Lake Storage Gen2 na podstawie usługi Azure Blob storage to ekonomiczne pojemności i transakcji. W przeciwieństwie do innych usług magazynu w chmurze dane przechowywane w Data Lake Storage Gen2 nie jest wymagany do przeniesienia lub przekształcane przed przeprowadzeniem analizy. Aby uzyskać więcej informacji o cenach, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage).

Ponadto funkcje takie jak [hierarchiczna przestrzeń nazw](data-lake-storage-namespace.md) znacząco zwiększają ogólną wydajność wielu zadań analitycznych. Ta poprawa wydajności oznacza, że wymagają mniej mocy obliczeniowej do przetwarzania tej samej ilości danych, co spowodowało niższy całkowity koszt posiadania (TCO) dla zadania analiza end-to-end.

### <a name="one-service-multiple-concepts"></a>Jedna usługa, wiele pojęć

Data Lake Storage Gen2 zapewnia dodatkową funkcję analizy danych big data, korzystających z usługi Azure Blob storage. Dostępnych jest wiele korzyści dzięki wykorzystaniu istniejących składników platformy obiektów blob, aby tworzyć i obsługiwać jeziora danych do analizy, to prowadzić do wielu koncepcji zawierająca tego samego, udostępnionych.

Poniżej przedstawiono równoważne jednostek, zgodnie z opisem w różnych pojęcia. Chyba że określono inaczej, te jednostki są bezpośrednio równoznaczny:

| Pojęcie                                | Najwyższego poziomu organizacji | Obniż poziom organizacji                                            | Kontener danych |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Obiekty BLOB — magazyn obiektów ogólnego przeznaczenia | Kontener              | Katalog wirtualny (zestaw SDK — nie zapewniają tylko manipulowania atomic) | Obiekt blob           |
| Azure Data Lake Storage Gen2 — magazyn analizy          | Kontener            | Katalog                                                           | Plik           |

## <a name="supported-blob-storage-features"></a>Obsługiwane funkcje magazynu obiektów BLOB

Funkcje magazynu obiektów blob, takie jak [rejestrowanie diagnostyczne](../common/storage-analytics-logging.md), [warstwy dostępu](storage-blob-storage-tiers.md)i [zasady zarządzania cyklem BLOB Storage](storage-lifecycle-management-concepts.md) , działają teraz z kontami, które mają hierarchiczną przestrzeń nazw. W związku z tym można włączyć hierarchiczne przestrzenie nazw na kontach magazynu obiektów Blob bez utraty dostępu do tych funkcji. 

Aby uzyskać listę obsługiwanych funkcji usługi BLOB Storage, zobacz [BLOB Storage funkcje dostępne w Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Obsługiwane integracje usług platformy Azure

Data Lake Storage Gen2 obsługuje kilka usług platformy Azure, których można użyć do pozyskiwania danych, wykonywania analiz i tworzenia reprezentacji wizualnych. Aby uzyskać listę obsługiwanych usług platformy Azure, zobacz [usługi platformy Azure, które obsługują Azure Data Lake Storage Gen2](data-lake-storage-supported-azure-services.md).

## <a name="supported-open-source-platforms"></a>Obsługiwane platformy typu open source

Wiele platform typu open source obsługi Gen2 magazynu programu Data Lake. Aby uzyskać pełną listę, zobacz [platformy typu "open source" obsługujące Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Zobacz też

- [Znane problemy z Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Dostęp z wieloprotokołem do Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)


