---
title: Wprowadzenie do usługi Azure Data Lake Storage Gen2
description: Zawiera omówienie usługi Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.topic: overview
ms.date: 02/25/2020
ms.author: normesta
ms.reviewer: jamesbak
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: 2f920e29fafdc55478e0e2c16d683bd1c3bc81d8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78942930"
---
# <a name="introduction-to-azure-data-lake-storage-gen2"></a>Wprowadzenie do usługi Azure Data Lake Storage Gen2

Usługa Azure Data Lake Storage Gen2 to zestaw funkcji dedykowanych do analizy dużych zbiorów danych, opartej na [magazynie obiektów Blob platformy Azure.](storage-blobs-introduction.md) Data Lake Storage Gen2 jest wynikiem zbieżności możliwości naszych dwóch istniejących usług magazynu, usługi Azure Blob storage i Usługi Azure Data Lake Storage Gen1. Funkcje [usługi Azure Data Lake Storage Gen1](https://docs.microsoft.com/azure/data-lake-store/index), takie jak semantyka systemu plików, katalog i zabezpieczenia i skalowanie na poziomie plików, są połączone z tanim, warstwowym magazynem, wysoką dostępnością/funkcjami odzyskiwania po awarii z magazynu obiektów [Blob platformy Azure.](storage-blobs-introduction.md)

## <a name="designed-for-enterprise-big-data-analytics"></a>Zaprojektowane z myślą o analizie dużych zbiorów danych w przedsiębiorstwie

Usługa Data Lake Storage Gen2 sprawia, że usługa Azure Storage stanowi podstawę do tworzenia jezior danych przedsiębiorstwa na platformie Azure. Zaprojektowany od początku do obsługi wielu petabajtów informacji przy jednoczesnym utrzymaniu setek gigabitów przepustowości, Data Lake Storage Gen2 pozwala łatwo zarządzać ogromnymi ilościami danych.

Podstawową częścią usługi Data Lake Storage Gen2 jest dodanie [hierarchicznego obszaru nazw](data-lake-storage-namespace.md) do magazynu obiektów Blob. Hierarchiczna przestrzeń nazw organizuje obiekty/pliki w hierarchię katalogów w celu uzyskania efektywnego dostępu do danych. Konwencja nazewnictwa wspólnego magazynu obiektów używa ukośników w nazwie, aby naśladować hierarchiczną strukturę katalogów. Ta struktura staje się realna dzięki data lake storage gen2. Operacje, takie jak zmiana nazwy lub usunięcie katalogu stają się pojedynczymi operacjami metadanych niepodzielnych w katalogu, a nie wyliczaniem i przetwarzaniem wszystkich obiektów, które współużytkują prefiks nazwy katalogu.

Data Lake Storage Gen2 opiera się na pamięci masowej obiektów Blob i zwiększa wydajność, zarządzanie i bezpieczeństwo w następujący sposób:

-   **Wydajność** jest zoptymalizowana, ponieważ nie trzeba kopiować ani przekształcać danych jako warunek wstępny analizy. W porównaniu do płaskiej przestrzeni nazw w magazynie obiektów Blob hierarchiczna przestrzeń nazw znacznie poprawia wydajność operacji zarządzania katalogami, co poprawia ogólną wydajność zadania.

-   **Zarządzanie** jest łatwiejsze, ponieważ można organizować pliki i manipulować nimi za pośrednictwem katalogów i podkatalogów.

-   **Zabezpieczenia** są wykonalne, ponieważ można zdefiniować uprawnienia POSIX do katalogów lub pojedynczych plików.

Ponadto data lake storage gen2 jest bardzo opłacalne, ponieważ jest zbudowany na szczycie tanich [magazynu obiektów Blob platformy Azure.](storage-blobs-introduction.md) Dodatkowe funkcje dodatkowo obniżają całkowity koszt posiadania do uruchamiania analizy dużych zbiorów danych na platformie Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Najważniejsze cechy Data Lake Storage Gen2

-   **Dostęp zgodny z Hadoop:** Data Lake Storage Gen2 pozwala na zarządzanie i dostęp do danych tak samo, jak w przypadku [rozproszonego systemu plików Hadoop (HDFS).](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html) Nowy [sterownik ABFS](data-lake-storage-abfs-driver.md) jest dostępny we wszystkich środowiskach Apache Hadoop, w tym [Azure HDInsight,](https://docs.microsoft.com/azure/hdinsight/index)*,* [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/index)i [SQL Data Warehouse,](https://docs.microsoft.com/azure/sql-data-warehouse/) aby uzyskać dostęp do danych przechowywanych w usłudze Data Lake Storage Gen2.

-   **Nadzstaw uprawnień POSIX:** Model zabezpieczeń dla usługi Data Lake Gen2 obsługuje uprawnienia ACL i POSIX wraz z pewnymi dodatkowymi szczegółowościami specyficznymi dla usługi Data Lake Storage Gen2. Ustawienia mogą być konfigurowane za pośrednictwem Eksploratora magazynu lub za pośrednictwem struktur, takich jak Hive i Spark.

-   **Opłacalność:** Data Lake Storage Gen2 oferuje tanią pojemność pamięci masowej i transakcje. W miarę przechodzenia danych przez cały cykl życia stawki opłat zmieniają się, utrzymując koszty do minimum za pomocą wbudowanych funkcji, takich jak [cykl życia magazynu obiektów blob platformy Azure.](storage-lifecycle-management-concepts.md)

-   **Zoptymalizowany sterownik:** Sterownik ABFS jest [zoptymalizowany specjalnie](data-lake-storage-abfs-driver.md) do analizy dużych zbiorów danych. Odpowiednie interfejsy API REST są rzuty `dfs.core.windows.net`powierzchniowe przez punkt końcowy .

### <a name="scalability"></a>Skalowalność

Usługa Azure Storage jest skalowalna zgodnie z projektem, niezależnie od tego, czy uzyskujesz dostęp za pośrednictwem interfejsów magazynu usługi Data Lake Storage Gen2 lub Blob. Jest w stanie przechowywać i obsługiwać *wiele eksabajtów danych.* Ta ilość pamięci masowej jest dostępna z przepustowością mierzoną w gigabitach na sekundę (Gb/s) przy wysokim poziomie operacji wejścia/wyjścia na sekundę (IOPS). Poza trwałością przetwarzanie jest wykonywane przy niemal stałych opóźnieniach na żądanie, które są mierzone w poziomach usługi, konta i pliku.

### <a name="cost-effectiveness"></a>Opłacalność

Jedną z wielu zalet tworzenia usługi Data Lake Storage Gen2 na podstawie magazynu obiektów Blob platformy Azure jest niski koszt pojemności magazynu i transakcji. W przeciwieństwie do innych usług magazynu w chmurze, dane przechowywane w usłudze Data Lake Storage Gen2 nie muszą być przenoszone lub przekształcane przed przeprowadzeniem analizy. Aby uzyskać więcej informacji na temat cen, zobacz [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage).

Ponadto funkcje, takie jak [hierarchiczna przestrzeń nazw,](data-lake-storage-namespace.md) znacznie zwiększają ogólną wydajność wielu zadań analizy. Ta poprawa wydajności oznacza, że wymagana jest mniejsza moc obliczeniowa do przetwarzania tej samej ilości danych, co skutkuje niższym całkowitym kosztem posiadania (TCO) dla zadania analizy end-to-end.

### <a name="one-service-multiple-concepts"></a>Jedna usługa, wiele koncepcji

Data Lake Storage Gen2 to dodatkowa funkcja analizy dużych zbiorów danych, oparta na magazynie obiektów Blob platformy Azure. Chociaż istnieje wiele korzyści w wykorzystaniu istniejących składników platformy obiektów Blobs do tworzenia i obsługi jezior danych do analizy, prowadzi to do wielu pojęć opisujących te same, udostępnione rzeczy.

Poniżej przedstawiono równoważne jednostki, zgodnie z opisem przez różne pojęcia. O ile nie określono inaczej, te jednostki są bezpośrednio synonimami:

| Pojęcie                                | Organizacja najwyższego poziomu | Organizacja niższego szczebla                                            | Kontener danych |
|----------------------------------------|------------------------|---------------------------------------------------------------------|----------------|
| Obiekty blob — magazyn obiektów ogólnego przeznaczenia | Kontener              | Katalog wirtualny (tylko SDK – nie zapewnia manipulacji atomowej) | Obiekt blob           |
| Usługa Azure Data Lake Storage Gen2 — magazyn analizy          | Kontener            | Katalog                                                           | Plik           |

## <a name="supported-blob-storage-features"></a>Obsługiwane funkcje magazynu obiektów blob

Funkcje magazynu obiektów blob, takie jak rejestrowanie [diagnostyczne, warstwy dostępu](storage-blob-storage-tiers.md)i zasady [zarządzania cyklem](../common/storage-analytics-logging.md) [życia magazynu obiektów blob,](storage-lifecycle-management-concepts.md) działają teraz z kontami, które mają hierarchiczny obszar nazw. W związku z tym można włączyć hierarchiczne przestrzenie nazw na kontach magazynu obiektów Blob bez utraty dostępu do tych funkcji. 

Aby uzyskać listę obsługiwanych funkcji magazynu obiektów Blob, zobacz [Funkcje magazynu obiektów Blob dostępnych w usłudze Azure Data Lake Storage Gen2](data-lake-storage-supported-blob-storage-features.md).

## <a name="supported-azure-service-integrations"></a>Obsługiwane integracje usług platformy Azure

Usługa Data Lake Storage gen2 obsługuje kilka usług platformy Azure, których można używać do pozyskiwania danych, przeprowadzania analiz i tworzenia reprezentacji wizualnych. Aby uzyskać listę obsługiwanych usług platformy Azure, zobacz [usługi platformy Azure obsługujące usługę Azure Data Lake Storage Gen2.](data-lake-storage-supported-azure-services.md)

## <a name="supported-open-source-platforms"></a>Obsługiwane platformy open source

Kilka platform open source obsługuje data lake storage gen2. Aby uzyskać pełną listę, zobacz [Platformy open source obsługujące usługę Azure Data Lake Storage Gen2](data-lake-storage-supported-open-source-platforms.md).

## <a name="see-also"></a>Zobacz też

- [Znane problemy z usługą Azure Data Lake Storage Gen2](data-lake-storage-known-issues.md)
- [Dostęp do wielu protokołów w usłudze Azure Data Lake Storage](data-lake-storage-multi-protocol-access.md)


