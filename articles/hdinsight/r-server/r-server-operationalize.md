---
title: Operacjonalizacja usług ML w programie HDInsight — Azure
description: Dowiedz się, jak operacjonalizacji modelu danych, aby prognozowania z usług ML w usłudze Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/27/2018
ms.openlocfilehash: a05bcdef2b7456fbab852e9728c156e57f847f57
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71123566"
---
# <a name="operationalize-ml-services-cluster-on-azure-hdinsight"></a>Operacjonalizacja klastra usług ML w usłudze Azure HDInsight

Po użyciu klastra usług ML w hdinsight do ukończenia modelowania danych, można operacjonalizacji modelu do prognozowania. Ten artykuł zawiera instrukcje dotyczące wykonywania tego zadania.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster usług ML w programie HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu portalu Azure](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybierz pozycję Usługi **ML** dla **typu klastra**.

* Klient protokołu Secure Shell (SSH): klient SSH jest używany do zdalnego łączenia z klastrem usługi HDInsight i uruchamiania poleceń bezpośrednio w klastrze. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-ml-services-cluster-with-one-box-configuration"></a>Operacjonalizacja klastra usług ML za pomocą konfiguracji jednokojowej

> [!NOTE]  
> Poniższe kroki dotyczą serwerów R Server 9.0 i ML Server 9.1. W przypadku serwera ML Server 9.3 zobacz [Zarządzanie konfiguracją operationalization za pomocą narzędzia administracyjnego](https://docs.microsoft.com/machine-learning-server/operationalize/configure-admin-cli-launch).

1. Połącz się z węzłem krawędzi za pomocą protokołu SSH.

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Aby uzyskać instrukcje dotyczące używania SSH z usługą Azure HDInsight, zobacz [Używanie SSH z hdinsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).

1. Zmień katalog dla odpowiedniej wersji i sudo dll netto kropki: 

    - W przypadku programu Microsoft ML Server 9.1:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - W przypadku oprogramowania Microsoft R Server 9.0:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Dostępne są opcje do wyboru. Wybierz pierwszą opcję, jak pokazano na poniższym zrzucie ekranu, aby **skonfigurować serwer ML do operacjonalizacji**.

    ![R Server Administracja narzędzie wybierz](./media/r-server-operationalize/admin-util-one-box-1.png)

1. Teraz zostanie wyświetlona opcja wyboru sposobu operacjonalizacji serwera ML. Z prezentowanych opcji wybierz pierwszą, wprowadzając **A**.

    ![Operacjonalizacja narzędzia administracji serwera R](./media/r-server-operationalize/admin-util-one-box-2.png)

1. Po wyświetleniu monitu wprowadź i wprowadź ponownie hasło dla lokalnego użytkownika administratora.

1. Powinny być widoczne dane wyjściowe sugerujące, że operacja zakończyła się pomyślnie. Zostanie również wyświetlony monit o wybranie innej opcji z menu. Wybierz E, aby wrócić do menu głównego.

    ![Sukces narzędzia administracji serwera R](./media/r-server-operationalize/admin-util-one-box-3.png)

1. Opcjonalnie można przeprowadzić testy diagnostyczne, uruchamiając test diagnostyczny w następujący sposób:

    a. Z menu głównego wybierz **6,** aby uruchomić testy diagnostyczne.

    ![Diagnostyka narzędzia administracji serwera R](./media/r-server-operationalize/hdinsight-diagnostic1.png)

    b. Z menu Testy diagnostyczne wybierz polecenie **A**. Po wyświetleniu monitu wprowadź hasło podane dla lokalnego użytkownika administratora.

    ![Test narzędzia administracji serwera R](./media/r-server-operationalize/hdinsight-diagnostic2.png)

    d. Sprawdź, czy dane wyjściowe pokazują, że ogólna kondycja jest przebiegiem.

    ![Przebieg narzędzia Administracja serwera R](./media/r-server-operationalize/hdinsight-diagnostic3.png)

    d. Z przedstawionych opcji menu wprowadź **E,** aby powrócić do menu głównego, a następnie wprowadź **8,** aby wyjść z narzędzia administracyjnego.

### <a name="long-delays-when-consuming-web-service-on-apache-spark"></a>Duże opóźnienia podczas korzystania z usługi internetowej na Apache Spark

Jeśli wystąpią duże opóźnienia podczas próby korzystania z usługi sieci web utworzonej za pomocą funkcji mrsdeploy w kontekście obliczeniowym Apache Spark, może być konieczne dodanie niektórych brakujących folderów. Aplikacja Spark należy do użytkownika o nazwie „*rserve2*” zawsze wtedy, gdy jest wywoływana z usługi internetowej przy użyciu funkcji mrsdeploy. Aby obejść ten problem:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


Na tym etapie konfiguracja operacjonalizacji jest ukończona. Teraz możesz użyć `mrsdeploy` pakietu na rClient, aby połączyć się z operacjonalizacji na węźle krawędzi i rozpocząć korzystanie z jego funkcji, takich jak [zdalne wykonanie](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) i [usługi internetowe.](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services) W zależności od tego, czy klaster został skonfigurowany w sieci wirtualnej, może być konieczne skonfigurowanie tunelowania przekierowania portów za pomocą logowania SSH. W poniższych sekcjach wyjaśniono, jak skonfigurować taki tunel.

### <a name="ml-services-cluster-on-virtual-network"></a>Klaster usług ML w sieci wirtualnej

Sprawdź, czy ruch przez port 12800 węzła krawędzi jest dozwolony. Pozwala to użyć węzła krawędzi do nawiązania połączenia z funkcją operacjonalizacji.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Jeśli metoda `remoteLogin()` nie może połączyć się z węzłem krawędzi, lecz nawiązanie połączenia SSH z węzłem krawędzi jest możliwe, sprawdź, czy reguła zezwalająca na ruch przez port 12800 jest skonfigurowana poprawnie. Jeśli problem nie ustąpi, możesz go obejść, konfigurując tunelowanie przekierowania portów przez połączenie SSH. Instrukcje można znaleźć w następującej sekcji:

### <a name="ml-services-cluster-not-set-up-on-virtual-network"></a>Klaster usług ML nie został skonfigurowany w sieci wirtualnej

Jeśli klaster nie jest skonfigurowany w sieci wirtualnej lub występują problemy z korzystaniem z sieci wirtualnej, możesz użyć tunelowania przekierowania portów za pomocą protokołu SSH:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Gdy sesja SSH jest aktywna, ruch z portu 12800 komputera lokalnego jest przekazywał dalej do portu węzła brzegowego 12800 za pośrednictwem sesji SSH. Upewnij się, że w metodzie `remoteLogin()` użyto adresu `127.0.0.1:12800`. To loguje się do operacjonalizacji węzła krawędzi za pośrednictwem przekazywania portów.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Skalowanie zdelegalizowanych węzłów obliczeniowych w węzłach procesu roboczego HDInsight

Aby skalować węzły obliczeniowe, należy najpierw zlikwidować węzły procesu roboczego, a następnie skonfigurować węzły obliczeniowe w wycofanych węzłach procesu roboczego.

### <a name="step-1-decommission-the-worker-nodes"></a>Krok 1: Likwidacja węzłów procesu roboczego

Klaster usług ML nie jest zarządzany za pośrednictwem [programu Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Jeśli węzły procesu roboczego nie są likwidowane, Menedżer zasobów YARN nie działa zgodnie z oczekiwaniami, ponieważ nie jest świadomy zasobów pobieranych przez serwer. Aby tego uniknąć, zalecamy zlikwidowanie węzłów procesu roboczego przed przystąpieniem do skalowania węzłów obliczeniowych na zewnątrz.

Wykonaj następujące kroki, aby zlikwidować węzły procesu roboczego:

1. Zaloguj się do konsoli Ambari klastra i kliknij kartę **Hosty.**

1. Wybierz węzły procesu roboczego (do wycofania).

1. Kliknij pozycję **Akcje** > **wybranych hostów** > **Hosty** > **włącz tryb konserwacji**. Na przykład na poniższej ilustracji węzły wn3 i wn4 są przeznaczone do likwidacji.  

   ![Apache Ambari włącza tryb konserwacji](./media/r-server-operationalize/get-started-operationalization.png)  

* Wybierz **pozycję Akcje** > **wybrane hosty** > **DataNodes** > kliknij przycisk **Likwiduj**.
* Wybierz **pozycję Akcje** > **wybrane hosty** > **NodeManagers** > kliknij przycisk **Likwiduj**.
* Wybierz **pozycję Akcje** > **wybrane hosty** > **DataNodes** > kliknij przycisk **Zatrzymaj**.
* Wybierz **akcje** > **wybrane hosty NodeManagers** > **NodeManagers** > kliknij **stop**.
* Wybierz **pozycję Akcje** > **wybrane hosty hosty** > **Hosts** > kliknij pozycję **Zatrzymaj wszystkie składniki**.
* Usuń zaznaczenie węzłów procesu roboczego i wybierz węzły główne.
* Wybierz **akcje** > **wybrane hosty** > "**Hosty** > **Uruchom ponownie wszystkie składniki**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Krok 2: Konfigurowanie węzłów obliczeniowych w każdym zlikwidowanym węźle(-ach) procesu roboczego

1. Za pomocą protokołu SSH połącz się z każdym zlikwidowanym węzłem procesu roboczego.

1. Uruchom narzędzie administracyjne przy użyciu odpowiedniej biblioteki DLL dla posiadanych usług ML. W przypadku serwera ML Server 9.1 uruchom następujące czynności:

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

1. Wprowadź **1,** aby wybrać opcję **Konfiguruj serwer ML do operacjonalizacji**.

1. Wprowadź **C,** `C. Compute node`aby wybrać opcję . Umożliwi to skonfigurowanie węzła obliczeniowego w węźle procesu roboczego.

1. Zamknij narzędzie administracyjne.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Krok 3: Dodawanie szczegółów węzłów obliczeniowych w węźle internetowym

Gdy wszystkie zlikwidowane węzły procesu roboczego są skonfigurowane do uruchamiania węzła obliczeniowego, wróć do węzła brzegowego i dodaj adresy IP wycofanych węzłów procesu roboczego w konfiguracji węzła sieci web ml server:

1. Połącz się z węzłem krawędzi za pomocą protokołu SSH.

1. Uruchom polecenie `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

1. Poszukaj sekcji "Uris" i dodaj szczegóły adresu IP i portu węzła procesu roboczego.

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>Następne kroki

* [Manage ML Services cluster on HDInsight (Zarządzanie klastrem usług ML w usłudze HDInsight)](r-server-hdinsight-manage.md)
* [Compute context options for ML Services cluster on HDInsight (Opcje kontekstu obliczeniowego dla klastra usług ML w usłudze HDInsight)](r-server-compute-contexts.md)
* [Azure Storage options for ML Services cluster on HDInsight (Opcje usługi Azure Storage dla klastra usług ML w usłudze HDInsight)](r-server-storage.md)
