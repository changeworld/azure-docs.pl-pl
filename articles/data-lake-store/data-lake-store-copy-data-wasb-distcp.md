---
title: Kopiowanie danych do i z WASB do usługi Data Lake Store za pomocą narzędzia Distcp | Dokumentacja firmy Microsoft
description: Kopiowanie danych do i z obiektów blob magazynu Azure do usługi Data Lake Store za pomocą narzędzia Distcp narzędzia
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ae2e9506-69dd-4b95-8759-4dadca37ea70
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: d6f4d1f7b974a3cd44e7cb9ffc2c63548f0bc321
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624398"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-store"></a>Kopiowanie danych między usługami Azure Storage Blobs a Data Lake Store za pomocą narzędzia Distcp
> [!div class="op_single_selector"]
> * [Korzystanie z narzędzia DistCp](data-lake-store-copy-data-wasb-distcp.md)
> * [Korzystanie z narzędzia AdlCopy](data-lake-store-copy-data-azure-storage-blob.md)
>
>

Jeśli masz klaster usługi HDInsight z dostępem do usługi Data Lake Store, aby skopiować dane, można użyć narzędzia ekosystemu Hadoop, takich jak narzędzia Distcp **do i z** magazynu klastra usługi HDInsight (WASB) do konta usługi Data Lake Store. Ten artykuł zawiera instrukcje, jak używać narzędzia narzędzia Distcp.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto usługi Azure Data Lake Store**. Aby uzyskać instrukcje na temat go utworzyć, zobacz [wprowadzenie do usługi Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Klaster HDInsight Azure** z dostępem do konta usługi Data Lake Store. Zobacz [tworzenia klastra usługi HDInsight z usługą Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Upewnij się, że włączenie pulpitu zdalnego dla klastra.

## <a name="do-you-learn-fast-with-videos"></a>Czy dzięki filmom szybko się uczysz?
[Obejrzyj ten film](https://mix.office.com/watch/1liuojvdx6sie) na kopiowanie danych między obiektach blob magazynu Azure i usługi Data Lake Store za pomocą narzędzia DistCp.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Za pomocą narzędzia Distcp z klastra usługi HDInsight w systemie Linux

Klaster usługi HDInsight jest dostarczany z narzędzia narzędzia Distcp, które można skopiować danych z różnych źródeł do klastra usługi HDInsight. Jeśli zostały skonfigurowane do używania usługi Data Lake Store jako dodatkowego miejsca do magazynowania klastra usługi HDInsight, narzędzie narzędzia Distcp mogą być używane out-of box można skopiować danych do i z na koncie usługi Data Lake Store. W tej sekcji opisano, jak używać narzędzia narzędzia Distcp.

1. Na pulpicie używanie protokołu SSH, aby połączyć się z klastrem. Zobacz [Połącz z klastrem usługi HDInsight opartej na systemie Linux](../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Uruchom polecenia w wierszu polecenia programu SSH.

2. Sprawdź, czy można uzyskać dostęp do obiektów blob magazynu Azure (WASB). Uruchom następujące polecenie:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    Dane wyjściowe powinny udostępnić listę zawartości obiektu blob magazynu.

3. Podobnie należy sprawdzić, czy masz dostęp do konta usługi Data Lake Store z klastra. Uruchom następujące polecenie:

        hdfs dfs -ls adl://<data_lake_store_account>.azuredatalakestore.net:443/

    Dane wyjściowe powinny zawierają listę plików/folderów w ramach konta usługi Data Lake Store.

4. Aby skopiować dane z WASB do konta usługi Data Lake Store za pomocą narzędzia Distcp.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder

    Polecenie kopiuje zawartość **/przykład/data/gutenberg/** folderu w WASB do **/myfolder** w ramach konta usługi Data Lake Store.

5. Podobnie Aby skopiować dane z konta usługi Data Lake Store do WASB za pomocą narzędzia Distcp.

        hadoop distcp adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    Polecenie kopiuje zawartość **/myfolder** w ramach konta usługi Data Lake Store, aby **/przykład/data/gutenberg/** folderu w WASB.

## <a name="performance-considerations-while-using-distcp"></a>Zagadnienia dotyczące wydajności podczas korzystania z narzędzia DistCp

Ponieważ w narzędzia DistCp najniższy poziom szczegółowości jest pojedynczy plik, ustawienie maksymalną liczbę jednoczesnych kopii jest parametr najważniejszych w celu zoptymalizowania go pod względem usługi Data Lake Store. Liczbę jednoczesnych kopii jest kontrolowany przez ustawienie liczby mapowań ('M ') parametru w wierszu polecenia. Ten parametr określa maksymalną liczbę mapowań, które są używane do kopiowania danych. Wartość domyślna to 20.

**Przykład**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg adl://<data_lake_store_account>.azuredatalakestore.net:443/myfolder -m 100

### <a name="how-do-i-determine-the-number-of-mappers-to-use"></a>Jak ustalić liczbę mapowań można użyć?

Oto kilka użytecznych wskazówek.

* **Krok 1: Określanie pamięć YARN** -pierwszym krokiem jest określenie dostępnych dla klastra, gdy zostanie uruchomione zadanie narzędzia DistCp pamięci YARN. Te informacje są dostępne w portalu Ambari skojarzony z klastrem. Przejdź do YARN, a następnie Wyświetl kartę Configs, aby wyświetlić pamięci YARN. Aby uzyskać całkowitej ilości pamięci YARN, należy pomnożyć pamięci YARN na węzeł z liczbą węzłów, że masz w klastrze.

* **Krok 2: Obliczanie liczby mapowań** — wartość **m** jest równa iloraz pamięć YARN podzielonej przez rozmiar kontenera YARN. Informacje o rozmiarze kontenera YARN jest dostępna w portalu Ambari, jak również. Przejdź do YARN, a następnie Wyświetl kartę Configs. W tym oknie zostanie wyświetlony rozmiar kontenera YARN. Równości na liczbę mapowań (**m**) jest

        m = (number of nodes * YARN memory for each node) / YARN container size

**Przykład**

Załóżmy, że masz 4 węzły D14v2s w klastrze, a chcesz przenieść 10 TB danych z różnych folderach 10. Zawiera foldery różnych ilości danych różni się od rozmiary plików w ramach każdego folderu.

* Całkowita liczba pamięci YARN - portal z Ambari okaże się, że pamięć YARN jest 96 GB dla węzła D14. Tak całkowita pamięć YARN czterech węzłów klastra jest: 

        YARN memory = 4 * 96GB = 384GB

* Liczba mapowań - portal z Ambari okaże się, że rozmiar kontenera YARN to 3072 D14 węzła klastra. Tak liczba mapowań to:

        m = (4 nodes * 96GB) / 3072MB = 128 mappers

Jeśli inne aplikacje korzystają z pamięci, można wybrać do użycia tylko część klastra YARN pamięci dla narzędzia DistCp.

### <a name="copying-large-datasets"></a>Kopiowanie dużych zestawów danych

Jeśli rozmiar zestawu danych do przeniesienia jest duży (na przykład, > 1 TB) lub jeśli masz wiele różnych folderach, należy rozważyć użycie wielu zadań narzędzia DistCp. Nie jest prawdopodobnie nie są bardziej wydajne, ale rozprzestrzenia się zadania tak, że jeśli jakiekolwiek zadanie nie powiedzie się, należy ponownie uruchomić określonego zadania, a nie całego zadania.

### <a name="limitations"></a>Ograniczenia

* Narzędzia DistCp próbuje utworzyć mapowań podobne rozmiaru w celu optymalizacji wydajności. Zwiększenie liczby mapowań może nie zawsze zwiększyć wydajność.

* Narzędzia DistCp jest ograniczona do tylko jednej mapowania na plik. W związku z tym nie powinna mieć mapowań więcej niż liczba kupionych plików. Ponieważ narzędzia DistCp można przypisać tylko jednego mapowania do pliku, ogranicza to czas współbieżności, który może służyć do kopiowania dużych plików.

* Jeśli masz niewielką liczbę duże pliki, następnie należy rozdzielić je na fragmenty plików 256 MB zapewniające współbieżności potencjalnych więcej. 
 
* Jeśli kopiujesz z konta magazynu obiektów Blob Azure, zadanie kopiowania może ograniczony po stronie magazynu obiektów blob. To powoduje spadek wydajności zadania kopiowania. Aby dowiedzieć się więcej na temat limitów magazynu obiektów Blob Azure, zobacz limity magazynu Azure w [subskrypcji platformy Azure i ograniczenia usługi](../azure-subscription-service-limits.md).

## <a name="see-also"></a>Zobacz także
* [Kopiowanie danych z obiektów blob magazynu Azure do usługi Data Lake Store](data-lake-store-copy-data-azure-storage-blob.md)
* [Zabezpieczanie danych w usłudze Data Lake Store](data-lake-store-secure-data.md)
* [Korzystanie z usługi Azure Data Lake Analytics z usługą Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Korzystanie z usługi Azure HDInsight z usługą Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)
