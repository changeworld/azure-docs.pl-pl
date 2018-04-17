---
title: Monitorowanie wydajności klastra - Azure HDInsight | Dokumentacja firmy Microsoft
description: Jak monitorować klastra usługi HDInsight na pojemność i wydajność.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: maxluk
manager: jhubbard
editor: cgronlun
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 09/27/2017
ms.author: maxluk
ms.openlocfilehash: 9bf49631da58de86ffa1881bca976cab86677805
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="monitor-cluster-performance"></a>Monitorowanie wydajności klastra

Monitorowanie kondycji i wydajności klastra usługi HDInsight jest istotne dla maksymalnej wydajności i wykorzystania zasobów. Monitorowanie może też pomóc Ci adres kodu lub błędy konfiguracji klastra.

W poniższych sekcjach opisano, jak zoptymalizować klastra ładowania, YARN kolejki wydajności i dostępności magazynu.

## <a name="cluster-loading"></a>Ładowanie klastra

Klastry Hadoop powinien równoważenie ładowania w węzłach klastra. Ta równoważenia zapobiega zadań przetwarzania są ograniczone przez ilość pamięci RAM, Procesor lub zasoby dyskowe.

Aby uzyskać ogólny wygląd w węzłach klastra i ich ładowania, zaloguj się do [Interfejsu sieci Web Ambari](hdinsight-hadoop-manage-ambari.md), a następnie wybierz pozycję **hostów** kartę. Hosty są wyświetlane według nazwy FQDN. Stan działania każdy host jest wyświetlany przez wskaźnik kolorowe kondycji:

| Kolor | Opis |
| --- | --- |
| Czerwony | Co najmniej jeden składnik główny na hoście jest wyłączony. Przesuń, aby wyświetlić etykietkę narzędzia list składniki dotknięte problemem. |
| Orange | Co najmniej jeden podrzędny składnika na hoście jest wyłączony. Przesuń, aby wyświetlić etykietkę narzędzia list składniki dotknięte problemem. |
| Żółty | Ambari serwer nie otrzymał pulsu z hosta dla więcej niż trzy minuty. |
| Zielony | Normalny uruchomiona stanu. |

Widoczny będzie również kolumn przedstawiający liczby rdzeni i ilości pamięci RAM dla każdego hosta, a średnie wykorzystanie dysku i obciążenia.

![Karta hosty](./media/hdinsight-key-scenarios-to-monitor/hosts-tab.png)

Wybierz jedno z nazwy hostów dla szczegółowe przyjrzeć się składniki działające na tym hoście i ich metryki. Metryki są wyświetlane jako możliwy osi czasu procesora CPU użycia, obciążenia, użycie dysku, pamięć, użycie sieci i liczby procesów.

![Szczegóły hosta](./media/hdinsight-key-scenarios-to-monitor/host-details.png)

Zobacz [Zarządzanie klastrami usługi HDInsight przy użyciu interfejsu użytkownika sieci Web Ambari](hdinsight-hadoop-manage-ambari.md) szczegółowe informacje na temat alertów i wyświetlania metryk.

## <a name="yarn-queue-configuration"></a>YARN kolejki konfiguracji

Hadoop ma różnych usług uruchomionych w rozproszonej platformy. YARN (jeszcze inny zasób moduł negocjowania) koordynuje tych usług, przydzielania zasobów klastra i zarządza dostępem do wspólnego zestawu danych.

YARN dwóch obowiązki JobTracker, zarządzanie zasobami oraz zadania planowania/monitorowania, są podzielone na dwie demonów: globalne ResourceManager i dla każdej aplikacji ApplicationMaster (AM).

Jest ResourceManager *czysty harmonogramu*i wyłącznie rozstrzyga o kolejności przetwarzania dostępnych zasobów między wszystkich współzawodniczącym aplikacjom. ResourceManager gwarantuje, że wszystkie zasoby są zawsze w użytkowania, optymalizacji dla różnych stałe, takie jak umowy SLA, pojemność gwarantuje i tak dalej. ApplicationMaster negocjuje zasobów z ResourceManager i współdziała z NodeManager(s) do wykonywania i monitorować kontenerów i ich wykorzystania zasobów.

Gdy wielu dzierżawców współużytkować duży klaster, brak konkurowanie o zasoby klastra. CapacityScheduler jest podłączany harmonogramu, która pomaga w zasobie udostępniania przez kolejkowanie zamówienia. Obsługuje również CapacityScheduler *hierarchiczna kolejek* aby upewnić się, że zasoby są wspólne dla kolejki podrzędne organizacji, przed kolejek inne aplikacje mogą korzystać z bezpłatnych zasobów.

YARN pozwala przydzielać zasoby te kolejki i pokazuje, czy wszystkie dostępne zasoby są przypisane. Aby wyświetlić informacje o Twojej kolejek, zaloguj się do Interfejsu sieci Web Ambari, a następnie wybierz **menedżera kolejek YARN** z górnego menu.

![YARN menedżera kolejek](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager.png)

Strona menedżera kolejek YARN to lista z kolejek po lewej stronie, oraz procent pojemności przypisane do każdego.

![Strona szczegółów YARN menedżera kolejek](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Aby bardziej szczegółowe przyjrzeć się z kolejek na pulpicie nawigacyjnym Ambari, wybierz **YARN** usługi na liście z lewej strony. Następnie w obszarze **szybkie linki** menu rozwijanego wybierz **interfejsu użytkownika Menedżera ResourceManager** poniżej z aktywnym węźle.

![Łącze menu ResourceManager interfejsu użytkownika](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

W interfejsie użytkownika ResourceManager wybierz **harmonogramu** z menu po lewej stronie. Zobacz listę z kolejek poniżej *kolejki aplikacji*. W tym miejscu zobaczysz pojemności używane dla każdego z kolejek, jak również zadania są dystrybuowane między nimi, i czy żadnego zadania są ograniczone przez zasób.

![Łącze menu ResourceManager interfejsu użytkownika](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui.png)

## <a name="storage-throttling"></a>Ograniczanie magazynu

Klaster wąskie gardło może się zdarzyć na poziomie magazynu. Ten typ wąskie gardło będące przyczyną jest najczęściej ze względu na *blokuje* wejścia/wyjścia (We/Wy) działań, które się zdarzyć, gdy uruchomionych zadań wysłać więcej we/wy niż Usługa magazynu może obsłużyć. To blokowanie tworzy kolejkę żądań We/Wy oczekujących na przetworzenie dopiero po przetworzeniu bieżącego systemu IOs. Bloki mają *ograniczania magazynu*, która nie jest ograniczenie fizycznej, ale raczej ograniczenie nałożone przez usługę magazynu przez umowy dotyczącej poziomu usług (SLA). Ten limit zapewnia, że nie jednego klienta lub dzierżawcy nie zajmuje całej usługi. Umowa SLA ogranicza liczbę systemu IOs na sekundę (IOPS) dla usługi Azure Storage — Aby uzyskać więcej informacji, zobacz [cele dotyczące wydajności i skalowalności magazynu Azure](https://docs.microsoft.com/azure/storage/storage-scalability-targets).

Jeśli używasz usługi Azure Storage, aby uzyskać informacje dotyczące monitorowania problemów związanych z magazynu, w tym ograniczania przepustowości, zobacz [monitorowanie, diagnozowanie i rozwiązywanie problemów z usługi Magazyn Microsoft Azure](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Magazynu zapasowego klastra w przypadku usługi Azure Data Lake przechowywania (ADLS), przepustowości sieci najprawdopodobniej z powodu ograniczeń przepustowości. Ograniczanie w takim przypadku można zidentyfikować przez obserwowania błędy ograniczania przepustowości w dziennikach zadań. ADLS w sekcji ograniczania przepustowości dla odpowiednią usługę w tych artykułach:

* [Wskazówki dotyczące dostrajania wydajności oprogramowania Hive w usługach HDInsight i Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Wskazówki dotyczące dostrajania wydajności oprogramowania MapReduce w usługach HDInsight i Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Wskazówki dotyczące dostrajania wydajności oprogramowania Storm w usługach HDInsight i Azure Data Lake Store](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="next-steps"></a>Kolejne kroki

Odwiedź poniższe łącza, aby uzyskać więcej informacji na temat rozwiązywania problemów i monitorowania klastrów:

* [Analizowanie dzienników usługi HDInsight](hdinsight-debug-jobs.md)
* [Debugowanie aplikacji przy użyciu dzienników YARN](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Włącz zrzuty stosu dla usługi Hadoop w HDInsight opartych na systemie Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
