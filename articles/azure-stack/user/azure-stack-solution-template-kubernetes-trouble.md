---
title: Rozwiązywanie problemów z wdrożenia rozwiązania Kubernetes (K8) do usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z wdrożenia rozwiązania Kubernetes (K8) do usługi Azure Stack.
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
ms.date: 10/29/2018
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: 7071e22d703ab7ec3a51eff02d1694fc04cb3417
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2018
ms.locfileid: "50231240"
---
# <a name="troubleshoot-your-deployment-to-kubernetes-to-azure-stack"></a>Rozwiązywanie problemów z wdrożenia rozwiązania Kubernetes w usłudze Azure Stack

*Dotyczy: Usługa Azure Stack zintegrowane systemy i usługi Azure Stack Development Kit*

> [!Note]  
> Rozwiązanie Kubernetes w usłudze Azure Stack jest w wersji zapoznawczej.

Następujący artykuł patrzy na rozwiązywanie problemów z klastrem Kubernetes. Można przejrzeć alert wdrażania i sprawdź stan wdrożenia, analizując elementów wymaganych do wdrożenia. Może być konieczne zebranie dzienników wdrażania z usługi Azure Stack lub maszyn wirtualnych systemu Linux obsługujących usługi Kubernetes. Ponadto może być konieczne współpraca z administratorem usługi Azure Stack można pobrać dzienników z punktu końcowego usługi administracyjne.

## <a name="overview-of-deployment"></a>Omówienie wdrażania

Przed zagłębieniem się w kroki rozwiązywania problemów z klastrem, warto zapoznać się z procesem wdrożenia klastra Azure Stack Kubernetes. Wdrożenie używa szablon rozwiązania usługi Azure Resource Manager w celu utworzenia maszyn wirtualnych i instaluje aparat ACS dla klastra.

### <a name="deployment-workflow"></a>Wdrażanie przepływu pracy

Na poniższym diagramie przedstawiono ogólny proces wdrażania klastra.

![Wdrażanie rozwiązania Kubernetes procesu](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>Kroki wdrażania

1. Zbiera dane wejściowe parametrów przy użyciu elementu portalu marketplace.

    Wprowadź wartości, które trzeba skonfigurować w tym klastrze Kubernetes:
    -  **Nazwa użytkownika** nazwę użytkownika dla maszyn wirtualnych systemu Linux, które są częścią klastra Kubernetes i Menedżer DVM.
    -  **Klucz publiczny SSH** klucz używany do autoryzacji do wszystkich utworzonych jako część klastra Kubernetes i Menedżer DVM maszyn z systemem Linux
    -  **Usługa Zasady** identyfikator używany przez dostawcę chmury Kubernetes Azure. Identyfikator klienta została zidentyfikowana jako identyfikator aplikacji, gdy są tworzone jednostki usługi. 
    -  **Klucz tajny klienta** one klucza utworzonego podczas tworzenia jednostki usługi.

2. Tworzy wdrożenie maszyny Wirtualnej i rozszerzenia niestandardowego skryptu.
    -  Tworzy wdrożenie maszyny Wirtualnej systemu Linux przy użyciu portalu marketplace Wyszukaj obraz systemu Linux, **Ubuntu Server 16.04-LTS**.
    -  Pobierz i wykonaj rozszerzenia skryptów klienta z witryny marketplace. Skrypt jest **niestandardowego skryptu dla systemu Linux w wersji 2.0**.
    -  Uruchamia skrypt niestandardowy Menedżer DVM. Skrypt:
        1. Punkt końcowy galerii są pobierane z punktu końcowego metadanych usługi Azure Resource Manager.
        2. Pobiera identyfikator zasobu usługi active directory z punktu końcowego metadanych usługi Azure Resource Manager.
        3. Ładuje modelu interfejsu API w przypadku aparatu usługi ACS.
        4. Wdraża aparatu usługi ACS w klastrze usługi Kubernetes i zapisuje profil chmury Azure Stack, aby `/etc/kubernetes/azurestackcloud.json`.
3. Tworzy głównych maszyn wirtualnych.

    Pobiera i uruchamia rozszerzenie skryptu klienta.

4. Uruchamia skrypt głównej.

    Skrypt:
    - Instaluje etcd, Docker i Kubernetes zasoby, takie jak agenta kubelet. etcd jest magazyn rozproszonych wartości klucza, który umożliwia przechowywanie danych w klastrze maszyn. Docker obsługuje kości bez systemu operacyjnego poziomie wirtualizacji komputerów PC znane jako kontenery. Agenta Kubelet to agent węzeł, który działa w każdym węźle usługi Kubernetes.
    - Konfiguruje usługę etcd.
    - Konfiguruje agenta Kubelet, usługę.
    - Uruchamia agenta kubelet. Obejmuje to następujące czynności:
        1. Uruchamia usługę interfejsu API.
        2. Uruchamia usługę kontrolera.
        3. Uruchamia usługę Scheduler.
5. Tworzy agenta maszyny wirtualne.

    Pobiera i uruchamia rozszerzenie skryptu klienta.

6. Uruchamia skrypt agenta. Skrypt niestandardowy agenta:
    - Zainstaluj etcd.
    - Skonfiguruj usługę agenta Kubelet.
    - Dołączy do klastra Kubernetes.

## <a name="steps-for-troubleshooting"></a>Kroki rozwiązywania problemów z

Możesz zbierać dzienniki na maszynach wirtualnych, obsługa klastra Kubernetes. Możesz również przejrzeć dziennik wdrażania. Może być również konieczne skontaktuj się z administratorem usługi Azure Stack, aby sprawdzić wersję usługi Azure Stack, którego używasz i pobieranie dzienników z usługi Azure Stack, powiązane z danym wdrożeniem.

1. Przegląd [stan wdrożenia](#review-deployment-status) i [Pobierz dzienniki](#get-logs-from-a-vm) z węzła głównego w klastrze Kubernetes.
2. Musisz użyć najnowszej wersji usługi Azure Stack. Jeśli nie masz tej wersji usługi Azure Stack, skontaktuj się z administratorem usługi Azure Stack. Czas marketplace klastra Kubernetes 0.3.0 wymaga usługi Azure Stack w wersji 1808 lub nowszej.
3.  Przejrzyj pliki tworzenia maszyny Wirtualnej. Mógł wystąpić następujące problemy:  
    - Klucz publiczny może być nieprawidłowa. Przejrzyj klucz, który został utworzony.  
    - Tworzenie maszyny Wirtualnej może być wyzwalane wystąpił błąd wewnętrzny lub wyzwalane błąd tworzenia. Błędy mogą być spowodowane przez szereg czynników, takich jak ograniczenia wydajności dla Twojej subskrypcji usługi Azure Stack.
    - W pełni kwalifikowana nazwa domeny (nazwy FDQN) dla maszyny Wirtualnej zaczynają się od prefiksu zduplikowane?
4.  Jeśli maszyna wirtualna jest **OK**, następnie ocenić Menedżer DVM. Jeśli Menedżer DVM zawiera komunikat o błędzie:

    - Klucz publiczny może być nieprawidłowa. Przejrzyj klucz, który został utworzony.  
     - Należy skontaktować się z administratorem usługi Azure Stack, aby pobrać dzienniki dla usługi Azure Stack przy użyciu uprzywilejowanych punktów końcowych. Aby uzyskać więcej informacji, zobacz [narzędzia diagnostyczne usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics).
5. Jeśli masz pytania dotyczące wdrożenia, możesz Opublikuj swoje pytanie lub zobaczyć, jeśli ktoś ma już odpowiedzi na pytania w [forum poświęcone systemowi Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="review-deployment-status"></a>Sprawdź stan wdrożenia

W przypadku wdrażania klastra Kubernetes do przeglądania wszelkich problemów, można sprawdzić stan wdrożenia.

1. Otwórz [portalu Azure Stack](https://portal.local.azurestack.external).
2. Wybierz **grup zasobów**, a następnie wybierz nazwę grupy zasobów używane podczas wdrażania klastra Kubernetes.
3. Wybierz **wdrożeń** i następnie **Nazwa wdrożenia**.

    ![Rozwiązywanie problemów](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  Zapoznaj się z okna rozwiązywania problemów. Każdy wdrożony zasób zawiera następujące informacje.
    
    | Właściwość | Opis |
    | ----     | ----        |
    | Zasób | Nazwa zasobu. |
    | Typ | Dostawca zasobów i typu zasobu. |
    | Stan | Stan elementu. |
    | Sygnatura czasowa | Sygnatura czasowa UTC czasu. |
    | Szczegóły operacji | Szczegóły operacji, takich jak dostawca zasobów zaangażowanych w operację, punkt końcowy zasobów i nazwę zasobu. |

    Każdy element ma ikona stanu zielony lub czerwony.

## <a name="get-logs-from-a-vm"></a>Pobieranie dzienników z maszyny Wirtualnej

Można będzie muszą połączyć się z główną maszyną Wirtualną dla klastra, otwórz wiersz bash i uruchomić skrypt w celu generowania dzienników. Wzorzec znajdują się w grupie zasobów klastra i nosi nazwę `k8s-master-<sequence-of-numbers>`. 

### <a name="prerequisites"></a>Wymagania wstępne

Konieczne będzie bash Monituj na maszynie używania do zarządzania usługi Azure Stack. Użyj powłoki bash, aby uruchamiać skrypty, które uzyskiwanie dostępu do dzienników. Na komputerze Windows można użyć wierszu polecenia powłoki bash, instalowane przy użyciu narzędzia Git. Aby uzyskać najbardziej aktualną wersję narzędzia git, zobacz [pobierania narzędzia git](https://git-scm.com/downloads).

### <a name="get-logs"></a>Pobierz dzienniki

1. Otwórz wiersz bash. Jeśli używasz narzędzia git na komputerze Windows, możesz Otwórz wiersz bash z następującą ścieżką: `c:\programfiles\git\bin\bash.exe`.
2. Uruchom następujące polecenia powłoki bash:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    sudo chmod 744 getkuberneteslogs.sh
    ```

    > [!Note]  
    > W Windows, nie trzeba uruchomić `sudo` i może po prostu użyj `chmod 744 getkuberneteslogs.sh`.

3. W tej samej sesji, uruchom następujące polecenie z parametrami zaktualizowane pod kątem danego środowiska.

    ```Bash  
    ./getkuberneteslogs.sh --identity-file id_rsa --user azureuser --vmdhost 192.168.102.37
    ```

    Przejrzyj parametry i ustaw wartości, w zależności od używanego środowiska.
    | Parametr           | Opis                                                                                                      | Przykład                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -i, — plik tożsamości | Plik klucza prywatnego RSA połączyć kubernetes główną maszyną Wirtualną. Klucz musi rozpoczynać się `-----BEGIN RSA PRIVATE KEY-----` | C:\data\privatekey.PEM                                                        |
    | -h,--hosta          | Publiczny adres ip lub w pełni kwalifikowana nazwa domeny (FQDN) węzła głównego klastra Kubernetes maszyny Wirtualnej. Nazwa maszyny Wirtualnej, który rozpoczyna się od `k8s-master-`.                       | ADRESÓW IP: 192.168.102.37<br><br>Nazwa FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u, — użytkownik          | Nazwa użytkownika maszyny Wirtualnej węzła głównego klastra Kubernetes. Ta nazwa jest ustawiany, podczas konfigurowania elementu portalu marketplace.                                                                    | użytkownik_azure                                                                     |
    | -d, — vmdhost       | Publiczny adres ip lub nazwa FQDN Menedżer DVM. Nazwa maszyny Wirtualnej, który rozpoczyna się od `vmd-`.                                                       | ADRESÓW IP: 192.168.102.38<br><br>Serwer DNS: vmd-dnsk8-frog.local.cloudapp.azurestack.external |

   Po dodaniu wartości parametrów, jego może wyglądać następująco:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\secretsecret.pem" --user azureuser --vmdhost 192.168.102.37
     ```

    Pomyślny przebieg tworzy dzienniki.

    ![Wygenerowanych dzienników](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-generated-logs.png)


4. Pobierz dzienniki w folderach utworzonych przy użyciu polecenia. Polecenie spowoduje utworzenie nowego folderu, a jego sygnatura czasowa.
    - KubernetesLogs*YYYY-MM-DD-XX-XX-XX-XXX*
        - Dvmlogs
        - Acsengine — kubernetes — dvm.log

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie rozwiązania Kubernetes do usługi Azure Stack](azure-stack-solution-template-kubernetes-deploy.md).

[Dodaj rozwiązania Kubernetes w portalu Marketplace (dla operatora infrastruktury Azure Stack)](..\azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes na platformie Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
