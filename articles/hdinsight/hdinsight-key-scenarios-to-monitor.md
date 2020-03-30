---
title: Monitorowanie wydajności klastra — usługa Azure HDInsight
description: Jak monitorować kondycję i wydajność klastrów Apache Hadoop w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/09/2020
ms.openlocfilehash: 75ac5a7fc352f877573d79a004d8da761c6f1cef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082884"
---
# <a name="monitor-cluster-performance-in-azure-hdinsight"></a>Monitorowanie wydajności klastra w usłudze Azure HDInsight

Monitorowanie kondycji i wydajności klastra HDInsight ma zasadnicze znaczenie dla utrzymania optymalnej wydajności i wykorzystania zasobów. Monitorowanie może również pomóc w wykrywaniu i rozwiązywaniu błędów konfiguracji klastra i problemów z kodem użytkownika.

W poniższych sekcjach opisano sposób monitorowania i optymalizacji obciążenia klastrów, apache Hadoop kolejki YARN i wykrywania problemów ograniczania przepustowości magazynu.

## <a name="monitor-cluster-load"></a>Monitorowanie obciążenia klastra

Klastry Hadoop może zapewnić najbardziej optymalną wydajność, gdy obciążenie klastra jest równomiernie rozłożone na wszystkie węzły. Dzięki temu zadania przetwarzania można uruchamiać bez ograniczania przez zasoby pamięci RAM, procesora CPU lub dysku w poszczególnych węzłach.

Aby uzyskać spojrzenie na węzły klastra i ich ładowanie, zaloguj się do interfejsu [użytkownika sieci Web Ambari,](hdinsight-hadoop-manage-ambari.md)a następnie wybierz kartę **Hosty.** Twoi gospodarze są wymienieni według w pełni kwalifikowanych nazw domen. Stan działania każdego hosta jest wyświetlany przez kolorowy wskaźnik kondycji:

| Kolor | Opis |
| --- | --- |
| Red | Co najmniej jeden składnik główny na hoście jest w dół. Umieść wskaźnik myszy, aby wyświetlić etykietkę narzędzia zawierającą listę składników, których dotyczy problem. |
| Orange | Co najmniej jeden składnik pomocniczy na hoście jest w dół. Umieść wskaźnik myszy, aby wyświetlić etykietkę narzędzia zawierającą listę składników, których dotyczy problem. |
| Yellow | Ambari Server nie otrzymał pulsu od hosta przez ponad 3 minuty. |
| Zielony | Normalny stan pracy. |

Zobaczysz również kolumny pokazujące liczbę rdzeni i ilość pamięci RAM dla każdego hosta oraz średnią użycia i obciążenia dysku.

![Apache Ambari hostuje zakładkę przegląd](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-hosts-tab.png)

Wybierz dowolną z nazw hostów, aby uzyskać szczegółowe spojrzenie na składniki uruchomione na tym hoście i ich metryki. Metryki są wyświetlane jako możliwość wyboru osi czasu użycia procesora CPU, obciążenia, użycia dysku, użycia pamięci, użycia sieci i liczby procesów.

![Apache Ambari — omówienie szczegółów hosta](./media/hdinsight-key-scenarios-to-monitor/apache-ambari-host-details.png)

Zobacz [Zarządzanie klastrami HDInsight przy użyciu interfejsu użytkownika sieci Web Apache Ambari,](hdinsight-hadoop-manage-ambari.md) aby uzyskać szczegółowe informacje na temat ustawiania alertów i wyświetlania metryk.

## <a name="yarn-queue-configuration"></a>Konfiguracja kolejki YARN

Hadoop ma różne usługi działające na swojej rozproszonej platformie. YARN (Jeszcze inny negocjator zasobów) koordynuje te usługi i przydziela zasoby klastra, aby upewnić się, że każde obciążenie jest równomiernie rozłożone w klastrze.

YARN dzieli dwa obowiązki JobTracker, zarządzanie zasobami i planowania/monitorowania zadań, na dwa demony: globalny Menedżer zasobów i dla aplikacji ApplicationMaster (AM).

Menedżer zasobów jest *czystym harmonogramem*i rozstrzyga wyłącznie dostępne zasoby między wszystkimi konkurencyjnymi aplikacjami. Menedżer zasobów zapewnia, że wszystkie zasoby są zawsze w użyciu, optymalizowanie dla różnych stałych, takich jak ula, gwarancje pojemności i tak dalej. ApplicationMaster negocjuje zasoby z Menedżera zasobów i współpracuje z NodeManager(y) do wykonywania i monitorowania kontenerów i ich zużycia zasobów.

Gdy wielu dzierżawców współużytkuje duży klaster, istnieje konkurencja dla zasobów klastra. CapacityScheduler jest pluggable harmonogram, który pomaga w udostępnianiu zasobów przez kolejkowanie żądań. Program CapacityScheduler obsługuje również *kolejki hierarchiczne,* aby zapewnić współdzielenie zasobów między podjechałościami w yskach w organizacji, zanim kolejki innych aplikacji będą mogły korzystać z wolnych zasobów.

YARN pozwala nam przydzielić zasoby do tych kolejek i pokazuje, czy wszystkie dostępne zasoby są przypisane. Aby wyświetlić informacje o kolejkach, zaloguj się do interfejsu użytkownika sieci Web Ambari, a następnie wybierz z górnego menu **Menedżera kolejek YARN.**

![Apache Ambari YARN Menedżer kolejki](./media/hdinsight-key-scenarios-to-monitor/apache-yarn-queue-manager.png)

Strona Menedżer kolejek YARN zawiera listę kolejek po lewej stronie wraz z procentem pojemności przypisanej do każdej z nich.

![Strona szczegółów Menedżera kolejek YARN](./media/hdinsight-key-scenarios-to-monitor/yarn-queue-manager-details.png)

Aby uzyskać bardziej szczegółowe spojrzenie na kolejki, z pulpitu nawigacyjnego Ambari wybierz usługę **YARN** z listy po lewej stronie. Następnie w menu rozwijanym **Szybkie łącza** wybierz pozycję Interfejs użytkownika **Menedżera zasobów** pod aktywnym węzłem.

![Łącza menu interfejsu użytkownika Menedżera zasobów](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu-link.png)

W interfejsie użytkownika Menedżera zasobów wybierz **pozycję Harmonogram** z menu po lewej stronie. Pod pozycją *kolejek aplikacji*zostanie widoczna lista kolejek . W tym miejscu można zobaczyć pojemność używaną dla każdej z kolejek, jak dobrze zadania są dystrybuowane między nimi i czy wszystkie zadania są ograniczone zasobami.

![Apache HAdoop Resource Manager Menu interfejsu użytkownika](./media/hdinsight-key-scenarios-to-monitor/resource-manager-ui-menu.png)

## <a name="storage-throttling"></a>Ograniczanie pamięci masowej

Wąskie gardło wydajności klastra może się zdarzyć na poziomie magazynu. Ten typ wąskiego gardła jest najczęściej ze względu *na blokowanie* operacji wejścia/wyjścia (IO), które zdarzają się, gdy uruchomione zadania wysyłają więcej operacji we/wy niż usługa magazynu może obsłużyć. To blokowanie tworzy kolejkę żądań we/wy oczekujących na przetworzenie, dopóki nie zostaną przetworzone bieżące usługi we/wy. Bloki są spowodowane *ograniczania magazynu*, który nie jest limit fizyczny, ale raczej limit narzucony przez usługę magazynu przez umowę dotyczącą poziomu usług (SLA). Ten limit gwarantuje, że żaden pojedynczy klient lub dzierżawca może zmonopolizować usługę. Umowy SLA ogranicza liczbę we/wy na sekundę (IOPS) dla usługi Azure Storage — aby uzyskać szczegółowe informacje, zobacz [Cele skalowalności i wydajności dla standardowych kont magazynu](../storage/common/scalability-targets-standard-account.md).

Jeśli korzystasz z usługi Azure Storage, aby uzyskać informacje dotyczące monitorowania problemów związanych z magazynem, w tym ograniczania przepustowości, zobacz [Monitorowanie, diagnozowanie i rozwiązywanie problemów z usługą Microsoft Azure Storage](https://docs.microsoft.com/azure/storage/storage-monitoring-diagnosing-troubleshooting).

Jeśli magazyn kopii zapasowej klastra to Usługa Azure Data Lake Storage (ADLS), ograniczanie przepustowości jest najprawdopodobniej spowodowane limitami przepustowości. Ograniczanie w tym przypadku można zidentyfikować, obserwując błędy ograniczania przepustowości w dziennikach zadań. W przypadku usługi ADLS zobacz sekcję ograniczania przepustowości dla odpowiedniej usługi w następujących artykułach:

* [Wskazówki dotyczące dostrajania wydajności dla gałęzi Apache w usłudze HDInsight i usłudze Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-hive.md)
* [Wskazówki dotyczące dostrajania wydajności mapReduce w usłudze HDInsight i usłudze Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-mapreduce.md)
* [Wskazówki dotyczące dostrajania wydajności dla usługi Apache Storm w witrynie HDInsight i usługi Azure Data Lake Storage](../data-lake-store/data-lake-store-performance-tuning-storm.md)

## <a name="troubleshoot-sluggish-node-performance"></a>Rozwiązywanie problemów z powolną wydajnością węzła

W niektórych przypadkach spowolnienie może wystąpić z powodu małej ilości miejsca na dysku w klastrze. Zbadaj za pomocą następujących kroków:

1. Użyj [polecenia ssh,](./hdinsight-hadoop-linux-use-ssh-unix.md) aby połączyć się z każdym z węzłów.

1. Sprawdź użycie dysku, uruchamiając jedno z następujących poleceń:

    ```bash
    df -h
    du -h --max-depth=1 / | sort -h
    ```

1. Przejrzyj dane wyjściowe i sprawdź obecność `mnt` dużych plików w folderze lub innych folderach. Zazwyczaj foldery `usercache`,, i `appcache` (mnt/resource/hadoop/yarn/local/usercache/hive/appcache/) zawierają duże pliki.

1. Jeśli istnieją duże pliki, bieżące zadanie powoduje wzrost pliku lub nieudane poprzednie zadanie mogło przyczynić się do tego problemu. Aby sprawdzić, czy to zachowanie jest spowodowane przez bieżące zadanie, uruchom następujące polecenie:

    ```bash
    sudo du -h --max-depth=1 /mnt/resource/hadoop/yarn/local/usercache/hive/appcache/
    ```

1. Jeśli to polecenie wskazuje określone zadanie, można zakończyć zadanie za pomocą polecenia podobnego do następującego:

    ```bash
    yarn application -kill -applicationId <application_id>
    ```

    Zamień `application_id` na identyfikator aplikacji. Jeśli nie są wskazane żadne konkretne zadania, przejdź do następnego kroku.

1. Po zakończeniu powyższego polecenia lub jeśli nie zostaną wskazane żadne określone zadania, usuń duże pliki zidentyfikowane przez uruchomienie polecenia podobnego do następującego:

    ```bash
    rm -rf filecache usercache
    ```

Aby uzyskać więcej informacji dotyczących problemów z miejscem na dysku, zobacz [Brak miejsca na dysku](./hadoop/hdinsight-troubleshoot-out-disk-space.md).

> [!NOTE]  
> Jeśli masz duże pliki, które chcesz zachować, ale przyczyniają się do problemu z małą przestrzenią dyskową, musisz skalować klaster HDInsight i ponownie uruchomić usługi. Po wykonaniu tej procedury i odczekaj kilka minut, można zauważyć, że magazyn jest zwalniany i węzła normalnej wydajności jest przywracany.

## <a name="next-steps"></a>Następne kroki

Odwiedź następujące łącza, aby uzyskać więcej informacji na temat rozwiązywania problemów i monitorowania klastrów:

* [Analizowanie dzienników usługi HDInsight](hdinsight-debug-jobs.md)
* [Aplikacje debugowania z dziennikami YARN Apache Hadoop](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Włącz zrzuty sterty dla usług Apache Hadoop w systemie HDInsight opartym na systemie Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)
