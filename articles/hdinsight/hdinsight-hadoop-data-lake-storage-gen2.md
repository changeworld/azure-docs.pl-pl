---
title: Za pomocą usług Azure Data Lake Storage (ADLS) Gen2 Apache Hadoop w usłudze Azure HDInsight
description: Omówienie usługi Azure Data Lake Storage Gen2 i jak współdziałają z usługi Azure HDInsight.
services: hdinsight,storage
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/21/2018
ms.openlocfilehash: b7cde3a8990a51e95a8ce9ad85bca524d5669e0c
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2018
ms.locfileid: "53721125"
---
# <a name="use-azure-data-lake-storage-gen2-with-apache-hadoop-in-azure-hdinsight"></a>Za pomocą usług Azure Data Lake Storage Gen2 Apache Hadoop w usłudze Azure HDInsight

Usługa Azure Data Lake Storage (ADLS) Gen2 przyjmuje podstawowe funkcje z usługi Azure Data Lake Storage Gen1, takich jak system plików zgodnych Hadoop, Azure Active Directory, a na podstawie listy kontroli dostępu POSIX i integruje usługi Azure Blob Storage. To połączenie umożliwia korzystanie z zalet wydajności usługi Azure Data Lake Storage Gen1 przy równoczesnym korzystaniu warstw magazynu obiektów Blob i zarządzanie cyklem życia danych.

## <a name="core-functionality-of-azure-data-lake-storage-gen2"></a>Podstawowe funkcje usługi Azure Data Lake Storage Gen2

- Dostęp zgodny z usługi Hadoop: Azure Data Lake magazynu Gen2 pozwala na zarządzanie i dostęp do danych, tak samo, jak za pomocą pliku System (HDFS, Hadoop Distributed). Sterownik ABFS jest dostępny w ramach wszystkich środowisk technologii Apache Hadoop, w tym usługi Azure HDInsight i Azure Databricks dostępu do danych przechowywanych w Data Lake Storage Gen2.

- Nadzbiór uprawnień POSIX: Model zabezpieczeń Data Lake Gen2 obsługuje listy ACL i POSIX uprawnienia oraz niektórych dodatkowy poziom szczegółowości specyficzne dla Data Lake Storage Gen2. Ustawienia można skonfigurować za pomocą narzędzia administracyjne lub środowisk, takich jak Apache Hive i Apache Spark.

- Ekonomiczne rozwiązanie: Data Lake Storage Gen2 oferuje ekonomiczne pojemności i transakcji. Funkcje, takie jak cykl życia magazynu obiektów Blob platformy Azure pomagają obniżyć koszty, dostosowując stawki rozliczeniowe, ponieważ dane są przenoszone przy użyciu jej cyklu projektowania.

- Działa z narzędzia magazynu obiektów Blob, struktur i aplikacji: Data Lake Storage Gen2 nadal działają z szeroką gamą narzędzi, struktur i aplikacje, które istnieją już dzisiaj dla magazynu obiektów Blob.

- Zoptymalizowane sterowników: Sterownik ABFS jest dostosowana specjalnie do analizy danych big data. Odpowiednie interfejsy API REST są udostępniane za pośrednictwem punktu końcowego systemu plików dfs, dfs.core.windows.net.

## <a name="whats-new-about-azure-data-lake-storage-gen-2"></a>Nowe funkcje usługi Azure Data Lake Storage Gen 2

### <a name="managed-identities-for-secure-file-access"></a>Zarządzana tożsamość przy dostępie do bezpiecznych plików

Usługa Azure HDInsight korzysta z tożsamości zarządzanego zabezpieczyć klastrowi dostęp do plików w usłudze Azure Data Lake magazynu Gen2. Zarządzanych tożsamości to funkcja usługi Azure Active Directory, która zapewnia zestaw poświadczeń, automatycznie zarządzanych usług platformy Azure. Te poświadczenia mogą służyć do uwierzytelniania do dowolnej usługi, która obsługuje uwierzytelnianie w usłudze AD. Przy użyciu tożsamości zarządzanej nie wymaga przechowywania poświadczeń w plikach kodu lub konfiguracji.

Aby uzyskać więcej informacji, zobacz [co to jest zarządzanych tożsamości dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="azure-blob-filesystem-abfs-driver"></a>Sterownik systemu plików (ABFS) obiektów Blob platformy Azure

Aplikacje platformy Apache Hadoop natywnie oczekują na odczytywanie i zapisywanie danych z magazynu lokalnego dysku. Sterownik systemu plików usługi Hadoop, takich jak ABFS umożliwia aplikacjom usługi Hadoop do pracy z magazynem w chmurze poprzez emulację regularne operacje systemu plików Hadoop dla aplikacji. Sterownik następnie konwertuje te operacje na operacje, które rozumie platforma usług w chmurze rzeczywisty magazyn.

Poprzednio sterownik systemu plików Hadoop przekonwertować wszystkie operacje systemu plików do wywołań interfejsu API REST magazynu Azure po stronie klienta i następnie wywołania interfejsu API REST. Wywołuje to po stronie klienta konwersji, jednak spowodowało wiele interfejsu API REST dla operacji systemu plików w jednym, takie jak zmiana nazwy pliku. ABFS został przeniesiony część logiki system plików Hadoop po stronie klienta po stronie serwera oraz interfejsu API usługi ADLS Gen2 — teraz jest uruchamiany równolegle z interfejsem API obiektu Blob. Ta migracja zwiększa wydajność, ponieważ teraz można wykonywać typowe operacje systemu plików usługi Hadoop za pomocą jednego wywołania interfejsu API REST.

Aby uzyskać więcej informacji, zobacz [sterownik systemu plików obiektów Blob platformy Azure (ABFS): Dedykowany sterownika usługi Azure Storage dla platformy Apache Hadoop](../storage/data-lake-storage/abfs-driver.md).

### <a name="adls-gen-2-uri-scheme"></a>Schemat identyfikatora URI 2 generacji Azure Data Lake Store

Gen2 Azure Data Lake Store używa nowy schemat identyfikatora URI do uzyskiwania dostępu do plików w usłudze Azure storage z HDInsight:

`abfs[s]://<FILE_SYSTEM_NAME>@<ACCOUNT_NAME>.dfs.core.windows.net/<PATH>`

Schemat identyfikatora URI zapewnia dostęp do szyfrowania SSL (`abfss://` prefiks) i niezaszyfrowanego dostępu (`abfs://` prefiks). Firma Microsoft zaleca używanie `abfss` wszędzie tam, gdzie to możliwe, nawet w przypadku uzyskiwania dostępu do danych, który znajduje się w tym samym regionie platformy Azure.

`<FILE_SYSTEM_NAME>` Określa ścieżkę systemu plików Data Lake Storage Gen2.

`<ACCOUNT_NAME>` Określa nazwę konta usługi Azure Storage. Wymagana jest w pełni kwalifikowana nazwa domeny (FQDN).

`<PATH>` Jest nazwą ścieżki pliku lub katalogu HDFS.

Jeśli wartości `<FILE_SYSTEM_NAME>` i `<ACCOUNT_NAME>` nie są określone, używany jest domyślny system plików. W przypadku plików w domyślnym systemie plików można używać ścieżki względnej lub bezwzględnej. Na przykład `hadoop-mapreduce-examples.jar` pliku, który jest dostarczany z klastrami HDInsight mogą się odwoływać przy użyciu jednej z następujących ścieżek:

```
abfss://myfilesystempath@myaccount.dfs.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
abfss:///example/jars/hadoop-mapreduce-examples.jar /example/jars/hadoop-mapreduce-examples.jar
```

> [!NOTE]  
> Nazwa pliku jest `hadoop-examples.jar` w klastrach w wersji 2.1 i 1.6 HDInsight. Podczas pracy z plikami poza HDInsight, większość narzędzi nie rozpoznają ABFS sformatowanie i zamiast tego oczekuje podstawowego formatu ścieżki, takich jak `example/jars/hadoop-mapreduce-examples.jar`.

Aby uzyskać więcej informacji, zobacz [Użyj identyfikator URI usługi Azure Data Lake magazynu Gen2](../storage/data-lake-storage/introduction-abfs-uri.md).

## <a name="next-steps"></a>Kolejne kroki
- [Wprowadzenie do usługi Azure Data Lake Storage Gen2](../storage/data-lake-storage/introduction.md)
- [Użyj usługi Azure Data Lake Storage Gen2 (wersja zapoznawcza) przy użyciu klastrów usługi Azure HDInsight](../storage/data-lake-storage/use-hdi-cluster.md)