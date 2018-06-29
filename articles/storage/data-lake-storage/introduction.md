---
title: Usługi Azure Data Lake Storage Gen2 Podgląd wprowadzenie
description: Zawiera omówienie usługi Azure Data Lake magazynu Gen2 podglądu
services: storage
documentationcenter: ''
author: jamesbak
manager: jahogg
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2018
ms.author: jamesbak
ms.component: data-lake-storage-gen2
ms.openlocfilehash: 0631b1d0c8da925858f0b7fb1d778cb1161eb737
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061528"
---
# <a name="introduction-to-azure-data-lake-storage-gen2-preview"></a>Wprowadzenie do usługi Azure Data Lake Storage Gen2 podglądu

Azure Data Lake magazynu Gen2 w wersji zapoznawczej to zestaw funkcji przeznaczonych do analizy danych big data, wbudowane nad [magazynu obiektów Blob Azure](../blobs/storage-blobs-introduction.md). Umożliwia łączenie z danymi przy użyciu obu wzorcami magazynu plików, jak systemu i obiekt. Data Lake magazynu Gen2 dzięki temu usługa tylko oparte na chmurze wielu modalne magazynu, umożliwiając wyodrębnić analytics wartość ze wszystkich danych.

Data Lake magazynu Gen2 funkcjami wszystkie właściwości, które są wymagane dla pełny cykl życia analizy danych. Wynika to z proces wywoływania zbieżności możliwości naszych dwóch istniejących usług magazynu. Funkcje z [Azure Data Lake magazynu Gen1](../../data-lake-store/index.md), takich jak semantyki systemu plików, pliku poziom zabezpieczeń i skali w połączeniu z ekonomicznych, warstwowy magazynu, wysokiej dostępności/awaryjnego odzyskiwania możliwości i dużych SDK/narzędzi ekosystem z [magazynu obiektów Blob Azure](../blobs/storage-blobs-introduction.md). W Data Lake magazynu Gen2 wszystkie właściwości obiektu magazynu pozostają podczas dodawania korzyści wynikające z interfejsem system plików zoptymalizowane pod kątem analytics obciążeń.

## <a name="designed-for-enterprise-big-data-analytics"></a>Przeznaczona dla organizacji, analizy danych big data

Data Lake magazynu Gen2 jest usługa podstawowych magazynu do tworzenia enterprise jeziora danych (EDL) na platformie Azure. Zaprojektowany od początku do obsługi wielu petabajtów danych podczas utrzymywanie setki gigabity przepływności, Data Lake magazynu Gen2 zapewnia prosty sposób zarządzać olbrzymich ilości danych.

Podstawową funkcją Data Lake magazynu Gen2 jest dodanie [hierarchicznej przestrzeni nazw](./namespace.md) do usługi magazynu obiektów Blob, która organizuje obiektów/pliki w hierarchii katalogów dla dostępu do danych wydajności. Hierarchicznej przestrzeni nazw umożliwia także Data Lake magazynu Gen2 do obsługi zarówno magazynu obiektów i pliku wzorcami systemu w tym samym czasie. Na przykład typowe konwencji nazewnictwa magazynu obiektów używa ukośniki nazwę aby naśladował strukturę hierarchiczną folderów. Ta struktura staje się rzeczywistych z Gen2 magazynu Lake danych. Operacje, takie jak zmiana nazwy lub usunięcie katalogu stają się operacji na metadanych atomic jednego katalogu zamiast wyliczania i przetwarzanie wszystkich obiektów, które mają prefiks nazwy katalogu.

W przeszłości oparte na chmurze analytics musiał naruszyć w zakresie wydajności, zarządzania i zabezpieczeń. Data Lake magazynu Gen2 dotyczy każdego z tych aspektów w następujący sposób:

- **Wydajność** jest zoptymalizowany, ponieważ nie trzeba skopiować lub Przekształć dane jako warunek wstępny dla analizy. Hierarchicznej przestrzeni nazw znacznie poprawia wydajność operacji zarządzania katalogu, co zwiększa ogólną wydajność zadania.

- **Zarządzanie** jest łatwiejsze, ponieważ można organizować i manipulowanie plikami za pośrednictwem katalogów i jego podkatalogach.

- **Opłacalności** jest możliwe, jak Data Lake magazynu Gen2 bazuje na niski koszt [magazynu obiektów Blob Azure](../blobs/storage-blobs-introduction.md). Dodatkowe funkcje dalszych obniżyć całkowity koszt posiadania uruchamiania analizy danych big data na platformie Azure.

## <a name="key-features-of-data-lake-storage-gen2"></a>Najważniejsze funkcje Data Lake magazynu Gen2

> [!NOTE]
> W publicznej wersji zapoznawczej Data Lake magazynu Gen2 niektóre funkcje wymienione poniżej może się różnić w ich dostępności. Nowe funkcje i regiony zostaną zwolnione w programie wersji zapoznawczej, te informacje będą przekazywane.
> [Zarejestruj się](https://aka.ms/adlsgen2signup) do publicznej wersji zapoznawczej Gen2 magazynu Lake danych.  

- **Dostęp zgodny z Hadoop**: Data Lake magazynu Gen2 służy do zarządzania i dostęp do danych, tak samo jak w przypadku [systemu Distributed plików Hadoop (HDFS)](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html). Nowy [sterownika ABFS](./abfs-driver.md) jest dostępne w ramach wszystkich środowisk Apache Hadoop, w tym [Azure HDInsight](../../hdinsight/index.yml) i [Azure Databricks](../../azure-databricks/index.yml) uzyskują dostęp do danych przechowywanych w usługi Data Lake Storage Gen2.

- **Dostęp do danych z wielu protokołów i wielu modalne**: Data Lake magazynu Gen2 jest uznawany za **wielu modalne** usługi magazynu, ponieważ zawiera zarówno magazynu obiektów, jak i plik interfejsy systemu do tych samych danych **w tym samym czas**. Jest to osiągane przez zapewnienie wiele punktów końcowych protokołu, które mogą uzyskiwać dostęp do tych samych danych. 

    W przeciwieństwie do innych rozwiązań analitycznych danych przechowywanych w Data Lake magazynu Gen2 nie trzeba przenieść lub zostać przekształcone, przed uruchomieniem różnych narzędzi do analizy. Można uzyskać dostępu do danych za pośrednictwem tradycyjnych [interfejsów API magazynu obiektów Blob](../blobs/storage-blobs-introduction.md) (na przykład: pozyskiwania danych za pośrednictwem [przechwytywania centra zdarzeń](../../event-hubs/event-hubs-capture-enable-through-portal.md)) i przetwarzania danych przy użyciu HDInsight lub Azure Databricks, w tym samym czasie. 

- **Ekonomiczne**: Data Lake magazynu Gen2 funkcji pojemność niedrogiego magazynu i transakcji. Jako przejścia danych za pośrednictwem jego pełny cykl życia, stawki fakturowania zmienić koszty przechowywania do minimum za pomocą wbudowanych funkcji takich jak [cyklu życia magazynu obiektów Blob platformy Azure](../common/storage-lifecycle-managment-concepts.md).

- **Współpracuje z narzędzia magazynu obiektów Blob, struktur i aplikacje**: Data Lake magazynu Gen2 w dalszym ciągu działać z szerokiej gamy narzędzi, platform i aplikacje, które obecnie istnieją dla magazynu obiektów Blob.

- **Sterownik zoptymalizowane**: `abfs` sterownik jest [specjalnie zoptymalizowana](./abfs-driver.md) do analizy danych big data. Odpowiednich interfejsów API REST są udostępniane za pośrednictwem `dfs` punktu końcowego, `dfs.core.windows.net`.

## <a name="scalability"></a>Skalowalność

Usługi Azure Storage jest skalowalna zgodnie z projektem, czy dostęp za pośrednictwem interfejsów magazynu Data Lake magazynu Gen2 lub obiektu Blob. Jest w stanie przechowywać i obsługiwać *wiele eksabajtów danych*. Ta ilość miejsca w magazynie jest dostępne przepływność mierzona wysokiego poziomu operacji wejścia/wyjścia na sekundę (IOPS) w gigabitach na sekundę (GB/s). Poza tylko trwałości przetwarzania jest wykonywana na opóźnienia w pobliżu stała na żądanie, są mierzone na poziomie usługi, konta i plików.

## <a name="cost-effectiveness"></a>Ekonomiczność

Jedną z wielu zalet tworzenia u góry magazynu obiektów Blob Azure Data Lake magazynu Gen2 jest [ekonomicznych](https://azure.microsoft.com/pricing/details/storage) pojemność magazynu i transakcji. W przeciwieństwie do innych usług magazynu w chmurze Data Lake magazynu Gen2 obniża koszty, ponieważ nie jest wymagana do przeniesienia lub przekształcone przed wykonaniem analizy danych.

Ponadto, funkcje, takie jak [hierarchicznej przestrzeni nazw](./namespace.md) znacznie zwiększyć ogólną wydajność wielu zadań usługi analytics. Ta poprawa wydajności oznacza wymagają mniejszej mocy obliczeniowej do przetworzenia tego samego ilość danych, co powoduje niższy całkowity koszt posiadania (TCO) zadania usługi analiza end-to-end.

## <a name="next-steps"></a>Kolejne kroki

Następujące artykuły opisano niektóre z głównych założeń Data Lake magazynu Gen2 i szczegóły przechowywania, dostępu, zarządzania i uzyskać wgląd w dane:

* [Hierarchicznej przestrzeni nazw](./namespace.md)
* [Tworzenie konta magazynu](./quickstart-create-account.md)
* [Tworzenie klastra usługi HDInsight z usługi Azure Data Lake magazynu Gen2](./quickstart-create-connect-hdi-cluster.md)
* [Użyj konta usługi Azure Data Lake magazynu Gen2 w Azure Databricks](./quickstart-create-databricks-account.md) 