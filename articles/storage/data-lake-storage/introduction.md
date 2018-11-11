---
title: Usługa Azure Data Lake Storage Gen2 w wersji zapoznawczej wprowadzenie
description: Zawiera omówienie usługi Azure Data Lake Gen2 — wersja zapoznawcza
services: storage
author: jamesbak
ms.service: storage
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: c86609ae5b993328beced468b74c7f2a1b65def4
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283618"
---
# <a name="introduction-to-azure-data-lake-storage-gen2-preview"></a>Wprowadzenie do usługi Azure Data Lake Storage Gen2 (wersja zapoznawcza)

Usługa Azure Data Lake Gen2 — wersja zapoznawcza to zbiór funkcji przeznaczonych do analizy danych big data, wbudowane w [usługi Azure Blob storage](../blobs/storage-blobs-introduction.md). Umożliwia łączenie się z danych za pomocą obu paradygmatów magazynu plików, jak systemu i obiekt. Dodanie Data Lake Storage Gen2 sprawia, że usługi Azure Storage tylko oparte na chmurze wielu modalne platformy, co umożliwia prowadzenie wartość analizy wszystkich danych.

Data Lake Storage Gen2 zapewnia wszystkich klas, które są wymagane przez pełny cykl życia analizy danych do usługi Azure Storage. Jest wynikiem zbieżności możliwości naszych dwa istniejące usługi magazynu, usługi Azure Blob Storage i Azure Data Lake Storage Gen1. Funkcje z [usługi Azure Data Lake Storage Gen1](../../data-lake-store/index.md), takich jak semantyki systemu plików, pliku poziom zabezpieczeń i skalowania są połączone z magazynem niskie koszty i warstwowego możliwości odzyskiwania wysoka dostępność/po awarii z [platformy Azure Magazynu obiektów blob](../blobs/storage-blobs-introduction.md).

## <a name="designed-for-enterprise-big-data-analytics"></a>Przeznaczona dla przedsiębiorstw analizy danych big data

Data Lake Storage Gen2 sprawia, że usługa Azure storage jako podstawa tworzenia jeziora danych przedsiębiorstwa na platformie Azure. Zaprojektowany od początku do obsługi wielu petabajtów informacji podczas zatwierdzeniu setki gigabity przepływności, Data Lake Storage Gen2 umożliwia łatwe zarządzanie ogromnych ilości danych.

Podstawową część Data Lake Storage Gen2, to dodanie [hierarchicznej przestrzeni nazw](./namespace.md) do usługi Blob storage, która organizuje pliki/obiekty w hierarchii katalogów dla dostępu do danych wydajne. Hierarchiczna przestrzeń nazw umożliwia także Data Lake Storage Gen2 obsługuje zarówno magazyn obiektów i plików paradygmatów systemu w tym samym czasie. Na przykład typowych konwencji nazewnictwa magazynu obiektów używa ukośników w nazwie do naśladowania hierarchicznej struktury folderów. Ta struktura staje się rzeczywistych za pomocą Data Lake Storage Gen2. Operacje, takie jak zmiana nazwy lub usunięcie katalogu stają się operacje dotyczące metadanych atomic pojedynczego katalogu zamiast wyliczanie i przetwarzanie wszystkich obiektów, które mają prefiks nazwy katalogu.

W przeszłości funkcje analizy chmurowej — było naruszenia bezpieczeństwa w zakresie wydajności, zarządzania i zabezpieczeń. Data Lake Storage Gen2 dotyczy każdego z tych aspektów w następujący sposób:

- **Wydajność** jest zoptymalizowany, ponieważ nie ma potrzeby kopiowania lub przekształcić dane jako warunek wstępny do analizy. Hierarchicznej przestrzeni nazw znacznie poprawia wydajność operacji zarządzania katalogu, co zwiększa ogólną wydajność zadania.

- **Zarządzanie** jest łatwiejsze, ponieważ można organizować i manipulowania plików, katalogów i podkatalogów.

- **Zabezpieczenia** obowiązuje, ponieważ możesz zdefiniować uprawnienia POSIX, folderów i klientów indywidualnych plików.

- **Przystępność cenową** jest możliwe, ponieważ Data Lake Storage Gen2 bazuje na usłudze niskie koszty [usługi Azure Blob storage](../blobs/storage-blobs-introduction.md). Dodatkowe funkcje dodatkowo obniżyć całkowity koszt posiadania, uruchamiania analizy danych big data na platformie Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Najważniejsze funkcje Data Lake Storage Gen2

> [!NOTE]
> W publicznej wersji zapoznawczej Data Lake Storage Gen2 niektóre funkcje wymienione poniżej mogą różnić się ich dostępność. Zgodnie z nowych funkcji i regiony są wydawane w programie wersji zapoznawczej, te informacje będą przekazywane.
> [Zarejestruj](https://aka.ms/adlsgen2signup) publicznej wersji zapoznawczej programu Data Lake Storage Gen2.  

- **Dostęp zgodny z usługi Hadoop**: Data Lake Storage Gen2 pozwala na zarządzanie i dostęp do danych, tak samo, jak za pomocą [pliku System (HDFS, Hadoop Distributed)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Nowy [sterownika ABFS](./abfs-driver.md) jest dostępna w ramach wszystkich środowisk technologii Apache Hadoop, w tym [Azure HDInsight](../../hdinsight/index.yml) i [usługi Azure Databricks](../../azure-databricks/index.yml) dostępu do danych przechowywanych w usługi Data Lake Storage Gen2.

- **Nadzbiór uprawnień POSIX**: model zabezpieczeń Data Lake Gen2 obsługuje listy ACL i POSIX uprawnienia oraz niektórych dodatkowy poziom szczegółowości specyficzne dla Data Lake Storage Gen2. Ustawienia można skonfigurować za pomocą narzędzia administracyjne lub środowisk, takich jak Hive, jak i platformy Spark.

- **Niskie koszty**: Data Lake Storage Gen2 oferuje ekonomiczne pojemności i transakcji. Jako przejścia danych za pośrednictwem jego pełny cykl życia, stawki rozliczeniowe zmienia zachowanie kosztów do minimum, za pomocą wbudowanych funkcji takich jak [cykl życia magazynu obiektów Blob platformy Azure](../common/storage-lifecycle-managment-concepts.md).

- **Współpracuje z narzędzia magazynu obiektów Blob, struktur i aplikacje**: Data Lake Storage Gen2 w dalszym ciągu działają z szeroką gamą narzędzi, struktur i aplikacje, które istnieją już dzisiaj dla magazynu obiektów Blob.

- **Sterownik zoptymalizowane**: `abfs` sterownik jest [specjalnie zoptymalizowane pod kątem](./abfs-driver.md) do analizy danych big data. Odpowiednie interfejsy API REST są udostępniane za pośrednictwem `dfs` punktu końcowego, `dfs.core.windows.net`.

## <a name="scalability"></a>Skalowalność

Usługa Azure Storage jest skalowalna, zgodnie z projektem, czy możesz uzyskać dostęp za pośrednictwem interfejsu Data Lake Storage Gen2 lub usługi Blob storage. Jest w stanie przechowywać i obsługiwać *wiele eksabajtów danych*. Ta ilość miejsca jest dostępna z przepływnością mierzoną w gigabitach na sekundę (GB/s) na dużą liczbą operacji wejścia/wyjścia na sekundę (IOPS). Poza trwałości po prostu przetwarzania jest wykonywana na opóźnienia w pobliżu stałą na żądanie, są mierzone w poziomie usługi, konta i plików.

## <a name="cost-effectiveness"></a>Ekonomiczność

Jedną z wielu zalet tworzenia Data Lake Storage Gen2 na podstawie usługi Azure Blob storage jest [niedrogiej](https://azure.microsoft.com/pricing/details/storage) pojemności i transakcji. W przeciwieństwie do innych usług magazynu w chmurze Data Lake Storage Gen2 obniża koszty, ponieważ dane nie są wymagane do przeniesienia lub przekształcane przed przeprowadzeniem analizy.

Ponadto funkcje, takie jak [hierarchicznej przestrzeni nazw](./namespace.md) znacznie zwiększyć ogólną wydajność wielu zadań usługi analytics. Ta poprawa wydajności oznacza, że wymagają mniej mocy obliczeniowej do przetwarzania tej samej ilości danych, co spowodowało niższy całkowity koszt posiadania (TCO) dla zadania analiza end-to-end.

## <a name="next-steps"></a>Kolejne kroki

Następujące artykuły opisano niektóre główne pojęcia Data Lake Storage Gen2 i szczegółów do przechowywania, uzyskać dostęp, zarządzać i uzyskiwać wgląd w dane:

* [Hierarchicznej przestrzeni nazw](./namespace.md)
* [Tworzenie konta magazynu](./quickstart-create-account.md)
* [Tworzenie klastra usługi HDInsight przy użyciu usługi Azure Data Lake Storage Gen2](./quickstart-create-connect-hdi-cluster.md)
* [Użyj konta usługi Azure Data Lake Storage Gen2 w usłudze Azure Databricks](./quickstart-create-databricks-account.md)