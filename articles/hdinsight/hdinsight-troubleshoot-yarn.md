---
title: Rozwiązywanie problemów z usługi YARN w usłudze Azure HDInsight
description: Uzyskaj odpowiedzi na często zadawane pytania na temat pracy z usługą Apache Hadoop YARN i Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: 8bfe249b0295bc860cf17a006c3787ff8afa676b
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69573707"
---
# <a name="troubleshoot-apache-hadoop-yarn-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z Apache Hadoop PRZĘDZą przy użyciu usługi Azure HDInsight

Dowiedz się więcej o najważniejszych problemach i ich rozwiązania podczas pracy z usługą Apache Hadoop YARN ładunków w Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Jak utworzyć nową kolejkę usługi YARN w klastrze?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów

Wykonaj następujące kroki w Ambari, aby utworzyć nową kolejkę usługi YARN, a następnie równoważyć przydziału pojemności wśród wszystkich kolejek.

W tym przykładzie dwie istniejącej kolejki (**domyślne** i **thriftsvr**) są zarówno zmieniła się z 50% pojemność przepustowości 25%, co daje nową pojemność kolejki (spark) w 50%.

| Kolejka | Pojemność | Maksymalna pojemność |
| --- | --- | --- |
| default | 25% | 50% |
| thrftsvr | 25% | 50% |
| Platforma Spark | 50% | 50% |

1. Wybierz **widoków Ambari** ikonę, a następnie wybierz wzorca siatki. Następnie wybierz pozycję **menedżera kolejki YARN**.

    ![Wybierz ikonę widoków Ambari](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
2. Wybierz **domyślne** kolejki.

    ![Wybierz kolejkę domyślne](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
3. Dla **domyślne** kolejki i zmienić **pojemności** z 50%, 25%. Aby uzyskać **thriftsvr** kolejki, zmień **pojemności** 25%.

    ![Zmienianie pojemności do 25% domyślnej i thriftsvr kolejek](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
4. Aby utworzyć nową kolejkę, wybierz **Dodaj kolejkę**.

    ![Wybierz opcję Dodaj kolejkę](media/hdinsight-troubleshoot-yarn/create-queue-4.png)

5. Nazwa nowej kolejki.

    ![Nazwa kolejki platformy Spark](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  

6. Pozostaw **pojemności** wartości na 50%, a następnie wybierz **akcje** przycisku.

    ![Wybierz przycisk Akcje](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
7. Wybierz **Zapisz i Odśwież kolejek**.

    ![Wybierz pozycję Zapisz i Odśwież kolejek](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

Te zmiany są widoczne od razu po Interfejsie użytkownika YARN harmonogramu.

### <a name="additional-reading"></a>Materiały uzupełniające

- [Apache Hadoop PRZĘDZy CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)

## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>Jak pobrać dzienniki platformy YARN z klastra?

### <a name="resolution-steps"></a>Kroki rozwiązywania problemów 

1. Nawiąż połączenie z klastrem HDInsight przy użyciu klienta Secure Shell (SSH). Aby uzyskać więcej informacji, zobacz [dodatkowe materiały](#additional-reading-2).

1. Aby wyświetlić listę wszystkich identyfikatorów aplikacji w aplikacji usługi YARN, które są aktualnie uruchomione, uruchom następujące polecenie:

    ```apache
    yarn top
    ```

    Identyfikatory są wymienione w **APPLICATIONID** kolumny. Możesz pobrać dzienniki z **APPLICATIONID** kolumny.

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

1. Aby pobrać dzienniki kontenera YARN dla wszystkich wzorców aplikacji, użyj następującego polecenia:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    To polecenie tworzy plik dziennika o nazwie amlogs.txt.

1. Aby pobrać dzienniki kontenera YARN tylko najnowsze głównego aplikacji, użyj następującego polecenia:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    To polecenie tworzy plik dziennika o nazwie latestamlogs.txt.

1. Aby pobrać dzienniki kontenera YARN dla pierwszego wzorców dwóch aplikacji, użyj następującego polecenia:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt
    ```

    To polecenie tworzy plik dziennika o nazwie first2amlogs.txt.

1. Aby pobrać wszystkie dzienniki kontenera YARN, użyj następującego polecenia:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    To polecenie tworzy plik dziennika o nazwie logs.txt.

1. Aby pobrać dziennik kontenera YARN dla określonego kontenera, użyj następującego polecenia:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt
    ```

    To polecenie tworzy plik dziennika o nazwie containerlogs.txt.

### <a name="additional-reading-2"></a>Materiały uzupełniające

- [Nawiązywanie połączenia z usługą HDInsight (Apache Hadoop) przy użyciu protokołu SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Apache Hadoop YARN pojęcia i aplikacji](https://hadoop.apache.org/docs/r2.7.4/hadoop-yarn/hadoop-yarn-site/WritingYarnApplications.html#Concepts_and_Flow)

## <a name="next-steps"></a>Następne kroki

Jeśli problem nie został wyświetlony lub nie można rozwiązać problemu, odwiedź jeden z następujących kanałów, aby uzyskać więcej pomocy:

- Uzyskaj odpowiedzi od ekspertów platformy Azure za pośrednictwem [pomocy technicznej dla społeczności platformy Azure](https://azure.microsoft.com/support/community/).

- Połącz się [@AzureSupport](https://twitter.com/azuresupport) z programem — oficjalnego konta Microsoft Azure, aby zwiększyć komfort obsługi klienta. Połączenie społeczności platformy Azure z właściwymi zasobami: odpowiedziami, wsparciem i ekspertami.

- Jeśli potrzebujesz więcej pomocy, możesz przesłać żądanie pomocy technicznej z [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Na pasku menu wybierz pozycję **Obsługa** , a następnie otwórz Centrum **pomocy i obsługi technicznej** . Aby uzyskać szczegółowe informacje, zapoznaj [się z tematem jak utworzyć żądanie pomocy technicznej platformy Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Dostęp do pomocy w zakresie zarządzania subskrypcjami i rozliczeń jest dostępny w ramach subskrypcji Microsoft Azure, a pomoc techniczna jest świadczona za pomocą jednego z [planów pomocy technicznej systemu Azure](https://azure.microsoft.com/support/plans/).
