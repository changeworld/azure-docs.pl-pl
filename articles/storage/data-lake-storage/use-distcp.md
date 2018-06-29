---
title: Kopiowanie danych do Azure Data Lake magazynu Gen2 w wersji zapoznawczej za pomocą narzędzia Distcp | Dokumentacja firmy Microsoft
description: Kopiowanie danych do i z wersji zapoznawczej usługi Data Lake magazynu Gen2 za pomocą narzędzia Distcp narzędzia
services: storage
documentationcenter: ''
author: seguler
manager: jahogg
editor: seguler
ms.component: data-lake-storage-gen2
ms.service: storage
ms.devlang: na
ms.topic: how-to
ms.date: 06/27/2018
ms.author: seguler
ms.openlocfilehash: 2a958ceb0b3a1db9d06d045a8161fa6cd3ef5aba
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/27/2018
ms.locfileid: "37059930"
---
# <a name="use-distcp-to-copy-data-between-azure-storage-blobs-and-data-lake-storage-gen2-preview"></a>Kopiowanie danych między obiektach blob magazynu Azure i w wersji zapoznawczej usługi Data Lake magazynu Gen2 za pomocą narzędzia Distcp

Jeśli masz klaster usługi HDInsight z dostępem do usługi Azure Data Lake magazynu Gen2 podglądu, aby skopiować dane można użyć narzędzia ekosystemu Hadoop, takich jak narzędzia Distcp **do i z** magazynu klastra usługi HDInsight (WASB) w stanie Gen2 magazynu Lake danych konto. Ten artykuł zawiera instrukcje, jak używać narzędzia narzędzia Distcp.

## <a name="prerequisites"></a>Wymagania wstępne

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Konto magazynu Azure z włączoną funkcją usługi Azure Data Lake Storage (wersja zapoznawcza)**. Aby uzyskać instrukcje na temat go utworzyć, zobacz [TODO](quickstart-create-account.md)
* **Klaster HDInsight Azure** z dostępem do konta usługi Data Lake Storage. Zobacz [Użyj Azure Data Lake magazynu Gen2 z usługą Azure HDInsight clusters](use-hdi-cluster.md). Upewnij się, że włączenie pulpitu zdalnego dla klastra.

## <a name="use-distcp-from-an-hdinsight-linux-cluster"></a>Za pomocą narzędzia Distcp z klastra usługi HDInsight w systemie Linux

Klaster usługi HDInsight jest dostarczany z narzędzia narzędzia Distcp, które można skopiować danych z różnych źródeł do klastra usługi HDInsight. Jeśli skonfigurowano klastra usługi HDInsight można używać razem magazynu obiektów Blob Azure i usługi Azure Data Lake Storage, narzędzie narzędzia Distcp może być używane out-of box także skopiować dane między. W tej sekcji opisano, jak używać narzędzia narzędzia Distcp.

1. Na pulpicie używanie protokołu SSH, aby połączyć się z klastrem. Zobacz [Połącz z klastrem usługi HDInsight opartej na systemie Linux](../../hdinsight/hdinsight-hadoop-linux-use-ssh-unix.md). Uruchom polecenia w wierszu polecenia programu SSH.

2. Sprawdź, czy można uzyskać dostęp do obiektów blob magazynu Azure (WASB). Uruchom następujące polecenie:

        hdfs dfs –ls wasb://<container_name>@<storage_account_name>.blob.core.windows.net/

    Dane wyjściowe powinny udostępnić listę zawartości obiektu blob magazynu.

3. Podobnie należy sprawdzić, czy masz dostęp do konta usługi Data Lake Storage z klastra. Uruchom następujące polecenie:

        hdfs dfs -ls abfs://<filesystem_name>@<storage_account_name>.dfs.core.windows.net/

    Dane wyjściowe powinny zawierają listę plików/folderów w ramach konta usługi Data Lake Storage.

4. Aby skopiować dane z WASB do konta usługi Data Lake Storage za pomocą narzędzia Distcp.

        hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg abfs://<filesystem_name>@<storage_account_name>.dfs.core.windows.net/myfolder

    Polecenie kopiuje zawartość **/przykład/data/gutenberg/** folderu w magazynie obiektów Blob do **/myfolder** w ramach konta usługi Data Lake Storage.

5. Podobnie Aby skopiować dane z konta usługi Data Lake Storage magazynu obiektów Blob (WASB) za pomocą narzędzia Distcp.

        hadoop distcp abfs://<filesystem_name>@<storage_account_name>.dfs.core.windows.net/myfolder wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg

    Polecenie kopiuje zawartość **/myfolder** w ramach konta usługi Data Lake Store, aby **/przykład/data/gutenberg/** folderu w WASB.

## <a name="performance-considerations-while-using-distcp"></a>Zagadnienia dotyczące wydajności podczas korzystania z narzędzia DistCp

Ponieważ w narzędzia DistCp najniższy poziom szczegółowości jest pojedynczy plik, ustawienie maksymalną liczbę jednoczesnych kopii jest parametr najważniejszych w celu zoptymalizowania go pod względem usługi Data Lake Storage. Liczbę jednoczesnych kopii jest kontrolowany przez ustawienie liczby mapowań ('M ') parametru w wierszu polecenia. Ten parametr określa maksymalną liczbę mapowań, które są używane do kopiowania danych. Wartość domyślna to 20.

**Przykład**

    hadoop distcp wasb://<container_name>@<storage_account_name>.blob.core.windows.net/example/data/gutenberg abfs://<filesystem_name>@<storage_account_name>.dfs.core.windows.net/myfolder -m 100

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
