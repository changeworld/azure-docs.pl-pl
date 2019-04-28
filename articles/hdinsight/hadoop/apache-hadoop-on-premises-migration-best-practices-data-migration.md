---
title: Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — najlepsze rozwiązania migracji danych
description: Dowiedz się, dane migracji najlepsze rozwiązania dotyczące migrowania lokalnych klastrów Hadoop do usługi Azure HDInsight.
author: hrasheed-msft
ms.reviewer: ashishth
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: hrasheed
ms.openlocfilehash: 732cb118b7a0eebdbf28c7d7fe6ced435ce7920e
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "62129250"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — najlepsze rozwiązania migracji danych

Ten artykuł zawiera zalecenia dotyczące migracji danych do usługi Azure HDInsight. Jest częścią serii, która zapewnia najlepsze rozwiązania w celu ułatwienia migrowania lokalnych systemów Apache Hadoop do usługi Azure HDInsight.

## <a name="migrate-on-premises-data-to-azure"></a>Migracja danych lokalnych na platformę Azure

Istnieją dwie główne opcje migracji danych ze środowiska lokalnego do środowiska platformy Azure:

1.  Transfer danych za pośrednictwem sieci przy użyciu protokołu TLS
    1. Za pośrednictwem Internetu — mogą przesyłać dane do usługi Azure storage za pośrednictwem regularne połączenia internetowego przy użyciu jednej z kilku narzędzi, takich jak: Interfejs wiersza polecenia usługi Azure Storage Explorer, narzędzia AzCopy, programu Azure Powershell i platformy Azure.  Zobacz [przenoszenie danych do i z usługi Azure Storage](../../storage/common/storage-moving-data.md) Aby uzyskać więcej informacji.
    2. Expressroute — ExpressRoute to usługa, która umożliwia tworzenie prywatnych połączeń między centrami danych firmy Microsoft i infrastrukturą lokalną lub w funkcji wspólnej lokalizacji. Połączenia ExpressRoute nie omijają publiczny Internet i oferują wyższe bezpieczeństwa, niezawodności i szybkości pracy krótsze opóźnienia niż typowe połączenia przez Internet. Aby uzyskać więcej informacji, zobacz [tworzenie i modyfikowanie obwodu ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).
    1. Transfer danych w trybie online pole danych — krawędź pola danych i bramy pola danych czy produkty transferu danych w trybie online, które pełnić rolę bram magazynu do zarządzania danymi między Twoją witryną a Azure sieci. Data Box Edge, lokalne urządzenie sieciowe, przesyła dane na platformę Azure i z niej oraz przetwarza dane przy użyciu funkcji obliczeniowej na brzegu sieci z obsługą sztucznej inteligencji. Data Box Gateway to urządzenie wirtualne z funkcjami bramy magazynu. Aby uzyskać więcej informacji, zobacz [Azure dokumentacją Data Box - Online transferu](https://docs.microsoft.com/azure/databox-online/).
1.  Wysyłanie danych w trybie Offline
    1. Dane pole transfer danych w trybie offline — Data Box dysku Data Box, i duże pole danych urządzeń ułatwiają przenoszenie dużych ilości danych na platformie Azure, gdy sieć nie jest dostępną opcją. Te urządzenia do przesyłania danych w trybie offline są dostarczane z Twojej organizacji do centrum danych platformy Azure. Korzystają one z szyfrowania AES, aby pomóc w ochronie Twoich danych podczas przesyłania, i przechodzą dokładny proces oczyszczania po przekazaniu, aby usunąć Twoje dane z urządzenia. Aby uzyskać więcej informacji na temat urządzenia Data Box transferu w trybie offline, zobacz [dokumentacją usługi Azure Data Box — w trybie Offline transferu](https://docs.microsoft.com/azure/databox/). Aby uzyskać więcej informacji na temat migracji klastrów Hadoop, zobacz [użycia usługi Azure Data Box do migracji z lokalnego systemu plików HDFS magazynu do usługi Azure Storage](../../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md).

Poniższa tabela zawiera czas transferu przybliżony danych na podstawie przepustowości woluminu i sieci danych. Użyj urządzenia Data box, jeśli migracja danych szacowany czas wynosi ponad trzy tygodnie.

|**Ilość danych**|**Przepustowość sieci**||||
|---|---|---|---|---|
|| **45 MB/s (T3)**|**100 MB/s**|**1 GB/s**|**10 GB/s**|
|1 TB|2 dni|1 dzień| 2 godziny|14 min|
|10 TB|22 dni|10 dni|1 dzień|2 godziny|
|35 TB|dni o 76|34 dni|3 dni|8 godzin|
|80 TB|173 dni|78 dni|8 dni|19 godz.|
|100 TB|216 dni|97 dni|10 dni|1 dzień|
|200 TB|1 rok|194 dni|19 dni|2 dni|
|500 TB|3 lata|1 rok|49 dni|5 dni|
|1 PB|6 lat|3 lata|97 dni|10 dni|
|2 PB|od 12 lat|5 lat|194 dni|19 dni|

Narzędzia natywnej na platformie Azure, takich jak Apache Hadoop DistCp, usługi Azure Data Factory i AzureCp, może służyć do przesyłania danych za pośrednictwem sieci. Narzędzia innych firm platforma WANDisco można również w tym samym celu. Apache Kafka z narzędzia Mirrormaker i Apache Sqoop może służyć do transferu bieżące dane ze środowiska lokalnego do systemów usługi Azure storage.


## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>Zagadnienia dotyczące wydajności w przypadku korzystania z narzędzia Apache Hadoop DistCp


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

### <a name="apache-hive"></a>Apache Hive

Magazyn metadanych hive można migrować za pomocą skryptów lub przy użyciu funkcji replikacji bazy danych.

#### <a name="hive-metastore-migration-using-scripts"></a>Migracja magazynu metadanych hive za pomocą skryptów

1. Generowanie DDLs Hive z na Magazyn metadanych Hive lokalnie. Ten krok można wykonać przy użyciu [skryptu powłoki systemowej otoki](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md).
1. Edytuj wygenerowany kod DDL, aby zastąpić adres url systemu HDFS przy użyciu adresów URL usługi ADLS/WASB/ABFS.
1. Uruchom zaktualizowane DDL na Magazyn metadanych z klastra HDInsight.
1. Upewnij się, że wersja magazynu metadanych Hive jest zgodna między lokalizacją lokalną i chmurą.

#### <a name="hive-metastore-migration-using-db-replication"></a>Migracja magazynu metadanych hive przy użyciu replikacji bazy danych

- Konfigurowanie replikacji bazy danych między Magazyn metadanych Hive lokalnej bazy danych i magazynu metadanych HDInsight bazy danych.
- Użyj "Hive MetaTool", aby zastąpić adres url systemu HDFS ADLS/WASB/ABFS adresów URL, na przykład:

```bash
./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
```

### <a name="apache-ranger"></a>Apache Ranger

- Eksportuj zasady platformy Ranger w środowisku lokalnym do plików xml.
- Przekształć w środowisku lokalnym określonych ścieżek opartych na systemie plików HDFS do WASB/Azure Data Lake Store przy użyciu narzędzia, takiego jak XSLT.
- Importowanie zasad do systemem HDInsight platformy Ranger.

## <a name="next-steps"></a>Kolejne kroki

Przeczytaj następny artykuł w tej serii:

- [Zabezpieczenia i metodyki DevOps najlepsze rozwiązania dotyczące lokalnych do migracji usługi Azure HDInsight Hadoop](apache-hadoop-on-premises-migration-best-practices-security-devops.md)
