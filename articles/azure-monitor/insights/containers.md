---
title: Rozwiązanie do monitorowania kontenerów w usłudze Azure Monitor | Dokumenty firmy Microsoft
description: Rozwiązanie do monitorowania kontenerów w usłudze Azure Monitor ułatwia wyświetlanie hostów kontenerów platformy Docker i Windows w jednej lokalizacji oraz zarządzanie nimi.
ms.subservice: logs
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 07/22/2019
ms.openlocfilehash: 171f897f6e110e8f759281c139addab477ecede3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77664698"
---
# <a name="container-monitoring-solution-in-azure-monitor"></a>Rozwiązanie do monitorowania kontenerów w usłudze Azure Monitor

![Symbol kontenerów](./media/containers/containers-symbol.png)

W tym artykule opisano sposób konfigurowania i używania rozwiązania do monitorowania kontenerów w usłudze Azure Monitor, które ułatwia wyświetlanie hostów kontenerów platformy Docker i Windows w jednej lokalizacji oraz zarządzanie nimi. Docker to system wirtualizacji oprogramowania używany do tworzenia kontenerów automatyzujących wdrażanie oprogramowania w ich infrastrukturze IT.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Rozwiązanie pokazuje, które kontenery są uruchomione, jaki obraz kontenera są uruchomione i gdzie kontenery są uruchomione. Można wyświetlić szczegółowe informacje inspekcji przedstawiające polecenia używane z kontenerami. Można też rozwiązywać problemy z kontenerami, wyświetlając i wyszukując scentralizowane dzienniki bez konieczności zdalnego wyświetlania hostów platformy Docker lub Windows. Można znaleźć kontenery, które mogą być hałaśliwe i zużywa nadmiar zasobów na hoście. Można też wyświetlać scentralizowane informacje o procesorze, pamięci, pamięci masowej i użyciu sieci oraz wydajności kontenerów. Na komputerach z systemem Windows można scentralizować i porównywać dzienniki z kontenerów systemu Windows Server, Funkcji Hyper-V i Platformy Docker. Rozwiązanie obsługuje następujące orchestrators kontenera:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Jeśli masz kontenery wdrożone w [usłudze Azure Service Fabric,](../../service-fabric/service-fabric-overview.md)zaleca się włączenie zarówno [rozwiązania sieci szkieletowej usług,](../../service-fabric/service-fabric-diagnostics-oms-setup.md) jak i tego rozwiązania w celu monitorowania zdarzeń klastra. Przed włączeniem rozwiązania sieci szkieletowej usług, przejrzyj [korzystanie z rozwiązania sieci szkieletowej usług,](../../service-fabric/service-fabric-diagnostics-event-analysis-oms.md) aby zrozumieć, co zapewnia i jak go używać.

Jeśli chcesz monitorować wydajność obciążeń wdrożonych w środowiskach kubernetes hostowanych w usłudze Azure Kubernetes Service (AKS), zobacz [Monitoruj usługę Azure Kubernetes.](../../azure-monitor/insights/container-insights-overview.md) Rozwiązanie do monitorowania kontenerów nie obsługuje monitorowania tej platformy.  

Na poniższym diagramie przedstawiono relacje między różnymi hostami kontenerów i agentami za pomocą usługi Azure Monitor.

![Diagram kontenerów](./media/containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Wymagania systemowe i obsługiwane platformy

Przed rozpoczęciem zapoznaj się z poniższymi szczegółami, aby sprawdzić, czy spełniasz wymagania wstępne.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Obsługa rozwiązań do monitorowania kontenerów dla platformy Docker Orchestrator i OS

W poniższej tabeli przedstawiono aranżacji platformy Docker i monitorowania systemu operacyjnego obsługi zapasów kontenerów, wydajności i dzienników za pomocą usługi Azure Monitor.   

| | ACS | Linux | Windows | Kontener<br>Spis | Image (Obraz)<br>Spis | Węzeł<br>Spis | Kontener<br>Wydajność | Kontener<br>Wydarzenie | Wydarzenie<br>Log | Kontener<br>Log |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mezosfera<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Rój | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Usługa<br>Fabric | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Czerwony kapelusz otwarty<br>Przesunięcia | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(samodzielny) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Serwer Linux<br>(samodzielny) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |

### <a name="docker-versions-supported-on-linux"></a>Wersje platformy Docker obsługiwane w systemie Linux

- Docker od 1,11 do 1,13
- Docker CE i EE v17.06

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>Dystrybucje x64 Linux obsługiwane jako hosty kontenerów

- Ubuntu 14.04 LTS i 16.04 LTS
- CoreOS (stabilny)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7,2 i 7,3
- SLES 12
- RHEL 7.2 i 7.3
- Platforma kontenerowa Red Hat OpenShift (OCP) 3.4 i 3.5
- ACS Mesosphere DC/OS 1.7.3 do 1.8.8
- Acs Kubernetes 1.4.5 do 1.6
    - Zdarzenia kubernetes, spisu Kubernetes i procesy kontenerów są obsługiwane tylko w wersji 1.4.1-45 i nowszych agenta usługi Log Analytics dla systemu Linux
- Rój dokowane ACS

[!INCLUDE [log-analytics-agent-note.md](../../../includes/log-analytics-agent-note.md)] 

### <a name="supported-windows-operating-system"></a>Obsługiwany system operacyjny Windows

- Windows Server 2016
- Windows 10 Anniversary Edition (Professional lub Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Wersje platformy Docker obsługiwane w systemie Windows

- Docker 1.12 i 1.13
- Docker 17.03.0 i nowsze

## <a name="installing-and-configuring-the-solution"></a>Instalowanie i konfigurowanie rozwiązania

Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązanie.

1. Dodaj rozwiązanie do monitorowania kontenerów do obszaru roboczego usługi Log Analytics z [witryny Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) lub przy użyciu procesu opisanego w [add monitoring solutions from the Solutions Gallery](../../azure-monitor/insights/solutions.md).

2. Zainstaluj i użyj platformy Docker za pomocą agenta usługi Log Analytics. Na podstawie systemu operacyjnego i programu Docker orchestrator można użyć następujących metod, aby skonfigurować agenta.
   - W przypadku hostów autonomicznych:
     - W obsługiwanych systemach operacyjnych Linux zainstaluj i uruchom platformę Docker, a następnie zainstaluj i skonfiguruj [agenta usługi Log Analytics dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md).  
     - W systemie CoreOS nie można uruchomić agenta usługi Log Analytics dla systemu Linux. Zamiast tego należy uruchomić konteneryzowaną wersję agenta usługi Log Analytics dla systemu Linux. Przejrzyj hosty kontenerów systemu Linux, w tym hosty kontenerów CoreOS lub Azure Government Linux, w tym CoreOS, jeśli pracujesz z kontenerami w usłudze Azure Government Cloud.
     - W systemach Windows Server 2016 i Windows 10 zainstaluj aparat platformy Docker, a następnie klienta, a następnie podłącz agenta, aby zebrać informacje i wysłać je do usługi Azure Monitor. Przejrzyj [instalowanie i konfigurowanie hostów kontenerów systemu Windows,](#install-and-configure-windows-container-hosts) jeśli masz środowisko systemu Windows.
   - W przypadku aranżacji wielu hostów platformy Docker:
     - Jeśli masz środowisko OpenShift Red Hat, przejrzyj Konfigurowanie agenta analizy dzienników dla Red Hat OpenShift.
     - Jeśli masz klaster Kubernetes przy użyciu usługi azure kontenera:
       - Przejrzyj [Konfigurowanie agenta systemu Linux usługi Log Analytics dla kubernetes](#configure-a-log-analytics-linux-agent-for-kubernetes).
       - Przejrzyj [Konfigurowanie agenta systemu Windows usługi Log Analytics dla aplikacji Kubernetes](#configure-a-log-analytics-windows-agent-for-kubernetes).
       - Przejrzyj użyj helma, aby wdrożyć agenta usługi Log Analytics w usłudze Kubernetes systemu Linux.
     - Jeśli masz klaster kontrolera domeny/systemu windows usługi kontenerowej platformy Azure, dowiedz się więcej na [temat Monitoruj klaster kontrolera domeny/systemu operacyjnego usługi kontenerowej platformy Azure za pomocą usługi Azure Monitor](../../container-service/dcos-swarm/container-service-monitoring-oms.md).
     - Jeśli masz środowisko trybu rój docker, dowiedz się więcej na temat Konfigurowanie agenta usługi Log Analytics dla usługi Docker Swarm.
     - Jeśli masz klaster sieci szkieletowej usług, dowiedz się więcej na [temat Kontenery monitora z usługą Azure Monitor](../../service-fabric/service-fabric-diagnostics-oms-containers.md).

Zapoznaj się z artykułem [Aparat platformy Docker w systemie Windows,](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) aby uzyskać dodatkowe informacje dotyczące instalowania i konfigurowania aparatów platformy Docker na komputerach z systemem Windows.

> [!IMPORTANT]
> Docker musi być uruchomiony **przed** zainstalowaniem [agenta usługi Log Analytics dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) na hostach kontenerów. Jeśli agent został już zainstalowany przed zainstalowaniem platformy Docker, należy ponownie zainstalować agenta usługi Log Analytics dla systemu Linux. Aby uzyskać więcej informacji na temat platformy Docker, zobacz [witrynę sieci Web platformy Docker](https://www.docker.com).

### <a name="install-and-configure-linux-container-hosts"></a>Instalowanie i konfigurowanie hostów kontenerów systemu Linux

Po zainstalowaniu platformy Docker użyj następujących ustawień hosta kontenera, aby skonfigurować agenta do użytku z platformą Docker. Najpierw potrzebujesz identyfikatora obszaru roboczego usługi Log Analytics i klucza, który można znaleźć w witrynie Azure portal. W obszarze roboczym kliknij pozycję **Szybkie uruchamianie** > **komputerów,** aby wyświetlić **identyfikator obszaru roboczego** i **klucz podstawowy**.  Skopiuj i wklej obie wartości do ulubionego edytora.

**Dla wszystkich hostów kontenerów systemu Linux z wyjątkiem CoreOS:**

- Aby uzyskać więcej informacji i kroków dotyczących instalowania agenta usługi Log Analytics dla systemu Linux, zobacz [Omówienie agenta usługi Log Analytics](../../azure-monitor/platform/log-analytics-agent.md).

**Dla wszystkich hostów kontenerów systemu Linux, w tym CoreOS:**

Uruchom kontener, który chcesz monitorować. Modyfikuj i użyj następującego przykładu:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Dla wszystkich hostów kontenerów systemu Azure dla instytucji rządowych z systemem Linux, w tym CoreOS:**

Uruchom kontener, który chcesz monitorować. Modyfikuj i użyj następującego przykładu:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -v /var/lib/docker/containers:/var/lib/docker/containers -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Przełączanie z używania zainstalowanego agenta systemu Linux na jeden w kontenerze**

Jeśli wcześniej używano bezpośrednio zainstalowanego agenta i zamiast tego chcesz użyć agenta uruchomionego w kontenerze, należy najpierw usunąć agenta usługi Log Analytics dla systemu Linux. Zobacz [Odinstalowywanie agenta usługi Log Analytics dla systemu Linux,](../../azure-monitor/learn/quick-collect-linux-computer.md) aby dowiedzieć się, jak pomyślnie odinstalować agenta.  

#### <a name="configure-a-log-analytics-agent-for-docker-swarm"></a>Konfigurowanie agenta usługi Log Analytics dla usługi Docker Swarm

Agent usługi Log Analytics można uruchomić jako usługę globalną w usłudze Docker Swarm. Use the following information to create a Log Analytics agent service. Należy podać identyfikator obszaru roboczego usługi Log Analytics i klucz podstawowy.

- Uruchom następujące czynności w węźle głównym.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Bezpieczne sekrety rój docker

W przypadku roju platformy Docker po utworzeniu klucza tajnego dla identyfikatora obszaru roboczego i klucza podstawowego użyj następujących informacji, aby utworzyć tajne informacje.

1. Uruchom następujące czynności w węźle głównym.

    ```
    echo "WSID" | docker secret create WSID -
    echo "KEY" | docker secret create KEY -
    ```

2. Sprawdź, czy wpisy tajne zostały utworzone prawidłowo.

    ```
    keiko@swarmm-master-13957614-0:/run# sudo docker secret ls
    ```

    ```
    ID                          NAME                CREATED             UPDATED
    j2fj153zxy91j8zbcitnjxjiv   WSID                43 minutes ago      43 minutes ago
    l9rh3n987g9c45zffuxdxetd9   KEY                 38 minutes ago      38 minutes ago
    ```

3. Uruchom następujące polecenie, aby zainstalować wpisy tajne do konteneryzowanego agenta usługi Log Analytics.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --mount type=bind,source=/var/lib/docker/containers,destination=/var/lib/docker/containers --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-a-log-analytics-agent-for-red-hat-openshift"></a>Konfigurowanie agenta usługi Log Analytics dla red hat openshift

Istnieją trzy sposoby, aby dodać agenta usługi Log Analytics do Red Hat OpenShift, aby rozpocząć zbieranie danych monitorowania kontenera.

* [Zainstaluj agenta usługi Log Analytics dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md) bezpośrednio w każdym węźle OpenShift  
* [Włącz rozszerzenie maszyny wirtualnej usługi Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md) w każdym węźle OpenShift zamieszkałym na platformie Azure  
* Instalowanie agenta usługi Log Analytics jako zestawu demonów OpenShift  

W tej sekcji omówimy kroki wymagane do zainstalowania agenta usługi Log Analytics jako zestawu demonów OpenShift.  

1. Zaloguj się do węzła głównego OpenShift i skopiuj plik yaml [ocp-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) z gitHub do węzła głównego i zmodyfikuj wartość za pomocą identyfikatora obszaru roboczego usługi Log Analytics i za pomocą klucza podstawowego.
2. Uruchom następujące polecenia, aby utworzyć projekt dla usługi Azure Monitor i ustawić konto użytkownika.

    ```
    oc adm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Aby wdrożyć zestaw demonów, uruchom następujące czynności:

    `oc create -f ocp-omsagent.yaml`

4. Aby sprawdzić, czy jest skonfigurowany i działa poprawnie, wpisz następujące czynności:

    `oc describe daemonset omsagent`  

    a wyjście powinno przypominać:

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

Jeśli chcesz użyć wpisów tajnych do zabezpieczenia identyfikatora obszaru roboczego usługi Log Analytics i klucza podstawowego podczas korzystania z pliku yaml demona zestawu demonów zestawu dziennika, wykonaj następujące kroki.

1. Zaloguj się do węzła głównego OpenShift i skopiuj plik yaml [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) i tajny skrypt generujący [ocp-secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) z GitHub.  Ten skrypt wygeneruje wpisy tajne yaml plik dla log analytics identyfikator obszaru roboczego i klucz podstawowy w celu zabezpieczenia informacji o tajności.  
2. Uruchom następujące polecenia, aby utworzyć projekt dla usługi Azure Monitor i ustawić konto użytkownika. Tajny skrypt generujący prosi o identyfikator obszaru `<WSID>` roboczego `<KEY>` usługi Log Analytics i klucz podstawowy, a po zakończeniu tworzy plik ocp-secret.yaml.  

    ```
    oc adm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oc adm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oc adm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

3. Wdrażanie tajnego pliku przez uruchomienie następujących elementów:

    `oc create -f ocp-secret.yaml`

4. Sprawdź wdrożenie, uruchamiając następujące elementy:

    `oc describe secret omsagent-secret`  

    a wyjście powinno przypominać:  

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

5. Wdrożyć plik yaml demona zestawu demonów agenta usługi Log Analytics, uruchamiając następujące czynności:

    `oc create -f ocp-ds-omsagent.yaml`  

6. Sprawdź wdrożenie, uruchamiając następujące elementy:

    `oc describe ds oms`

    a wyjście powinno przypominać:

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

#### <a name="configure-a-log-analytics-linux-agent-for-kubernetes"></a>Konfigurowanie agenta systemu Linux usługi Log Analytics dla sieci Kubernetes

W przypadku aplikacji Kubernetes można użyć skryptu do wygenerowania wpisów tajnych pliku yaml dla identyfikatora obszaru roboczego i klucza podstawowego, aby zainstalować agenta usługi Log Analytics dla systemu Linux. Na stronie [Usługi Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) usługi Log Analytics znajdują się pliki, których można używać z tajnymi informacjami lub bez niego.

- Domyślny agent analizy dzienników dla zestawu Demononset systemu Linux nie ma tajnych informacji (omsagent.yaml)
- Agent log analytics dla pliku yaml Demona Systemu Linux używa tajnych informacji (omsagent-ds-secrets.yaml) z tajnymi skryptami generowania do generowania plików tajemnic yaml (omsagentsecret.yaml).

Można utworzyć demony omsagent z wpisami tajnymi lub bez.

**Domyślny plik yaml demona OMSagent bez wpisów tajnych**

- Dla domyślnego agenta log analytics DaemonSet `<WSID>` `<KEY>` yaml pliku, zastąp i do WSID i KLUCZ. Skopiuj plik do węzła głównego i uruchom następujące czynności:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Domyślny plik yaml demona OMSagent z wpisami tajnymi**

1. Aby użyć agenta usługi Log Analytics DaemonSet przy użyciu tajnych informacji, należy najpierw utworzyć wpisy tajne.
    1. Skopiuj skrypt i tajny plik szablonu i upewnij się, że znajdują się w tym samym katalogu.
        - Tajny skrypt generujący - secret-gen.sh
        - tajny szablon - secret-template.yaml
    2. Uruchom skrypt, jak w poniższym przykładzie. Skrypt prosi o identyfikator obszaru roboczego usługi Log Analytics i klucz podstawowy, a po ich wprowadzeniu skrypt tworzy tajny plik yaml, dzięki czemu można go uruchomić.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Utwórz zasobnik wpisów tajnych, uruchamiając następujące elementy:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Aby zweryfikować, uruchom następujące czynności:

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

    5. Stwórz swój demon omsagent-set, uruchamiając```sudo kubectl create -f omsagent-ds-secrets.yaml```

2. Sprawdź, czy agent usługi Log Analytics DaemonSet jest uruchomiony, podobnie jak w następujących produktach:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```

W przypadku aplikacji Kubernetes użyj skryptu do wygenerowania wpisów tajnych pliku yaml dla identyfikatora obszaru roboczego i klucza podstawowego dla agenta usługi Log Analytics dla systemu Linux. Użyj poniższych przykładowych informacji z [plikiem omsagent yaml,](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) aby zabezpieczyć tajne informacje.

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

#### <a name="configure-a-log-analytics-windows-agent-for-kubernetes"></a>Konfigurowanie agenta systemu Windows usługi Log Analytics dla usługi Kubernetes

W przypadku aplikacji Kubernetes systemu Windows do wygenerowania wpisów tajnych pliku yaml dla identyfikatora obszaru roboczego i klucza podstawowego można użyć skryptu do zainstalowania agenta usługi Log Analytics. Na stronie [Usługi Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) usługi Log Analytics znajdują się pliki, których można używać z tajnymi informacjami.  Agent usługi Log Analytics należy zainstalować oddzielnie dla węzłów głównych i agenta.  

1. Aby użyć agenta usługi Log Analytics DaemonSet przy użyciu tajnych informacji w węźle wzorca, zaloguj się i najpierw utwórz wpisy tajne.
    1. Skopiuj skrypt i tajny plik szablonu i upewnij się, że znajdują się w tym samym katalogu.
        - Tajny skrypt generujący - secret-gen.sh
        - tajny szablon - secret-template.yaml

    2. Uruchom skrypt, jak w poniższym przykładzie. Skrypt prosi o identyfikator obszaru roboczego usługi Log Analytics i klucz podstawowy, a po ich wprowadzeniu skrypt tworzy tajny plik yaml, dzięki czemu można go uruchomić.

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Stwórz swój demon omsagent-set, uruchamiając```kubectl create -f omsagentsecret.yaml```
    4. Aby sprawdzić, uruchom następujące czynności:

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

    5. Stwórz swój demon omsagent-set, uruchamiając```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Sprawdź, czy agent usługi Log Analytics DaemonSet jest uruchomiony, podobnie jak w następujących produktach:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Aby zainstalować agenta w węźle procesu roboczego z systemem Windows, wykonaj czynności opisane w sekcji [Instalowanie i konfigurowanie hostów kontenerów systemu Windows](#install-and-configure-windows-container-hosts).

#### <a name="use-helm-to-deploy-log-analytics-agent-on-linux-kubernetes"></a>Użyj helma do wdrażania agenta usługi Log Analytics w usłudze Windows Kubernetes

Aby użyć helm do wdrożenia agenta usługi Log Analytics w środowisku Kubernetes systemu Linux, wykonaj następujące kroki.

1. Stwórz swój demon omsagent-set, uruchamiając```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
2. Wyniki będą wyglądać podobnie do następujących:

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

3. Stan omsagenta można sprawdzić, uruchamiając: ```helm status "omsagent"``` a dane wyjściowe będą wyglądać podobnie do następującego:

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
   
    Aby uzyskać więcej informacji, odwiedź stronę [Container Solution Helm Chart](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Instalowanie i konfigurowanie hostów kontenerów systemu Windows

Użyj informacji w sekcji, aby zainstalować i skonfigurować hosty kontenerów systemu Windows.

#### <a name="preparation-before-installing-windows-agents"></a>Przygotowanie przed zainstalowaniem agentów systemu Windows

Przed zainstalowaniem agentów na komputerach z systemem Windows należy skonfigurować usługę Platformy Docker. Konfiguracja umożliwia agentowi systemu Windows lub rozszerzenie maszyny wirtualnej usługi Azure Monitor używanie gniazda TCP platformy Docker, dzięki czemu agenci mogą zdalnie uzyskiwać dostęp do demona platformy Docker i przechwytywać dane do monitorowania.

##### <a name="to-configure-the-docker-service"></a>Aby skonfigurować usługę Docker  

Wykonaj następujące polecenia programu PowerShell, aby włączyć potok TCP i potok nazwany dla systemu Windows Server:

```
Stop-Service docker
dockerd --unregister-service
dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
Start-Service docker
```

Aby uzyskać więcej informacji na temat konfiguracji demona platformy Docker używanej z kontenerami systemu Windows, zobacz [Aparat platformy Docker w systemie Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).

#### <a name="install-windows-agents"></a>Instalowanie agentów systemu Windows

Aby włączyć monitorowanie kontenerów systemu Windows i funkcji Hyper-V, zainstaluj agenta monitorowania firmy Microsoft (MMA) na komputerach z systemem Windows, które są hostami kontenerów. W przypadku komputerów z systemem Windows w środowisku lokalnym zobacz [Łączenie komputerów z systemem Windows z monitorem Azure .](../../azure-monitor/platform/agent-windows.md) W przypadku maszyn wirtualnych uruchomionych na platformie Azure połącz je z usługą Azure Monitor przy użyciu [rozszerzenia maszyny wirtualnej.](../../azure-monitor/learn/quick-collect-azurevm.md)

Kontenery systemu Windows uruchomione w sieci szkieletowej usług. Jednak tylko [maszyny wirtualne działające](../../azure-monitor/learn/quick-collect-azurevm.md) na platformie Azure i komputery z systemem Windows w środowisku lokalnym są obecnie obsługiwane dla sieci [szkieletowej](../../azure-monitor/platform/agent-windows.md) usług.

Można sprawdzić, czy rozwiązanie do monitorowania kontenerów jest poprawnie ustawione dla systemu Windows. Aby sprawdzić, czy pakiet administracyjny został pobrany prawidłowo, *poszukaj ContainerManagement.xxx*. Pliki powinny znajdować się w folderze C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs folder.

## <a name="solution-components"></a>Składniki rozwiązania

W witrynie Azure portal przejdź do *galerii rozwiązań* i dodaj **rozwiązanie do monitorowania kontenerów.** Jeśli używasz agentów systemu Windows, po dodaniu tego rozwiązania na każdym komputerze z agentem jest zainstalowany następujący pakiet administracyjny. Dla pakietu administracyjnego nie jest wymagana żadna konfiguracja ani konserwacja.

- *ContainerManagement.xxx* zainstalowany w C:\Program Files\Microsoft Monitoring Agent\Agent\Health Service State\Management Packs

## <a name="container-data-collection-details"></a>Szczegóły zbierania danych kontenera

Rozwiązanie do monitorowania kontenerów zbiera różne metryki wydajności i dane dziennika z hostów kontenerów i kontenerów przy użyciu agentów, które można włączyć.

Dane są zbierane co trzy minuty przez następujące typy agentów.

- [Agent analizy dzienników dla systemu Linux](../../azure-monitor/learn/quick-collect-linux-computer.md)
- [Agent systemu Windows](../../azure-monitor/platform/agent-windows.md)
- [Rozszerzenie maszyny Wirtualnej usługi Log Analytics](../../azure-monitor/learn/quick-collect-azurevm.md)

### <a name="container-records"></a>Rekordy kontenerów

W poniższej tabeli przedstawiono przykłady rekordów zebranych przez rozwiązanie monitorowania kontenerów i typy danych, które pojawiają się w wynikach wyszukiwania dziennika.

| Typ danych | Typ danych w wyszukiwaniu dzienników | Pola |
| --- | --- | --- |
| Wydajność dla hostów i kontenerów | `Perf` | Komputer, ObjectName, CounterName &#40;%Czas procesora, Odczyty dysku MB, Zapisy dysków MB, Mb użycia pamięci, Bajty odbierania sieci, Bajty wysyłania sieci, Użycie procesora w s,&#41; sieci, Wartość licznika, Czasogenerowany, CounterPath, System źródłowy |
| Zapasy kontenerów | `ContainerInventory` | TimeGenerated, Komputer, nazwa kontenera, ContainerHostname, Image, ImageTag, ContainerState, ExitCode, EnvironmentVar, Command, CreatedTime, StartedTime, FinishedTime, SourceSystem, ContainerID, ImageID |
| Spis obrazów kontenerów | `ContainerImageInventory` | CzasGenerowany, Komputer, Obraz, ImageTag, ImageSize, VirtualSize, Running, Wstrzymane, Zatrzymane, Nie powiodło się, System źródłowy, ImageID, TotalContainer |
| Dziennik kontenerów | `ContainerLog` | CzasGenerowany, Komputer, identyfikator obrazu, nazwa kontenera, LogEntrySource, LogEntry, SourceSystem, ContainerID |
| Dziennik usługi kontenera | `ContainerServiceLog`  | CzasGenerowany, Komputer, TimeOfCommand, Obraz, Polecenie, SourceSystem, ContainerID |
| Magazyn węzła kontenera | `ContainerNodeInventory_CL`| CzasGenerowany, Komputer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, System źródłowy|
| Spis Kubernetes | `KubePodInventory_CL` | CzasGenerowany, Komputer, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, System źródłowy |
| Proces kontenerowy | `ContainerProcess_CL` | CzasGenerowany, Komputer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Zdarzenia Kubernetes | `KubeEvents_CL` | CzasGenerowany, Komputer, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, System źródłowy, Wiadomość |

Etykiety dołączone do typów danych *PodLabel* są własnymi etykietami niestandardowymi. Dołączona etykieta PodLabel pokazana w tabeli są przykładami. Tak `PodLabel_deployment_s`więc, , `PodLabel_deploymentconfig_s` `PodLabel_docker_registry_s` będzie się różnić w środowisku `PodLabel_yourlabel_s`zestawu danych i ogólnie przypominają .

## <a name="monitor-containers"></a>Monitorowanie kontenerów
Po włączeniu rozwiązania w witrynie Azure portal kafelka **Kontenery** zawiera informacje podsumowujące dotyczące hostów kontenerów i kontenerów działających w hostach.

![Płytki kontenerowe](./media/containers/containers-title.png)

Kafelek zawiera przegląd liczby kontenerów w środowisku i czy są one nie powiodło się, uruchomiony lub zatrzymany.

### <a name="using-the-containers-dashboard"></a>Korzystanie z pulpitu nawigacyjnego Kontenery

Kliknij **kafelek Kontenery.** Stamtąd zobaczysz widoki uporządkowane według:

- **Zdarzenia kontenera** — pokazuje stan kontenera i komputery z kontenerami, których nie powiodło się.
- **Dzienniki kontenerów** — pokazuje wykres plików dziennika kontenerów generowanych w czasie oraz listę komputerów z największą liczbą plików dziennika.
- **Zdarzenia kubernetes** — pokazuje wykres zdarzeń Kubernetes generowane w czasie i listę przyczyn, dla których zasobników generowane zdarzenia. *Ten zestaw danych jest używany tylko w środowiskach linux.*
- **Spis obszaru nazw kubernetes** — pokazuje liczbę obszarów nazw i zasobników oraz pokazuje ich hierarchię. *Ten zestaw danych jest używany tylko w środowiskach linux.*
- **Kontener Node Inventory** — pokazuje liczbę typów aranżacji używanych w węzłach/hostach kontenerów. Węzły/hosty komputera są również wyświetlane według liczby kontenerów. *Ten zestaw danych jest używany tylko w środowiskach linux.*
- **Spis obrazów kontenerów** — pokazuje całkowitą liczbę używanych obrazów kontenerów i liczbę typów obrazów. Liczba obrazów jest również wyświetlana przez znacznik obrazu.
- **Stan kontenerów** — pokazuje całkowitą liczbę węzłów kontenerów/komputerów hosta, na których uruchomione są kontenery. Komputery są również wymienione przez liczbę uruchomionych hostów.
- **Proces kontenera** — pokazuje wykres liniowy procesów kontenerów uruchomionych w czasie. Kontenery są również wymienione przez uruchomienie polecenia/procesu w kontenerach. *Ten zestaw danych jest używany tylko w środowiskach linux.*
- **Wydajność procesora CPU kontenera** — pokazuje wykres liniowy średniego wykorzystania procesora CPU w czasie dla węzłów/hostów komputera. Wyświetla również listę węzłów/hostów komputera na podstawie średniego wykorzystania procesora CPU.
- **Wydajność pamięci kontenera** — pokazuje wykres liniowy użycia pamięci w czasie. Wyświetla również listę wykorzystania pamięci komputera na podstawie nazwy wystąpienia.
- **Wydajność komputera** — pokazuje wykresy liniowe procent wydajności procesora CPU w czasie, procent użycia pamięci w czasie i megabajty wolnego miejsca na dysku w czasie. Możesz najechać kursorem na dowolny wiersz na wykresie, aby wyświetlić więcej szczegółów.

Każdy obszar pulpitu nawigacyjnego jest wizualną reprezentacją wyszukiwania uruchamianego na zebranych danych.

![Kontenery na desce rozdzielcz](./media/containers/containers-dash01.png)

![Kontenery na desce rozdzielcz](./media/containers/containers-dash02.png)

W obszarze **Stan kontenera** kliknij górny obszar, jak pokazano poniżej.

![Stan kontenerów](./media/containers/containers-status.png)

Usługa Log Analytics zostanie otwarta, wyświetlając informacje o stanie kontenerów.

![Analiza dzienników kontenerów](./media/containers/containers-log-search.png)

W tym miejscu możesz edytować zapytanie wyszukiwania, aby je zmodyfikować, aby znaleźć interesujące Cię informacje. Aby uzyskać więcej informacji na temat zapytań dziennika, zobacz [Kwerendy dziennika w usłudze Azure Monitor](../log-query/log-query-overview.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Rozwiązywanie problemów przez znalezienie nieudanego kontenera

Usługa Log Analytics oznacza kontener jako **nieudany,** jeśli został wycofany z kodem zakończenia niezerowego. Można zobaczyć przegląd błędów i błędów w środowisku w obszarze **Kontenery awaryjne.**

### <a name="to-find-failed-containers"></a>Aby znaleźć kontenery, które nie powiodły się

1. Kliknij obszar **Stan kontenera.**  
   ![stan kontenerów](./media/containers/containers-status.png)
2. Usługa Log Analytics otwiera i wyświetla stan kontenerów, podobnie jak poniżej.  
   ![stan kontenerów](./media/containers/containers-log-search.png)
3. Rozwiń wiersz Niepowodzenie i kliknij przycisk +, aby dodać jego kryteria do kwerendy. Następnie wyskoknij wiersza Podsumuj w kwerendzie.
   ![nieudane kontenery](./media/containers/containers-state-failed-select.png)  
1. Uruchom kwerendę, a następnie rozwiń wiersz w wynikach, aby wyświetlić identyfikator obrazu.  
   ![nieudane kontenery](./media/containers/containers-state-failed.png)  
1. Wpisz następujące elementy w kwerendzie dziennika. `ContainerImageInventory | where ImageID == <ImageID>`, aby wyświetlić szczegółowe informacje o obrazie, takie jak rozmiar obrazu i liczba obrazów zatrzymanych i nieudanych.  
   ![nieudane kontenery](./media/containers/containers-failed04.png)

## <a name="query-logs-for-container-data"></a>Dzienniki kwerend dotyczące danych kontenera

Podczas rozwiązywania problemów z określonym błędem, może pomóc zobaczyć, gdzie występuje w danym środowisku. Następujące typy dzienników ułatwią tworzenie kwerend w celu zwrócenia żądanych informacji.

- **ContainerImageInventory** — użyj tego typu, gdy próbujesz znaleźć informacje uporządkowane według obrazu i wyświetlić informacje o obrazie, takie jak identyfikatory lub rozmiary obrazu.
- **ContainerInventory** — użyj tego typu, gdy chcesz uzyskać informacje o lokalizacji kontenera, jakie są ich nazwy i jakie obrazy są uruchomione.
- **ContainerLog** — użyj tego typu, gdy chcesz znaleźć określone informacje dziennika błędów i wpisy.
- **ContainerNodeInventory_CL**  Użyj tego typu, jeśli chcesz uzyskać informacje o hoście/węźle, w którym znajdują się kontenery. Zapewnia wersję platformy Docker, typ aranżacji, magazyn i informacje o sieci.
- **ContainerProcess_CL** Użyj tego typu, aby szybko wyświetlić proces uruchomiony w kontenerze.
- **ContainerServiceLog** — użyj tego typu podczas próby znalezienia informacji o szlaku inspekcji dla demona platformy Docker, takich jak uruchamianie, zatrzymywania, usuwanie lub ściąganie poleceń.
- **KubeEvents_CL**  Użyj tego typu, aby wyświetlić zdarzenia Kubernetes.
- **KubePodInventory_CL**  Tego typu należy używać, aby zrozumieć informacje o hierarchii klastra.


### <a name="to-query-logs-for-container-data"></a>Aby zbadać dzienniki dotyczące danych kontenera

* Wybierz obraz, o który wiesz, że ostatnio nie powiódł się, i znajdź dla niego dzienniki błędów. Zacznij od znalezienia nazwy kontenera, który jest uruchomiony, że obraz z **ContainerInventory** wyszukiwania. Na przykład, wyszukaj`ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Szukaj kontenerów Ubuntu](./media/containers/search-ubuntu.png)

  Rozwiń dowolny wiersz w wynikach, aby wyświetlić szczegóły dla tego kontenera.

## <a name="example-log-queries"></a>Przykładowe kwerendy dziennika

Często jest przydatne do tworzenia zapytań, począwszy od przykładu lub dwóch, a następnie modyfikowanie ich, aby pasowały do środowiska. Jako punkt wyjścia można eksperymentować z **przykładowych zapytań** obszaru, aby ułatwić tworzenie bardziej zaawansowanych zapytań.

![Zapytania kontenerów](./media/containers/containers-queries.png)

## <a name="saving-log-queries"></a>Zapisywanie zapytań dziennika

Zapisywanie zapytań jest standardową funkcją w usłudze Azure Monitor. Zapisując je, będziesz mieć te, które znalazłeś przydatne przydatne do wykorzystania w przyszłości.

Po utworzeniu kwerendy, która okaże się przydatna, zapisz ją, klikając **pozycję Ulubione** u góry strony Wyszukiwanie dzienników. Następnie możesz łatwo uzyskać do niego dostęp później ze strony **Mój pulpit nawigacyjny.**

## <a name="next-steps"></a>Następne kroki

[Dzienniki kwerendy,](../log-query/log-query-overview.md) aby wyświetlić szczegółowe rekordy danych kontenera.
