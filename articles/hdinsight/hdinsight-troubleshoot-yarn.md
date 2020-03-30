---
title: Rozwiązywanie problemów z usługą YARN w usłudze Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące pracy z apache Hadoop YARN i Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: f0c7b966b9fa7580809d2df0f4d05a7146ca0fd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272203"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z platformą YARN usługi Apache Hadoop za pomocą usługi Azure HDInsight

Dowiedz się więcej o najważniejszych problemach i ich rozwiązaniach podczas pracy z ładunkami Apache Hadoop YARN w Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Jak utworzyć nową kolejkę YARN w klastrze?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

Aby utworzyć nową kolejkę YARN, użyj następujących kroków, aby utworzyć nową kolejkę YARN, a następnie zrównoważyć alokację zdolności produkcyjnych między wszystkimi kolejkami.

W tym przykładzie dwie istniejące kolejki **(domyślne** i **thriftsvr)** obie są zmieniane z 50% pojemności do 25% pojemności, co daje nową kolejkę (iskrę) 50% pojemności.

| Kolejka | Pojemność | Maksymalna pojemność |
| --- | --- | --- |
| default | 25% | 50% |
| thrftsvr | 25% | 50% |
| spark | 50% | 50% |

1. Wybierz ikonę **Widoki Ambari,** a następnie wybierz deseń siatki. Następnie wybierz pozycję **Menedżer kolejek YARN**.

    ![Apache Ambari deski rozdzielczej YARN Queue Manager](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-1.png)
2. Wybierz kolejkę **domyślną.**

    ![Apache Ambari YARN wybierz domyślną kolejkę](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-2.png)
3. W przypadku kolejki **domyślnej** zmień **pojemność** z 50% na 25%. W **kolejce thriftsvr** zmień **pojemność** na 25%.

    ![Zmiana pojemności na 25% dla kolejek domyślnych i thriftsvr](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-3.png)
4. Aby utworzyć nową kolejkę, wybierz pozycję **Dodaj kolejkę**.

    ![Apache Ambari YARN deska rozdzielcza Dodaj kolejkę](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-4.png)

5. Nazwij nową kolejkę.

    ![Apache Ambari Nazwa pulpitu nawigacyjnego YARN Kolejka](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-5.png)  

6. Pozostaw wartości **pojemności** na poziomie 50%, a następnie wybierz przycisk **Akcje.**

    ![Apache Ambari YARN wybierz akcję](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-6.png)  
7. Wybierz **pozycję Zapisz i odśwież kolejki**.

    ![Wybieranie kolejek zapisywania i odświeżania](media/hdinsight-troubleshoot-yarn/apache-yarn-create-queue-7.png)  

Zmiany te są widoczne natychmiast w interfejsie użytkownika harmonogramu YARN.

### <a name="additional-reading"></a>Dodatkowa lektura

- [Apache Hadoop YARN CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)

## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Jak pobrać dzienniki YARN z klastra?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

1. Połącz się z klastrem HDInsight przy użyciu klienta bezpiecznej powłoki (SSH). Aby uzyskać więcej informacji, zobacz [Dodatkowe czytanie](#additional-reading-2).

1. Aby wyświetlić listę wszystkich identyfikatorów aplikacji aplikacji YARN, które są aktualnie uruchomione, uruchom następujące polecenie:

    ```apache
    yarn top
    ```

    Identyfikatory są wyświetlane w kolumnie **APPLICATIONID.** Dzienniki można pobrać z kolumny **APPLICATIONID.**

    ```apache
    YARN top - 18:00:07, up 19d, 0:14, 0 active users, queue(s): root
    NodeManager(s): 4 total, 4 active, 0 unhealthy, 0 decommissioned, 0 lost, 0 rebooted
    Queue(s) Applications: 2 running, 10 submitted, 0 pending, 8 completed, 0 killed, 0 failed
    Queue(s) Mem(GB): 97 available, 3 allocated, 0 pending, 0 reserved
    Queue(s) VCores: 58 available, 2 allocated, 0 pending, 0 reserved
    Queue(s) Containers: 2 allocated, 0 pending, 0 reserved

                      APPLICATIONID USER             TYPE      QUEUE   #CONT  #RCONT  VCORES RVCORES     MEM    RMEM  VCORESECS    MEMSECS %PROGR       TIME NAME
     application_1490377567345_0007 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628407    2442611  10.00   18:20:20 Thrift JDBC/ODBC Server
     application_1490377567345_0006 hive            spark  thriftsvr       1       0       1       0      1G      0G    1628430    2442645  10.00   18:20:20 Thrift JDBC/ODBC Server
    ```

1. Aby pobrać dzienniki kontenerów YARN dla wszystkich wzorców aplikacji, użyj następującego polecenia:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    To polecenie tworzy plik dziennika o nazwie amlogs.txt.

1. Aby pobrać dzienniki kontenerów YARN tylko dla najnowszego wzorca aplikacji, użyj następującego polecenia:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    To polecenie tworzy plik dziennika o nazwie latestamlogs.txt.

1. Aby pobrać dzienniki kontenerów YARN dla dwóch pierwszych wzorców aplikacji, należy użyć następującego polecenia:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

    To polecenie tworzy plik dziennika o nazwie first2amlogs.txt.

1. Aby pobrać wszystkie dzienniki kontenerów YARN, użyj następującego polecenia:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    To polecenie tworzy plik dziennika o nazwie logs.txt.

1. Aby pobrać dziennik kontenera YARN dla określonego kontenera, użyj następującego polecenia:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

    To polecenie tworzy plik dziennika o nazwie containerlogs.txt.

### <a name="additional-reading"></a><a name="additional-reading-2"></a>Dodatkowa lektura

- [Połącz się z HDInsight (Apache Hadoop) za pomocą SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Apache Hadoop YARN koncepcje i aplikacje](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html#Concepts_and_Flow)

## <a name="next-steps"></a>Następne kroki

Jeśli nie widzisz problemu lub nie możesz rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy technicznej:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej platformy Azure Community.](https://azure.microsoft.com/support/community/)

- Połącz [@AzureSupport](https://twitter.com/azuresupport) się z — oficjalnym kontem platformy Microsoft Azure w celu poprawy jakości obsługi klienta. Łączenie społeczności platformy Azure z odpowiednimi zasobami: odpowiedziami, pomocą techniczną i ekspertami.

- Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy z [witryny Azure portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Wybierz **pozycję Obsługa z** paska menu lub otwórz centrum pomocy + pomocy **technicznej.** Aby uzyskać bardziej szczegółowe informacje, zapoznaj [się z instrukcjami tworzenia żądania pomocy technicznej platformy Azure.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Dostęp do obsługi zarządzania subskrypcjami i rozliczeń jest dołączony do subskrypcji platformy Microsoft Azure, a pomoc techniczna jest świadczona za pośrednictwem jednego z [planów pomocy technicznej platformy Azure.](https://azure.microsoft.com/support/plans/)
