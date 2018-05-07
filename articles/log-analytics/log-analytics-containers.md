---
title: Rozwiązania monitorowanie kontenera Azure Log Analytics | Dokumentacja firmy Microsoft
description: Rozwiązanie monitorowania kontenera w Log Analytics pomaga wyświetlać i zarządzać Docker i Windows hostów kontenera w jednym miejscu.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: e1e4b52b-92d5-4bfa-8a09-ff8c6b5a9f78
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/26/2018
ms.author: magoedte
ms.openlocfilehash: 6adde6a76a7675ef4d8b63757fc9419500872dd9
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/04/2018
---
# <a name="container-monitoring-solution-in-log-analytics"></a>Kontener rozwiązania monitorowanie analizy dzienników

![Symbol kontenerów](./media/log-analytics-containers/containers-symbol.png)

W tym artykule opisano sposób konfigurowania i używania to rozwiązanie monitorowanie kontenera w analizy dzienników, która ułatwia wyświetlanie i zarządzanie Docker i Windows hostów kontenera w jednym miejscu. Docker to system wirtualizacji oprogramowania pozwala utworzyć kontenerów, które automatyzują wdrożenia oprogramowania na infrastruktury informatycznej.

Rozwiązanie zawiera kontenery, które są, obrazów kontenera nich uruchomiony uruchomiona, i gdy kontenery są. Można wyświetlić szczegółowe informacje o inspekcji przedstawiający poleceń używanych przez kontenery. I rozwiązywać kontenery wyświetlanie i wyszukując scentralizowane dzienniki bez konieczności zdalnie wyświetlić hostach Docker lub systemu Windows. Można znaleźć kontenerów, które mogą być zakłócenia i spójniejsze nadmiarowe zasobów na hoście. I można wyświetlić scentralizowane procesora CPU, pamięci, magazynu i użycia i wydajności informacje o sieci dla kontenerów. Na komputerach z systemem Windows, można scentralizować i porównaj dzienniki systemu Windows Server, Hyper-V i kontenery Docker. Rozwiązanie obsługuje następujące orchestrators kontenera:

- Docker Swarm
- DC/OS
- Kubernetes
- Service Fabric
- Red Hat OpenShift

Jeśli interesuje Cię monitorowania wydajności obciążeń wdrożonych w środowiskach Kubernetes hostowanej na AKS (usługi kontenera platformy Azure), zobacz [usługi kontenera platformy Azure Monitor](../monitoring/monitoring-container-health.md).  To rozwiązanie monitorowanie kontenera nie obsługują monitorowanie tej platformy.  

Na poniższym diagramie przedstawiono relacje między różnymi hostów kontenera i agentów z analizy dzienników.

![Diagram kontenerów](./media/log-analytics-containers/containers-diagram.png)

## <a name="system-requirements-and-supported-platforms"></a>Wymagania systemowe i obsługiwane platformy

Przed rozpoczęciem należy przejrzeć następujące informacje, aby sprawdzić, czy zostały spełnione wymagania wstępne.

### <a name="container-monitoring-solution-support-for-docker-orchestrator-and-os-platform"></a>Rozwiązanie monitorowania kontener obsługuje platformy Docker Orchestrator i systemu operacyjnego
W poniższej tabeli przedstawiono aranżacji Docker i monitorowania obsługę kontenera magazynu, wydajności i dzienniki z analizy dzienników systemu operacyjnego.   

| | ACS | Linux | Windows | Kontener<br>Spis | Image (Obraz)<br>Spis | Węzeł<br>Spis | Kontener<br>Wydajność | Kontener<br>Wydarzenie | Wydarzenie<br>Log | Kontener<br>Log |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Kubernetes | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Mesosphere<br>DC/OS | &#8226; | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; |
| Docker<br>Swarm | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Usługa<br>Sieć szkieletowa | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; |
| Red Hat Otwórz<br>Shift | | &#8226; | | &#8226; | &#8226;| &#8226; | &#8226; | &#8226; | | &#8226; |
| Windows Server<br>(autonomiczna) | | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |
| Serwer systemu Linux<br>(autonomiczna) | | &#8226; | | &#8226; | &#8226; | &#8226; | &#8226; | &#8226; | | &#8226; |


### <a name="docker-versions-supported-on-linux"></a>Wersje docker obsługiwane w systemie Linux

- Docker 1.11 do 1.13
- V17.06 docker CE i EE

### <a name="x64-linux-distributions-supported-as-container-hosts"></a>x64 dystrybucje systemu Linux obsługiwane jako hosty kontenera

- Ubuntu 14.04 LTS i 16.04 LTS
- CoreOS(stable)
- Amazon Linux 2016.09.0
- openSUSE 13.2
- openSUSE LEAP 42.2
- CentOS 7.2 i 7.3
- SLES 12
- RHEL 7.2 i 7.3
- Red Hat OpenShift kontenera platformy (OCP) 3.4 i 3.5
- ACS Mesosphere DC/OS 1.7.3 do 1.8.8
- Usługi ACS Kubernetes 1.4.5 do wersji 1.6
    - Zdarzenia Kubernetes, Kubernetes magazynu i kontenera procesów są obsługiwane tylko z wersji 1.4.1-45 i później Agent pakietu OMS dla systemu Linux
- Usługi ACS Docker Swarm

### <a name="supported-windows-operating-system"></a>Obsługiwany system operacyjny Windows

- Windows Server 2016
- Windows 10 Anniversary Edition (Professional lub Enterprise)

### <a name="docker-versions-supported-on-windows"></a>Wersje docker obsługiwane w systemie Windows

- Docker 1.12 i 1.13
- Docker 17.03.0 i nowsze

## <a name="installing-and-configuring-the-solution"></a>Instalowanie i konfigurowanie rozwiązania
Skorzystaj z poniższych informacji, aby zainstalować i skonfigurować rozwiązania.

1. Dodaj rozwiązanie monitorowania kontenera do obszaru roboczego analizy dzienników z [witrynę Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ContainersOMS?tab=Overview) lub przy użyciu procesu opisanego w [rozwiązań dodać analizy dzienników z galerii rozwiązań](log-analytics-add-solutions.md).

2. Zainstalować i korzystać z agentem pakietu OMS Docker. Oparte na systemie operacyjnym i Docker orchestrator, można użyć następujących metod można skonfigurować agenta.
  - W przypadku autonomicznych hostów:
    - W obsługiwanych systemach operacyjnych Linux, zainstaluj i uruchom Docker, a następnie zainstaluj i skonfiguruj [Agent pakietu OMS Linux](log-analytics-agent-linux.md).  
    - Na CoreOS nie można uruchomić agenta pakietu OMS dla systemu Linux. Zamiast tego należy uruchomić konteneryzowanych wersję agenta pakietu OMS dla systemu Linux. Przegląd [hostów kontenera systemu Linux, w tym CoreOS](#for-all-linux-container-hosts-including-coreos) lub [hostów kontenera platformy Azure dla instytucji rządowych Linux, w tym CoreOS](#for-all-azure-government-linux-container-hosts-including-coreos) podczas pracy z kontenerami w chmurze Azure dla instytucji rządowych.
    - W systemie Windows Server 2016 i Windows 10 zainstalować aparatem platformy Docker i klienta, a następnie połącz agenta w celu zbierania informacji oraz wysyłać je do analizy dzienników. Przegląd [zainstalować i skonfigurować hosty kontenera systemu Windows](#install-and-configure-windows-container-hosts) Jeśli środowisko systemu Windows.
  - Do aranżacji wielu hostów Docker:
    - Jeśli masz środowisko Red Hat OpenShift przejrzeć [skonfigurować agenta pakietu OMS dla Red Hat OpenShift](#configure-an-oms-agent-for-red-hat-openshift).
    - Jeśli masz klaster Kubernetes za pomocą usługi kontenera platformy Azure:
       - Przegląd [konfiguracji agenta pakietu OMS Linux pod kątem Kubernetes](#configure-an-oms-linux-agent-for-kubernetes).
       - Przegląd [konfiguracji agenta pakietu OMS systemu Windows pod kątem Kubernetes](#configure-an-oms-windows-agent-for-kubernetes).
       - Przegląd [Helm używany do wdrażania agenta pakietu OMS na systemie Linux Kubernetes](#use-helm-to-deploy-oms-agent-on-linux-kubernetes).
    - W przypadku klastra usługi kontenera platformy Azure DC/OS więcej w [monitorować klastra usługi kontenera platformy Azure DC/OS w usłudze Operations Management Suite](../container-service/dcos-swarm/container-service-monitoring-oms.md).
    - Jeśli masz środowisku trybu Docker Swarm, dowiedzieć się więcej o [skonfigurować agenta pakietu OMS dla rozwiązania Docker Swarm](#configure-an-oms-agent-for-docker-swarm).
    - Jeśli masz klaster sieci szkieletowej usług dowiedzieć się więcej na [monitorować kontenery z analizy dzienników OMS](../service-fabric/service-fabric-diagnostics-oms-containers.md).

Przegląd [aparatem platformy Docker w systemie Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon) artykułu, aby uzyskać dodatkowe informacje o sposobie instalowania i konfigurowania silnik Docker na komputerach z systemem Windows.

> [!IMPORTANT]
> Musi być uruchomiona docker **przed** zainstalowaniu [Agent pakietu OMS Linux](log-analytics-agent-linux.md) na hostach kontenera. Jeśli agent został już zainstalowany przed zainstalowaniem Docker, należy ponownie zainstalować agenta pakietu OMS dla systemu Linux. Aby uzyskać więcej informacji na temat rozwiązania Docker, zobacz [Docker witryny sieci Web](https://www.docker.com).


### <a name="install-and-configure-linux-container-hosts"></a>Instalowanie i konfigurowanie hostów kontenera systemu Linux

Po zainstalowaniu Docker Użyj następujących ustawień dla hosta kontenera, aby skonfigurować agenta do użycia z Docker. Najpierw należy swój identyfikator obszaru roboczego analizy dzienników i klucz, który można znaleźć w portalu Azure. W obszarze roboczym, kliknij przycisk **Szybki Start** > **komputerów** do wyświetlania Twojego **identyfikator obszaru roboczego** i **klucz podstawowy**.  Skopiuj i wklej obie wartości do ulubionego edytora.

**Dla wszystkich hostów kontenera Linux z wyjątkiem CoreOS:**

- Aby uzyskać więcej informacji i kroki dotyczące instalowania agenta pakietu OMS dla systemu Linux, zobacz [połączenie komputerów Linux Log Analytics](log-analytics-concept-hybrid.md).

**Dla wszystkich hostów kontenera Linux CoreOS w tym:**

Uruchom kontenera, w którym mają być monitorowane. Zmodyfikuj i skorzystaj z następującego przykładu:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -e WSID="your workspace id" -e KEY="your key" -h=`hostname` -p 127.0.0.1:25225:25225 --name="omsagent" --restart=always microsoft/oms
```

**Dla wszystkich hostów kontenera platformy Azure dla instytucji rządowych Linux CoreOS w tym:**

Uruchom kontenera, w którym mają być monitorowane. Zmodyfikuj i skorzystaj z następującego przykładu:

```
sudo docker run --privileged -d -v /var/run/docker.sock:/var/run/docker.sock -v /var/log:/var/log -e WSID="your workspace id" -e KEY="your key" -e DOMAIN="opinsights.azure.us" -p 127.0.0.1:25225:25225 -p 127.0.0.1:25224:25224/udp --name="omsagent" -h=`hostname` --restart=always microsoft/oms
```

**Zmiana typu zapytania z przy użyciu zainstalowanych agentów systemu Linux na jedną w kontenerze**

Jeśli wcześniej używana bezpośrednio zainstalować agenta i chcesz zamiast tego użyć agenta uruchomionego w kontenerze, należy najpierw usunąć agenta pakietu OMS dla systemu Linux. Zobacz [odinstalowanie agenta pakietu OMS Linux](log-analytics-agent-linux.md) zrozumienie, jak pomyślnie odinstalowania agenta.  

#### <a name="configure-an-oms-agent-for-docker-swarm"></a>Konfigurowanie agenta pakietu OMS dla rozwiązania Docker Swarm

Agent pakietu OMS można uruchomić jako usługę globalnej w rozwiązaniu Docker Swarm. Skorzystaj z poniższych informacji, aby utworzyć usługę agenta pakietu OMS. Należy podać identyfikator obszaru roboczego analizy dzienników i klucz podstawowy.

- Uruchom następujące polecenie na węzła głównego.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock  -e WSID="<WORKSPACE ID>" -e KEY="<PRIMARY KEY>" -p 25225:25225 -p 25224:25224/udp  --restart-condition=on-failure microsoft/oms
    ```

##### <a name="secure-secrets-for-docker-swarm"></a>Bezpiecznego hasła dla rozwiązania Docker Swarm

Po klucz tajny dla identyfikator i klucz podstawowy dla rozwiązania Docker Swarm, użyj następujące informacje Aby utworzyć tajnych informacji.

1. Uruchom następujące polecenie na węzła głównego.

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

3. Uruchom następujące polecenie, aby zainstalować kluczy tajnych konteneryzowanych Agent pakietu OMS.

    ```
    sudo docker service create  --name omsagent --mode global  --mount type=bind,source=/var/run/docker.sock,destination=/var/run/docker.sock --secret source=WSID,target=WSID --secret source=KEY,target=KEY  -p 25225:25225 -p 25224:25224/udp --restart-condition=on-failure microsoft/oms
    ```

#### <a name="configure-an-oms-agent-for-red-hat-openshift"></a>Konfigurowanie agenta pakietu OMS dla OpenShift Red Hat
Istnieją trzy sposoby dodawania Agent pakietu OMS do Red Hat OpenShift można uruchomić zbierania danych monitorowania kontenera.

* [Zainstaluj dla systemu Linux Agent pakietu OMS](log-analytics-agent-linux.md) bezpośrednio w każdym węźle OpenShift  
* [Włączanie rozszerzenia maszyny Wirtualnej analizy dziennika](log-analytics-azure-vm-extension.md) w każdym węźle OpenShift znajdującej się na platformie Azure  
* Zainstaluj agenta pakietu OMS jako zestaw demon OpenShift  

W tej sekcji możemy opisano kroki wymagane do zainstalowania agenta pakietu OMS jako zbiór demon OpenShift.  

1. Zaloguj się do węzła głównego OpenShift, a następnie skopiuj plik yaml programu [ocp omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-omsagent.yaml) z serwisu GitHub, aby Twój główny węzeł i zmodyfikować wartości z Identyfikatorem obszaru roboczego analizy dzienników i kluczem podstawowym.
2. Uruchom następujące polecenia, aby utworzyć projekt analizy dzienników i ustaw konto użytkownika.

    ```
    oadm new-project omslogging --node-selector='zone=default'
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. Aby wdrożyć zestaw demon, uruchom następujące polecenie:

    `oc create -f ocp-omsagent.yaml`

5. Aby sprawdzić, czy jest on skonfigurowany i działa poprawnie, wpisz następujące polecenie:

    `oc describe daemonset omsagent`  

    i powinien wyglądać dane wyjściowe:

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

Jeśli chcesz zabezpieczyć dziennika analizy identyfikator i klucz podstawowy, korzystając z pliku zestawu demon yaml programu Agent pakietu OMS przy użyciu kluczy tajnych, wykonaj następujące kroki.

1. Zaloguj się do węzła głównego OpenShift, a następnie skopiuj plik yaml programu [ocp-ds-omsagent.yaml](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-ds-omsagent.yaml) i klucz tajny Generowanie skryptu [ocp secretgen.sh](https://github.com/Microsoft/OMS-docker/blob/master/OpenShift/ocp-secretgen.sh) z usługi GitHub.  Ten skrypt spowoduje wygenerowanie pliku yaml programu kluczy tajnych identyfikator obszaru roboczego analizy dzienników i klucz podstawowy zabezpieczyć Twoje secrete informacji.  
2. Uruchom następujące polecenia, aby utworzyć projekt analizy dzienników i ustaw konto użytkownika. Klucz tajny Generowanie skryptu poprosi o podanie Identyfikatora obszaru roboczego analizy dzienników <WSID> i klucz podstawowy <KEY> i po jego ukończeniu, tworzy plik ocp secret.yaml.  

    ```
    oadm new-project omslogging --node-selector='zone=default'  
    oc project omslogging  
    oc create serviceaccount omsagent  
    oadm policy add-cluster-role-to-user cluster-reader   system:serviceaccount:omslogging:omsagent  
    oadm policy add-scc-to-user privileged system:serviceaccount:omslogging:omsagent  
    ```

4. Wdróż plik za pomocą tajne, uruchamiając następujące czynności:

    `oc create -f ocp-secret.yaml`

5. Sprawdź wdrożenie, uruchamiając następujące czynności:

    `oc describe secret omsagent-secret`  

    i powinien wyglądać dane wyjściowe:  

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

6. Wdróż plik zestawu demon yaml programu Agent pakietu OMS, uruchamiając następujące:

    `oc create -f ocp-ds-omsagent.yaml`  

7. Sprawdź wdrożenie, uruchamiając następujące czynności:

    `oc describe ds oms`

    i powinien wyglądać dane wyjściowe:

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

#### <a name="configure-an-oms-linux-agent-for-kubernetes"></a>Konfigurowanie agenta pakietu OMS Linux pod kątem Kubernetes

Dla Kubernetes skrypt będzie używany do generowania kluczy tajnych yaml programu w pliku o identyfikator obszaru roboczego i klucz podstawowy zainstalować agenta pakietu OMS dla systemu Linux. W [OMS Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes) są dostępne pliki korzystające z lub bez tajnych informacji.

- Agent pakietu OMS domyślnych dla systemu Linux DaemonSet nie ma tajnych informacji (omsagent.yaml)
- Agent pakietu OMS pliku yaml programu Linux DaemonSet używa tajnych informacji (omsagent-ds-secrets.yaml) z tajny generowania skryptów do generowania kluczy tajnych pliku yaml programu (omsagentsecret.yaml).

Można utworzyć omsagent DaemonSets z lub bez kluczy tajnych.

**Domyślny OMSagent DaemonSet yaml programu plik bez hasła**

- Dla domyślnego pliku yaml programu DaemonSet Agent pakietu OMS, Zastąp `<WSID>` i `<KEY>` WSID i klucza. Skopiuj plik do główny węzeł i uruchom następujące polecenie:

    ```
    sudo kubectl create -f omsagent.yaml
    ```

**Domyślny OMSagent DaemonSet yaml programu plik z kluczy tajnych**

1. Aby użyć DaemonSet Agent pakietu OMS przy użyciu poufne informacje, należy najpierw utworzyć kluczy tajnych.
    1. Skopiuj skrypt i pliku szablonu tajne i upewnij się, że znajdują się w tym samym katalogu.
        - Generowanie skryptu - gen.sh klucz tajny klucz tajny
        - Szablon tajne — template.yaml klucz tajny
    2. Uruchom skrypt, jak w następującym przykładzie. Skrypt wprowadza identyfikator obszaru roboczego analizy dzienników i klucz podstawowy i po wprowadzeniu ich skrypt tworzy plik tajny yaml programu, dlatego może być uruchomiony.   

        ```
        #> sudo bash ./secret-gen.sh
        ```

    3. Utwórz pod kluczy tajnych, uruchamiając następujące czynności:
        ```
        sudo kubectl create -f omsagentsecret.yaml
        ```

    4. Aby sprawdzić, uruchom następujące polecenie:

        ```
        keiko@ubuntu16-13db:~# sudo kubectl get secrets
        ```

        Dane wyjściowe powinny wyglądać:

        ```
        NAME                  TYPE                                  DATA      AGE
        default-token-gvl91   kubernetes.io/service-account-token   3         50d
        omsagent-secret       Opaque                                2         1d
        ```

        ```
        keiko@ubuntu16-13db:~# sudo kubectl describe secrets omsagent-secret
        ```

        Dane wyjściowe powinny wyglądać:

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

    5. Tworzenie sieci omsagent demon set, uruchamiając ``` sudo kubectl create -f omsagent-ds-secrets.yaml ```

2. Sprawdź, czy DaemonSet Agent pakietu OMS jest uruchomiona, podobny do następującego:

    ```
    keiko@ubuntu16-13db:~# sudo kubectl get ds omsagent
    ```

    ```
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   3         3         <none>          1h
    ```


Dla Kubernetes aby wygenerować plik yaml programu klucze tajne dla identyfikator i klucz podstawowy dla agenta pakietu OMS dla systemu Linux za pomocą skryptu. Skorzystaj z poniższych informacji przykład z [pliku yaml programu omsagent](https://github.com/Microsoft/OMS-docker/blob/master/Kubernetes/omsagent.yaml) zabezpieczyć dane poufne.

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

#### <a name="configure-an-oms-windows-agent-for-kubernetes"></a>Konfigurowanie agenta pakietu OMS systemu Windows dla Kubernetes
Dla systemu Windows Kubernetes skrypt będzie używany do generowania kluczy tajnych yaml programu w pliku o identyfikator obszaru roboczego i klucz podstawowy zainstalować agenta pakietu OMS. W [OMS Docker Kubernetes GitHub](https://github.com/Microsoft/OMS-docker/tree/master/Kubernetes/windows) są dostępne pliki korzystające z informacjami o poufne.  Musisz zainstalować agenta pakietu OMS oddzielnie dla węzłów master i agenta.  

1. Do użycia przy użyciu tajnych informacji we wzorcu DaemonSet Agent pakietu OMS węzła, zaloguj się i najpierw utworzyć kluczy tajnych.
    1. Skopiuj skrypt i pliku szablonu tajne i upewnij się, że znajdują się w tym samym katalogu.
        - Generowanie skryptu - gen.sh klucz tajny klucz tajny
        - Szablon tajne — template.yaml klucz tajny

    2. Uruchom skrypt, jak w następującym przykładzie. Wyświetleniu zapytania o OMS identyfikator i klucz podstawowy i po wprowadzeniu ich skrypt tworzy plik tajny yaml programu, dlatego może być uruchomiony.   

        ```
        #> sudo bash ./secret-gen.sh
        ```
    3. Tworzenie sieci omsagent demon set, uruchamiając ``` kubectl create -f omsagentsecret.yaml ```
    4. Aby sprawdzić, uruchom następujące polecenie:

        ```
        root@ubuntu16-13db:~# kubectl get secrets
        ```

        Dane wyjściowe powinny wyglądać:

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

    5. Tworzenie sieci omsagent demon set, uruchamiając ```kubectl create -f ws-omsagent-de-secrets.yaml```

2. Sprawdź, czy DaemonSet Agent pakietu OMS jest uruchomiona, podobny do następującego:

    ```
    root@ubuntu16-13db:~# kubectl get deployment omsagent
    NAME       DESIRED   CURRENT   NODE-SELECTOR   AGE
    omsagent   1         1         <none>          1h
    ```

3. Aby zainstalować agenta w węźle procesu roboczego, które są uruchomione systemu Windows, wykonaj czynności opisane w sekcji [zainstalować i skonfigurować hosty kontenera systemu Windows](#install-and-configure-windows-container-hosts).

#### <a name="use-helm-to-deploy-oms-agent-on-linux-kubernetes"></a>Użyj Helm, aby wdrożyć agenta pakietu OMS na Kubernetes systemu Linux
Aby używać helm do wdrożenia w środowisku Linux Kubernetes Agent pakietu OMS, wykonaj następujące kroki.

1. Tworzenie sieci omsagent demon set, uruchamiając ```helm install --name omsagent --set omsagent.secret.wsid=<WSID>,omsagent.secret.key=<KEY> stable/msoms```
2. Wyniki będą podobne do następującego:

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
3. Sprawdź stan omsagent, uruchamiając: ```helm status "omsagent"``` i dane wyjściowe będą podobne do następującego:

    ```
    keiko@k8s-master-3814F33-0:~$ helm status omsagent
    LAST DEPLOYED: Tue Sep 19 20:37:46 2017
    NAMESPACE: default
    STATUS: DEPLOYED
 
    RESOURCES:
    ==> v1/Secret
    NAME            TYPE    DATA  AGE
    omsagent-msoms  Opaque  3     17m
 
    ==> v1beta1/DaemonSet
    NAME            DESIRED  CURRENT  READY  UP-TO-DATE  AVAILABLE  NODE-SELECTOR  AGE
    omsagent-msoms  3        3        3      3           3          <none>         17m
    ```
Aby uzyskać więcej informacji, odwiedź stronę [kontenera rozwiązania Helm wykresu](https://aka.ms/omscontainerhelm).

### <a name="install-and-configure-windows-container-hosts"></a>Instalowanie i konfigurowanie hostów kontenera systemu Windows

Skorzystaj z informacji w sekcji, aby zainstalować i skonfigurować hosty kontenera systemu Windows.

#### <a name="preparation-before-installing-windows-agents"></a>Przygotowanie przed zainstalowaniem agentów systemu Windows

Aby zainstalować agentów na komputerach z systemem Windows, należy skonfigurować usługę Docker. Konfiguracji umożliwia agent systemu Windows lub analizy dzienników rozszerzenie maszyny wirtualnej do używania gniazda Docker TCP, dzięki czemu agenci dostęp zdalnie demona Docker i przechwytywania danych monitorowania.

##### <a name="to-start-docker-and-verify-its-configuration"></a>Aby uruchomić Docker i sprawdzić jego konfigurację

Istnieją kroki niezbędne do skonfigurowania TCP nazwany potok dla systemu Windows Server:

1. W programie Windows PowerShell umożliwiają potoku TCP i nazwanego potoku.

    ```
    Stop-Service docker
    dockerd --unregister-service
    dockerd --register-service -H npipe:// -H 0.0.0.0:2375  
    Start-Service docker
    ```

2. Konfigurowanie Docker z pliku konfiguracji dla potoku TCP i nazwany potok. Plik konfiguracji znajduje się w C:\ProgramData\docker\config\daemon.json.

    W pliku daemon.json potrzebne następujące elementy:

    ```
    {
    "hosts": ["tcp://0.0.0.0:2375", "npipe://"]
    }
    ```

Aby uzyskać więcej informacji na temat konfiguracji demon Docker używane z kontenerami systemu Windows, temacie [aparatem platformy Docker w systemie Windows](https://docs.microsoft.com/virtualization/windowscontainers/manage-docker/configure-docker-daemon).


#### <a name="install-windows-agents"></a>Zainstaluj agentów systemu Windows

Aby włączyć monitorowania kontenera systemu Windows i funkcji Hyper-V, należy zainstalować program Microsoft Monitoring Agent (MMA) na komputerach z systemem Windows, które są hostami kontenera. Na komputerach z systemem Windows w środowisku lokalnym, zobacz [połączyć komputery do analizy dzienników](log-analytics-windows-agent.md). W przypadku maszyn wirtualnych działających na platformie Azure, podłącz je do analizy dzienników przy użyciu [rozszerzenie maszyny wirtualnej](log-analytics-azure-vm-extension.md).

Można monitorować kontenery systemu Windows uruchomiona na sieć szkieletowa usług. Jednak tylko [maszyn wirtualnych działających na platformie Azure](log-analytics-azure-vm-extension.md) i [komputery z systemem Windows w środowisku lokalnym](log-analytics-windows-agent.md) są obecnie obsługiwane dla sieci szkieletowej usług.

Możesz sprawdzić, czy to rozwiązanie monitorowanie kontenera jest prawidłowo dla systemu Windows. Aby sprawdzić, czy pakiet administracyjny został poprawnie pobierania, należy wyszukać *ContainerManagement.xxx*. Pliki powinny być w folderze C:\Program Files\Microsoft Monitoring Agent\Agent\Health usługi State\Management pakietów.


## <a name="solution-components"></a>Składniki rozwiązania

Jeśli korzystasz z agentów systemu Windows, następujące pakiet administracyjny jest zainstalowany na każdym komputerze z agentem po dodaniu tego rozwiązania. Nie konfiguracji lub konserwacji jest wymagany dla pakietu administracyjnego.

- *ContainerManagement.xxx* zainstalowane w C:\Program Files\Microsoft Monitoring Agent\Agent\Health usługi State\Management pakietów

## <a name="container-data-collection-details"></a>Szczegóły pobierania danych kontenera
To rozwiązanie monitorowanie kontenera zbiera różne metryki i dziennika dane dotyczące wydajności z kontenera hostów i kontenerów przy użyciu agentów, które można włączyć.

Dane są zbierane co 3 minuty przez następujące typy agenta.

- [Agent pakietu OMS dla systemu Linux](log-analytics-linux-agents.md)
- [Agent systemu Windows](log-analytics-windows-agent.md)
- [Rozszerzenia maszyny Wirtualnej analizy dzienników](log-analytics-azure-vm-extension.md)


### <a name="container-records"></a>Rejestruje kontenera

W poniższej tabeli przedstawiono przykłady rekordów zebrane przez rozwiązanie monitorowanie kontenera i typy danych, które są wyświetlane w wynikach wyszukiwania dziennika.

| Typ danych | Typ danych w dzienniku wyszukiwania | Pola |
| --- | --- | --- |
| Wydajność dla hostów i kontenerów | `Perf` | Komputer, nazwa obiektu, CounterName &#40;czas procesora (%), dysk odczytuje MB, dysku zapisuje MB, użycie pamięć (MB), sieci odbieranie bajtów, sieci wysyłania w bajtach, procesor s użycia, sieć&#41;, równowartości, TimeGenerated, Ścieżka_licznika, SourceSystem |
| Kontener magazynu | `ContainerInventory` | TimeGenerated, komputera, nazwę kontenera, ContainerHostname, obraz, ImageTag, ContainerState, ExitCode, EnvironmentVar, polecenia, CreatedTime, StartedTime, FinishedTime, SourceSystem, identyfikatora kontenera, ImageID |
| Kontener magazynu obrazu | `ContainerImageInventory` | TimeGenerated, komputer, obraz, ImageTag, ImageSize, VirtualSize, działa wstrzymana, zatrzymana, nie powiodło się, SourceSystem, ImageID, TotalContainer |
| Kontener dziennika | `ContainerLog` | TimeGenerated, komputer, identyfikator obrazu, nazwy kontenera, LogEntrySource, LogEntry, SourceSystem, identyfikatora kontenera |
| Dziennik usługi kontenera | `ContainerServiceLog`  | TimeGenerated, Computer, TimeOfCommand, Image, Command, SourceSystem, ContainerID |
| Kontener węzła magazynu | `ContainerNodeInventory_CL`| TimeGenerated, Computer, ClassName_s, DockerVersion_s, OperatingSystem_s, Volume_s, Network_s, NodeRole_s, OrchestratorType_s, InstanceID_g, SourceSystem|
| Kubernetes spisu | `KubePodInventory_CL` | TimeGenerated, Computer, PodLabel_deployment_s, PodLabel_deploymentconfig_s, PodLabel_docker_registry_s, Name_s, Namespace_s, PodStatus_s, PodIp_s, PodUid_g, PodCreationTimeStamp_t, SourceSystem |
| Proces kontenera | `ContainerProcess_CL` | TimeGenerated, Computer, Pod_s, Namespace_s, ClassName_s, InstanceID_s, Uid_s, PID_s, PPID_s, C_s, STIME_s, Tty_s, TIME_s, Cmd_s, Id_s, Name_s, SourceSystem |
| Zdarzenia Kubernetes | `KubeEvents_CL` | TimeGenerated, Computer, Name_s, ObjectKind_s, Namespace_s, Reason_s, Type_s, SourceComponent_s, SourceSystem, Message |

Etykiety dołączany do *PodLabel* typy danych są etykiet niestandardowych. Dołączany etykiety PodLabel przedstawione w tabeli przedstawiono przykłady. Tak `PodLabel_deployment_s`, `PodLabel_deploymentconfig_s`, `PodLabel_docker_registry_s` różnią się w zestawie danych w danym środowisku, a objęty przypominać `PodLabel_yourlabel_s`.


## <a name="monitor-containers"></a>Monitorowanie kontenerów
Po rozwiązaniu włączone w portalu usługi Analiza dzienników **kontenery** kafelka zawiera podsumowanie informacji o hostach kontenera i kontenery uruchomione na hostach.

![Kontenery kafelka](./media/log-analytics-containers/containers-title.png)

Kafelek Wyświetla liczbę kontenerów masz w środowisku, czy jest nie, uruchamiania i zatrzymywana.

### <a name="using-the-containers-dashboard"></a>Przy użyciu pulpitu nawigacyjnego kontenerów
Kliknij przycisk **kontenery** kafelka. Z tego miejsca zobaczysz widoki uporządkowane według:

- **Kontenery zdarzeń** -pokazuje stan kontenera i komputerów z kontenerami nie powiodło się.
- **Dzienniki kontenera** — przedstawia wykres kontenera pliki dziennika wygenerowane przez czas i listę komputerów o najwyższym numerze plików dziennika.
- **Zdarzenia Kubernetes** — przedstawia wykres Kubernetes zdarzenia generowane przez czas i listę przyczyn, dlaczego stanowiskami wygenerowane zdarzenia. *Ten zestaw danych jest używany tylko w środowiskach Linux.*
- **Spis Namespace Kubernetes** — pokazuje liczbę obszary nazw i stanowiskami i przedstawiono ich hierarchii. *Ten zestaw danych jest używany tylko w środowiskach Linux.*
- **Kontener węzeł spisu** — pokazuje liczbę typów aranżacji używanych na hosty węzłów kontenera. Węzły komputera/hostów są także wyświetlane przez liczbę kontenerów. *Ten zestaw danych jest używany tylko w środowiskach Linux.*
- **Kontener obrazów spisu** -pokazuje całkowitą liczbę obrazów kontenera używane oraz liczbę typów obrazów. Liczba obrazów są także wyświetlane przez tag obrazu.
- **Stan kontenery** — zawiera całkowitą liczbę kontenera Komputery węzłów/hosta uruchomionych kontenerów. Komputery są również wyświetlane według liczba uruchomionych na hostach.
- **Proces kontenera** — przedstawia wykres liniowy procesów kontenera działających w czasie. Kontenery znajdują się przez uruchomienie polecenia procesu w kontenerach. *Ten zestaw danych jest używany tylko w środowiskach Linux.*
- **Wydajność Procesora kontenera** — przedstawia wykres liniowy w średnie wykorzystanie procesora CPU, wraz z upływem czasu hosty węzłów komputera. Również listy węzłów komputera/hostów na podstawie średniego użycia Procesora.
- **Kontener wydajności pamięci** — przedstawia wykres liniowy użycia pamięci w czasie. Zawiera także listę wykorzystania opartego na nazwie wystąpienia pamięci komputera.
- **Wydajność komputera** — zawiera wykresy liniowe procent wydajności Procesora w czasie, procent użycia pamięci przez czas i megabajtów wolnego miejsca na dysku wraz z upływem czasu. Można umieść kursor nad wiersza na wykresie, aby wyświetlić więcej szczegółów.


Każdy obszar pulpitu nawigacyjnego jest wizualną reprezentację wyszukiwania, które jest uruchamiane na zebranych danych.

![Kontenery pulpitu nawigacyjnego](./media/log-analytics-containers/containers-dash01.png)

![Kontenery pulpitu nawigacyjnego](./media/log-analytics-containers/containers-dash02.png)

W **stan kontenera** obszarze kliknij górny obszar, jak pokazano poniżej.

![Stan kontenerów](./media/log-analytics-containers/containers-status.png)

Otwiera dziennik wyszukiwania, wyświetlania informacji o stanie kontenerów.

![Dziennik wyszukiwania dla kontenerów](./media/log-analytics-containers/containers-log-search.png)

W tym miejscu można edytować zapytania wyszukiwania można zmodyfikować, aby znaleźć określone informacje, że jesteś zainteresowany. Aby uzyskać więcej informacji na temat wyszukiwania dziennika, zobacz [Zaloguj wyszukiwania analizy dzienników](log-analytics-log-searches.md).

## <a name="troubleshoot-by-finding-a-failed-container"></a>Rozwiązywanie problemów z znajdując kontenera nie powiodło się

Analiza dzienników oznacza kontener jako **nie powiodło się** Jeśli został zakończony z kodem zakończenia inną niż zero. Można zapoznać się z omówieniem błędów i awarii w środowisku w **nie powiodło się kontenery** obszaru.

### <a name="to-find-failed-containers"></a>Aby znaleźć kontenery nie powiodło się
1. Kliknij przycisk **stan kontenera** obszaru.  
   ![Stan kontenerów](./media/log-analytics-containers/containers-status.png)
2. Dziennik wyszukiwania otwiera i wyświetla stan kontenerów, podobny do następującego.  
   ![Stan kontenerów](./media/log-analytics-containers/containers-log-search.png)
3. Następnie kliknij przycisk zagregowane wartości kontenery nie powiodło się, aby wyświetlić dodatkowe informacje. Rozwiń węzeł **Pokaż więcej** Aby wyświetlić identyfikator obrazu.  
   ![kontenery nie powiodło się](./media/log-analytics-containers/containers-state-failed.png)  
4. Następnie wpisz następujące polecenie w zapytaniu wyszukiwania. `ContainerInventory <ImageID>` Aby wyświetlić szczegóły dotyczące obrazu, takich jak rozmiar obrazu i Liczba obrazów zatrzymane, a nie powiodło się.  
   ![kontenery nie powiodło się](./media/log-analytics-containers/containers-failed04.png)

## <a name="search-logs-for-container-data"></a>Dzienniki wyszukiwania danych kontenera
W przypadku Rozwiązywanie problemów z określonego błędu, ułatwia Zobacz, gdzie występuje w danym środowisku. Następujące typy dziennika ułatwia tworzenie kwerend do zwracania informacji, które chcesz.


- **ContainerImageInventory** — Użyj tego typu, jeśli próbujesz można znaleźć informacje są zorganizowane według obrazu oraz wyświetlać informacje obrazu, takich jak obraz lub identyfikatory rozmiarów.
- **ContainerInventory** — tego typu należy używać informacji o lokalizację kontenera, ich nazwy są i jakie obrazy są na nich uruchomione.
- **ContainerLog** — Użyj tego typu można znaleźć informacje dziennika błędu i zapisów.
- **ContainerNodeInventory_CL** tego typu należy używać informacji o hoście/węzła gdzie są znajdującej się kontenerów. Umożliwia Docker wersji, typ aranżacji magazynu i informacje o sieci.
- **ContainerProcess_CL** użyć tego typu, aby szybko zapoznać się z procesem uruchomione w kontenerze.
- **ContainerServiceLog** — Użyj tego typu, gdy użytkownik próbuje odnaleźć informacje dziennika inspekcji demona Docker jak uruchamianie, Zatrzymaj, usunąć lub poleceń ściągnięcia.
- **KubeEvents_CL** umożliwia wyświetlanie zdarzeń Kubernetes tego typu.
- **KubePodInventory_CL** użyć tego typu, jeśli chcesz poznać informacje o klastrze hierarchii.


### <a name="to-search-logs-for-container-data"></a>Do wyszukania w dziennikach dane w kontenerze
* Wybierz obraz, który ostatnio nie powiodło się i znaleźć w dziennikach błędów. Uruchom znajdując nazwę kontenera, która działa obrazu z **ContainerInventory** wyszukiwania. Na przykład wyszukaj `ContainerInventory | where Image == "ubuntu" and ContainerState == "Failed"`  
    ![Wyszukaj kontenery Ubuntu](./media/log-analytics-containers/search-ubuntu.png)

  Nazwa kontenera dalej, aby **nazwa**i poszukaj tych dzienników. W tym przykładzie jest to `ContainerLog | where Name == "cranky_stonebreaker"`.

**Wyświetlanie informacji o wydajności**

W przypadku od tworzyć zapytania, ułatwia Zobacz, co jest możliwe najpierw. Na przykład aby wyświetlić wszystkie dane dotyczące wydajności, spróbuj szerokie zapytania, wpisując następujące zapytanie wyszukiwania.

```
Perf
```

![kontenery wydajności](./media/log-analytics-containers/containers-perf01.png)

Można określić zakres dane wydajności, które widać do określonego kontenera przez wpisanie nazwy tego zapytania z prawej strony.

```
Perf <containerName>
```

Które z listą metryki wydajności, które są zbierane dla poszczególnych kontenera.

![kontenery wydajności](./media/log-analytics-containers/containers-perf03.png)

## <a name="example-log-search-queries"></a>Przykładowe zapytania wyszukiwania dziennika
Często jest przydatne do tworzenia zapytań w programie przykład lub dwóch, a następnie modyfikując je do środowiska. Jako punkt początkowy, możesz eksperymentować z **przykładowe zapytania** obszaru do tworzenia bardziej zaawansowanych zapytań.

![Kontenery zapytań](./media/log-analytics-containers/containers-queries.png)


## <a name="saving-log-search-queries"></a>Zapisywanie dziennika zapytania wyszukiwania
Zapisywanie zapytań jest standardowa funkcja Log Analytics. Zapisując je, należy te, które zostały znalezione przydatne przydatne do użytku w przyszłości.

Po utworzeniu kwerendę, która jest użyteczna, zapisz go, klikając **ulubione** w górnej części strony wyszukiwania dziennika. Następnie możesz z łatwością później z **Mój pulpit nawigacyjny** strony.

## <a name="next-steps"></a>Kolejne kroki
* [Wyszukaj dzienniki](log-analytics-log-searches.md) Aby wyświetlić szczegółowe kontenera rekordów danych.
