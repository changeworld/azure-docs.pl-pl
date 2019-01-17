---
title: Rozwiązywanie problemów z wdrożenia rozwiązania Kubernetes w usłudze Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z wdrożenia rozwiązania Kubernetes w usłudze Azure Stack.
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
ms.date: 01/16/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.openlocfilehash: dc6994cd682997dd00e76b57b4996ebad1f73fbb
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352398"
---
# <a name="troubleshoot-your-deployment-to-kubernetes-to-azure-stack"></a>Rozwiązywanie problemów z wdrożenia rozwiązania Kubernetes w usłudze Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

> [!Note]  
> Rozwiązanie Kubernetes w usłudze Azure Stack jest w wersji zapoznawczej.

Następujący artykuł patrzy na rozwiązywanie problemów z klastrem Kubernetes. Można przejrzeć alert wdrażania i sprawdź stan wdrożenia według elementów wymaganych do wdrożenia. Może być konieczne zebranie dzienników wdrażania z usługi Azure Stack lub maszyn wirtualnych systemu Linux obsługujących usługi Kubernetes. Konieczne może również pracować z administratorem usługi Azure Stack, aby pobrać dzienniki z administracyjne punktu końcowego.

## <a name="overview-of-deployment"></a>Omówienie wdrażania

Przed rozpoczęciem rozwiązywania problemów z klastrem, warto zapoznać się z procesem wdrożenia klastra Azure Stack Kubernetes. Wdrożenie używa szablon rozwiązania usługi Azure Resource Manager do tworzenia maszyn wirtualnych i instaluje aparatu ACS dla klastra.

### <a name="deployment-workflow"></a>Wdrażanie przepływu pracy

Na poniższym diagramie przedstawiono ogólny proces wdrażania klastra.

![Wdrażanie rozwiązania Kubernetes procesu](media/azure-stack-solution-template-kubernetes-trouble/002-Kubernetes-Deploy-Flow.png)

### <a name="deployment-steps"></a>Kroki wdrażania

1. Zbierz parametry wejściowe z elementu portalu marketplace.

    Wprowadź wartości potrzebne do skonfigurowania klastra Kubernetes, w tym:
    -  **Nazwa użytkownika**: Nazwa użytkownika dla maszyn wirtualnych systemu Linux, które są częścią klastra Kubernetes i Menedżer DVM.
    -  **Klucz publiczny SSH**: Klucz, który jest używany do autoryzacji wszystkich maszyn z systemem Linux, które zostały utworzone w ramach klastra Kubernetes i Menedżer DVM.
    -  **Usługa Zasady**: Identyfikator, który jest używany przez dostawcę chmury Kubernetes Azure. Identyfikator klienta zidentyfikowane jako identyfikator aplikacji, podczas tworzenia jednostki usługi. 
    -  **Klucz tajny klienta**: One klucza utworzonego podczas tworzenia jednostki usługi.

2. Tworzenie wdrożenia maszyny Wirtualnej i rozszerzenia niestandardowego skryptu.
    -  Tworzenie wdrożenia maszyny Wirtualnej systemu Linux przy użyciu obrazu systemu Linux w witrynie marketplace **Ubuntu Server 16.04-LTS**.
    -  Pobierz i uruchom rozszerzenia skryptów klienta z witryny marketplace. Skrypt jest **niestandardowego skryptu dla systemu Linux w wersji 2.0**.
    -  Uruchamia skrypt niestandardowy Menedżer DVM. Ten skrypt wykonuje następujące zadania:
        1. Punkt końcowy galerii są pobierane z punktu końcowego metadanych usługi Azure Resource Manager.
        2. Pobiera identyfikator zasobu usługi active directory z punktu końcowego metadanych usługi Azure Resource Manager.
        3. Ładuje modelu interfejsu API w przypadku aparatu usługi ACS.
        4. Wdraża aparatu usługi ACS w klastrze usługi Kubernetes i zapisuje profil chmury Azure Stack, aby `/etc/kubernetes/azurestackcloud.json`.
3. Utwórz głównych maszyn wirtualnych.

4. Pobierz i uruchom rozszerzenia skryptów klienta.

5. Uruchom skrypt głównej.

    Ten skrypt wykonuje następujące zadania:
    - Instaluje etcd, Docker i Kubernetes zasoby, takie jak agenta kubelet. etcd jest magazyn rozproszonych wartości klucza, który umożliwia przechowywanie danych w klastrze maszyn. Docker obsługuje znane jako kontenery wirtualizacji komputerów PC bez kości poziom systemu operacyjnego. Agenta Kubelet to agent węzeł, który działa w każdym węźle usługi Kubernetes.
    - Konfiguruje usługę etcd.
    - Konfiguruje usługę agenta kubelet.
    - Uruchamia agenta kubelet. To zadanie obejmuje następujące czynności:
        1. Uruchamia usługę interfejsu API.
        2. Uruchamia usługę kontrolera.
        3. Uruchamia usługę scheduler.
6. Tworzenie agentów maszyn wirtualnych.

7. Pobierz i Uruchom rozszerzenie skryptu klienta.

7. Uruchom skrypt agenta. Skrypt niestandardowy agenta wykonuje następujące zadania:
    - Instaluje etcd
    - Konfiguruje usługę agenta kubelet
    - Zostanie przyłączony do klastra Kubernetes

## <a name="steps-for-troubleshooting"></a>Kroki rozwiązywania problemów z

Możesz zbierać dzienniki na maszynach wirtualnych, które obsługują klastra Kubernetes. Możesz również przejrzeć dziennik wdrażania. Konieczne może komunikować się z administratorem usługi Azure Stack, aby sprawdzić wersję programu Azure Stack, które są potrzebne do użycia i pobieranie dzienników z usługi Azure Stack, które są powiązane z danym wdrożeniem.

1. Przegląd [stan wdrożenia](#review-deployment-status) i [Pobierz dzienniki](#get-logs-from-a-vm) z węzła głównego w klastrze Kubernetes.
2. Pamiętaj, że używasz najnowszej wersji usługi Azure Stack. Jeśli wiesz, której wersji używasz, skontaktuj się z administratorem usługi Azure Stack. Czas marketplace klastra Kubernetes 0.3.0 wymaga usługi Azure Stack w wersji 1808 lub nowszej.
3.  Przejrzyj pliki tworzenia maszyny Wirtualnej. Mogli mieć następujące problemy:  
    - Klucz publiczny może być nieprawidłowy. Przejrzyj klucza, który został utworzony.  
    - Tworzenie maszyny Wirtualnej może być wyzwalane wystąpił błąd wewnętrzny lub wyzwalane błąd tworzenia. Wiele czynników może powodować błędy, łącznie z ograniczenia wydajności dla Twojej subskrypcji usługi Azure Stack.
    - Upewnij się, że w pełni kwalifikowana nazwa domeny (FQDN) dla maszyny Wirtualnej zaczyna się od zduplikowany prefiks.
4.  Jeśli maszyna wirtualna jest **OK**, następnie ocenę, Menedżer DVM. Jeśli Menedżer DVM zawiera komunikat o błędzie:

    - Klucz publiczny może być nieprawidłowy. Przejrzyj klucza, który został utworzony.  
    - Musisz skontaktować się z administratorem usługi Azure Stack, aby pobrać dzienniki dla usługi Azure Stack przy użyciu uprzywilejowanych punktów końcowych. Aby uzyskać więcej informacji, zobacz [narzędzia diagnostyczne usługi Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-diagnostics).
5. Jeśli masz pytanie dotyczące wdrożenia, możesz nielegalne lub zobaczyć, jeśli ktoś ma już odpowiedzi na pytania w [forum usługi Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). 

## <a name="review-deployment-status"></a>Sprawdź stan wdrożenia

Podczas wdrażania klastra Kubernetes, możesz sprawdzić stan wdrożenia można sprawdzić ewentualne problemy z.

1. Otwórz [portalu Azure Stack](https://portal.local.azurestack.external).
2. Wybierz **grup zasobów**, a następnie wybierz nazwę grupy zasobów, które zostały użyte podczas wdrażania klastra Kubernetes.
3. Wybierz **wdrożeń**, a następnie wybierz pozycję **Nazwa wdrożenia**.

    ![Rozwiązywanie problemów](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-kub-trouble-report.png)

4.  Zapoznaj się z okna rozwiązywania problemów. Każdy wdrożony zasób zawiera następujące informacje:
    
    | Właściwość | Opis |
    | ----     | ----        |
    | Zasób | Nazwa zasobu. |
    | Typ | Dostawca zasobów i typu zasobu. |
    | Stan | Stan elementu. |
    | Sygnatura czasowa | Sygnatura czasowa UTC czasu. |
    | Szczegóły operacji | Szczegóły operacji, takich jak dostawcy zasobów, które brały udział w operacji, punkt końcowy zasobu i nazwę zasobu. |

    Każdy element ma ikona stanu zielony lub czerwony.

## <a name="get-logs-from-a-vm"></a>Pobieranie dzienników z maszyny Wirtualnej

Aby wygenerować dzienniki, możesz muszą połączyć się z główną maszyną Wirtualną dla klastra, otwórz wiersz bash, a następnie uruchom skrypt. Wzorzec maszyny Wirtualnej można znaleźć w grupie zasobów klastra i nosi nazwę `k8s-master-<sequence-of-numbers>`. 

### <a name="prerequisites"></a>Wymagania wstępne

Potrzebujesz bash monitu na komputerze który służy do zarządzania usługi Azure Stack. Użyj powłoki bash, aby uruchamiać skrypty, które uzyskiwanie dostępu do dzienników. Na komputerze Windows można użyć wierszu polecenia powłoki bash, który został zainstalowany przy użyciu narzędzia Git. Aby uzyskać najbardziej aktualną wersję narzędzia git, zobacz [pobierania narzędzia Git](https://git-scm.com/downloads).

### <a name="get-logs"></a>Pobierz dzienniki

Aby uzyskać dzienniki, wykonaj następujące czynności:

1. Otwórz wiersz bash. Jeśli używasz narzędzia Git na komputerze Windows, można otworzyć wiersz bash w następującej ścieżce: `c:\programfiles\git\bin\bash.exe`.
2. Uruchom następujące polecenia powłoki bash:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    sudo chmod 744 getkuberneteslogs.sh
    ```

    > [!Note]  
    > W Windows, nie trzeba uruchomić `sudo`. Zamiast tego można użyć `chmod 744 getkuberneteslogs.sh`.

3. W tej samej sesji, uruchom następujące polecenie z parametrami zaktualizowane pod kątem danego środowiska:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file id_rsa --user azureuser --vmdhost 192.168.102.37
    ```

4. Przejrzyj parametry i ustaw wartości, w zależności od używanego środowiska.
    | Parametr           | Opis                                                                                                      | Przykład                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -i, — plik tożsamości | RSA pliku klucza prywatnego do łączenia z głównej maszynie Wirtualnej platformy Kubernetes. Klucz musi rozpoczynać się `-----BEGIN RSA PRIVATE KEY-----` | C:\data\privatekey.pem                                                        |
    | -h,--hosta          | Publiczny adres IP lub w pełni kwalifikowana nazwa domeny (FQDN) węzła głównego klastra Kubernetes maszyny Wirtualnej. Nazwa maszyny Wirtualnej, który rozpoczyna się od `k8s-master-`.                       | Adres IP: 192.168.102.37<br><br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u, — użytkownik          | Nazwa użytkownika maszyny Wirtualnej węzła głównego klastra Kubernetes. Ta nazwa jest ustawiona, podczas konfigurowania elementu portalu marketplace.                                                                    | użytkownik_azure                                                                     |
    | -d, --vmdhost       | Publiczny adres IP lub nazwa FQDN Menedżer DVM. Nazwa maszyny Wirtualnej, który rozpoczyna się od `vmd-`.                                                       | Adres IP: 192.168.102.38<br><br>DNS: vmd-dnsk8-frog.local.cloudapp.azurestack.external |

   Po dodaniu wartości parametrów, on może wyglądać podobnie do poniższego kodu:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\secretsecret.pem" --user azureuser --vmdhost 192.168.102.37
     ```

    Pomyślny przebieg tworzy dzienniki.

    ![Wygenerowanych dzienników](media/azure-stack-solution-template-kubernetes-trouble/azure-stack-generated-logs.png)


4. Pobierz dzienniki w folderach, które zostały utworzone przy użyciu polecenia. Polecenie spowoduje utworzenie nowych folderów i ich sygnatury czasowe.
    - KubernetesLogs*YYYY-MM-DD-XX-XX-XX-XXX*
        - Dvmlogs
        - Acsengine-kubernetes-dvm.log

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie rozwiązania Kubernetes do usługi Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)

[Dodaj klaster Kubernetes w portalu Marketplace (dla operatora infrastruktury Azure Stack)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes na platformie Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
