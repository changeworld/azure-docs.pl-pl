---
title: Rozwiązywanie problemów z wydajnością oprogramowania Apache HBase w usłudze Azure HDInsight
description: Różne wskazówki dotyczące dostrajania wydajności Apache HBase i wskazówki dotyczące uzyskiwania optymalnej wydajności w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 09/24/2019
ms.openlocfilehash: 93698fadcecf190dd8bbc24a9d03978899d3c5e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887159"
---
# <a name="troubleshoot-apache-hbase-performance-issues-on-azure-hdinsight"></a>Rozwiązywanie problemów z wydajnością oprogramowania Apache HBase w usłudze Azure HDInsight

W tym artykule opisano różne wskazówki dotyczące dostrajania wydajności apache HBase i wskazówki dotyczące uzyskiwania optymalnej wydajności w usłudze Azure HDInsight. Wiele z tych wskazówek zależy od określonego obciążenia i wzorca odczytu/zapisu/skanowania. Przed zastosowaniem zmian konfiguracji w środowisku produkcyjnym należy dokładnie je przetestować.

## <a name="hbase-performance-insights"></a>Szczegółowe informacje o wydajności bazy danych HBase

Głównym wąskim gardłem w większości obciążeń HBase jest Dziennik zapisu z wyprzedzeniem (WAL). To poważnie wpływa na wydajność zapisu. HDInsight HBase ma oddzielny model obliczeniowy magazynu. Dane są przechowywane zdalnie w usłudze Azure Storage, nawet jeśli maszyny wirtualne obsługują serwery regionu. Do niedawna wal został również napisany do usługi Azure Storage. W umiań HD, to zachowanie wzmocniło to wąskie gardło. Funkcja [Przyspieszone zapisy](./apache-hbase-accelerated-writes.md) ma na celu rozwiązanie tego problemu. Zapisuje wa w dyskach zarządzanych w usłudze Azure Premium SSD. To ogromnie korzyści wydajności zapisu i pomaga wiele problemów napotykanych przez niektóre obciążenia intensywnie zapisu.

Aby uzyskać znaczną poprawę operacji odczytu, użyj [konta magazynu obiektów blob w wersji Premium](https://azure.microsoft.com/blog/azure-premium-block-blob-storage-is-now-generally-available/) jako magazynu zdalnego. Ta opcja jest możliwa tylko wtedy, gdy funkcja WAL jest włączona.

## <a name="compaction"></a>Zagęszczania

Zagęszczanie jest kolejnym potencjalnym wąskim gardłem, które jest zasadniczo uzgodnione w społeczności. Domyślnie główne zagęszczanie jest wyłączone w klastrach HDInsight HBase. Zagęszczanie jest wyłączone, ponieważ mimo że jest to proces intensywnie korzystający z zasobów, klienci mają pełną elastyczność, aby zaplanować go zgodnie z ich obciążeń. Na przykład mogą zaplanować go poza godzinami szczytu. Ponadto lokalizacja danych nie jest problemem, ponieważ nasz magazyn jest zdalny (wspierany przez usługę Azure Storage) zamiast lokalnego systemu rozproszonych plików (HDFS) usługi Hadoop.

Klienci powinni zaplanować duże zagęszczenie w dogodnie. Jeśli nie wykonają tej konserwacji, zagęszczanie niekorzystnie wpłynie na wydajność odczytu na dłuższą metę.

W przypadku operacji skanowania oznacza opóźnienia, które są znacznie wyższe niż 100 milisekund, powinny być powodem do niepokoju. Sprawdź, czy główne zagęszczenie zostało dokładnie zaplanowane.

## <a name="apache-phoenix-workload"></a>Obciążenie apache Phoenix

Udzielenie odpowiedzi na następujące pytania pomoże Ci lepiej zrozumieć obciążenie apache phoenix:

* Czy wszystkie twoje "odczyty" przekładają się na skany?
    * Jeśli tak, jakie są cechy tych skanów?
    * Czy zoptymalizowałeś schemat tabeli Phoenix pod kątem tych skanów, w tym odpowiedniego indeksowania?
* Czy użyłeś `EXPLAIN` instrukcji, aby zrozumieć plany kwerendy generowane przez "odczyty"?
* Czy twoje zapisy "upsert-selects"?
    * Jeśli tak, będą również robić skany. Oczekiwane opóźnienie skanowania wynosi średnio około 100 milisekund, w porównaniu do 10 milisekund dla punktu dostaje w HBase.  

## <a name="test-methodology-and-metrics-monitoring"></a>Metodologia badań i monitorowanie metryk

Jeśli używasz testów porównawczych, takich jak Yahoo! Cloud Serving Benchmark, JMeter lub Pherf, aby przetestować i dostroić wydajność, upewnij się, że:

- Maszyny klienckie nie stają się wąskim gardłem. Aby to zrobić, sprawdź użycie procesora CPU na komputerach klienckich.

- Konfiguracje po stronie klienta, podobnie jak liczba wątków, są odpowiednio dostrojone, aby nasycić przepustowość klienta.

- Wyniki badań są rejestrowane dokładnie i systematycznie.

Jeśli zapytania nagle zaczął robić znacznie gorzej niż wcześniej, sprawdź potencjalne błędy w kodzie aplikacji. Czy nagle generuje duże ilości nieprawidłowych danych? Jeśli tak, może zwiększyć opóźnienia odczytu.

## <a name="migration-issues"></a>Problemy z migracją

Jeśli przeprowadzasz migrację do usługi Azure HDInsight, upewnij się, że migracja odbywa się systematycznie i dokładnie, najlepiej za pośrednictwem automatyzacji. Unikaj migracji ręcznej. Upewnij się, że:

- Atrybuty tabeli są migrowane dokładnie. Atrybuty mogą obejmować kompresję, filtry kwitnienia i tak dalej.

- Ustawienia soli w tabelach Phoenix są odpowiednio mapowane na nowy rozmiar klastra. Na przykład liczba zasobników soli powinna być wielokrotnością liczby węzłów procesu roboczego w klastrze. I należy użyć wielu, które jest czynnikiem ilości hot spotting.

## <a name="server-side-configuration-tunings"></a>Strojenie konfiguracji po stronie serwera

W hdinsight HBase HFiles są przechowywane w magazynie zdalnym. Gdy istnieje brak pamięci podręcznej, koszt odczytów jest wyższy niż w systemach lokalnych, ponieważ dane w systemach lokalnych są wspierane przez lokalny system HDFS. W większości scenariuszy inteligentne użycie pamięci podręcznej HBase (pamięć podręczna bloku i pamięć podręczna zasobników) jest przeznaczony do obejścia tego problemu. W przypadkach, gdy problem nie jest obchodzony, przy użyciu konta bloku premii blob może pomóc w tym problemie. Sterownik obiektów blob usługi Windows Azure Storage `fs.azure.read.request.size` opiera się na niektórych właściwości, takich jak pobieranie danych w blokach na podstawie tego, co określa do odczytu tryb (sekwencyjne i losowe), więc może nadal być wystąpienia wyższych opóźnień z odczytów. Dzięki eksperymentom empirycznym odkryliśmy, że ustawienie`fs.azure.read.request.size`rozmiaru bloku żądania odczytu ( ) na 512 KB i dopasowanie rozmiaru bloku tabel HBase do tego samego rozmiaru daje najlepszy wynik w praktyce.

W przypadku większości klastrów węzłów o dużym `bucketcache` rozmiarze usługa HDInsight HBase udostępnia jako plik na lokalnym `regionservers`dysku SSD premium dołączonym do maszyny wirtualnej, która działa. Zamiast tego użycie pamięci podręcznej poza stertą może zapewnić pewną poprawę. To obejście ma ograniczenie korzystania z dostępnej pamięci i potencjalnie jest mniejsza niż pamięć podręczna oparta na plikach, więc nie zawsze może być najlepszym wyborem.

Oto niektóre z innych konkretnych parametrów, które dostroiliśmy, i które wydawały się pomagać w różnym stopniu:

- Zwiększ `memstore` rozmiar z domyślnego 128 MB do 256 MB. Zazwyczaj to ustawienie jest zalecane w przypadku scenariuszy zapisu ciężkich.

- Zwiększ liczbę wątków przeznaczonych do zagęszczania, z domyślnego ustawienia **1** do **4**. To ustawienie jest istotne, jeśli obserwujemy częste drobne zagęszczenia.

- Należy `memstore` unikać blokowania opróżniania ze względu na limit sklepu. Aby zapewnić ten bufor, należy zwiększyć `Hbase.hstore.blockingStoreFiles` ustawienie do **100**.

- Aby kontrolować spłukiwanie, należy użyć następujących ustawień:

    - `Hbase.regionserver.maxlogs`: **140** (pozwala uniknąć spłukiwania z powodu limitów WAL)

    - `Hbase.regionserver.global.memstore.lowerLimit`: **0,55**

    - `Hbase.regionserver.global.memstore.upperLimit`: **0,60**

- Konfiguracje specyficzne dla phoenix do strojenia puli wątków:

    - `Phoenix.query.queuesize`: **10000**

    - `Phoenix.query.threadpoolsize`: **512**

- Inne konfiguracje specyficzne dla Phoenix:

    - `Phoenix.rpc.index.handler.count`: **50** (jeśli istnieje duże lub wiele odnośnych indeksów)

    - `Phoenix.stats.updateFrequency`: **1 godzina**

    - `Phoenix.coprocessor.maxmetadatacachetimetolivems`: **1 godzina**

    - `Phoenix.coprocessor.maxmetadatacachesize`: **50 MB**

- Limity czasu RPC: **3 minuty**

   - Limity czasu RPC obejmują limit czasu HBase RPC, limit czasu skanera klienta HBase i limit czasu zapytania Phoenix. 
   - Upewnij się, `hbase.client.scanner.caching` że parametr jest ustawiony na tę samą wartość zarówno na końcu serwera, jak i na końcu klienta. Jeśli nie są takie same, to ustawienie prowadzi do błędów `OutOfOrderScannerException`po stronie klienta, które są związane z . To ustawienie powinno być ustawione na niską wartość dla dużych skanów. Ustawiliśmy tę wartość na **100**.

## <a name="other-considerations"></a>Inne zagadnienia

Poniżej przedstawiono dodatkowe parametry, które należy wziąć pod uwagę:

- `Hbase.rs.cacheblocksonwrite`– domyślnie w hdi, to ustawienie jest ustawione na **true**.

- Ustawienia, które umożliwiają odroczenie drobnego zagęszczania na później.

- Ustawienia eksperymentalne, takie jak dostosowywanie procentów kolejek zarezerwowanych dla żądań odczytu i zapisu.

## <a name="next-steps"></a>Następne kroki

Jeśli problem pozostaje nierozwiązany, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

- Połącz [@AzureSupport](https://twitter.com/azuresupport)się z . Jest to oficjalne konto platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączy społeczność platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

- Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Subskrypcja platformy Microsoft Azure obejmuje dostęp do obsługi zarządzania subskrypcjami i rozliczeń, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
