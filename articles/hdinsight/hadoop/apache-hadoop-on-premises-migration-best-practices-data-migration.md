---
title: Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — najlepsze rozwiązania migracji danych
description: Dowiedz się, dane migracji najlepsze rozwiązania dotyczące migrowania lokalnych klastrów Hadoop do usługi Azure HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/25/2018
ms.author: hrasheed
ms.openlocfilehash: 6b06b8eb8d5e18acd3107ec5cccac79fc7be7edc
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418181"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — najlepsze rozwiązania migracji danych

Ten artykuł zawiera zalecenia dotyczące migracji danych do usługi Azure HDInsight. Jest częścią serii, która zapewnia najlepsze rozwiązania w celu ułatwienia migrowania lokalnych systemów Apache Hadoop do usługi Azure HDInsight.

## <a name="migrate-data-from-on-premises-to-azure"></a>Migrowanie danych ze środowiska lokalnego na platformę Azure

Istnieją dwie główne opcje migracji danych ze środowiska lokalnego do środowiska platformy Azure:

1.  Transfer danych za pośrednictwem sieci przy użyciu protokołu TLS
    1.  Za pośrednictwem sieci internet
    2.  ExpressRoute
2.  Wysyłanie danych
    1.  Import / Eksport usługi
        - HDD SATA wewnętrznego lub tylko dyski SSD
        - Szyfrowane w stanie spoczynku (AES-128 / AES-256)
        - Zadania importu może mieć maksymalnie 10 dysków
        - Dostępny w wszystkich publicznych regionach & ug
    1.  Data Box
        - Do 80 TB danych na urządzenie Data box
        - Szyfrowane w stanie spoczynku (AES-256)
        - Używa protokołów NAS oraz obsługę popularnych narzędzi do kopiowania danych
        - Odporne na wstrząsy sprzętu
        - Dostępne tylko w Stanach Zjednoczonych i publicznej wersji zapoznawczej

Poniższa tabela zawiera czas transferu przybliżony danych na podstawie przepustowości woluminu i sieci danych. Użyj urządzenia Data box, jeśli migracja danych szacowany czas wynosi ponad trzy tygodnie.

|**Ilość danych**|**Przepustowość sieci**|||
|---|---|---|---|
|| **45 MB/s (T3)**|**100 MB/s**|**1 GB/s**|**10 GB/s**
|1 TB|2 dni|1 dzień| 2 godziny|14 min|
|10 TB|22 dni|10 dni|1 dzień|2 godziny|
|35 TB|dni o 76|34 dni|3 dni|8 godzin|
|80 TB|173 dni|78 dni|8 dni|19 godz.|
|100 TB|216 dni|97 dni|10 dni|1 dzień|
|200 TB|1 rok|194 dni|19 dni|2 dni|
|500 TB|3 lata|1 rok|49 dni|5 dni|
|1 PB|6 lat|3 lata|97 dni|10 dni|
|2 PB|od 12 lat|5 lat|194 dni|19 dni|

Narzędzia natywnej na platformie Azure, takich jak narzędzia DistCp, usługi Azure Data Factory i AzureCp, może służyć do przesyłania danych za pośrednictwem sieci. Narzędzia innych firm platforma WANDisco można również w tym samym celu. Narzędzia Mirrormaker platformy Kafka i Sqoop mogą służyć do transferu bieżące dane ze środowiska lokalnego do systemów usługi Azure storage.

## <a name="performance-considerations-when-using-apache-distcp"></a>Zagadnienia dotyczące wydajności w przypadku korzystania z narzędzia DistCp Apache

Narzędzia DistCp jest projektu Apache, który używa zadania MapReduce mapy w celu transferu danych, obsługa błędów i odzyskać z tych błędów. Lista plików źródłowych przypisuje do każdego zadania mapy. Zadanie mapy następnie kopiuje wszystkie jego pliki przypisane do miejsca docelowego. Istnieje kilka technik może poprawić wydajność DistCp.

### <a name="increase-the-number-of-mappers"></a>Zwiększ liczbę liczby Maperów

Narzędzia DistCp próbuje utworzyć mapę zadania, kopiuje każdy z nich mniej więcej taką samą liczbę bajtów. Domyślnie zadania DistCp używają liczby maperów 20. Za pomocą więcej liczby Maperów dla narzędzia Distcp (przy użyciu am "parametr w wierszu polecenia) zwiększa równoległości w trakcie transferu danych i zmniejsza długość transferu danych. Istnieją jednak wziąć pod uwagę przy jednoczesnym zwiększeniu liczby liczby Maperów dwie rzeczy:

1. Firmy DistCp najniższy poziom szczegółowości jest pojedynczy plik. Określanie liczby liczby Maperów więcej niż liczba plików źródłowych nie jest pomocne i będzie marnować zasoby klastra dostępny.
1. Należy wziąć pod uwagę ilość dostępnej pamięci usługi Yarn w klastrze, aby określić liczbę liczby Maperów. Każde zadanie mapy jest uruchamiany jako kontenera Yarn. Przy założeniu, że żadne inne duże obciążenia są uruchomione w klastrze, liczby Maperów może być określana za pomocą następującego wzoru: m = (liczba węzłów procesu roboczego \* pamięci usługi YARN dla każdego węzła procesu roboczego) / YARN rozmiar kontenera. Jednak jeśli inne aplikacje używają pamięci, następnie wybrać do użycia tylko część pamięci usługi YARN dla zadań narzędzia DistCp.

### <a name="use-more-than-one-distcp-job"></a>Użyj więcej niż jedno zadanie narzędzia DistCp

Gdy rozmiar zestawu danych do przeniesienia jest większa niż 1 TB, należy użyć więcej niż jedno zadanie DistCp. Za pomocą więcej niż jedno zadanie ogranicza wpływ awarii. Jeśli jakiekolwiek zadanie nie powiedzie się, wystarczy ponownie uruchomić określonego zadania, a nie dla wszystkich zadań.

### <a name="consider-splitting-files"></a>Należy rozważyć rozdzielenie plików

W przypadku niewielkiej liczby dużych plików, należy rozważyć, dzieląc je na fragmenty plików 256 MB, aby uzyskać więcej potencjalnych współbieżności przy użyciu liczby Maperów więcej.

### <a name="use-the-strategy-command-line-parameter"></a>Należy użyć parametru wiersza polecenia "strategii"

Należy rozważyć użycie `strategy = dynamic` parametr w wierszu polecenia. Wartość domyślna `strategy` parametr jest `uniform size`, w którym to przypadku każda mapa kopiuje mniej więcej taką samą liczbę bajtów. Jeśli ten parametr jest zmieniana na `dynamic`, plik listingu jest podzielony na kilka "fragmentów files". Liczba fragmentów plików jest wielokrotnością liczby mapy. Każde zadanie mapy jest przypisany jeden z plików fragmentów. Po przetworzeniu wszystkich ścieżek w fragment zostanie usunięta w bieżącym fragmencie i nabywa się nowych fragmentów. Proces jest kontynuowany, dopóki nie ma więcej fragmentów są dostępne. Takie podejście "dynamiczne" umożliwia szybsze zadania mapy korzystać więcej ścieżek te wolniej, co zwiększyło szybkość ogólne zadania narzędzia DistCp.

### <a name="increase-the-number-of-threads"></a>Zwiększ liczbę wątków

Sprawdź, czy zwiększenie `-numListstatusThreads` parametr zwiększa wydajność. Ten parametr określa liczbę wątków używanych podczas tworzenia pliku listy i jest to maksymalna wartość 40.

### <a name="use-the-output-committer-algorithm"></a>Użycie algorytmu osobą zatwierdzającą w danych wyjściowych

Sprawdź, czy przekazywania parametru `-Dmapreduce.fileoutputcommitter.algorithm.version=2` zapewnia lepszą wydajność narzędzia DistCp. Ten algorytm osobą zatwierdzającą w danych wyjściowych zawiera optymalizacje wokół zapisywania plików wyjściowych do miejsca docelowego. Następujące polecenie jest przykład pokazujący sposób użycia różnych parametrów:

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>Migracja metadanych

### <a name="hive"></a>Hive

Magazyn metadanych hive można migrować za pomocą skryptów lub przy użyciu funkcji replikacji bazy danych.

#### <a name="hive-metastore-migration-using-scripts"></a>Migracja magazynu metadanych hive za pomocą skryptów

1. Generuj Hive DDLs z Magazyn metadanych Hive środowiska lokalnego. Ten krok można wykonać przy użyciu [otoki skryptu powłoki systemowej]. (https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md)
1. Edytuj wygenerowany kod DDL, aby zastąpić adres url systemu HDFS przy użyciu adresów URL usługi ADLS/WASB/ABFS
1. Uruchom zaktualizowane DDL na magazynu metadanych klastra HDInsight
1. Upewnij się, czy wersja magazynu metadanych Hive jest zgodna między lokalizacją lokalną i chmurą

#### <a name="hive-metastore-migration-using-db-replication"></a>Migracja magazynu metadanych hive przy użyciu replikacji bazy danych

- Konfigurowanie replikacji bazy danych między Magazyn metadanych Hive lokalnej bazy danych i magazynu metadanych HDInsight bazy danych
- Użyj "Hive MetaTool", aby zastąpić adres url systemu HDFS ADLS/WASB/ABFS adresów URL, na przykład:

```bash
./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
```

### <a name="ranger"></a>Ranger

- Eksportuj zasady platformy Ranger w środowisku lokalnym do plików xml.
- Przekształć lokalnych określonych opartych na systemie plików HDFS ścieżki do WASB/Azure Data Lake Store przy użyciu narzędzia, takiego jak XSLT.
- Importowanie zasad do systemem HDInsight platformy Ranger.

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj następny artykuł w tej serii:

- [Zabezpieczenia i metodyki DevOps najlepsze rozwiązania dotyczące lokalnych do migracji usługi Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-security-devops.md)