---
title: Rozwiązywanie problemów z YARN za pomocą usługi Azure HDInsight | Dokumentacja firmy Microsoft
description: Odpowiedzi na często zadawane pytania na temat pracy z Apache Hadoop YARN i usłudze Azure HDInsight.
keywords: Azure HDInsight, YARN, często zadawane pytania, rozwiązywanie problemów z przewodnika, często zadawane pytania
services: Azure HDInsight
documentationcenter: na
author: arijitt
manager: ''
editor: ''
ms.assetid: F76786A9-99AB-4B85-9B15-CA03528FC4CD
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.date: 11/2/2017
ms.author: arijitt
ms.openlocfilehash: 338d678fb31a86046b8bc6424d0e8aac2de1a0c5
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2018
---
# <a name="troubleshoot-yarn-by-using-azure-hdinsight"></a>Rozwiązywanie problemów z YARN za pomocą usługi Azure HDInsight

Dowiedz się więcej o Najważniejsze problemy i rozwiązania ich podczas pracy z ładunków Apache Hadoop YARN w Apache Ambari.

## <a name="how-do-i-create-a-new-yarn-queue-on-a-cluster"></a>Jak utworzyć nową kolejkę YARN w klastrze?


### <a name="resolution-steps"></a>Kroki rozwiązywania problemów 

Wykonaj następujące kroki w Ambari, aby utworzyć nową kolejkę YARN, a następnie saldo alokacji pojemności wśród wszystkich kolejek. 

W tym przykładzie dwie istniejącej kolejki (**domyślne** i **thriftsvr**) zarówno są zmieniane przy użyciu pojemności 50% do 25% pojemności, zapewniający możliwości 50% kolejki (spark).
| Kolejka | Pojemność | Maksymalna pojemność |
| --- | --- | --- | --- |
| domyślnie | 25% | 50% |
| thrftsvr | 25% | 50% |
| Platforma Spark | 50% | 50% |

1. Wybierz **widoków Ambari** ikonę, a następnie wybierz wzorzec siatki. Następnie wybierz pozycję **menedżera kolejek YARN**.

    ![Wybierz ikonę widoków Ambari](media/hdinsight-troubleshoot-yarn/create-queue-1.png)
2. Wybierz **domyślne** kolejki.

    ![Wybierz kolejki domyślne](media/hdinsight-troubleshoot-yarn/create-queue-2.png)
3. Dla **domyślne** kolejka, zmień **pojemności** od 50% do 25%. Aby uzyskać **thriftsvr** kolejka, zmień **pojemności** 25%.

    ![Zmiana zdolności do 25% dla domyślnej i thriftsvr kolejek](media/hdinsight-troubleshoot-yarn/create-queue-3.png)
4. Aby utworzyć nową kolejkę, wybierz **dodać kolejki**.

    ![Wybierz opcję Dodaj kolejki](media/hdinsight-troubleshoot-yarn/create-queue-4.png)

5. Nazwa nowej kolejki.

    ![Nazwa kolejki Spark](media/hdinsight-troubleshoot-yarn/create-queue-5.png)  

6. Pozostaw **pojemności** wartości na 50%, a następnie wybierz **akcje** przycisku.

    ![Wybierz przycisk Akcje](media/hdinsight-troubleshoot-yarn/create-queue-6.png)  
7. Wybierz **Zapisz i Odśwież kolejek**.

    ![Wybierz polecenie Zapisz i Odśwież kolejek](media/hdinsight-troubleshoot-yarn/create-queue-7.png)  

Te zmiany są widoczne natychmiast na Interfejsie użytkownika YARN harmonogramu.

### <a name="additional-reading"></a>Dodatkowe materiały

- [YARN CapacityScheduler](https://hadoop.apache.org/docs/r2.7.2/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html)


## <a name="how-do-i-download-yarn-logs-from-a-cluster"></a>W jaki sposób pobierać dzienników YARN z klastra?


### <a name="resolution-steps"></a>Kroki rozwiązywania problemów 

1. Połącz się z klastrem usługi HDInsight przy użyciu klienta Secure Shell (SSH). Aby uzyskać więcej informacji, zobacz [dodatkowe materiały](#additional-reading-2).

2. Aby wyświetlić listę wszystkich identyfikatorów aplikacji aplikacji YARN, które są aktualnie uruchomione, uruchom następujące polecenie:

    ```apache
    yarn top
    ```
    Identyfikatory są wymienione w **APPLICATIONID** kolumny. Możesz pobrać dzienniki **APPLICATIONID** kolumny.

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

3. Aby pobrać dzienniki YARN kontenera dla wszystkich wzorców aplikacji, użyj następującego polecenia:
   
    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am ALL > amlogs.txt
    ```

    To polecenie tworzy plik dziennika o nazwie amlogs.txt. 

4. Aby pobrać dzienniki YARN kontenera tylko najnowsze głównego aplikacji, użyj następującego polecenia:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am -1 > latestamlogs.txt
    ```

    To polecenie tworzy plik dziennika o nazwie latestamlogs.txt. 

4. Aby pobrać dzienniki YARN kontenera dla pierwszego wzorców dwóch aplikacji, użyj następującego polecenia:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -am 1,2 > first2amlogs.txt 
    ```

    To polecenie tworzy plik dziennika o nazwie first2amlogs.txt. 

5. Aby pobrać wszystkie dzienniki kontenera YARN, użyj następującego polecenia:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> > logs.txt
    ```

    To polecenie tworzy plik dziennika o nazwie logs.txt. 

6. Aby pobrać dziennik YARN kontenera dla określonego kontenera, użyj następującego polecenia:

    ```apache
    yarn logs -applicationIdn logs -applicationId <application_id> -containerId <container_id> > containerlogs.txt 
    ```

    To polecenie tworzy plik dziennika o nazwie containerlogs.txt.

### <a name="additional-reading-2"></a>Dodatkowe materiały

- [Nawiązać połączenia z usługą HDInsight (Hadoop) przy użyciu protokołu SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-linux-use-ssh-unix)
- [Apache Hadoop YARN pojęcia i aplikacji](https://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/)


### <a name="see-also"></a>Zobacz też
[Rozwiązywanie problemów przy użyciu usługi Azure HDInsight](hdinsight-troubleshoot-guide.md)







