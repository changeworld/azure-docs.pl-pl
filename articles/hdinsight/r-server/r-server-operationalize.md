---
title: Operacjonalizowanie usług HDInsight — usługi Azure ML
description: Dowiedz się, jak operacjonalizować usługi ML w usłudze Azure HDInsight.
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: 916c4fae8eed9451080f92e97743876e89bd25ea
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64719744"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Operacjonalizowanie klastra usługi ML w usłudze Azure HDInsight

Po zastosowaniu klastra usługi ML w HDInsight przeprowadzenie modelowania danych, można zoperacjonalizować model do przewidywania przyszłych zdarzeń. Ten artykuł zawiera instrukcje dotyczące sposobu wykonania tego zadania.

## <a name="prerequisites"></a>Wymagania wstępne

* **Klaster usługi ML w HDInsight**: Aby uzyskać instrukcje, zobacz [Rozpocznij pracę z usługami uczenia Maszynowego na HDInsight](r-server-get-started.md).

* **Klient protokołu Secure Shell (SSH)** : Klient SSH jest używany do zdalnego łączenia z klastrem HDInsight i uruchamianie poleceń bezpośrednio w klastrze. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Operacjonalizowanie klastra usługi ML z jedną konfiguracją

> [!NOTE]  
> Poniższe kroki mają zastosowanie do oprogramowania R Server 9.0 i ML Server 9.1. ML 9.3 serwera, można znaleźć [Użyj narzędzia do administrowania, aby zarządzać konfiguracją operacjonalizacji](https://docs.microsoft.com/machine-learning-server/operationalize/configure-admin-cli-launch).

1. Połącz się z węzłem krawędzi za pomocą protokołu SSH.

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Aby uzyskać instrukcje dotyczące sposobu używania protokołu SSH przy użyciu usługi Azure HDInsight, zobacz [używać protokołu SSH w usłudze HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Zmień katalog dla odpowiedniej wersji i "sudo" dot net biblioteki dll: 

    - For Microsoft ML Server 9.1:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - W przypadku oprogramowania Microsoft R Server 9.0:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Dostępne są opcje do wyboru. Z pierwszej opcji, jak pokazano na poniższym zrzucie ekranu do **skonfigurować ML Server for Operationalization**.

    ![opernacjonalizacja przy użyciu jednej maszyny](./media/r-server-operationalize/admin-util-one-box-1.png)

1. Teraz otrzymasz możliwość wyboru sposobu obsługi operacji serwera uczenia Maszynowego. Z prezentowanych opcji Wybierz pierwszy z nich, wprowadzając **A**.

    ![opernacjonalizacja przy użyciu jednej maszyny](./media/r-server-operationalize/admin-util-one-box-2.png)

1. Po wyświetleniu monitu wprowadź, a następnie wprowadź ponownie hasło dla użytkownika administratora lokalnego.

1. Powinny być widoczne dane wyjściowe sugerujące, że operacja zakończyła się pomyślnie. Monit również wybrać inną opcję z menu. Wybierz E, aby wrócić do menu głównego.

    ![opernacjonalizacja przy użyciu jednej maszyny](./media/r-server-operationalize/admin-util-one-box-3.png)

1. Opcjonalnie możesz wykonać kontrolę diagnostyczną, uruchamiając test diagnostyczny w następujący sposób:

    a. W menu głównym wybierz **6** do uruchamiania testów diagnostycznych.

    ![opernacjonalizacja przy użyciu jednej maszyny](./media/r-server-operationalize/diagnostic-1.png)

    b. Wybierz z menu testy diagnostyczne **A**. Po wyświetleniu monitu wprowadź hasło, które podano dla użytkownika administratora lokalnego.

    ![opernacjonalizacja przy użyciu jednej maszyny](./media/r-server-operationalize/diagnostic-2.png)

    c. Upewnij się, że dane wyjściowe pokazuje, że ogólna kondycja — dostęp próbny.

    ![opernacjonalizacja przy użyciu jednej maszyny](./media/r-server-operationalize/diagnostic-3.png)

    d. Z opcji menu przedstawione, wprowadź **E** wróć do menu głównego, a następnie wprowadź **8** do Zamknij narzędzie administracyjne.

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Duże opóźnienia podczas używania usługi sieci web na platformie Apache Spark

Jeśli wystąpią duże opóźnienia podczas próby korzystania z usługi internetowej utworzonej za pomocą funkcji mrsdeploy w Apache Spark kontekstu obliczeniowego, może być konieczne dodanie niektórych brakujących folderów. Aplikacja Spark należy do użytkownika o nazwie „*rserve2*” zawsze wtedy, gdy jest wywoływana z usługi internetowej przy użyciu funkcji mrsdeploy. Aby obejść ten problem:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


Na tym etapie konfiguracja operacjonalizacji jest ukończona. Teraz możesz używać `mrsdeploy` pakietów w programie RClient, aby nawiązać połączenie z operacjonalizacją w węźle krawędzi i rozpocząć korzystanie z jej funkcje, takie jak [zdalne wykonywanie kodu](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) i [usług sieci web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). W zależności od tego, czy klaster został skonfigurowany w sieci wirtualnej, może być konieczne skonfigurowanie tunelowania przekierowania portów za pomocą logowania SSH. W poniższych sekcjach wyjaśniono, jak skonfigurować taki tunel.

### <a name="ml-services-cluster-on-virtual-network"></a>Klaster usługi ML w sieci wirtualnej

Sprawdź, czy ruch przez port 12800 węzła krawędzi jest dozwolony. Pozwala to użyć węzła krawędzi do nawiązania połączenia z funkcją operacjonalizacji.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Jeśli metoda `remoteLogin()` nie może połączyć się z węzłem krawędzi, lecz nawiązanie połączenia SSH z węzłem krawędzi jest możliwe, sprawdź, czy reguła zezwalająca na ruch przez port 12800 jest skonfigurowana poprawnie. Jeśli problem nie ustąpi, możesz go obejść, konfigurując tunelowanie przekierowania portów przez połączenie SSH. Aby uzyskać instrukcje zobacz następującą sekcję:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>Klaster usługi ML w sieci wirtualnej

Jeśli klaster nie jest skonfigurowany w sieci wirtualnej lub występują problemy z korzystaniem z sieci wirtualnej, możesz użyć tunelowania przekierowania portów za pomocą protokołu SSH:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Gdy sesja SSH jest aktywna, ruch z portu 12800 maszyny lokalnej jest przekazywany do portu 12800 węzła krawędzi, za pomocą sesji SSH. Upewnij się, że w metodzie `remoteLogin()` użyto adresu `127.0.0.1:12800`. Logują operacjonalizacji węzła krawędzi przez przekierowanie portów.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Skalowanie z podziałem na operacje obliczeniowe węzłów na węzłach procesu roboczego HDInsight

Skalowanie węzłów obliczeniowych, należy najpierw likwidowanie węzłów procesu roboczego, a następnie skonfigurować węzłów obliczeniowych na zlikwidowanych węzłów procesu roboczego.

### <a name="step-1-decommission-the-worker-nodes"></a>Krok 1: Likwidowanie węzłów procesu roboczego

Klaster usługi ML nie odbywa się za pośrednictwem [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Jeśli węzły procesu roboczego nie zostaną zlikwidowane, Menedżer zasobów usługi YARN nie działa zgodnie z oczekiwaniami, ponieważ nie ma informacji o znał zasobów zajętych przez serwer. Aby tego uniknąć, zalecamy zlikwidowanie węzłów procesu roboczego przed przystąpieniem do skalowania węzłów obliczeniowych na zewnątrz.

Wykonaj następujące kroki, aby zlikwidować węzły procesu roboczego:

1. Zaloguj się do konsoli Ambari klastra, a następnie kliknij przycisk na **hosty** kartę.

1. Wybierz węzły procesu roboczego (aby mogą zostać wyłączone).

1. Kliknij przycisk **akcje** > **wybrane hosty** > **hosty** > **Włącz tryb konserwacji**. Na przykład na poniższej ilustracji węzły wn3 i wn4 są przeznaczone do likwidacji.  

   ![likwidowanie węzłów procesu roboczego](./media/r-server-operationalize/get-started-operationalization.png)  

* Wybierz **akcje** > **wybrane hosty** > **DataNodes** > kliknij **Decommission**.
* Wybierz **akcje** > **wybrane hosty** > **NodeManagers** > kliknij **Decommission**.
* Wybierz **akcje** > **wybrane hosty** > **DataNodes** > kliknij **zatrzymać**.
* Wybierz **akcje** > **wybrane hosty** > **NodeManagers** > kliknij **zatrzymać**.
* Wybierz **akcje** > **wybrane hosty** > **hosty** > kliknij **Zatrzymaj wszystkie składniki**.
* Usuń zaznaczenie węzłów procesu roboczego i wybierz węzły główne.
* Wybierz **akcje** > **wybrane hosty** > "**hosty** > **Uruchom ponownie wszystkie składniki**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Krok 2: Konfigurowanie węzłów obliczeniowych na każdym zlikwidowanych węzłach procesu roboczego

1. Za pomocą protokołu SSH połącz się z każdym zlikwidowanym węzłem procesu roboczego.

1. Uruchom narzędzie administracyjne za pomocą odpowiednich biblioteki DLL dla klastra usługi uczenie Maszynowe, czy masz. ML Server 9.1 Uruchom następujące polecenie:

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Wprowadź **1** aby wybrać opcję **skonfigurować ML Server for Operationalization**.

1. Wprowadź **C** aby wybrać opcję `C. Compute node`. Umożliwi to skonfigurowanie węzła obliczeniowego w węźle procesu roboczego.

1. Zamknij narzędzie administracyjne.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Krok 3: Dodawanie szczegółów węzłów obliczeniowych na węźle sieci web

Po skonfigurowaniu wszystkich zlikwidowanych węzłów procesu roboczego do uruchamiania węzła obliczeniowego wróć do węzła krawędzi i Dodaj adresy IP zlikwidowanych węzłów procesu roboczego w konfiguracji węzła internetowego oprogramowania Server ML:

1. Połącz się z węzłem krawędzi za pomocą protokołu SSH.

1. Uruchom polecenie `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

1. Wyszukaj w sekcji "URI" i Dodaj adres IP węzła procesu roboczego i port.

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>Kolejne kroki

* [Manage ML Services cluster on HDInsight (Zarządzanie klastrem usług ML w usłudze HDInsight)](r-server-hdinsight-manage.md)
* [Compute context options for ML Services cluster on HDInsight (Opcje kontekstu obliczeniowego dla klastra usług ML w usłudze HDInsight)](r-server-compute-contexts.md)
* [Azure Storage options for ML Services cluster on HDInsight (Opcje usługi Azure Storage dla klastra usług ML w usłudze HDInsight)](r-server-storage.md)
