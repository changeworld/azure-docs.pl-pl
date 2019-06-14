---
title: Rozwiązanie do monitorowania kontenerów w usłudze Azure Monitor | Dokumentacja firmy Microsoft
description: Rozwiązanie do monitorowania kontenerów w usłudze Azure Monitor ułatwia wyświetlanie i zarządzanie platformy Docker i Windows hostach kontenerów w jednej lokalizacji.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: e1e4b52b-92d5-4bfa-8a09-ff8c6b5a9f78
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/28/2019
ms.author: magoedte
ms.openlocfilehash: 0a45c84b01cace7e14bd1a945617598c6295631e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60496207"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Rozwiązanie do monitorowania kontenerów w usłudze Azure Monitor

![Symbol kontenerów](./media/containers/containers-symbol.png)

W tym artykule opisano sposób konfigurowania i używania rozwiązania do monitorowania kontenerów w usłudze Azure Monitor, który ułatwia wyświetlanie i zarządzanie platformy Docker i Windows hostach kontenerów w jednej lokalizacji. Docker to system wirtualizacji oprogramowania, jest używany do tworzenia kontenerów, które można zautomatyzować wdrażanie oprogramowania infrastruktury informatycznej.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

To rozwiązanie przedstawia, kontenery, które są uruchomione, jakie obraz kontenera są one uruchamiane i gdzie są uruchomione kontenery. Możesz wyświetlić szczegółowe informacje o inspekcji polecenia używane z kontenerami pokazywanie. I kontenerów można rozwiązać, wyświetlania i przeszukiwania scentralizowanych dzienników bez konieczności zdalnie wyświetlić hostów platformy Docker lub Windows. Możesz znaleźć kontenerów, które mogą być hałas i korzystanie z nich nadmiar zasobów na hoście. Ponadto można wyświetlić scentralizowane procesora CPU, pamięci, magazynu i informacje użycia i wydajności sieci dla kontenerów. Na komputerach z systemem Windows, można scentralizować i porównać dzienników z systemu Windows Server, Hyper-V i kontenery platformy Docker. Rozwiązanie obsługuje następujące koordynatorów kontenerów:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Jeśli interesuje Cię w ramach monitorowania wydajności obciążeń wdrożonych na Kubernetes środowisk hostowanych w usłudze Azure Kubernetes Service (AKS), zobacz [monitora usługi Azure Kubernetes Service](../../azure-monitor/insights/container-insights-overview.md). To rozwiązanie monitorowanie kontenera nie obejmuje pomocy technicznej w celu monitorowania danej platformy.  

Na poniższym diagramie przedstawiono relacje między różnymi hostach kontenerów i agentów z usługą Azure Monitor.

![Diagram kontenerów](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Wymagania systemowe i obsługiwane platformy

Przed rozpoczęciem należy przejrzeć następujące szczegóły, aby sprawdzić, czy zostały spełnione wymagania wstępne.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Rozwiązanie do monitorowania kontenerów pomocy technicznej dla platformy Orchestrator platformy Docker i systemu operacyjnego
W poniższej tabeli przedstawiono organizowania platformy Docker i systemu operacyjnego, monitorowanie obsługi kontenera magazynu, wydajności i dzienników za pomocą usługi Azure Monitor.   

| | ACS | Linux | Windows | Kontener<br>Spis | Image (Obraz)<br>Spis | Węzeł<br>Spis | Kontener<br>Wydajność | Kontener<br>Wydarzenie | Wydarzenie<br>Log | Kontener<br>Log |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Usługa<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Otwórz w systemie Red Hat<br>Shift | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(Wersja autonomiczna) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Serwer systemu Linux<br>(Wersja autonomiczna) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |


### <a name="docker-versions-supported-on-linux"></a>Obsługiwane w systemie Linux wersje platformy docker

- Docker 1.11 do 1.13
- Docker CE i EE v17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>x64 dystrybucje systemu Linux obsługiwane jako hosty kontenerów


- Ubuntu 14.04 LTS i 16.04 LTS
- CoreOS(stable)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7.2 i 7.3
- SLES 12
- RHEL 7.2 i 7.3
- Red Hat OpenShift Container Platform (OCP) 3.4 i 3.5
- Usługi ACS Mesosphere DC/OS 1.7.3 do 1.8.8
- Usługi ACS Kubernetes 1.4.5 do wersji 1.6
    - Zdarzenia Kubernetes, Kubernetes zapasów i kontenerów, procesy są obsługiwane tylko z wersji 1.4.1-45 i później agenta usługi Log Analytics dla systemu Linux
- Usługi ACS Docker Swarm

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Obsługiwany system operacyjny Windows

- Windows Server 2016
- Windows 10 Anniversary Edition (Professional lub Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Obsługiwane na Windows wersje platformy docker

- Docker 1.12 i 1.13
- Docker 17.03.0 lub nowszy

## <a name="installing-and-configuring-the-solution"></a>Instalowanie i konfigurowanie rozwiązania
Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązanie.

1. Dodaj rozwiązanie do monitorowania kontenerów do obszaru roboczego usługi Log Analytics z [portalu Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) lub przy użyciu procesu opisanego w [Dodaj rozwiązania z galerii rozwiązań do monitorowania](../../azure-monitor/insights/solutions.md).

2. Zainstalować i używać platformy Docker przy użyciu agenta usługi Log Analytics. Na podstawie systemu operacyjnego i platformy Docker programu orchestrator, można użyć następujących metod konfigurowania agenta.
   - W przypadku autonomicznych hostów:
     - W obsługiwanych systemach operacyjnych Linux, zainstaluj i uruchom platformy Docker i następnie zainstaluj i skonfiguruj [agenta usługi Log Analytics dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md).  
     - W systemie CoreOS nie można uruchomić agenta usługi Log Analytics dla systemu Linux. Zamiast tego możesz uruchomić konteneryzowanych wersję agenta usługi Log Analytics dla systemu Linux. Przejrzyj hostów kontenera systemu Linux, w tym CoreOS lub hostów kontenera platformy Azure dla instytucji rządowych w systemie Linux, w tym CoreOS, jeśli pracujesz z kontenerami w chmurze Azure Government.
     - W systemie Windows Server 2016 i Windows 10 zainstalować aparat platformy Docker i klienta, a następnie połącz agenta, aby zebrać informacje i wysyłać je do usługi Azure Monitor. Przegląd [Instalowanie i konfigurowanie hostów kontenerów Windows](#install-and-configure-windows-container-hosts) Jeśli w środowisku Windows.
   - Do aranżacji wielu hostów platformy Docker:
     - Jeśli masz środowisko Red Hat OpenShift, zapoznaj się z Konfigurowanie agenta usługi Log Analytics, aby uzyskać Red Hat OpenShift.
     - Jeśli masz klaster usługi Kubernetes przy użyciu usługi Azure Container Service:
       - Przegląd [konfigurowania agenta Log Analytics w systemie Linux dla platformy Kubernetes](#configure-a-log-analytics-linux-agent-for-kubernetes).
       - Przegląd [Konfigurowanie agenta Log Analytics Windows dla rozwiązania Kubernetes](#configure-a-log-analytics-windows-agent-for-kubernetes).
       - Przejrzyj użycie narzędzia Helm, aby wdrożyć agenta Log Analytics na platformie Kubernetes z systemem Linux.
     - Jeśli istnieje klastra usługi Azure Container Service DC/OS, dowiedzieć się więcej o [monitorowanie klastra usługi Azure Container Service DC/OS z usługą Azure Monitor](../../container-service/dcos-swarm/container-service-monitoring-oms.md).
     - Jeśli masz środowisku trybu Docker Swarm, Dowiedz się więcej o Konfigurowanie agenta usługi Log Analytics dla rozwiązania Docker Swarm.
     - Jeśli masz klaster usługi Service Fabric, dowiedzieć się więcej o [monitorowania kontenerów za pomocą usługi Azure Monitor](../../service-fabric/service-fabric-diagnostics-oms-containers.md).

Przegląd [aparat Docker na Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) artykuł, aby uzyskać dodatkowe informacje o sposobie instalowania i konfigurowania silników swojej platformy Docker na komputerach z systemem Windows.

> [!IMPORTANT]
> Platformy docker musi być uruchomiona **przed** instalowania [agenta usługi Log Analytics dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) na hostach kontenerów. Jeśli po zainstalowaniu agenta przed rozpoczęciem instalacji platformy Docker, należy ponownie zainstalować agenta usługi Log Analytics dla systemu Linux. Aby uzyskać więcej informacji na temat platformy Docker, zobacz [witryny sieci Web Docker](https://www.docker.com).


### <a name="install-and-configure-linux-container-hosts"></a>Instalowanie i konfigurowanie hostów kontenera systemu Linux

Po zainstalowaniu platformy Docker, użyj następujących ustawień na hoście kontenera, aby skonfigurować agenta do użycia z rozwiązaniem Docker. Należy najpierw Twojego Identyfikatora obszaru roboczego usługi Log Analytics i klucz, który można znaleźć w witrynie Azure portal. W obszarze roboczym, kliknij przycisk **— Szybki Start** > **komputerów** Aby wyświetlić swoje **identyfikator obszaru roboczego** i **klucz podstawowy**.  Skopiuj i wklej obie wartości do ulubionego edytora.

**Na wszystkich hostach kontenerów systemu Linux z wyjątkiem systemu CoreOS:**

- Aby uzyskać więcej informacji oraz instrukcje dotyczące sposobu instalowania agenta usługi Log Analytics dla systemu Linux, zobacz [omówienie agenta usługi Log Analytics](../../azure-monitor/platform/log-analytics-agent.md).

**Na wszystkich hostach kontenerów systemu Linux CoreOS w tym:**

Uruchom kontener, który chcesz monitorować. Zmodyfikuj i skorzystaj z następującego przykładu:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Na wszystkich hostach kontenerów platformy Azure dla instytucji rządowych w systemie Linux CoreOS w tym:**

Uruchom kontener, który chcesz monitorować. Zmodyfikuj i skorzystaj z następującego przykładu:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Przełączanie z za pomocą zainstalowanego agenta do jednego w kontenerze systemu Linux**

Jeśli wcześniej używana bezpośrednio zainstalować agenta i chcesz zamiast tego użyć agenta uruchomionego w kontenerze, należy najpierw usunąć agenta usługi Log Analytics dla systemu Linux. Zobacz [odinstalowywanie agenta usługi Log Analytics dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) zrozumienie, jak pomyślnie odinstalowania agenta.  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>Skonfiguruj agenta usługi Log Analytics dla rozwiązania Docker Swarm

Możesz uruchomić agenta usługi Log Analytics jako globalne usługi w Docker Swarm. Skorzystaj z poniższych informacji, aby utworzyć usługę agenta usługi Log Analytics. Należy podać identyfikator obszaru roboczego usługi Log Analytics i klucz podstawowy.

- Uruchom następujące polecenie w węźle głównym.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Zabezpiecz wpisy tajne dla rozwiązania Docker Swarm

Po utworzeniu wpisu tajnego dla Identyfikatora obszaru roboczego i klucz podstawowy dla rozwiązania Docker Swarm, skorzystaj z poniższych informacji do utworzenia wpisu tajnego informacji.

1. Uruchom następujące polecenie w węźle głównym.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Sprawdź, czy klucze tajne zostały utworzone prawidłowo.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Uruchom następujące polecenie, aby zainstalować kluczy tajnych konteneryzowanych agenta usługi Log Analytics.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>Skonfiguruj agenta usługi Log Analytics dla Red Hat OpenShift
Istnieją trzy sposoby, aby dodać agenta usługi Log Analytics do Red Hat OpenShift, aby rozpocząć zbieranie danych monitorowania kontenera.

* [Zainstaluj agenta usługi Log Analytics dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) bezpośrednio w każdym węźle platformy OpenShift  
* [Włączanie rozszerzenia Log Analytics VM Extension](../../azure-monitor/learn/quick-collect-azurevm.md) w każdym węźle OpenShift znajdującej się na platformie Azure  
* Zainstaluj agenta usługi Log Analytics jako zestawie demona platformy OpenShift  

W tej sekcji omówione kroki wymagane do zainstalowania agenta usługi Log Analytics jako zestawie demona platformy OpenShift.  

1. Zaloguj się do węzła głównego OpenShift, a następnie skopiuj plik yaml [ocp omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) z serwisu GitHub, aby Twój węzeł główny i zmodyfikuj wartość za pomocą Identyfikatora obszaru roboczego analizy dzienników i klucz podstawowy.
2. Uruchom następujące polecenia, aby utworzyć projekt dla usługi Azure Monitor i konto użytkownika.

    ```
    oadm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. Aby wdrożyć zestaw demona, uruchom następujące polecenie:

    `oc create -f ocp-omsagent.yaml`

5. Aby sprawdzić, czy jest ono skonfigurowane i działa prawidłowo, wpisz następujące polecenie:

    `oc describe daemonset omsagent`  

    i dane wyjściowe powinny przypominać:

    ```
    [ocpadmin@khm-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

Jeśli chcesz użyć kluczy tajnych do zabezpieczenia Twojego Identyfikatora obszaru roboczego usługi Log Analytics i klucz podstawowy, korzystając z pliku yaml zestaw demona agenta usługi Log Analytics, wykonaj następujące czynności.

1. Zaloguj się do węzła głównego OpenShift, a następnie skopiuj plik yaml [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) i wpis tajny Generowanie skryptu [ocp secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) z usługi GitHub.  Ten skrypt spowoduje wygenerowanie pliku yaml wpisów tajnych dla Identyfikatora obszaru roboczego usługi Log Analytics i klucz podstawowy zabezpieczyć swoje secrete informacji.  
2. Uruchom następujące polecenia, aby utworzyć projekt dla usługi Azure Monitor i konto użytkownika. Klucz tajny, Generowanie skryptu poprosi o podanie Identyfikatora obszaru roboczego analizy dzienników `<WSID>` i klucz podstawowy `<KEY>` i po jego ukończeniu, tworzy plik ocp secret.yaml.  

    ```
    oadm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. Wdróż plik wpisów tajnych, uruchamiając następujące czynności:

    `oc create -f ocp-secret.yaml`

5. Sprawdź wdrożenie, uruchamiając następujące czynności:

    `oc describe secret omsagent-secret`  

    i dane wyjściowe powinny przypominać:  

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe ds oms  
    Name:           oms  
    Image(s):       microsoft/oms  
    Selector:       name=omsagent  
    Node-Selector:  zone=default  
    Labels:         agentVersion=1.4.0-12  
                    dockerProviderVersion=10.0.0-25  
                    name=omsagent  
    Desired Number of Nodes Scheduled: 3  
    Current Number of Nodes Scheduled: 3  
    Number of Nodes Misscheduled: 0  
    Pods Status:    3 Running / 0 Waiting / 0 Succeeded / 0 Failed  
    No events.  
    ```

6. Wdróż plik yaml zestaw demona agenta usługi Log Analytics, uruchamiając następujące:

    `oc create -f ocp-ds-omsagent.yaml`  

7. Sprawdź wdrożenie, uruchamiając następujące czynności:

    `oc describe ds oms`

    i dane wyjściowe powinny przypominać:

    ```
    [ocpadmin@khocp-master-0 ~]$ oc describe secret omsagent-secret  
    Name:           omsagent-secret  
    Namespace:      omslogging  
    Labels:         <none>  
    Annotations:    <none>  

    Type:   Opaque  

     Data  
     ====  
     KEY:    89 bytes  
     WSID:   37 bytes  
    ```

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Skonfiguruj agenta Log Analytics w systemie Linux dla platformy Kubernetes

Dla rozwiązania Kubernetes możesz użyć skryptu do generowania pliku yaml wpisów tajnych dla Identyfikatora obszaru roboczego i klucz podstawowy zainstalować agenta usługi Log Analytics dla systemu Linux. W [Log Analytics Docker Kubernetes w usłudze GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) strony, dostępne są pliki korzystające z lub bez niego dane poufne.

- Agent domyślne usługi Log Analytics dla systemu Linux DaemonSet ma tajnych informacji (omsagent.yaml)
- Agenta usługi Log Analytics dla systemu Linux DaemonSet pliku yaml używa tajnych informacji (omsagent-ds-secrets.yaml) przy użyciu klucza tajnego generowania skryptów do wygenerowania pliku yaml (omsagentsecret.yaml) wpisów tajnych.

Można utworzyć omsagent DaemonSets z lub bez wpisów tajnych.

**Pliku yaml OMSagent DaemonSet domyślnej bez tajemnic**

- Domyślne usługi Log Analytics agent DaemonSet pliku yaml, Zastąp `<WSID>` i `<KEY>` WSID i klucz. Skopiuj plik na Twój węzeł główny i uruchom następujące polecenie:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Pliku yaml OMSagent DaemonSet domyślne przy użyciu kluczy tajnych**

1. Usługa Log Analytics agent DaemonSet przy użyciu informacji tajnych, wpisy tajne najpierw utworzyć.
    1. Skopiuj skrypt i plik szablonu wpisu tajnego i upewnij się, że znajdują się w tym samym katalogu.
        - Generowanie skryptu - gen.sh klucz tajny klucz tajny
        - Szablon tajny - template.yaml wpisu tajnego
    2. Uruchom skrypt, jak w poniższym przykładzie. Skrypt prosi o identyfikator obszaru roboczego usługi Log Analytics i klucz podstawowy, a po ich wprowadzeniu, skrypt tworzy plik yaml wpisu tajnego, aby można było go uruchomić.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Utwórz pod wpisami tajnymi, uruchamiając następujące:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Aby sprawdzić, uruchom następujące polecenie:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        Dane wyjściowe powinny przypominać:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        Dane wyjściowe powinny przypominać:

        ```
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Utwórz swoje omsagent demona zestawu, uruchamiając ```sudo kubectl create -f omsagent-ds-secrets.yaml```

2. Sprawdź, czy agent usługi Log Analytics DaemonSet jest uruchomiona, podobny do następującego:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```


Dla rozwiązania Kubernetes należy użyć skryptu, aby wygenerować plik yaml wpisów tajnych dla Identyfikatora obszaru roboczego i klucz podstawowy dla agenta usługi Log Analytics dla systemu Linux. Skorzystaj z poniższych informacji przykład za pomocą [pliku yaml omsagent](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) zabezpieczyć dane poufne.

```
keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
Name:           omsagent-secret
Namespace:      default
Labels:         <none>
Annotations:    <none>

Type:   Opaque

Data
====
WSID:   36 bytes
KEY:    88 bytes
```

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>Skonfiguruj agenta Log Analytics Windows dla rozwiązania Kubernetes
Dla rozwiązania Kubernetes Windows skrypt będzie używany do generowania pliku yaml wpisów tajnych dla Identyfikatora obszaru roboczego i klucz podstawowy zainstalować agenta usługi Log Analytics. W [Log Analytics Docker Kubernetes w usłudze GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) strony, dostępne są pliki korzystające z informacjami klucza tajnego.  Musisz zainstalować agenta usługi Log Analytics oddzielnie dla głównych i węzłów agentów.  

1. Aby użyć agenta usługi Log Analytics DaemonSet przy użyciu tajnych informacji we wzorcu węzła, zaloguj się i najpierw utworzyć wpisy tajne.
    1. Skopiuj skrypt i plik szablonu wpisu tajnego i upewnij się, że znajdują się w tym samym katalogu.
        - Generowanie skryptu - gen.sh klucz tajny klucz tajny
        - Szablon tajny - template.yaml wpisu tajnego

    2. Uruchom skrypt, jak w poniższym przykładzie. Skrypt prosi o identyfikator obszaru roboczego usługi Log Analytics i klucz podstawowy, a po ich wprowadzeniu, skrypt tworzy plik yaml wpisu tajnego, aby można było go uruchomić.   

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Utwórz swoje omsagent demona zestawu, uruchamiając ```kubectl create -f omsagentsecret.yaml```
    4. Aby sprawdzić, uruchom następujące polecenie:

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        Dane wyjściowe powinny przypominać:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        root@ubuntu16-13db:~# kubectl describe secrets omsagent-secret
        Name:           omsagent-secret
        Namespace:      default
        Labels:         <none>
        Annotations:    <none>

        Type:   Opaque

        Data
        ====
        WSID:   36 bytes
        KEY:    88 bytes
        ```

    5. Utwórz swoje omsagent demona zestawu, uruchamiając ```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Sprawdź, czy agent usługi Log Analytics DaemonSet jest uruchomiona, podobny do następującego:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Aby zainstalować agenta w węźle procesu roboczego systemem Windows, wykonaj kroki opisane w sekcji [zainstalować i skonfigurować hosty kontenerów Windows](#install-and-configure-windows-container-hosts).

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Użyj narzędzia Helm, aby wdrożyć agenta Log Analytics na platformie Kubernetes z systemem Linux
Aby wdrożyć agenta Log Analytics w środowisku Kubernetes w systemie Linux przy użyciu narzędzia helm, wykonaj następujące kroki.

1. Utwórz swoje omsagent demona zestawu, uruchamiając ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
2. Wyniki będzie wyglądać podobnie do następującego:

    ```
    NAME:   omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED

    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     3s

    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         3s
    ```
3. Możesz sprawdzić stan omsagent, uruchamiając: ```helm status "omsagent"``` i dane wyjściowe będą wyglądać podobnie do poniższego:

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
   Aby uzyskać więcej informacji, odwiedź [narzędzia Helm rozwiązania kontenera](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Instalowanie i konfigurowanie hostach kontenerów Windows

Skorzystaj z informacji w sekcji, aby zainstalować i skonfigurować hosty kontenerów Windows.

#### <a name="preparation-before-installing-windows-agents"></a>Przygotowanie przed zainstalowaniem agentów Windows

Przed zainstalowaniem agentów na komputerach z systemem Windows, należy skonfigurować usługę Docker. Konfiguracja umożliwia Windows z agentem lub rozszerzenie maszyny wirtualnej usługi Azure Monitor do użycia gniazda TCP platformy Docker, dzięki czemu agenci mogą uzyskiwać dostęp do demona platformy Docker zdalnie i przechwytywania danych monitorowania.

##### <a name="to-configure-the-docker-service"></a>Aby skonfigurować usługę Docker  

Wykonaj następujące polecenia programu PowerShell, aby włączyć potoku TCP i nazwanych potoków dla systemu Windows Server:

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

Aby uzyskać więcej informacji na temat konfigurację demona platformy Docker, które są używane, za pomocą kontenerów Windows zobacz [aparat Docker na Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).


#### <a name="install-windows-agents"></a>Zainstaluj agentów Windows

Aby włączyć monitorowanie kontenerów Windows i funkcji Hyper-V, należy zainstalować program Microsoft Monitoring Agent (MMA) na komputerach Windows, znajdujących się na hostach kontenerów. Na komputerach z systemem Windows w środowisku w środowisku lokalnym, zobacz [Windows łączenie komputerów do usługi Azure Monitor](../../azure-monitor/platform/agent-windows.md). W przypadku maszyn wirtualnych działających na platformie Azure, łącz je z przy użyciu usługi Azure Monitor [rozszerzenie maszyny wirtualnej](../../azure-monitor/learn/quick-collect-azurevm.md).

Można monitorować kontenery Windows, działające w usłudze Service Fabric. Jednak tylko [maszyn wirtualnych działających na platformie Azure](../../azure-monitor/learn/quick-collect-azurevm.md) i [komputerów z systemem Windows w środowisku lokalnych](../../azure-monitor/platform/agent-windows.md) są obecnie obsługiwane dla usługi Service Fabric.

Aby sprawdzić, czy rozwiązanie do monitorowania kontenerów jest ustawione prawidłowo dla Windows. Aby sprawdzić, czy pakiet administracyjny został prawidłowo pobierania, poszukaj *ContainerManagement.xxx*. Pliki powinny być w folderze C:\Program Files\Microsoft Monitoring Agent\Agent\Health usługi State\Management Packs.


## <a name="solution-components"></a>Składniki rozwiązania

W witrynie Azure portal, przejdź do *galerii rozwiązań* i Dodaj **rozwiązanie do monitorowania kontenerów**. Jeśli używasz agenci Windows następujące pakiety administracyjne jest zainstalowany na każdym komputerze z agentem po dodaniu tego rozwiązania. Żadna konfiguracja ani obsługa jest wymagana dla pakietu administracyjnego.

- *ContainerManagement.xxx* zainstalowane w C:\Program Files\Microsoft Monitoring Agent\Agent\Health State\Management dodatki

## <a name="container-data-collection-details"></a>Szczegóły zbierania danych kontenera
To rozwiązanie monitorowanie kontenera zbiera różne dane metryk i dzienników wydajności z hostach kontenerów i kontenery przy użyciu agentów, które zostanie włączone.

Dane są gromadzone co trzy minuty przez następujące typy agenta.

- [Log Analytics agent dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md)
- [Windows agent](../../azure-monitor/platform/agent-windows.md)
- [Rozszerzenia log Analytics VM extension](../../azure-monitor/learn/quick-collect-azurevm.md)


### <a name="container-records"></a>Rekordów kontenera

W poniższej tabeli przedstawiono przykłady rekordów zbieranych przez to rozwiązanie monitorowanie kontenerów i typy danych, które są wyświetlane w wynikach wyszukiwania w dzienniku.

| Typ danych | Typ danych podczas wyszukiwania dziennika | Pola |
| --- | --- | --- |
| Wydajność dla hostów i kontenerów | `Perf` | CounterName komputera, nazwa obiektu, &#40;czas procesora (%), dysk odczytuje MB, dysku zapisuje MB, MB użycia pamięci, sieci odbierania bajtów, sieci wysyłania w bajtach, procesor s użycia, sieć&#41;, CounterValue, TimeGenerated, Ścieżka_licznika, system źródłowy |
| Kontener magazynu | `ContainerInventory` | TimeGenerated, komputera, nazwa kontenera, ContainerHostname, obraz, ImageTag, ContainerState, ExitCode, EnvironmentVar, polecenia, wartością CreatedTime, StartedTime, FinishedTime, system źródłowy, ContainerID, ImageID |
| Spis obrazów kontenera | `ContainerImageInventory` | TimeGenerated, komputer, obraz, ImageTag, ImageSize, VirtualSize, uruchamianie, wstrzymana, zatrzymana, nie powiodło się, system źródłowy, ImageID, TotalContainer |
| Dziennik kontenera | `ContainerLog` | TimeGenerated, komputer, identyfikator obrazu, pola Nazwa kontenera LogEntrySource, LogEntry, system źródłowy, ContainerID |
| Dziennik usługi kontenera | `ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |
| Spis węzłów kontenerów | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Spis rozwiązania Kubernetes | `KubePodInventory_CL` | TimeGenerated, Computer, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Proces kontenera | `ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Zdarzenia Kubernetes | `KubeEvents_CL` | TimeGenerated, Computer, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, Message |

Etykiety są dołączane do *PodLabel* typy danych są etykiet niestandardowych. Przykładami są dołączonych etykiety PodLabel pokazano w tabeli. Dlatego `PodLabel_deployment_s`, `PodLabel_deploymentconfig_s`, `PodLabel_docker_registry_s` będą różnić się w zestawie danych w danym środowisku i ogólnie wyglądać `PodLabel_yourlabel_s`.


## <a name="monitor-containers"></a>Monitorowanie kontenerów
Po utworzeniu rozwiązania włączone w witrynie Azure portal **kontenery** Kafelek zawiera podsumowanie informacji o hostach kontenerów i kontenery działające na hostach.


![Kafelek kontenerów](./media/containers/containers-title.png)

Kafelek Wyświetla liczbę kontenerów, masz w środowisku, czy są one nie powiodło się, uruchomiona lub zatrzymana.

### <a name="using-the-containers-dashboard"></a>Korzystanie z pulpitu nawigacyjnego kontenerów
Kliknij przycisk **kontenery** kafelka. W tym miejscu zobaczysz widoki uporządkowane według:

- **Zdarzenia kontenera** — pokazuje stan kontenera i komputerami przy użyciu kontenery z błędem.
- **Dzienniki kontenerów** — zawiera wykres pliki dziennika kontenera generowanym przez czas i listę komputerów z najwyższą liczbą plików dziennika.
- **Zdarzenia Kubernetes** — zawiera wykres generowanych na przestrzeni czasu i lista powodów dlaczego zasobników wygenerowanych zdarzeń zdarzeń Kubernetes. *Ten zestaw danych jest używana tylko w środowiskach systemu Linux.*
- **Spis Namespace Kubernetes** — pokazuje liczbę przestrzenie nazw i zasobników i przedstawiono ich hierarchii. *Ten zestaw danych jest używana tylko w środowiskach systemu Linux.*
- **Spis węzłów kontenerów** — pokazuje liczbę typów aranżacji używany na hostach węzły kontenerów. Komputer węzły/hosty są także wyświetlane przez liczbę kontenerów. *Ten zestaw danych jest używana tylko w środowiskach systemu Linux.*
- **Spis obrazów kontenera** — pokazuje, całkowita liczba obrazów kontenerów, używane i liczba typów obrazu. Liczba obrazów są także wyświetlane przez tag obrazu.
- **Stan kontenerów** — Pokazuje łączną liczbę kontenerów węzły/hostów, które zostały uruchomione kontenery. Komputery są także wyświetlane według liczby uruchomionych na hostach.
- **Proces kontenera** — przedstawia wykres liniowy kontener procesy uruchomione wraz z upływem czasu. Kontenery są również wyszczególnione przez uruchomienie polecenia i przetwarzanie w kontenerach. *Ten zestaw danych jest używana tylko w środowiskach systemu Linux.*
- **Wydajność procesora CPU kontenera** — przedstawia wykres liniowy w średnie wykorzystanie procesora CPU w miarę upływu czasu hosty węzłów komputera. Również Wyświetla komputera węzły/hosty na podstawie średniego wykorzystanie procesora CPU.
- **Wydajność pamięci kontenera** — przedstawia wykres liniowy, użycia pamięci w czasie. Zawiera również listę pamięci komputera użycie na podstawie wystąpienia nazwy.
- **Wydajność komputera** — pokazuje wykresów liniowych procent wydajności procesorów CPU, wraz z upływem czasu, procent użycia pamięci przestrzeni czasu i megabajtów wolnego miejsca na dysku wraz z upływem czasu. Możesz umieścić kursor żadnych linii na wykresie, aby wyświetlić więcej szczegółów.


Każdy obszar pulpitu nawigacyjnego jest wizualną reprezentację wyszukiwania, który jest uruchamiany na zebranych danych.

![Pulpit nawigacyjny rozwiązania containers](./media/containers/containers-dash01.png)

![Pulpit nawigacyjny rozwiązania containers](./media/containers/containers-dash02.png)

W **stan kontenera** obszaru, kliknij przycisk górnym obszarze, jak pokazano poniżej.

![Stan kontenerów](./media/containers/containers-status.png)

Usługa log Analytics zostanie otwarty, informacje o stanie kontenerów.

![Usługa log Analytics dla kontenerów](./media/containers/containers-log-search.png)

W tym miejscu można edytować zapytania wyszukiwania można zmodyfikować, aby znaleźć konkretne informacje, czy interesuje Cię. Aby uzyskać więcej informacji o zapytaniach dzienników, zobacz [rejestrowania zapytań w usłudze Azure Monitor](../log-query/log-query-overview.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Rozwiązywanie problemów, wyszukując kontenera nie powiodło się

Usługa log Analytics oznacza kontenera jako **niepowodzenie** Jeśli został zakończony z kodem zakończenia różny od zera. Zostanie wyświetlony przegląd błędy i awarie w środowisku w **kontenerów nie powiodło się** obszaru.

### <a name="to-find-failed-containers"></a>Aby znaleźć kontenery z błędem
1. Kliknij przycisk **stan kontenera** obszaru.  
   ![Stan kontenerów](./media/containers/containers-status.png)
2. Usługa log Analytics otwiera i wyświetla stan kontenerów, podobny do następującego.  
   ![Stan kontenerów](./media/containers/containers-log-search.png)
3. Rozwiń węzeł wiersza nie powiodło się, a następnie kliknij przycisk +, aby dodać jego kryteria zapytania. Następnie komentarz wiersz Summarize w zapytaniu.
   ![kontenery z błędem](./media/containers/containers-state-failed-select.png)  
1. Uruchom zapytanie, a następnie rozwiń węzeł wiersza w wynikach, aby wyświetlić identyfikatora obrazu.  
   ![kontenery z błędem](./media/containers/containers-state-failed.png)  
1. Wpisz następujące polecenie w zapytaniu dziennika. `ContainerImageInventory | where ImageID == <ImageID>` Aby wyświetlić szczegóły dotyczące obrazu, takie jak rozmiar obrazu i Liczba obrazów zatrzymane, a nie powiodło się.  
   ![kontenery z błędem](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Zapytanie dzienniki dla kontenera danych
W przypadku Rozwiązywanie problemów z określonym błędem, może pomóc aby zobaczyć, gdzie występuje w danym środowisku. Następujące typy dziennika są pomocne podczas tworzenia zapytania do zwracania informacji, że chcesz użyć.


- **ContainerImageInventory** — Użyj tego typu, które próbujesz odnaleźć informacje są organizowane według obrazu i wyświetlić informacje o obrazie, takich jak obraz lub identyfikatory rozmiarów.
- **ContainerInventory** — tego typu należy użyć informacji o lokalizację kontenera, co to są nazwami i co obrazy są one uruchamiane.
- **ContainerLog** — Użyj tego typu, aby znaleźć konkretny błąd informacji dziennika i wpisów.
- **ContainerNodeInventory_CL** tego typu należy użyć informacji o hoście/node gdzie kontenery są znajdującej się. Umożliwia wersji platformy Docker, typ aranżacji, magazynu i informacje o sieci.
- **ContainerProcess_CL** Użyj tego typu, aby szybko wyświetlić proces uruchomiony w kontenerze.
- **ContainerServiceLog** — Użyj tego typu, które próbujesz znaleźć informacje o dziennik inspekcji dla demona platformy Docker, takich jak uruchamianie, zatrzymywanie, usuń lub ściągania poleceń.
- **KubeEvents_CL** Użyj tego typu, aby zobaczyć zdarzenia Kubernetes.
- **KubePodInventory_CL** Użyj tego typu, które chcesz poznać informacje o klastrze hierarchii.


### <a name="to-query-logs-for-container-data"></a>Zapytanie dzienniki dla kontenera danych
* Wybierz obraz, który znasz ostatnio nie powiodło się i znajdowanie dzienników błędów dla niego. Rozpocznij, wyszukując nazwę kontenera, która jest uruchomiony ten obraz z **ContainerInventory** wyszukiwania. Na przykład wyszukiwanie `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Wyszukaj kontenery Ubuntu](./media/containers/search-ubuntu.png)

  Rozwiń wszystkie wiersze w wynikach, aby wyświetlić szczegóły dla tego kontenera.


## <a name="example-log-queries"></a>Przykładowe zapytania dziennika
Często jest to przydatne do tworzenia zapytań, począwszy od przykładem lub dwóch, a następnie modyfikować ją do dopasowane do środowiska. Jako punktu wyjścia, możesz eksperymentować z **przykładowe zapytania** obszar, aby pomóc w utworzeniu bardziej zaawansowanych zapytań.

![Kontenery zapytań](./media/containers/containers-queries.png)


## <a name="saving-log-queries"></a>Zapisywanie zapytań log
Zapisywanie zapytań jest funkcją standard w usłudze Azure Monitor. Zapisując je, będziesz mieć tych, które znajdziesz przydatne przydatna do użytku w przyszłości.

Po utworzeniu zapytania, które możesz się przydać, zapisz go, klikając **ulubione** w górnej części strony wyszukiwanie w dziennikach. Nie będzie można łatwo uzyskać dostępu to później **Mój pulpit nawigacyjny** strony.

## <a name="next-steps"></a>Kolejne kroki
* [Zapytanie dzienniki](../log-query/log-query-overview.md) do wyświetlania rekordów danych szczegółowych kontenera.
