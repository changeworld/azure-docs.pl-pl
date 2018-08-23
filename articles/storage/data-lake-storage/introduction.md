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
ms.openlocfilehash: 072573b16fbeebac1ec942b0be508cf901b5cd27
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2018
ms.locfileid: "42060198"
---
# <a name="introduction-to-azure-data-lake-storage-gen2-preview"></a>Wprowadzenie do usługi Azure Data Lake Storage Gen2 (wersja zapoznawcza)

Usługa Azure Data Lake Gen2 — wersja zapoznawcza to zbiór funkcji przeznaczonych do analizy danych big data, utworzone w górnej części [usługi Azure Blob storage](../blobs/storage-blobs-introduction.md). Umożliwia łączenie się z danych za pomocą obu paradygmatów magazynu plików, jak systemu i obiekt. To sprawia, że Data Lake Storage Gen2 usługa tylko oparte na chmurze wielu modalne magazynu, co umożliwia prowadzenie wartość analizy wszystkich danych.

Data Lake Storage Gen2 funkcje wszystkich klas, które są wymagane przez pełny cykl życia analizy danych. Wynika to z zbieżności możliwości naszych dwóch istniejących usług magazynu. Funkcje z [usługi Azure Data Lake Storage Gen1](../../data-lake-store/index.md), takich jak semantyki systemu plików, pliku poziom zabezpieczeń i skalowania są połączone z niskie koszty i warstwowego magazynu, wysokiej dostępności/zastosowanie funkcji i duży zestaw SDK/narzędzia ekosystem z [usługi Azure Blob storage](../blobs/storage-blobs-introduction.md). W Data Lake Storage Gen2 jakość magazynu obiektów pozostają podczas dodawania zalety interfejsu systemu plików zoptymalizowana pod kątem analizy obciążeń.

## <a name="designed-for-enterprise-big-data-analytics"></a>Przeznaczona dla przedsiębiorstw analizy danych big data

Data Lake Storage Gen2 to usługa magazynu podstawowego do tworzenia enterprise data Lake (EDL) na platformie Azure. Zaprojektowany od początku do obsługi wielu petabajtów informacji podczas zatwierdzeniu setki gigabity przepływności, Data Lake Storage Gen2 zapewnia prosty sposób zarządzać ogromnych ilości danych.

Podstawową cechą Data Lake Storage Gen2, to dodanie [hierarchicznej przestrzeni nazw](./namespace.md) do usługi Blob storage, która organizuje pliki/obiekty w hierarchii katalogów wydajne dostępu do danych. Hierarchiczna przestrzeń nazw umożliwia także Data Lake Storage Gen2 obsługuje zarówno magazyn obiektów i plików paradygmatów systemu w tym samym czasie. Na przykład typowych konwencji nazewnictwa magazynu obiektów używa ukośników w nazwie do naśladowania hierarchicznej struktury folderów. Ta struktura staje się rzeczywistych za pomocą Data Lake Storage Gen2. Operacje, takie jak zmiana nazwy lub usunięcie katalogu stają się operacje dotyczące metadanych atomic pojedynczego katalogu zamiast wyliczanie i przetwarzanie wszystkich obiektów, które mają prefiks nazwy katalogu.

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

- **Nadzbiór uprawnień POSIX**: model zabezpieczeń Gen2 Data Lake — w pełni obsługuje listy ACL i POSIX uprawnienia oraz niektórych dodatkowy poziom szczegółowości specyficzne dla Data Lake Storage Gen2. Ustawienia można skonfigurować za pomocą narzędzia administracyjne lub środowisk, takich jak Hive, jak i platformy Spark.

- **Niskie koszty**: Data Lake Storage Gen2 funkcji niedrogiej pojemności i transakcji. Jako przejścia danych za pośrednictwem jego pełny cykl życia, stawki rozliczeniowe zmienia zachowanie kosztów do minimum, za pomocą wbudowanych funkcji takich jak [cykl życia magazynu obiektów Blob platformy Azure](../common/storage-lifecycle-managment-concepts.md).

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