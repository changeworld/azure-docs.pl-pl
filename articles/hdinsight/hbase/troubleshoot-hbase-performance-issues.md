---
title: Rozwiązywanie problemów z wydajnością oprogramowania Apache HBase w usłudze Azure HDInsight
description: Różne wskazówki i wskazówki dotyczące dostrajania wydajności usługi Apache HBase w celu uzyskania optymalnej wydajności w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: c67f21a6ed8a7697977bb7737f0e46348efb2530
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2019
ms.locfileid: "71266655"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Rozwiązywanie problemów z wydajnością oprogramowania Apache HBase w usłudze Azure HDInsight

W tym dokumencie opisano różne wskazówki i wskazówki dotyczące dostrajania wydajności usługi Apache HBase w celu uzyskania optymalnej wydajności w usłudze Azure HDInsight. Wiele z tych wskazówek zależą od określonego obciążenia i wzorca odczytu/zapisu/skanowania. Należy dokładnie przetestować zmiany konfiguracji przed zastosowaniem ich w środowisku produkcyjnym.

## <a name="hdinsight-hbase-performance-insights"></a>Szczegółowe informacje o wydajności usługi HDInsight HBase

Największe wąskie gardło w większości obciążeń HBase to zapis z wyprzedzeniem (WAL). Znacznie wpływa na wydajność zapisu. Usługa HDInsight HBase ma rozdzielony model magazynu — to oznacza, że dane są przechowywane zdalnie w usłudze Azure Storage, ale serwery regionów są hostowane na maszynach wirtualnych. Do czasu ostatniego dziennik zapisu w przód został również zapisany w usłudze Azure Storage w taki sposób, aby wzmocnić ten wąskie gardło w przypadku usługi HDInsight. Funkcja [szybsze zapisy](./apache-hbase-accelerated-writes.md) została zaprojektowana w celu rozwiązania tego problemu, przez zapisanie dziennika zapisu z wyprzedzeniem do dysków zarządzanych w usłudze Azure Premium SSD. Dzięki temu można w znacznym stopniu zapisywać wydajność i pomóc w wielu problemach spowodowanych przez niektóre obciążenia intensywnie korzystające z zapisu.

Użyj [konta magazynu blokowych obiektów blob w warstwie Premium](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) jako magazynu zdalnego, aby uzyskać znaczne przyspieszenie operacji odczytu. Ta opcja jest możliwa tylko wtedy, gdy jest włączona funkcja zapisu dziennika z wyprzedzeniem.

## <a name="compaction"></a>Kompaktowania

Kompaktowanie to kolejna potencjalne wąskie gardło w społeczności.  Domyślnie kompaktowanie główne jest wyłączone w klastrach usługi HDInsight HBase. Wynika to z faktu, że jest to proces intensywnie obciążający zasoby, chcemy zapewnić klientom pełną elastyczność, aby zaplanować ją zgodnie z ich charakterystykami obciążeń — czyli w godzinach szczytu. Usługa Storage jest również oparta na lokalizacji zdalnej (za pomocą usługi Azure Storage), a w przeciwieństwie do lokalnego systemu plików HDFS, który jest często używany w większości wystąpień Premium, nie jest to problem, który jest jednym z głównych celów głównych kompaktowania.

Przyjęto założenie, że klient będzie miał zadbać o zaplanowanie poważnej kompaktowania w miarę ich wygody. Jeśli ta konserwacja nie zostanie wykonana, kompaktowanie znacznie wpływa na wydajność odczytu w długim przebiegu.

W szczególności operacje skanowania oznaczają opóźnienia znacznie wyższe niż 100 ms powinny być przyczyną problemu. Sprawdź, czy Poprzednia wersja kompaktowania została dokładnie zaplanowana.

## <a name="know-your-apache-phoenix-workload"></a>Informacje o obciążeniu Apache Phoenix

Udzielenie odpowiedzi na następujące pytania pomoże Ci lepiej zrozumieć Apache Phoenix obciążenie:

* Czy wszystkie Twoje "odczyty" są używane do skanowania?
    * Jeśli tak, jakie są cechy tych skanów?
    * Czy schemat tabeli platformy Phoenix został zoptymalizowany dla tych skanów, w tym do odpowiedniego indeksowania?
* Wykorzystano instrukcję `EXPLAIN`, aby zrozumieć plany zapytań, które generują dane.
* Czy Twoje zapisy to "upsert-Select"?
    * Jeśli tak, również będą wykonywały skanowanie. Oczekiwane opóźnienie skanowania jest rzędu o wartości 100 ms średnio, w przeciwieństwie do 10 ms dla punktu w HBase.  

## <a name="test-methodology-and-metrics-monitoring"></a>Metodologia testowania i monitorowanie metryk

Jeśli używasz testów porównawczych, takich jak YCSB, JMeter lub Pherf do testowania i dostrajania wydajności, upewnij się, że:

1. Komputery klienckie nie stają się wąskim gardłem (Sprawdź użycie procesora na komputerach klienckich).

1. Konfiguracje po stronie klienta, takie jak liczba wątków itd., są odpowiednio dopasowane, aby zwiększyć nasycenie przepustowości klienta.

1. Wyniki testu są rejestrowane dokładnie i systematycznie.

Jeśli zapytania nagle rozpoczęły działanie znacznie gorszy niż wcześniej — Sprawdź, czy nie występują potencjalne błędy w kodzie aplikacji — czy nagle generuje duże ilości nieprawidłowych danych, a tym samym znacząco wzrasta opóźnień odczytu?

## <a name="migration-issues"></a>Problemy z migracją

W przypadku migrowania do usługi Azure HDInsight upewnij się, że migracja jest przeprowadzana systematycznie i dokładnie, najlepiej za pośrednictwem automatyzacji. Unikaj ręcznej migracji. Upewnij się, że:

1. Atrybuty tabeli — takie jak kompresja, filtry zakwiatowe i tak dalej, powinny być migrowane dokładnie.

1. W przypadku tabel w Phoenix ustawienia solenia powinny być odpowiednio mapowane do nowego rozmiaru klastra. Na przykład liczba przedziałów soli jest zalecana jako wielokrotność liczby węzłów procesu roboczego w klastrze — konkretna część jest czynnikiem obserwowanej gorącej wykrywania trendów.  

## <a name="server-side-config-tunings"></a>Dostrajania konfiguracji po stronie serwera

W usłudze HDInsight HBase HFiles są przechowywane w magazynie zdalnym — w takiej sytuacji w przypadku braku pamięci podręcznej koszt operacji odczytu będzie miał czas wyższy niż systemy Premium, które mają dane obsługiwane przez lokalny system plików HDFS, z powodu opóźnienia sieci. W przypadku większości scenariuszy inteligentne użycie pamięci podręcznej HBase (pamięć podręczna bloków i pamięć podręczna zasobników) zostało zaprojektowane w celu obejścia tego problemu. Jednak pozostaną sporadyczne przypadki, w których może to być problem dla klienta. Użycie konta blokowych obiektów BLOB w warstwie Premium znacznie pomogło. Jednak za pomocą obiektu BLOB WASB (Windows Azure Storage Driver) polegają na niektórych właściwościach, takich jak `fs.azure.read.request.size`, aby pobrać dane w blokach na podstawie tego, co jest określane jako tryb odczytu (sekwencyjne i losowe), możemy nadal zobaczyć wystąpienia większej liczby opóźnień z odczytami. Znaleźliśmy za pomocą eksperymentów doświadczalnych, które powodują ustawienie rozmiaru bloku żądania odczytu (`fs.azure.read.request.size`) na 512 KB i dopasowanie rozmiaru bloku tabel HBase, aby były takie same jak najlepsze wyniki.

Usługa HDInsight HBase dla większości klastrów węzłów o dużej wielkości zawiera `bucketcache` jako plik na lokalnym dysku SSD podłączonym do maszyny wirtualnej, w której jest uruchomiony `regionservers`. Czasami korzystanie z pamięci podręcznej sterty może spowodować pewne ulepszenia. Jest to ograniczenie wykorzystania dostępnej pamięci i potencjalnie mniejszej niż rozmiar pamięci podręcznej opartej na plikach, dzięki czemu może to nie zawsze być najlepszy wybór.

Niektóre z innych określonych parametrów, które zostały dostrojone, były przydatne w różnych stopniach z pewnymi uzasadnieniami:

1. Zwiększ rozmiar `memstore` z domyślnych 128 MB do 256 MB — to ustawienie jest zazwyczaj zalecane w przypadku dużego scenariusza zapisu.

1. Zwiększenie liczby wątków przeznaczonych do kompaktowania — od wartości domyślnej z przeznaczeniem od 1 do 4. To ustawienie ma zastosowanie, jeśli obserwujemy często drobne kompakty.

1. Unikaj blokowania opróżniania `memstore` z powodu limitu magazynu. `Hbase.hstore.blockingStoreFiles` można zwiększyć do 100, aby zapewnić ten bufor.

1. Dla kontrolek opróżniania można rozmieścić wartości domyślne w następujący sposób:

    1. `Hbase.regionserver.maxlogs` można upped do 140 z 32 (unikając opróżniania z powodu limitów WAL).

    1. `Hbase.regionserver.global.memstore.lowerLimit` = 0,55.

    1. `Hbase.regionserver.global.memstore.upperLimit` = 0,60.

1. Konfiguracje związane z Phoenix dla dostrajania puli wątków:

    1. `Phoenix.query.queuesize` można zwiększyć do 10000.

    1. `Phoenix.query.threadpoolsize` można zwiększyć do 512.

1. Inne konfiguracje specyficzne dla Phoenix:

    1. `Phoenix.rpc.index.handler.count` można ustawić na 50, jeśli mamy duże lub liczne wyszukiwania indeksów.

    1. `Phoenix.stats.updateFrequency` — wartość domyślna to 1 godzina z wartości domyślnej wynoszącej 15 minut.

    1. `Phoenix.coprocessor.maxmetadatacachetimetolivems` — może być upped do 1 godziny w ciągu 30 minut.

    1. `Phoenix.coprocessor.maxmetadatacachesize` — można upped do 50 MB z 20 MB.

1. Limity czasu wywołań RPC — HBase limit czasu zdalnego wywoływania procedur (HBase) i limit czasu zapytania w Phoenix można zwiększyć do 3 minut. Należy pamiętać, że parametr `hbase.client.scanner.caching` ma ustawioną wartość zgodną z końcem serwera i końcem klienta. W przeciwnym razie to ustawienie prowadzi do błędów związanych z `OutOfOrderScannerException` na końcu klienta. Dla tego ustawienia należy ustawić niską wartość dla dużych skanów. Ta wartość jest ustawiana na 100.

## <a name="other-considerations"></a>Inne zagadnienia

Niektóre inne parametry, które należy wziąć pod uwagę w celu dostrajania:

1. `Hbase.rs.cacheblocksonwrite` — to ustawienie ma wartość true domyślnie w HDI.

1. Ustawienia, które zezwalają na odroczenie mniejszych kompaktowania w przyszłości.

1. Ustawienia eksperymentalne, takie jak dopasowanie procentu kolejek zarezerwowanych dla żądań odczytu i zapisu.

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

- Połącz się z [@AzureSupport](https://twitter.com/azuresupport) — oficjalnego konta Microsoft Azure w celu poprawy obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

- Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
