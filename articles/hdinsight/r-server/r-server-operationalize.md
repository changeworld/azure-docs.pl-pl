---
title: Operacjonalizuj R Server w usłudze HDInsight - Azure | Dokumentacja firmy Microsoft
description: Dowiedz się, jak operacjonalizacji R Server w usłudze Azure HDInsight.
services: HDInsight
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: HDInsight
ms.custom: hdinsightactive
ms.devlang: R
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/23/2018
ms.author: nitinme
ms.openlocfilehash: 93957b7ee10527039bf2e96cc5470420cdef0651
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2018
---
# <a name="operationalize-r-server-cluster-on-azure-hdinsight"></a>Operacjonalizuj klastra R Server w usłudze Azure HDInsight

Po zastosowaniu klastra R Server w usłudze HDInsight przeprowadzenie modelowania danych, aby operacjonalizować modelu w celu tworzenia prognoz. Ten artykuł zawiera instrukcje dotyczące sposobu wykonania tego zadania.

## <a name="prerequisites"></a>Wymagania wstępne

* **Klaster R Server w usłudze HDInsight**: Aby uzyskać instrukcje, zobacz [Rozpoczynanie pracy z serwerem R w usłudze HDInsight](r-server-get-started.md).

* **Klient protokołu Secure Shell (SSH)**: klient SSH jest używany do zdalnego łączenia z klastrem usługi HDInsight i uruchamiania poleceń bezpośrednio w klastrze. Aby uzyskać więcej informacji, zobacz [Używanie protokołu SSH w usłudze HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="operationalize-r-server-cluster-with-one-box-configuration"></a>Operacjonalizuj R Server klaster z konfiguracją jednego pola

1. Połącz się z węzłem krawędzi za pomocą protokołu SSH.  

        ssh USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

    Aby uzyskać instrukcje dotyczące sposobu używania protokołu SSH z usługą Azure HDInsight, zobacz [Użyj SSH z usługą HDInsight.](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Zmień katalog dla odpowiednich wersji i sudo dll net kropka: 

    - W przypadku oprogramowania Microsoft R Server 9.1:

            cd /usr/lib64/microsoft-r/rserver/o16n/9.1.0
            sudo dotnet Microsoft.RServer.Utils.AdminUtil/Microsoft.RServer.Utils.AdminUtil.dll

    - W przypadku oprogramowania Microsoft R Server 9.0:

            cd /usr/lib64/microsoft-deployr/9.0.1
            sudo dotnet Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Dostępne są opcje do wyboru. Z pierwszej opcji, jak pokazano na poniższym zrzucie ekranu do **skonfigurować R Server dla Operationalization**.

    ![opernacjonalizacja przy użyciu jednej maszyny](./media/r-server-operationalize/admin-util-one-box-1.png)

4. Teraz otrzymasz opcję, aby wybrać sposób operacjonalizacji R Server. Opcje przedstawioną wybierz pierwszą wprowadzając **A**.

    ![opernacjonalizacja przy użyciu jednej maszyny](./media/r-server-operationalize/admin-util-one-box-2.png)

5. Po wyświetleniu monitu wprowadź i Potwierdź hasło dla użytkownika administratora lokalnego.

6. Powinny pojawić się dane wyjściowe sugerują, że operacja zakończyła się pomyślnie. Monit również wybrać inną opcję z menu. Wybierz E, aby wrócić do menu głównego.

    ![opernacjonalizacja przy użyciu jednej maszyny](./media/r-server-operationalize/admin-util-one-box-3.png)

7. Opcjonalnie można wykonywać diagnostycznych przez uruchomienie testu diagnostycznego w następujący sposób:

    a. Wybierz z menu głównego **6** do uruchomienia testów diagnostycznych.

    ![opernacjonalizacja przy użyciu jednej maszyny](./media/r-server-operationalize/diagnostic-1.png)

    b. Wybierz z menu testów diagnostycznych **A**. Po wyświetleniu monitu wprowadź hasło, podane dla użytkownika administratora lokalnego.

    ![opernacjonalizacja przy użyciu jednej maszyny](./media/r-server-operationalize/diagnostic-2.png)

    c. Sprawdź, czy dane wyjściowe wskazują, że ogólnej kondycji jest przebiegu.

    ![opernacjonalizacja przy użyciu jednej maszyny](./media/r-server-operationalize/diagnostic-3.png)

    d. Z menu opcji dostępnych, wprowadź **E** aby wrócić do menu głównego, a następnie wprowadź **8** aby zamknąć narzędzie administracyjne.

### <a name="long-delays-when-consuming-web-service-on-spark"></a>Duże opóźnienia podczas konsumowania usługi sieci web na Spark

Jeśli wystąpią duże opóźnienia podczas próby korzystania z usługi internetowej utworzonej za pomocą funkcji mrsdeploy w ramach kontekstu obliczeniowego platformy Spark, może być konieczne dodanie niektórych brakujących folderów. Aplikacja Spark należy do użytkownika o nazwie „*rserve2*” zawsze wtedy, gdy jest wywoływana z usługi internetowej przy użyciu funkcji mrsdeploy. Aby obejść ten problem:

    # Create these required folders for user 'rserve2' in local and hdfs:

    hadoop fs -mkdir /user/RevoShare/rserve2
    hadoop fs -chmod 777 /user/RevoShare/rserve2

    mkdir /var/RevoShare/rserve2
    chmod 777 /var/RevoShare/rserve2


    # Next, create a new Spark compute context:
 
    rxSparkConnect(reset = TRUE)


Na tym etapie konfiguracja operacjonalizacji jest ukończona. Teraz można używać `mrsdeploy` pakietu w Twojej RClient nawiązać operationalization węzła krawędzi i rozpocząć korzystanie z jej funkcje, takie jak [zdalne wykonywanie kodu](https://docs.microsoft.com/machine-learning-server/r/how-to-execute-code-remotely) i [usług sieci web](https://docs.microsoft.com/machine-learning-server/operationalize/concept-what-are-web-services). W zależności od tego, czy klaster został skonfigurowany w sieci wirtualnej, może być konieczne skonfigurowanie tunelowania przekierowania portów za pomocą logowania SSH. W poniższych sekcjach wyjaśniono, jak skonfigurować taki tunel.

### <a name="r-server-cluster-on-virtual-network"></a>Klaster R Server w sieci wirtualnej

Sprawdź, czy ruch przez port 12800 węzła krawędzi jest dozwolony. Pozwala to użyć węzła krawędzi do nawiązania połączenia z funkcją operacjonalizacji.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://[your-cluster-name]-ed-ssh.azurehdinsight.net:12800",
        username = "admin",
        password = "xxxxxxx"
    )


Jeśli metoda `remoteLogin()` nie może połączyć się z węzłem krawędzi, lecz nawiązanie połączenia SSH z węzłem krawędzi jest możliwe, sprawdź, czy reguła zezwalająca na ruch przez port 12800 jest skonfigurowana poprawnie. Jeśli problem nie ustąpi, możesz go obejść, konfigurując tunelowanie przekierowania portów przez połączenie SSH. Aby uzyskać instrukcje zobacz sekcję poniżej:

### <a name="r-server-cluster-not-set-up-on-virtual-network"></a>Serwer R klastra nie są skonfigurowane w sieci wirtualnej

Jeśli klaster nie jest skonfigurowany w sieci wirtualnej lub występują problemy z korzystaniem z sieci wirtualnej, możesz użyć tunelowania przekierowania portów za pomocą protokołu SSH:

    ssh -L localhost:12800:localhost:12800 USERNAME@CLUSTERNAME-ed-ssh.azurehdinsight.net

Gdy sesja SSH jest aktywna, ruch z portu 12800 maszyny jest przekazywany do portu 12800 węzła krawędzi za pomocą sesji SSH. Upewnij się, że w metodzie `remoteLogin()` użyto adresu `127.0.0.1:12800`. Logują operationalization węzła krawędzi za pomocą przekierowania portów.


    library(mrsdeploy)

    remoteLogin(
        deployr_endpoint = "http://127.0.0.1:12800",
        username = "admin",
        password = "xxxxxxx"
    )


## <a name="scale-operationalized-compute-nodes-on-hdinsight-worker-nodes"></a>Skala operationalized obliczeniowe węzłów w usłudze HDInsight węzłów procesu roboczego

Skalowanie węzłów obliczeniowych, należy najpierw zlikwidować węzłów procesu roboczego, a następnie skonfiguruj węzłów obliczeniowych w węzłach wycofany z eksploatacji procesu roboczego.

### <a name="step-1-decommission-the-worker-nodes"></a>Krok 1: Zlikwidować węzłów procesu roboczego

Serwer R klastra nie jest zarządzana za pomocą YARN. Jeśli węzłów procesu roboczego nie są wycofany z eksploatacji, Menedżer zasobów YARN nie działa zgodnie z oczekiwaniami, ponieważ nie został powiadomiony o zasoby są zajmowane przez serwer. Aby tego uniknąć, zalecamy zlikwidowanie węzłów procesu roboczego przed przystąpieniem do skalowania węzłów obliczeniowych na zewnątrz.

Wykonaj następujące kroki, aby zlikwidować węzłów procesu roboczego:

1. Zaloguj się do konsoli Ambari klastra i wybierz polecenie **hostów** kartę.

2. Wybierz węzłów procesu roboczego (jako zlikwidowana).

3. Kliknij przycisk **akcje** > **wybrane hosty** > **hostów** > **Włącz tryb konserwacji**. Na przykład na poniższej ilustracji węzły wn3 i wn4 są przeznaczone do likwidacji.  

   ![likwidowanie węzłów procesu roboczego](./media/r-server-operationalize/get-started-operationalization.png)  

* Wybierz **akcje** > **wybrane hosty** > **DataNodes** > kliknij **Decommission**.
* Wybierz **akcje** > **wybrane hosty** > **NodeManagers** > kliknij **Decommission**.
* Wybierz **akcje** > **wybrane hosty** > **DataNodes** > kliknij **zatrzymać**.
* Wybierz **akcje** > **wybrane hosty** > **NodeManagers** > kliknij **zatrzymać**.
* Wybierz **akcje** > **wybrane hosty** > **hostów** > kliknij **zatrzymania wszystkich składników**.
* Usuń zaznaczenie węzłów procesu roboczego i wybierz węzły główne.
* Wybierz **akcje** > **wybrane hosty** > "**hostów** > **Uruchom ponownie wszystkie składniki**.

### <a name="step-2-configure-compute-nodes-on-each-decommissioned-worker-nodes"></a>Krok 2: Konfigurowanie obliczeniowe węzłów na poszczególnych węzłów procesu roboczego wycofany z eksploatacji

1. Za pomocą protokołu SSH połącz się z każdym zlikwidowanym węzłem procesu roboczego.

2. Uruchom narzędzie administracyjne, przy użyciu odpowiednich biblioteki DLL dla klastra serwera R, czy masz. R Server 9.1 Uruchom następujące polecenie:

        dotnet /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Utils.AdminUtil/Microsoft.DeployR.Utils.AdminUtil.dll

3. Wprowadź **1** aby wybrać opcję **skonfigurować R Server dla Operationalization**.

4. Wprowadź **C** aby wybrać opcję `C. Compute node`. Umożliwi to skonfigurowanie węzła obliczeniowego w węźle procesu roboczego.

5. Zamknij narzędzie administracyjne.

### <a name="step-3-add-compute-nodes-details-on-web-node"></a>Krok 3: Dodawanie obliczeniowe szczegóły węzłów w węźle sieci web

Po skonfigurowaniu wszystkich węzłów procesu roboczego wycofany z eksploatacji do uruchomienia węzła obliczeń wrócić na węzeł brzegowy i dodać węzłów procesu roboczego wycofany z eksploatacji adresów IP w konfiguracji węzła sieci web R Server:

1. Połącz się z węzłem krawędzi za pomocą protokołu SSH.

2. Uruchom polecenie `vi /usr/lib64/microsoft-deployr/9.0.1/Microsoft.DeployR.Server.WebAPI/appsettings.json`.

3. Wyszukaj w sekcji "URI" i Dodaj adres IP węzła procesu roboczego i Szczegóły portu.

       "Uris": {
         "Description": "Update 'Values' section to point to your backend machines. Using HTTPS is highly recommended",
         "Values": [
           "http://localhost:12805", "http://[worker-node1-ip]:12805", "http://[workder-node2-ip]:12805"
         ]
       }

## <a name="next-steps"></a>Kolejne kroki

* [Zarządzanie klastrem R Server w usłudze HDInsight](r-server-hdinsight-manage.md)
* [Obliczenia bazy danych opcji kontekstu dla klastra serwera R w usłudze HDInsight](r-server-compute-contexts.md)
* [Opcje magazynu systemu Azure dla klastra serwera R w usłudze HDInsight](r-server-storage.md)