---
title: Nie można uruchomić apache HBase Master w usłudze Azure HDInsight
description: Nie można uruchomić apache HBase Master (HMaster) w usłudze Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/14/2019
ms.openlocfilehash: 290b541d9b5e86616373d2e426241fca07e780ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75887210"
---
# <a name="apache-hbase-master-hmaster-fails-to-start-in-azure-hdinsight"></a>Nie można uruchomić apache HBase Master (HMaster) w usłudze Azure HDInsight

W tym artykule opisano kroki rozwiązywania problemów i możliwe rozwiązania problemów podczas interakcji z klastrami usługi Azure HDInsight.

## <a name="scenario-atomic-renaming-failure"></a>Scenariusz: Niepowodzenie zmiany nazwy atomowej

### <a name="issue"></a>Problem

Nieoczekiwane pliki zidentyfikowane podczas procesu uruchamiania.

### <a name="cause"></a>Przyczyna

Podczas procesu uruchamiania HMaster wykonuje wiele kroków inicjowania, w tym przenoszenie danych od podstaw (tmp) do folderu danych. HMaster również patrzy na zapis z wyprzedzeniem logi (WAL) folder, aby sprawdzić, czy istnieją jakieś serwery regionu nie odpowiada.

HMaster wykonuje podstawowe polecenie listy w folderach WAL. Jeśli w dowolnym momencie HMaster widzi nieoczekiwany plik w dowolnym z tych folderów, zgłasza wyjątek i nie uruchamia się.

### <a name="resolution"></a>Rozwiązanie

Sprawdź stos wywołań i spróbuj ustalić, który folder może być przyczyną problemu (na przykład może to być folder WAL lub folder tmp). Następnie w Eksploratorze chmury lub za pomocą poleceń HDFS spróbuj zlokalizować plik problematyki. Zazwyczaj jest to `*-renamePending.json` plik. (Plik `*-renamePending.json` jest plikiem dziennika, który jest używany do implementacji operacji zmiany nazwy atomowej w sterowniku WASB. Ze względu na błędy w tej implementacji, pliki te mogą być pozostawione po awarii procesu i tak dalej.) Wymuś usunięcie tego pliku w Eksploratorze chmury lub za pomocą poleceń HDFS.

Czasami może również istnieć plik tymczasowy `$$$.$$$` o nazwie coś takiego w tej lokalizacji. Aby wyświetlić ten `ls` plik, musisz użyć polecenia HDFS; nie widać pliku w Eksploratorze chmury. Aby usunąć ten plik, użyj `hdfs dfs -rm /\<path>\/\$\$\$.\$\$\$`polecenia HDFS .

Po uruchomieniu tych poleceń HMaster powinien rozpocząć się natychmiast.

---

## <a name="scenario-no-server-address-listed"></a>Scenariusz: Brak adresu serwera na liście

### <a name="issue"></a>Problem

Może zostać wyświetlony komunikat informujący, że tabela `hbase: meta` nie jest w trybie online. Uruchomiony `hbck` może `hbase: meta table replicaId 0 is not found on any region.` zgłosić, że w dziennikach HMaster `No server address listed in hbase: meta for region hbase: backup <region name>`może zostać wyświetlony komunikat: .  

### <a name="cause"></a>Przyczyna

HMaster nie może zainicjować po ponownym uruchomieniu bazy HBase.

### <a name="resolution"></a>Rozwiązanie

1. W powłoce HBase wprowadź następujące polecenia (zmień wartości rzeczywiste, stosownie do przypadku):

    ```hbase
    scan 'hbase:meta'
    delete 'hbase:meta','hbase:backup <region name>','<column name>'
    ```

1. Usuń `hbase: namespace` wpis. Ten wpis może być tym samym błędem, który jest zgłaszany podczas skanowania `hbase: namespace` tabeli.

1. Uruchom ponownie aktywny HMaster z interfejsu użytkownika Ambari, aby przywołać HBase w stanie uruchomionym.

1. W powłoce HBase, aby przywołać wszystkie tabele w trybie offline, uruchom następujące polecenie:

    ```hbase
    hbase hbck -ignorePreCheckPermission -fixAssignments
    ```

---

## <a name="scenario-javaioioexception-timedout"></a>Scenariusz: java.io.IOException: Timedout

### <a name="issue"></a>Problem

HMaster upochonie z `java.io.IOException: Timedout 300000ms waiting for namespace table to be assigned`fatalnym wyjątkiem podobnym do: .

### <a name="cause"></a>Przyczyna

Ten problem może wystąpić, jeśli masz wiele tabel i regionów, które nie zostały opróżnione po ponownym uruchomieniu usług HMaster. Limit czasu jest znaną wadą hmastera. Ogólne zadania uruchamiania klastra może zająć dużo czasu. HMaster zamyka się, jeśli tabela obszaru nazw nie jest jeszcze przypisana. Długie zadania uruchamiania występują tam, gdzie istnieje duża ilość niechłodnionych danych, a limit czasu pięciu minut nie jest wystarczający.

### <a name="resolution"></a>Rozwiązanie

1. Z interfejsu Apache Ambari przejdź do **HBase** > **Configs**. W pliku `hbase-site.xml` niestandardowym dodaj następujące ustawienie:

    ```
    Key: hbase.master.namespace.init.timeout Value: 2400000  
    ```

1. Uruchom ponownie wymagane usługi (HMaster i ewentualnie inne usługi HBase).

---

## <a name="scenario-frequent-region-server-restarts"></a>Scenariusz: częste ponowne uruchamianie serwera regionu

### <a name="issue"></a>Problem

Węzły są okresowo uruchamiane ponownie. Z dzienników serwera regionu mogą być widoczne wpisy podobne do:

```
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
2017-05-09 17:45:07,683 WARN  [JvmPauseMonitor] util.JvmPauseMonitor: Detected pause in JVM or host machine (eg GC): pause of approximately 31000ms
```

### <a name="cause"></a>Przyczyna

Długa `regionserver` pauza JVM GC. Pauza spowoduje, że `regionserver` nie będzie reagować i nie będzie w stanie wysłać bicia serca do HMaster w ciągu limitu czasu sesji zk 40s. HMaster uwierzy, że `regionserver` jest martwy `regionserver` i będzie przerwać i ponownie uruchomić.

### <a name="resolution"></a>Rozwiązanie

Zmień limit czasu sesji Zookeeper, nie tylko `hbase-site` ustawienie, `zookeeper.session.timeout` ale także ustawienie Zookeeper `zoo.cfg` `maxSessionTimeout` muszą zostać zmienione.

1. Uzyskaj dostęp do interfejsu użytkownika Ambari, przejdź do **sekcji HBase -> Configs -> Settings**, w sekcji Limity czasu zmień wartość limitu czasu sesji zookeeper.

1. Uzyskaj dostęp do interfejsu użytkownika Ambari, przejdź do **zookeeper -> Configs -> Custom** `zoo.cfg`, dodaj/zmień następujące ustawienie. Upewnij się, że wartość jest `zookeeper.session.timeout`taka sama jak HBase .

    ```
    Key: maxSessionTimeout Value: 120000  
    ```

1. Uruchom ponownie wymagane usługi.

---

## <a name="scenario-log-splitting-failure"></a>Scenariusz: Niepowodzenie dzielenia dziennika

### <a name="issue"></a>Problem

HMasters nie udało się wymyślić w klastrze HBase.

### <a name="cause"></a>Przyczyna

Nieprawidłowo skonfigurowane ustawienia hdfs i bazy danych bazy danych dla dodatkowego konta magazynu.

### <a name="resolution"></a>Rozwiązanie

set hbase.rootdir: wasb://@.blob.core.windows.net/hbase i uruchom ponownie usługi na Ambari.

---

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

* Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

* Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

* Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
