---
title: Nie można uruchomić oprogramowania Apache HBase Master w usłudze Azure HDInsight
description: Nie można uruchomić oprogramowania Apache HBase Master (serwera hmaster) w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 08/06/2019
ms.openlocfilehash: 8368ebfca4cdd72c5c455a04e29b6c0cb44938ea
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/09/2019
ms.locfileid: "68935408"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Nie można uruchomić oprogramowania Apache HBase Master (serwera hmaster) w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów oraz możliwe rozwiązania problemów występujących w przypadku współpracy z klastrami usługi Azure HDInsight.

## <a name="scenario-atomic-renaming-failure"></a>Scenariusz: Niepowodzenie zmiany nazwy Atomowej

### <a name="issue"></a>Problem

Podczas procesu uruchamiania zidentyfikowano nieoczekiwane pliki.

### <a name="cause"></a>Przyczyna

W trakcie procesu uruchamiania serwera hmaster wykonuje wiele kroków inicjowania, w tym przeniesienie danych z folderu Scratch (tmp) do folderu danych. Serwera hmaster również przegląda folder WALs (zapis z wyprzedzeniem), aby sprawdzić, czy istnieją wolne serwery regionów. We wszystkich tych sytuacjach wykonuje podstawowe `list` polecenie dla tych folderów. Jeśli w dowolnym momencie widzi nieoczekiwany plik w dowolnym z tych folderów, zgłosi wyjątek, dlatego nie zostanie uruchomiony.

### <a name="resolution"></a>Rozwiązanie

W takiej sytuacji Sprawdź stos wywołań, aby zobaczyć, który folder może powodować problem (na przykład WALs folder lub tmp folder). Następnie za pomocą programu Cloud Explorer lub systemu HDFS, aby zlokalizować plik problemu. Plik problemu to zwykle `*-renamePending.json` plik (plik dziennika używany do implementowania operacji niepodzielnej zmiany nazwy w sterowniku WASB). Ze względu na błędy w tej implementacji takie pliki mogą pozostać w przypadku awarii procesu. Wymuś usunięcie tego pliku za pośrednictwem programu Cloud Explorer. Ponadto w tej lokalizacji może istnieć tymczasowy plik o charakterze $. Nie można zobaczyć pliku za pośrednictwem Eksploratora chmury i tylko za `ls` pośrednictwem polecenia HDFS. Aby usunąć ten plik, `hdfs dfs -rm //\$\$\$.\$\$\$` można użyć polecenia HDFS.

Po usunięciu pliku problemu serwera hmaster powinien zostać uruchomiony od razu.

---

## <a name="scenario-no-server-address-listed"></a>Scenariusz: Brak adresu serwera na liście

### <a name="issue"></a>Problem

W dzienniku serwera hmaster jest wyświetlany komunikat o błędzie podobny do "brak adresu serwera wymienionego w HBase: meta dla regionu XXX".

### <a name="cause"></a>Przyczyna

Nie można zainicjować serwera hmaster po ponownym uruchomieniu HBase.

### <a name="resolution"></a>Rozwiązanie

1. Wykonaj następujące polecenia w powłoce HBase (Zmień wartości rzeczywiste stosownie do potrzeb):

    ```
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>' 
    ```

1. Usuń wpis HBase: przestrzeń nazw, ponieważ ten sam błąd może być raportowany podczas skanowania HBase: tabela przestrzeni nazw.

1. Uruchom ponownie aktywne serwera hmaster z interfejsu użytkownika Ambari, aby wyświetlić stan HBase w stanie uruchomienia.

1. Uruchom następujące polecenie w powłoce HBase, aby wyświetlić wszystkie tabele w trybie offline:

    ```
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Scenariusz: Java. IO. IOException: Timedout

### <a name="issue"></a>Problem

Serwera hmaster limit czasu przy użyciu wyjątku krytycznego, takiego jak `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`.

### <a name="cause"></a>Przyczyna

Limit czasu jest znaną wadą z serwera hmaster. Ogólne zadania uruchamiania klastra mogą zająć dużo czasu. Serwera hmaster zamyka się, jeśli tabela przestrzeni nazw nie jest jeszcze przypisana. Długotrwałe zadania uruchamiania są wykonywane w przypadku, gdy istnieje duża ilość nieopróżnionych danych i przekroczenie limitu czasu wynoszącego pięć minut.

### <a name="resolution"></a>Rozwiązanie

1. Dostęp do interfejsu użytkownika Ambari, przejdź do konfiguracji HBase->, w `hbase-site.xml` obszarze niestandardowe Dodaj następujące ustawienie:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Uruchom ponownie wymagane usługi (głównie serwera hmaster i inne usługi HBase).

---

## <a name="scenario-frequent-regionserver-restarts"></a>Scenariusz: Częste ponowne uruchomienia regionserver

### <a name="issue"></a>Problem

Okresowe ponowne uruchamianie węzłów. Z dzienników regionserver można zobaczyć wpisy podobne do:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Przyczyna

Długi regionserver JVM GC. Wstrzymanie spowoduje, że regionserver nie odpowiada i nie będzie mógł wysyłać pulsu do serwera hmaster w ramach 40s limitu czasu sesji ZK. Serwera hmaster uważa, że regionserver jest martwa i spowoduje przerwanie regionserver i ponowne uruchomienie.

### <a name="resolution"></a>Rozwiązanie

Zmień limit czasu sesji dozorcy, a nie tylko ustawienia `zookeeper.session.timeout` HBase-site, ale również dozorcy zoo. cfg należy zmienić ustawienie. `maxSessionTimeout`

1. Dostęp do interfejsu użytkownika Ambari, przejdź do **HBase-> configs-> ustawienia**, w sekcji Limity czasu, Zmień wartość limitu czasu sesji dozorcy.

1. Dostęp do interfejsu użytkownika Ambari, przejdź do **dozorcy-> configs — > Custom** zoo. cfg, Dodaj/Zmień następujące ustawienie. Upewnij się, że wartość jest taka sama jak `zookeeper.session.timeout`HBase.

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Uruchom ponownie wymagane usługi.

---

## <a name="scenario-log-splitting-failure"></a>Scenariusz: Błąd podziału dziennika

### <a name="issue"></a>Problem

HMasters nie powiodło się w klastrze HBase.

### <a name="cause"></a>Przyczyna

Nieprawidłowo skonfigurowane ustawienia systemu plików HDFS i HBase dla konta magazynu pomocniczego.

### <a name="resolution"></a>Rozwiązanie

Ustaw HBase. ROOTDIR: wasb://@.blob.core.windows.net/hbase i ponownie uruchom usługi na Ambari.

---

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

* Połącz się [@AzureSupport](https://twitter.com/azuresupport) z programem — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
