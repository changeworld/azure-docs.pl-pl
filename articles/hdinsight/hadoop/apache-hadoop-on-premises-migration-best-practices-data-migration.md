---
title: 'Migracja danych: lokalna usługa Apache Hadoop do usługi Azure HDInsight'
description: Poznaj najlepsze rozwiązania dotyczące migracji danych podczas migracji lokalnych klastrów Hadoop do usługi Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: ashishth
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: 41112359408497d84243ed9bb06f396acf008dc5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74666005"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---data-migration-best-practices"></a>Migrowanie lokalnych klastrów Apache Hadoop do usługi Azure HDInsight — najważniejsze wskazówki dotyczące migracji danych

Ten artykuł zawiera zalecenia dotyczące migracji danych do usługi Azure HDInsight. Jest to część serii, która zapewnia najlepsze rozwiązania ułatwiające migrację lokalnych systemów Apache Hadoop do usługi Azure HDInsight.

## <a name="migrate-on-premises-data-to-azure"></a>Migrowanie danych lokalnych na platformę Azure

Istnieją dwie główne opcje migracji danych z lokalnego środowiska platformy Azure:

* Przesyłanie danych przez sieć za pomocą protokołu TLS
    * Przez Internet — można przesyłać dane do magazynu platformy Azure za pośrednictwem zwykłego połączenia internetowego przy użyciu jednego z kilku narzędzi, takich jak: Azure Storage Explorer, AzCopy, Azure Powershell i Azure CLI. Aby uzyskać więcej informacji, zobacz [Przenoszenie danych do i z usługi Azure Storage](../../storage/common/storage-moving-data.md).

    * Trasa ekspresowa — usługa ExpressRoute to usługa platformy Azure, która umożliwia tworzenie prywatnych połączeń między centrami danych firmy Microsoft a infrastrukturą, która znajduje się w twoim lokalinie lub w zakładzie kolokacji. Połączenia usługi ExpressRoute nie przechodzą przez publiczny Internet i oferują większe bezpieczeństwo, niezawodność i szybkość przy niższych opóźnieniach niż typowe połączenia przez Internet. Aby uzyskać więcej informacji, zobacz [Tworzenie i modyfikowanie obwodu usługi ExpressRoute](../../expressroute/expressroute-howto-circuit-portal-resource-manager.md).

    * Data Box transfer danych online — data box edge i data box gateway to produkty do transferu danych online, które działają jako bramy magazynu sieciowego do zarządzania danymi między witryną a platformą Azure. Data Box Edge, lokalne urządzenie sieciowe, przesyła dane do i z platformy Azure i używa obliczeń brzegowych obsługujących sztuczną inteligencję (AI) do przetwarzania danych. Data Box Gateway to urządzenie wirtualne z możliwościami bramy magazynu. Aby uzyskać więcej informacji, zobacz [Dokumentacja pola danych platformy Azure — transfer online](https://docs.microsoft.com/azure/databox-online/).

* Dane wysyłki w trybie offline

    Data Box transfer danych w trybie offline — data box, Data Box Disk i Data Box Heavy urządzenia ułatwiają przesyłanie dużych ilości danych na platformę Azure, gdy sieć nie jest opcją. Te urządzenia do transferu danych w trybie offline są dostarczane między organizacją a centrum danych platformy Azure. Używają szyfrowania AES, aby chronić dane podczas przesyłania, i przechodzą dokładny proces dezynfekcji po przesłaniu, aby usunąć dane z urządzenia. Aby uzyskać więcej informacji na temat urządzeń do transferu w trybie offline pola danych, zobacz [Dokumentacja pola danych platformy Azure — Transfer w trybie offline](https://docs.microsoft.com/azure/databox/). Aby uzyskać więcej informacji na temat migracji klastrów Programu Hadoop, zobacz [Udostępnianie z lokalnego magazynu HDFS do usługi Azure Storage za pomocą usługi Azure Data Box.](../../storage/blobs/data-lake-storage-migrate-on-premises-hdfs-cluster.md)

Poniższa tabela ma przybliżony czas trwania transferu danych na podstawie woluminu danych i przepustowości sieci. Użyj pola Dane, jeśli migracja danych ma potrwać dłużej niż trzy tygodnie.

|Ilość danych|Przepustowość sieci||||
|---|---|---|---|---|
|| **45 Mb/s (T3)**|**100 Mb/s**|**1 Gb/s**|**10 Gb/s**|
|1 TB|2 dni|1 dzień| 2 godziny|14 min.|
|10 TB|22 dni|10 dni|1 dzień|2 godziny|
|35 TB|76 dni|34 dni|3 dni|8 godzin|
|80 TB|173 dni|78 dni|8 dni|19 godz.|
|100 TB|216 dni|97 dni|10 dni|1 dzień|
|200 TB|1 rok|194 dni|19 dni|2 dni|
|500 TB|3 lata|1 rok|49 dni|5 dni|
|1 pb|6 lat|3 lata|97 dni|10 dni|
|2 pb|12 lat|5 lat|194 dni|19 dni|

Narzędzia natywne dla platformy Azure, takie jak Apache Hadoop DistCp, Azure Data Factory i AzureCp, mogą być używane do przesyłania danych za pośrednictwem sieci. Narzędzie innej firmy WANDisco może być również używane w tym samym celu. Apache Kafka Mirrormaker i Apache Sqoop mogą być używane do ciągłego przesyłania danych z lokalnych systemów magazynu platformy Azure.

## <a name="performance-considerations-when-using-apache-hadoop-distcp"></a>Zagadnienia dotyczące wydajności podczas korzystania z Apache Hadoop DistCp

DistCp to projekt Apache, który używa zadania MapReduce Map do przesyłania danych, obsługi błędów i odzyskiwania po tych błędach. Przypisuje listę plików źródłowych do każdego zadania Mapy. Zadanie Mapa następnie kopiuje wszystkie przypisane pliki do miejsca docelowego. Istnieje kilka technik, które mogą poprawić wydajność DistCp.

### <a name="increase-the-number-of-mappers"></a>Zwiększenie liczby mapperów

DistCp próbuje utworzyć zadania mapy, tak aby każdy z nich kopiuje mniej więcej taką samą liczbę bajtów. Domyślnie zadania DistCp używają 20 maperów. Użycie większej liczby mapperów dla Distcp (z parametrem "m" w wierszu polecenia) zwiększa równoległość podczas procesu transferu danych i zmniejsza długość transferu danych. Istnieją jednak dwie rzeczy, które należy wziąć pod uwagę przy jednoczesnym zwiększeniu liczby mapperów:

* Najniższa szczegółowość DistCp to pojedynczy plik. Określenie liczby mapperów więcej niż liczba plików źródłowych nie pomaga i będzie marnować dostępne zasoby klastra.

* Należy wziąć pod uwagę dostępne pamięci przędzy w klastrze, aby określić liczbę mapperów. Każde zadanie mapy jest uruchamiane jako kontener przędzy. Przy założeniu, że żadne inne duże obciążenia są uruchomione w klastrze, liczba mapperów można \* określić za pomocą następującej formuły: m = (liczba węzłów procesu roboczego pamięci YARN dla każdego węzła procesu roboczego) / rozmiar kontenera YARN. Jeśli jednak inne aplikacje używają pamięci, wybierz opcję używania tylko części pamięci YARN dla zadań DistCp.

### <a name="use-more-than-one-distcp-job"></a>Użyj więcej niż jednego zadania DistCp

Gdy rozmiar zestawu danych do przeniesienia jest większy niż 1 TB, należy użyć więcej niż jednego zadania DistCp. Użycie więcej niż jednego zadania ogranicza wpływ błędów. Jeśli jakiekolwiek zadanie nie powiedzie się, wystarczy ponownie uruchomić to zadanie, a nie wszystkie zadania.

### <a name="consider-splitting-files"></a>Rozważ podział plików

Jeśli istnieje niewielka liczba dużych plików, należy rozważyć podzielenie ich na 256 MB fragmentów plików, aby uzyskać więcej potencjalnych współbieżności z więcej mappers.

### <a name="use-the-strategy-command-line-parameter"></a>Użyj parametru wiersza polecenia "strategia"

Należy `strategy = dynamic` rozważyć użycie parametru w wierszu polecenia. Domyślną wartością `strategy` parametru jest `uniform size`, w którym to przypadku każda mapa kopiuje mniej więcej taką samą liczbę bajtów. Po zmianie tego `dynamic`parametru na plik aukcji jest podzielony na kilka "plików fragmentów". Liczba plików fragmentów jest wielokrotnością liczby map. Każdemu zadaniu mapy jest przypisywany jeden z plików fragmentów. Po przetworzeniu wszystkich ścieżek we fragmencie bieżący fragment jest usuwany i uzyskuje się nowy fragment. Proces jest kontynuowany, dopóki nie są dostępne żadne żadne fragmenty. To "dynamiczne" podejście pozwala szybciej map-zadania zużywają więcej ścieżek niż wolniejsze, przyspieszając w ten sposób zadanie DistCp ogólnej.

### <a name="increase-the-number-of-threads"></a>Zwiększenie liczby wątków

Sprawdź, czy `-numListstatusThreads` zwiększenie parametru zwiększa wydajność. Ten parametr kontroluje liczbę wątków do użycia do wyświetlania pliku listy i 40 jest maksymalną wartością.

### <a name="use-the-output-committer-algorithm"></a>Użyj algorytmu zawładniacza danych wyjściowych

Sprawdź, czy `-Dmapreduce.fileoutputcommitter.algorithm.version=2` przekazanie parametru zwiększa wydajność DistCp. Ten algorytm committer danych ma optymalizacje wokół zapisywania plików wyjściowych do miejsca docelowego. Następujące polecenie jest przykładem, który pokazuje użycie różnych parametrów:

```bash
hadoop distcp -Dmapreduce.fileoutputcommitter.algorithm.version=2 -numListstatusThreads 30 -m 100 -strategy dynamic hdfs://nn1:8020/foo/bar wasb://<container_name>@<storage_account_name>.blob.core.windows.net/foo/
```

## <a name="metadata-migration"></a>Migracja metadanych

### <a name="apache-hive"></a>Ul Apache

Magazyn sieciowej bazy można migrować za pomocą skryptów lub przy użyciu replikacji bazy danych.

#### <a name="hive-metastore-migration-using-scripts"></a>Migracja metasklepu hive przy użyciu skryptów

1. Generowanie DDLs hive z lokalnego magazynu hive. Ten krok można wykonać za pomocą [skryptu bash otoki](https://github.com/hdinsight/hdinsight.github.io/blob/master/hive/hive-export-import-metastore.md).
1. Edytuj wygenerowany DDL, aby zastąpić adres URL HDFS adresami URL WASB/ADLS/ABFS.
1. Uruchom zaktualizowaną bibliotekę DDL w magazynie meteorsów z klastra HDInsight.
1. Upewnij się, że wersja magazynu mete hive jest zgodna między lokalnie i w chmurze.

#### <a name="hive-metastore-migration-using-db-replication"></a>Migracja metasklepu hive przy użyciu replikacji bazy danych

- Konfigurowanie replikacji bazy danych między lokalnym magazynem bazy danych hive DB i HDInsight db.
- Użyj "Hive MetaTool", aby zastąpić adres URL HDFS adresami URL WASB/ADLS/ABFS, na przykład:

    ```bash
    ./hive --service metatool -updateLocation hdfs://nn1:8020/ wasb://<container_name>@<storage_account_name>.blob.core.windows.net/
    ```

### <a name="apache-ranger"></a>Apache Ranger

- Eksportowanie lokalnych zasad Ranger do plików xml.
- Przekształcaj ścieżki oparte na systemie HDFS specyficzne dla środowiska lokalnego do wasb/adls za pomocą narzędzia, takiego jak XSLT.
- Zaimportuj zasady do Ranger uruchomiony w programie HDInsight.

## <a name="next-steps"></a>Następne kroki

Przeczytaj następny artykuł z tej serii:

- [Najważniejsze rozwiązania dotyczące zabezpieczeń i programów DevOps dotyczących migracji usługi Azure HDInsight Hadoop w środowisku lokalnym](apache-hadoop-on-premises-migration-best-practices-security-devops.md)
