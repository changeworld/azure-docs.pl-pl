---
title: Rozwiązanie do monitorowania kontenerów w Azure Monitor | Microsoft Docs
description: Rozwiązanie do monitorowania kontenerów w Azure Monitor ułatwia wyświetlanie hostów platformy Docker i kontenerów systemu Windows oraz zarządzanie nimi w jednej lokalizacji.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/22/2019
ms.openlocfilehash: 171f897f6e110e8f759281c139addab477ecede3
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664698"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Rozwiązanie do monitorowania kontenerów w Azure Monitor

![Symbol kontenerów](./media/containers/containers-symbol.png)

W tym artykule opisano sposób konfigurowania i używania rozwiązania do monitorowania kontenerów w Azure Monitor, które ułatwia wyświetlanie hostów platformy Docker i kontenerów systemu Windows oraz zarządzanie nimi w jednej lokalizacji. Docker to system wirtualizacji oprogramowania, jest używany do tworzenia kontenerów, które można zautomatyzować wdrażanie oprogramowania infrastruktury informatycznej.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

To rozwiązanie przedstawia, kontenery, które są uruchomione, jakie obraz kontenera są one uruchamiane i gdzie są uruchomione kontenery. Możesz wyświetlić szczegółowe informacje o inspekcji polecenia używane z kontenerami pokazywanie. I kontenerów można rozwiązać, wyświetlania i przeszukiwania scentralizowanych dzienników bez konieczności zdalnie wyświetlić hostów platformy Docker lub Windows. Możesz znaleźć kontenerów, które mogą być hałas i korzystanie z nich nadmiar zasobów na hoście. Ponadto można wyświetlić scentralizowane procesora CPU, pamięci, magazynu i informacje użycia i wydajności sieci dla kontenerów. Na komputerach z systemem Windows, można scentralizować i porównać dzienników z systemu Windows Server, Hyper-V i kontenery platformy Docker. Rozwiązanie obsługuje następujące koordynatorów kontenerów:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Jeśli masz kontenery wdrożone w [usłudze Azure Service Fabric](../../service-fabric/service-fabric-overview.md), zalecamy włączenie [rozwiązania Service Fabric](../../service-fabric/service-fabric-diagnostics-oms-setup.md) i tego rozwiązania w celu uwzględnienia monitorowania zdarzeń klastra. Przed włączeniem rozwiązania Service Fabric, zapoznaj się z tematem [Service Fabric rozwiązanie](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) , aby zrozumieć, jakie informacje zawiera i jak z nich korzystać.

Jeśli interesuje Cię monitorowanie wydajności obciążeń wdrożonych w środowiskach Kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS), zobacz [monitorowanie usługi Azure Kubernetes](../../azure-monitor/insights/container-insights-overview.md). Rozwiązanie do monitorowania kontenerów nie obsługuje monitorowania tej platformy.  

Na poniższym diagramie przedstawiono relacje między różnymi hostami kontenerów i agentami z Azure Monitor.

![Diagram kontenerów](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Wymagania systemowe i obsługiwane platformy

Przed rozpoczęciem należy przejrzeć następujące szczegóły, aby sprawdzić, czy zostały spełnione wymagania wstępne.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Rozwiązanie do monitorowania kontenerów pomocy technicznej dla platformy Orchestrator platformy Docker i systemu operacyjnego

W poniższej tabeli przedstawiono wsparcie w zakresie organizowania i monitorowania systemu operacyjnego platformy Docker, a następnie obsługę spisu kontenerów, wydajności i dzienników przy użyciu Azure Monitor.   

| | ACS | Linux | System Windows | Kontener<br>Spis | Image (Obraz)<br>Spis | Węzeł<br>Spis | Kontener<br>Wydajność | Kontener<br>Wydarzenie | Wydarzenie<br>Log | Kontener<br>Log |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Usługa<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Otwórz w systemie Red Hat<br>Shift | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Oprogramowanie Windows Server<br>(Wersja autonomiczna) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
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

1. Dodaj rozwiązanie do monitorowania kontenerów do obszaru roboczego Log Analytics z [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) lub przy użyciu procesu opisanego w temacie [Dodawanie rozwiązań monitorowania z Galeria rozwiązań](../../azure-monitor/insights/solutions.md).

2. Zainstalować i używać platformy Docker przy użyciu agenta usługi Log Analytics. Na podstawie systemu operacyjnego i platformy Docker programu orchestrator, można użyć następujących metod konfigurowania agenta.
   - W przypadku autonomicznych hostów:
     - W obsługiwanych systemach operacyjnych Linux Zainstaluj i Uruchom platformę Docker, a następnie zainstaluj i skonfiguruj [agenta log Analytics dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md).  
     - W systemie CoreOS nie można uruchomić agenta usługi Log Analytics dla systemu Linux. Zamiast tego możesz uruchomić konteneryzowanych wersję agenta usługi Log Analytics dla systemu Linux. Przejrzyj hosty kontenerów systemu Linux, w tym CoreOS lub Azure Government hosty z systemem Linux, w tym CoreOS, jeśli pracujesz z kontenerami w Azure Government Cloud.
     - W systemach Windows Server 2016 i Windows 10 Zainstaluj aparat platformy Docker i klienta, a następnie połącz agenta w celu zebrania informacji i wysłania go do Azure Monitor. Zapoznaj się z tematem [Instalowanie i Konfigurowanie hostów kontenera systemu Windows](#install-and-configure-windows-container-hosts) , jeśli masz środowisko systemu Windows.
   - Do aranżacji wielu hostów platformy Docker:
     - Jeśli masz środowisko Red Hat OpenShift, zapoznaj się z tematem Konfigurowanie agenta Log Analytics dla Red Hat OpenShift.
     - Jeśli masz klaster usługi Kubernetes przy użyciu usługi Azure Container Service:
       - Zapoznaj [się z tematem konfigurowanie log Analytics agenta systemu Linux dla usługi Kubernetes](#configure-a-log-analytics-linux-agent-for-kubernetes).
       - Zapoznaj [się z tematem konfigurowanie log Analytics agenta systemu Windows dla Kubernetes](#configure-a-log-analytics-windows-agent-for-kubernetes).
       - Zapoznaj się z tematem korzystanie z Helm w celu wdrożenia agenta Log Analytics w systemie Linux Kubernetes.
     - Jeśli masz klaster DC/OS Azure Container Service, Dowiedz się więcej na temat [monitorowania Azure Container Service klastra DC/OS przy użyciu Azure monitor](../../container-service/dcos-swarm/container-service-monitoring-oms.md).
     - Jeśli masz środowisko Docker Swarm Mode, Dowiedz się więcej na temat konfigurowania agenta Log Analytics dla platformy Docker Swarm.
     - Jeśli masz klaster Service Fabric, Dowiedz się więcej w obszarze [monitorowanie kontenerów z Azure monitor](../../service-fabric/service-fabric-diagnostics-oms-containers.md).

Zapoznaj się z artykułem [aparat platformy Docker w systemie Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) , aby uzyskać dodatkowe informacje na temat instalowania i konfigurowania aparatów platformy Docker na komputerach z systemem Windows.

> [!IMPORTANT]
> Program Docker musi być uruchomiony **przed** zainstalowaniem [agenta log Analytics dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) na hostach kontenerów. Jeśli po zainstalowaniu agenta przed rozpoczęciem instalacji platformy Docker, należy ponownie zainstalować agenta usługi Log Analytics dla systemu Linux. Aby uzyskać więcej informacji na temat platformy Docker, zobacz [witrynę sieci Web platformy Docker](https://www.docker.com).

### <a name="install-and-configure-linux-container-hosts"></a>Instalowanie i konfigurowanie hostów kontenera systemu Linux

Po zainstalowaniu platformy Docker, użyj następujących ustawień na hoście kontenera, aby skonfigurować agenta do użycia z rozwiązaniem Docker. Należy najpierw Twojego Identyfikatora obszaru roboczego usługi Log Analytics i klucz, który można znaleźć w witrynie Azure portal. W obszarze roboczym kliknij pozycję **Szybki start** > **komputery** , aby wyświetlić **Identyfikator obszaru roboczego** i **klucz podstawowy**.  Skopiuj i wklej obie wartości do ulubionego edytora.

**Dla wszystkich hostów kontenera systemu Linux z wyjątkiem CoreOS:**

- Aby uzyskać więcej informacji i zapoznać się z instrukcjami dotyczącymi sposobu instalowania agenta Log Analytics dla systemu Linux, zobacz [Omówienie agenta usługi log Analytics](../../azure-monitor/platform/log-analytics-agent.md).

**Wszystkie hosty kontenerów systemu Linux, w tym CoreOS:**

Uruchom kontener, który chcesz monitorować. Zmodyfikuj i skorzystaj z następującego przykładu:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Wszystkie hosty kontenerów Azure Government Linux, w tym CoreOS:**

Uruchom kontener, który chcesz monitorować. Zmodyfikuj i skorzystaj z następującego przykładu:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Przełączanie z używania zainstalowanego agenta systemu Linux do jednego w kontenerze**

Jeśli wcześniej używana bezpośrednio zainstalować agenta i chcesz zamiast tego użyć agenta uruchomionego w kontenerze, należy najpierw usunąć agenta usługi Log Analytics dla systemu Linux. Zobacz [Odinstalowywanie agenta log Analytics dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) , aby poznać sposób pomyślnego odinstalowania agenta.  

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

* [Zainstaluj agenta log Analytics dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) bezpośrednio w każdym węźle OpenShift  
* [Włącz log Analytics rozszerzenie maszyny wirtualnej](../../azure-monitor/learn/quick-collect-azurevm.md) na każdym węźle OpenShift znajdującym się na platformie Azure  
* Instalowanie agenta Log Analytics jako zestawu demonów OpenShift  

W tej sekcji omówione kroki wymagane do zainstalowania agenta usługi Log Analytics jako zestawie demona platformy OpenShift.  

1. Zaloguj się do węzła głównego OpenShift i skopiuj plik YAML [OCP-omsagent. YAML](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) z usługi GitHub do węzła głównego i zmodyfikuj wartość przy użyciu identyfikatora obszaru roboczego log Analytics i klucza podstawowego.
2. Uruchom następujące polecenia, aby utworzyć projekt dla Azure Monitor i ustawić konto użytkownika.

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Aby wdrożyć zestaw demona, uruchom następujące polecenie:

    `oc create -f ocp-omsagent.yaml`

4. Aby sprawdzić, czy jest ono skonfigurowane i działa prawidłowo, wpisz następujące polecenie:

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

1. Zaloguj się do węzła głównego OpenShift i skopiuj plik YAML [OCP-ds-omsagent. YAML](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) i Secret generujący skrypt [OCP-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) z usługi GitHub.  Ten skrypt spowoduje wygenerowanie pliku yaml wpisów tajnych dla Identyfikatora obszaru roboczego usługi Log Analytics i klucz podstawowy zabezpieczyć swoje secrete informacji.  
2. Uruchom następujące polecenia, aby utworzyć projekt dla Azure Monitor i ustawić konto użytkownika. Skrypt generujący wpis tajny prosi o Log Analytics identyfikator obszaru roboczego `<WSID>` i `<KEY>` klucz podstawowy i po zakończeniu tworzy plik OCP-Secret. YAML.  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Wdróż plik wpisów tajnych, uruchamiając następujące czynności:

    `oc create -f ocp-secret.yaml`

4. Sprawdź wdrożenie, uruchamiając następujące czynności:

    `oc describe secret omsagent-secret`  

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

5. Wdróż plik yaml zestaw demona agenta usługi Log Analytics, uruchamiając następujące:

    `oc create -f ocp-ds-omsagent.yaml`  

6. Sprawdź wdrożenie, uruchamiając następujące czynności:

    `oc describe ds oms`

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

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Skonfiguruj agenta Log Analytics w systemie Linux dla platformy Kubernetes

Dla rozwiązania Kubernetes możesz użyć skryptu do generowania pliku yaml wpisów tajnych dla Identyfikatora obszaru roboczego i klucz podstawowy zainstalować agenta usługi Log Analytics dla systemu Linux. Na stronie [log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) znajdują się pliki, których można używać z lub bez informacji o kluczu tajnym.

- Agent domyślne usługi Log Analytics dla systemu Linux DaemonSet ma tajnych informacji (omsagent.yaml)
- Agenta usługi Log Analytics dla systemu Linux DaemonSet pliku yaml używa tajnych informacji (omsagent-ds-secrets.yaml) przy użyciu klucza tajnego generowania skryptów do wygenerowania pliku yaml (omsagentsecret.yaml) wpisów tajnych.

Można utworzyć omsagent DaemonSets z lub bez wpisów tajnych.

**Domyślny plik OMSagent elementu daemonset YAML bez wpisów tajnych**

- W przypadku Log Analytics domyślnego pliku YAML elementu daemonset agenta Zastąp `<WSID>` i `<KEY>` KLUCZami WSID i. Skopiuj plik na Twój węzeł główny i uruchom następujące polecenie:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Domyślny plik OMSagent elementu daemonset YAML z wpisami tajnymi**

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

    5. Utwórz zestaw demonów omsagent, uruchamiając ```sudo kubectl create -f omsagent-ds-secrets.yaml```

2. Sprawdź, czy agent usługi Log Analytics DaemonSet jest uruchomiona, podobny do następującego:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

Dla rozwiązania Kubernetes należy użyć skryptu, aby wygenerować plik yaml wpisów tajnych dla Identyfikatora obszaru roboczego i klucz podstawowy dla agenta usługi Log Analytics dla systemu Linux. Skorzystaj z następujących przykładowych informacji w [pliku omsagent YAML](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) , aby zabezpieczyć tajne informacje.

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

Dla rozwiązania Kubernetes Windows skrypt będzie używany do generowania pliku yaml wpisów tajnych dla Identyfikatora obszaru roboczego i klucz podstawowy zainstalować agenta usługi Log Analytics. Na stronie [log Analytics Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) znajdują się pliki, których można używać z informacjami o kluczowym poziomie użytkownika.  Musisz zainstalować agenta usługi Log Analytics oddzielnie dla głównych i węzłów agentów.  

1. Aby użyć agenta usługi Log Analytics DaemonSet przy użyciu tajnych informacji we wzorcu węzła, zaloguj się i najpierw utworzyć wpisy tajne.
    1. Skopiuj skrypt i plik szablonu wpisu tajnego i upewnij się, że znajdują się w tym samym katalogu.
        - Generowanie skryptu - gen.sh klucz tajny klucz tajny
        - Szablon tajny - template.yaml wpisu tajnego

    2. Uruchom skrypt, jak w poniższym przykładzie. Skrypt prosi o identyfikator obszaru roboczego usługi Log Analytics i klucz podstawowy, a po ich wprowadzeniu, skrypt tworzy plik yaml wpisu tajnego, aby można było go uruchomić.

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Utwórz zestaw demonów omsagent, uruchamiając ```kubectl create -f omsagentsecret.yaml```
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

    5. Utwórz zestaw demonów omsagent, uruchamiając ```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Sprawdź, czy agent usługi Log Analytics DaemonSet jest uruchomiona, podobny do następującego:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Aby zainstalować agenta w węźle procesu roboczego z systemem Windows, wykonaj kroki opisane w sekcji [Instalowanie i Konfigurowanie hostów kontenera systemu Windows](#install-and-configure-windows-container-hosts).

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Użyj narzędzia Helm, aby wdrożyć agenta Log Analytics na platformie Kubernetes z systemem Linux

Aby wdrożyć agenta Log Analytics w środowisku Kubernetes w systemie Linux przy użyciu narzędzia helm, wykonaj następujące kroki.

1. Utwórz zestaw demonów omsagent, uruchamiając ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
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

3. Stan omsagent można sprawdzić, uruchamiając: ```helm status "omsagent"```, a dane wyjściowe będą wyglądać podobnie do następujących:

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
   
    Aby uzyskać więcej informacji, odwiedź [Wykres Helm rozwiązania kontenera](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Instalowanie i konfigurowanie hostach kontenerów Windows

Skorzystaj z informacji w sekcji, aby zainstalować i skonfigurować hosty kontenerów Windows.

#### <a name="preparation-before-installing-windows-agents"></a>Przygotowanie przed zainstalowaniem agentów Windows

Przed zainstalowaniem agentów na komputerach z systemem Windows, należy skonfigurować usługę Docker. Konfiguracja umożliwia agentowi systemu Windows lub Azure Monitor rozszerzenie maszyny wirtualnej do korzystania z gniazda TCP platformy Docker, dzięki czemu agenci mogą zdalnie uzyskiwać dostęp do demona platformy Docker i przechwytywać dane do monitorowania.

##### <a name="to-configure-the-docker-service"></a>Aby skonfigurować usługę platformy Docker  

Aby włączyć potok protokołu TCP i nazwany potok dla systemu Windows Server, wykonaj następujące polecenia programu PowerShell:

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

Aby uzyskać więcej informacji na temat konfiguracji demona platformy Docker używanej z kontenerami systemu Windows, zobacz [aparat platformy Docker w systemie Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).

#### <a name="install-windows-agents"></a>Zainstaluj agentów Windows

Aby włączyć monitorowanie kontenerów Windows i funkcji Hyper-V, należy zainstalować program Microsoft Monitoring Agent (MMA) na komputerach Windows, znajdujących się na hostach kontenerów. W przypadku komputerów z systemem Windows w środowisku lokalnym zobacz [łączenie komputerów z systemem Windows w celu Azure monitor](../../azure-monitor/platform/agent-windows.md). W przypadku maszyn wirtualnych działających na platformie Azure podłącz je do Azure Monitor przy użyciu [rozszerzenia maszyny wirtualnej](../../azure-monitor/learn/quick-collect-azurevm.md).

Można monitorować kontenery Windows, działające w usłudze Service Fabric. Jednak tylko [maszyny wirtualne działające na platformie Azure](../../azure-monitor/learn/quick-collect-azurevm.md) i [komputerach z systemem Windows w środowisku lokalnym](../../azure-monitor/platform/agent-windows.md) są obecnie obsługiwane dla Service Fabric.

Aby sprawdzić, czy rozwiązanie do monitorowania kontenerów jest ustawione prawidłowo dla Windows. Aby sprawdzić, czy pakiet administracyjny został pobrany prawidłowo, poszukaj *ContainerManagement.xxx*. Pliki powinny być w folderze C:\Program Files\Microsoft Monitoring Agent\Agent\Health usługi State\Management Packs.

## <a name="solution-components"></a>Składniki rozwiązania

W Azure Portal przejdź do *Galeria rozwiązań* i Dodaj **rozwiązanie do monitorowania kontenerów**. Jeśli używasz agenci Windows następujące pakiety administracyjne jest zainstalowany na każdym komputerze z agentem po dodaniu tego rozwiązania. Żadna konfiguracja ani obsługa jest wymagana dla pakietu administracyjnego.

- *ContainerManagement.xxx* zainstalowany w folderze C:\Program Files\Microsoft monitoring Agent\Agent\Health Service State\Management Pack

## <a name="container-data-collection-details"></a>Szczegóły zbierania danych kontenera

To rozwiązanie monitorowanie kontenera zbiera różne dane metryk i dzienników wydajności z hostach kontenerów i kontenery przy użyciu agentów, które zostanie włączone.

Dane są gromadzone co trzy minuty przez następujące typy agenta.

- [Agent Log Analytics dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md)
- [Agent systemu Windows](../../azure-monitor/platform/agent-windows.md)
- [Log Analytics rozszerzenie maszyny wirtualnej](../../azure-monitor/learn/quick-collect-azurevm.md)

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

Etykiety dołączone do typów danych *PodLabel* są własnymi etykietami niestandardowymi. Przykładami są dołączonych etykiety PodLabel pokazano w tabeli. Dlatego `PodLabel_deployment_s`, `PodLabel_deploymentconfig_s`, `PodLabel_docker_registry_s` będzie różnić się w zestawie danych środowiska i w sposób ogólny przypomina `PodLabel_yourlabel_s`.

## <a name="monitor-containers"></a>Monitorowanie kontenerów
Po włączeniu rozwiązania w Azure Portal na kafelku **kontenery** zostaną wyświetlone informacje podsumowujące dotyczące hostów kontenerów i kontenerów uruchomionych na hostach.

![Kafelek kontenerów](./media/containers/containers-title.png)

Kafelek Wyświetla liczbę kontenerów, masz w środowisku, czy są one nie powiodło się, uruchomiona lub zatrzymana.

### <a name="using-the-containers-dashboard"></a>Korzystanie z pulpitu nawigacyjnego kontenerów

Kliknij kafelek **kontenery** . W tym miejscu zobaczysz widoki uporządkowane według:

- **Zdarzenia kontenerów** — pokazuje stan kontenera i komputery z kontenerami zakończonymi niepowodzeniem.
- **Dzienniki kontenerów** — pokazuje wykres plików dziennika kontenerów generowanych w czasie i listę komputerów z największą liczbą plików dziennika.
- **Zdarzenia Kubernetes** — pokazuje wykres zdarzeń Kubernetes generowanych w czasie oraz listę przyczyn, dla których te zdarzenia zostały wygenerowane. *Ten zestaw danych jest używany tylko w środowiskach systemu Linux.*
- **Spis przestrzeni nazw Kubernetes** — pokazuje liczbę przestrzeni nazw i zasobników oraz pokazuje ich hierarchię. *Ten zestaw danych jest używany tylko w środowiskach systemu Linux.*
- **Spis węzłów kontenera** — pokazuje liczbę typów aranżacji używanych na węzłach kontenerów/hostach. Komputer węzły/hosty są także wyświetlane przez liczbę kontenerów. *Ten zestaw danych jest używany tylko w środowiskach systemu Linux.*
- **Spis obrazów kontenerów** — pokazuje łączną liczbę użytych obrazów kontenerów i liczbę typów obrazów. Liczba obrazów są także wyświetlane przez tag obrazu.
- **Stan kontenerów** — pokazuje łączną liczbę węzłów kontenera/komputerów hostów, na których działają kontenery. Komputery są także wyświetlane według liczby uruchomionych na hostach.
- **Proces kontenera** — pokazuje wykres liniowy procesów kontenerów uruchomionych w czasie. Kontenery są również wyszczególnione przez uruchomienie polecenia i przetwarzanie w kontenerach. *Ten zestaw danych jest używany tylko w środowiskach systemu Linux.*
- **Wydajność procesora CPU kontenera** — przedstawia wykres liniowy średniego użycia procesora CPU w czasie dla węzłów/hostów komputerów. Również Wyświetla komputera węzły/hosty na podstawie średniego wykorzystanie procesora CPU.
- **Wydajność pamięci kontenera** — pokazuje wykres liniowy użycia pamięci w czasie. Zawiera również listę pamięci komputera użycie na podstawie wystąpienia nazwy.
- **Wydajność komputera** — przedstawia wykresy liniowe procentu wydajności procesora CPU w miarę upływu czasu, procent użycia pamięci w czasie i megabajty wolnego miejsca na dysku w czasie. Możesz umieścić kursor żadnych linii na wykresie, aby wyświetlić więcej szczegółów.

Każdy obszar pulpitu nawigacyjnego jest wizualną reprezentację wyszukiwania, który jest uruchamiany na zebranych danych.

![Pulpit nawigacyjny rozwiązania containers](./media/containers/containers-dash01.png)

![Pulpit nawigacyjny rozwiązania containers](./media/containers/containers-dash02.png)

W obszarze **stan kontenera** kliknij górny obszar, jak pokazano poniżej.

![Stan kontenerów](./media/containers/containers-status.png)

Log Analytics zostanie otwarty, wyświetlając informacje o stanie kontenerów.

![Log Analytics kontenerów](./media/containers/containers-log-search.png)

W tym miejscu można edytować zapytania wyszukiwania można zmodyfikować, aby znaleźć konkretne informacje, czy interesuje Cię. Aby uzyskać więcej informacji na temat zapytań dzienników, zobacz [Dziennik zapytań w Azure monitor](../log-query/log-query-overview.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Rozwiązywanie problemów, wyszukując kontenera nie powiodło się

Log Analytics oznacza kontener jako **zakończony niepowodzeniem** , jeśli zakończył działanie z kodem zakończenia innym niż zero. W obszarze **kontenerów zakończonych niepowodzeniem** można zobaczyć Omówienie błędów i niepowodzeń w środowisku.

### <a name="to-find-failed-containers"></a>Aby znaleźć kontenery z błędem

1. Kliknij obszar **stan kontenera** .  
   ![stanu kontenerów](./media/containers/containers-status.png)
2. Log Analytics otwiera i wyświetla stan kontenerów, podobny do poniższego.  
   ![Stan kontenerów](./media/containers/containers-log-search.png)
3. Rozwiń wiersz niepowodzenie i kliknij przycisk +, aby dodać jego kryteria do zapytania. Następnie Dodaj komentarz do wiersza podsumowania w zapytaniu.
   ![kontenerów zakończonych niepowodzeniem](./media/containers/containers-state-failed-select.png)  
1. Uruchom zapytanie, a następnie rozwiń wiersz w wynikach, aby wyświetlić identyfikator obrazu.  
   ![Kontenery zakończone niepowodzeniem](./media/containers/containers-state-failed.png)  
1. Wpisz następujące w zapytaniu dziennika. `ContainerImageInventory | where ImageID == <ImageID>`, aby wyświetlić szczegółowe informacje o obrazie, takie jak rozmiar obrazu i Liczba obrazów zatrzymanych i zakończonych niepowodzeniem.  
   ![kontenerów zakończonych niepowodzeniem](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Dzienniki zapytań dla danych kontenera

W przypadku Rozwiązywanie problemów z określonym błędem, może pomóc aby zobaczyć, gdzie występuje w danym środowisku. Następujące typy dziennika są pomocne podczas tworzenia zapytania do zwracania informacji, że chcesz użyć.

- **ContainerImageInventory** — Użyj tego typu, gdy próbujesz znaleźć informacje uporządkowane według obrazu i wyświetlać informacje o obrazach, takie jak identyfikatory obrazów lub rozmiary.
- **ContainerInventory** — Użyj tego typu, aby uzyskać informacje o lokalizacji kontenera, jego nazwach i obrazach, które są w nich uruchamiane.
- **ContainerLog** — Użyj tego typu, aby znaleźć szczegółowe informacje o dzienniku błędów i wpisy.
- **ContainerNodeInventory_CL**  Użyj tego typu, aby uzyskać informacje na temat hosta/węzła, gdzie znajdują się kontenery. Umożliwia wersji platformy Docker, typ aranżacji, magazynu i informacje o sieci.
- **ContainerProcess_CL** Użyj tego typu, aby szybko zobaczyć proces uruchomiony w kontenerze.
- **ContainerServiceLog** — ten typ jest używany podczas próby znalezienia informacji o dzienniku inspekcji dla demona platformy Docker, takich jak uruchamianie, zatrzymywanie, usuwanie lub ściąganie poleceń.
- **KubeEvents_CL**  Użyj tego typu, aby wyświetlić zdarzenia Kubernetes.
- **KubePodInventory_CL**  Użyj tego typu, aby zrozumieć informacje o hierarchii klastra.


### <a name="to-query-logs-for-container-data"></a>Aby zbadać dzienniki dla danych kontenera

* Wybierz obraz, który znasz ostatnio nie powiodło się i znajdowanie dzienników błędów dla niego. Zacznij od znalezienia nazwy kontenera, na którym działa ten obraz z wyszukiwaniem **ContainerInventory** . Na przykład wyszukaj `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![wyszukiwanie kontenerów Ubuntu](./media/containers/search-ubuntu.png)

  Rozwiń dowolny wiersz w wynikach, aby wyświetlić szczegóły dla tego kontenera.

## <a name="example-log-queries"></a>Przykładowe zapytania dziennika

Często jest to przydatne do tworzenia zapytań, począwszy od przykładem lub dwóch, a następnie modyfikować ją do dopasowane do środowiska. Jako punkt początkowy można eksperymentować z obszarem **zapytań przykładowych** , aby ułatwić tworzenie bardziej zaawansowanych zapytań.

![Kontenery zapytań](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>Zapisywanie zapytań dziennika

Zapisywanie zapytań jest standardową funkcją w Azure Monitor. Zapisując je, będziesz mieć tych, które znajdziesz przydatne przydatna do użytku w przyszłości.

Po utworzeniu kwerendy, która jest przydatna, Zapisz ją, klikając pozycję **Ulubione** w górnej części strony przeszukiwania dzienników. Następnie możesz łatwo uzyskać do niego dostęp z poziomu strony **mój pulpit nawigacyjny** .

## <a name="next-steps"></a>Następne kroki

[Dzienniki zapytań](../log-query/log-query-overview.md) , aby wyświetlić szczegółowe rekordy danych kontenera.
