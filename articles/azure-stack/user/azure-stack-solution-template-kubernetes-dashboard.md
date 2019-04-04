---
title: Dostęp do pulpitu nawigacyjnego rozwiązania Kubernetes w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak dostęp do pulpitu nawigacyjnego rozwiązania Kubernetes w usłudze Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/07/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 02/27/2019
ms.openlocfilehash: 4e9df0d413b964b4a14cf9ca48db8b7956b441f9
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2019
ms.locfileid: "58482593"
---
# <a name="access-the-kubernetes-dashboard-in-azure-stack"></a>Dostęp do pulpitu nawigacyjnego rozwiązania Kubernetes w usłudze Azure Stack 

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit* 
> [!Note]   
> Rozwiązanie Kubernetes w usłudze Azure Stack jest w wersji zapoznawczej. Odłączony platformę Azure Stack nie jest obecnie obsługiwane przez wersję zapoznawczą. 

Kubernetes obejmuje pulpit nawigacyjny sieci web, używanego do podstawowych operacji zarządzania. Ten pulpit nawigacyjny pozwala wyświetlić stan kondycji podstawowych i metryk dla aplikacji, tworzenia i wdrażania usług i edytować istniejące aplikacje. W tym artykule pokazano, jak skonfigurować pulpit nawigacyjny platformy Kubernetes w usłudze Azure Stack.

## <a name="prerequisites-for-kubernetes-dashboard"></a>Wymagania wstępne dotyczące pulpitu nawigacyjnego rozwiązania Kubernetes

* Klaster usługi Azure Stack Kubernetes

    Musisz mieć wdrożono klaster Kubernetes w usłudze Azure Stack. Aby uzyskać więcej informacji, zobacz [wdrażanie Kubernetes](azure-stack-solution-template-kubernetes-deploy.md).

* Klient SSH

    Będziesz potrzebować klienta SSH do zabezpieczeń nawiązać połączenie z węzłem głównym w klastrze. Jeśli używasz programu Windows, możesz użyć [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm). Konieczne będzie klucz prywatny używany podczas wdrażania klastra Kubernetes.

* FTP (PSCP)

    Możesz także klienta FTP, który obsługuje protokół SSH, jak i SSH File Transfer Protocol można przetransferować certyfikaty z węzła głównego na komputerze zarządzania usługi Azure Stack. Możesz użyć [FileZilla](https://filezilla-project.org/download.php?type=client). Konieczne będzie klucz prywatny używany podczas wdrażania klastra Kubernetes.

## <a name="overview-of-steps-to-enable-dashboard"></a>Przegląd czynności, aby umożliwić pulpitu nawigacyjnego

1.  Eksportowanie certyfikatów Kubernetes z węzła głównego w klastrze. 
2.  Zaimportuj certyfikaty do usługi Azure Stack na komputerze zarządzania.
2.  Otwórz pulpit nawigacyjny sieci web rozwiązania Kubernetes. 

## <a name="export-certificate-from-the-master"></a>Eksportowanie certyfikatu z poziomu głównego 

Adres URL pulpitu nawigacyjnego można pobrać z węzła głównego w klastrze.

1. Uzyskaj publiczny adres IP i nazwy użytkownika w przypadku głównego klastra na pulpicie nawigacyjnym usługi Azure Stack. Aby uzyskać te informacje:

    - Zaloguj się do [portalu Azure Stack](https://portal.local.azurestack.external/)
    - Wybierz **wszystkich usług** > **wszystkie zasoby**. Znajdź główną w swojej grupie zasobów klastra. Nosi nazwę główną `k8s-master-<sequence-of-numbers>`. 

2. Otwórz węzeł główny w portalu. Kopiuj **publiczny adres IP** adresu. Kliknij przycisk **Connect** można pobrać swoją nazwę użytkownika w **Zaloguj się przy użyciu lokalnego konta maszyny Wirtualnej** pole. Jest to ta sama nazwa użytkownika, którego jest ustawiany podczas tworzenia klastra usługi. Użyj publicznego adresu IP, a nie prywatnego adresu IP na liście w bloku connect.

3.  Otwórz klient SSH, aby nawiązać połączenie z wzorcem. Jeśli pracujesz w Windows, możesz użyć [Putty](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-connect-vm) do utworzenia połączenia. Będzie dla węzła głównego, nazwę użytkownika, przy użyciu publicznego adresu IP, a następnie dodaj klucz prywatny, którego użyto podczas tworzenia klastra.

4.  Gdy nawiązuje połączenie terminal, wpisz `kubectl` otworzyć klienta wiersza polecenia usługi Kubernetes.

5. Uruchom następujące polecenie:

    ```Bash   
    kubectl cluster-info 
    ``` 
    Adres URL można znaleźć na pulpicie nawigacyjnym. Na przykład: `https://k8-1258.local.cloudapp.azurestack.external/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`

6.  Wyodrębnij certyfikatu z podpisem własnym, a następnie przekonwertować go na PFX format. Uruchom następujące polecenie:

    ```Bash  
    sudo su 
    openssl pkcs12 -export -out /etc/kubernetes/certs/client.pfx -inkey /etc/kubernetes/certs/client.key  -in /etc/kubernetes/certs/client.crt -certfile /etc/kubernetes/certs/ca.crt 
    ```

7.  Pobieranie listy wpisów tajnych w **systemu kubernetes** przestrzeni nazw. Uruchom następujące polecenie:

    ```Bash  
    kubectl -n kube-system get secrets
    ```

    Zanotuj kubernetes — pulpit nawigacyjny — token -\<XXXXX > wartość. 

8.  Pobierz token i zapisz go. Aktualizacja `kubernetes-dashboard-token-<####>` wartością klucza tajnego z poprzedniego kroku.

    ```Bash  
    kubectl -n kube-system describe secret kubernetes-dashboard-token-<####>| awk '$1=="token:"{print $2}' 
    ```

## <a name="import-the-certificate"></a>Zaimportuj certyfikat

1. Otwórz Filezilla i nawiązać połączenie z węzłem głównym. Konieczne będzie:

    - publiczny adres IP węzła głównego
    - Nazwa użytkownika
    - prywatny klucz tajny
    - Użyj **SFTP - SSH protokół transferu plików**

2. Kopiuj `/etc/kubernetes/certs/client.pfx` i `/etc/kubernetes/certs/ca.crt` maszyną zarządzania usługi Azure Stack.

3. Zanotuj lokalizacji plików. Zaktualizuj skrypt przy użyciu lokalizacji, a następnie otwórz program PowerShell z podwyższonym poziomem uprawnień wiersza. Uruchom zaktualizowanego skryptu:  

    ```powershell   
    Import-Certificate -Filepath "ca.crt" -CertStoreLocation cert:\LocalMachine\Root 
    $pfxpwd = Get-Credential -UserName 'Enter password below' -Message 'Enter password below' 
    Import-PfxCertificate -Filepath "client.pfx" -CertStoreLocation cert:\CurrentUser\My -Password $pfxpwd.Password 
    ``` 

## <a name="open-the-kubernetes-dashboard"></a>Otwórz pulpit nawigacyjny platformy Kubernetes 

1. Wyłącz blokowanie wyskakujących okienek w przeglądarce sieci Web.

2. Punkt, w przeglądarce na adres URL zauważyć po uruchomieniu polecenia `kubectl cluster-info`. Na przykład: https:\//azurestackdomainnamefork8sdashboard/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard: / serwera proxy 
3. Wybierz certyfikat klienta.
4. Wprowadź token. 
5. Połącz ponownie w wierszu polecenia powłoki bash w węźle głównym i nadać uprawnienia do `kubernetes-dashboard`. Uruchom następujące polecenie:

    ```Bash  
    kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard 
    ``` 

    Skrypt podaje `kubernetes-dashboard` uprawnień administratora w chmurze. Aby uzyskać więcej informacji, zobacz [klastrów z obsługą funkcji RBAC dla](https://docs.microsoft.com/azure/aks/kubernetes-dashboard).

Można użyć pulpitu nawigacyjnego. Aby uzyskać więcej informacji na temat pulpitu nawigacyjnego rozwiązania Kubernetes, zobacz [pulpit nawigacyjny interfejsu użytkownika sieci Web rozwiązania Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/) 

![Pulpit nawigacyjny platformy Kubernetes w usłudze Azure Stack](media/azure-stack-solution-template-kubernetes-dashboard/azure-stack-kub-dashboard.png)

## <a name="next-steps"></a>Kolejne kroki 

[Wdrażanie rozwiązania Kubernetes do usługi Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)  

[Dodaj klaster Kubernetes w portalu Marketplace (dla operatora infrastruktury Azure Stack)](../azure-stack-solution-template-kubernetes-cluster-add.md)  

[Kubernetes na platformie Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)  
