---
title: Rozwiązywanie problemów z wdrożenia rozwiązania Kubernetes do usługi Azure Stack | Dokumentacja firmy Microsoft
description: Dowiedz się, jak rozwiązywać problemy z wdrożenia rozwiązania Kubernetes do usługi Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.author: mabrigg
ms.date: 03/20/2019
ms.reviewer: waltero
ms.lastreviewed: 03/20/2019
ms.openlocfilehash: 9af4b7a622bfb47d44c3da0edcece8c9528b08c4
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361544"
---
# <a name="troubleshoot-your-kubernetes-deployment-to-azure-stack"></a>Rozwiązywanie problemów z wdrożenia rozwiązania Kubernetes do usługi Azure Stack

*Dotyczy: Zintegrowane usługi Azure Stack, systemy i usługi Azure Stack Development Kit*

> [!Note]  
> Rozwiązanie Kubernetes w usłudze Azure Stack jest w wersji zapoznawczej. Odłączony platformę Azure Stack nie jest obecnie obsługiwane przez wersję zapoznawczą.

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
    -  **Nazwa główna usługi**: Identyfikator, który jest używany przez dostawcę chmury Kubernetes Azure. Identyfikator klienta zidentyfikowane jako identyfikator aplikacji, podczas tworzenia jednostki usługi. 
    -  **Klucz tajny klienta**: One klucza utworzonego podczas tworzenia jednostki usługi.

2. Tworzenie wdrożenia maszyny Wirtualnej i rozszerzenia niestandardowego skryptu.
    -  Tworzenie wdrożenia maszyny Wirtualnej systemu Linux przy użyciu obrazu systemu Linux w witrynie marketplace **Ubuntu Server 16.04-LTS**.
    -  Pobierz i Uruchom rozszerzenie niestandardowego skryptu z witryny marketplace. Skrypt jest **niestandardowego skryptu dla systemu Linux w wersji 2.0**.
    -  Uruchamia skrypt niestandardowy Menedżer DVM. Ten skrypt wykonuje następujące zadania:
        1. Punkt końcowy galerii są pobierane z punktu końcowego metadanych usługi Azure Resource Manager.
        2. Pobiera identyfikator zasobu usługi active directory z punktu końcowego metadanych usługi Azure Resource Manager.
        3. Ładuje modelu interfejsu API w przypadku aparatu usługi ACS.
        4. Wdraża aparatu usługi ACS w klastrze usługi Kubernetes i zapisuje profil chmury Azure Stack, aby `/etc/kubernetes/azurestackcloud.json`.
3. Utwórz głównych maszyn wirtualnych.

4. Pobierz i uruchom rozszerzenia skryptu niestandardowego.

5. Uruchom skrypt głównej.

    Ten skrypt wykonuje następujące zadania:
    - Instaluje etcd, Docker i Kubernetes zasoby, takie jak agenta kubelet. etcd jest magazyn rozproszonych wartości klucza, który umożliwia przechowywanie danych w klastrze maszyn. Docker obsługuje znane jako kontenery wirtualizacji komputerów PC bez kości poziom systemu operacyjnego. Agenta Kubelet to agent węzeł, który działa w każdym węźle usługi Kubernetes.
    - Konfiguruje **etcd** usługi.
    - Konfiguruje **agenta kubelet** usługi.
    - Uruchamia agenta kubelet. To zadanie obejmuje następujące czynności:
        1. Uruchamia usługę interfejsu API.
        2. Uruchamia usługę kontrolera.
        3. Uruchamia usługę scheduler.
6. Tworzenie agentów maszyn wirtualnych.

7. Pobierz i Uruchom rozszerzenie skryptu niestandardowego.

7. Uruchom skrypt agenta. Skrypt niestandardowy agenta wykonuje następujące zadania:
    - Instaluje **etcd**.
    - Konfiguruje **agenta kubelet** usługi.
    - Dołączy do klastra Kubernetes.

## <a name="steps-for-troubleshooting"></a>Kroki rozwiązywania problemów z

Możesz zbierać dzienniki na maszynach wirtualnych, które obsługują klastra Kubernetes. Możesz również przejrzeć dziennik wdrażania. Konieczne może komunikować się z administratorem usługi Azure Stack, aby sprawdzić wersję programu Azure Stack, które są potrzebne do użycia i pobieranie dzienników z usługi Azure Stack, które są powiązane z danym wdrożeniem.

1. Przegląd [stan wdrożenia](#review-deployment-status) i [Pobierz dzienniki](#get-logs-from-a-vm) z węzła głównego w klastrze Kubernetes.
2. Pamiętaj, że używasz najnowszej wersji usługi Azure Stack. Jeśli wiesz, której wersji używasz, skontaktuj się z administratorem usługi Azure Stack.
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
    | Type | Dostawca zasobów i typu zasobu. |
    | Stan | Stan elementu. |
    | Sygnatura czasowa | Sygnatura czasowa UTC czasu. |
    | Szczegóły operacji | Szczegóły operacji, takich jak dostawcy zasobów, które brały udział w operacji, punkt końcowy zasobu i nazwę zasobu. |

    Każdy element ma ikona stanu zielony lub czerwony.

## <a name="review-deployment-logs"></a>Przejrzyj dzienniki wdrażania

Jeśli portalu usługi Azure Stack nie zawiera informacji wystarczających do rozwiązania lub wyeliminowanie niepowodzenie wdrożenia, następnym krokiem jest, aby wyświetlić szczegółowe dzienniki klastra. Aby ręcznie pobrać dzienników wdrażania, zazwyczaj należy połączyć się z jednej z maszyn wirtualnych z głównego klastra. Pobierz i uruchom następujące polecenie powinno być prostszej metody alternatywnej [skryptu powłoki systemowej](https://aka.ms/AzsK8sLogCollectorScript) dostarczane przez zespół usługi Azure Stack. Ten skrypt łączy się z Menedżer DVM i maszyn wirtualnych klastra, umożliwia zbieranie informacji o odpowiednich system i dzienniki klastra i pobiera je do swojej stacji roboczej.

### <a name="prerequisites"></a>Wymagania wstępne

Konieczne będzie wiersz Bash na komputerze, który służy do zarządzania usługi Azure Stack. Na komputerze Windows, możesz uzyskać Bash prompt, instalując [Git dla Windows](https://git-scm.com/downloads). Po zakończeniu instalacji, poszukaj _powłoki Git Bash_ w start menu.

### <a name="retrieving-the-logs"></a>Trwa pobieranie dzienników

Wykonaj następujące kroki, aby zbierać i pobrać dzienniki klastra:

1. Otwórz wiersz Bash. Na komputerze Windows otwórz _powłoki Git Bash_ lub uruchomić: `C:\Program Files\Git\git-bash.exe`.

2. Pobierz skrypt zbierających dane dziennika, uruchamiając następujące polecenia w wierszu polecenia z powłoki Bash:

    ```Bash  
    mkdir -p $HOME/kuberneteslogs
    cd $HOME/kuberneteslogs
    curl -O https://raw.githubusercontent.com/msazurestackworkloads/azurestack-gallery/master/diagnosis/getkuberneteslogs.sh
    chmod 744 getkuberneteslogs.sh
    ```

3. Wyszukaj informacje wymagane przez skrypt i uruchom go:

    | Parametr           | Opis                                                                                                      | Przykład                                                                       |
    |---------------------|------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------|
    | -d, --vmd-host      | Publiczny adres IP lub w pełni kwalifikowana nazwa domeny (FQDN) programu Menedżer DVM. Nazwa maszyny wirtualnej, który rozpoczyna się od `vmd-`. | Adres IP: 192.168.102.38<br>DNS: vmd-myk8s.local.cloudapp.azurestack.external |
    | -h, — pomoc  | Drukowanie użycie polecenia. | |
    | -i, — plik tożsamości | Plik klucza prywatnego RSA przekazany do elementu portalu marketplace, podczas tworzenia klastra Kubernetes. Wymagane do zdalnego w węzłach usługi Kubernetes. | C:\data\id_rsa.PEM (Putty)<br>~/.SSH/id_rsa (SSH)
    | -m,--host główny   | Publiczny adres IP lub w pełni kwalifikowana nazwa domeny (FQDN) węzła głównego usługi Kubernetes. Nazwa maszyny wirtualnej, który rozpoczyna się od `k8s-master-`. | Adres IP: 192.168.102.37<br>FQDN: k8s-12345.local.cloudapp.azurestack.external      |
    | -u, — użytkownik          | Nazwa użytkownika jest przekazywany do elementu portalu marketplace podczas tworzenia klastra Kubernetes. Wymagane do zdalnego w węzłach Kubernetes | azureuser (wartość domyślna) |


   Podczas dodawania wartości parametrów polecenia może wyglądać mniej więcej tak:

    ```Bash  
    ./getkuberneteslogs.sh --identity-file "C:\id_rsa.pem" --user azureuser --vmd-host 192.168.102.37
     ```

4. Po kilku minutach skryptu zostanie wygenerowana dzienniki zebrane do katalogu o nazwie `KubernetesLogs_{{time-stamp}}`. Można znaleźć katalogu dla każdej maszyny wirtualnej, który należy do klastra.

    Skrypt modułu zbierającego dziennika również sprawdzić występowanie błędów w plikach dziennika i obejmować kroki rozwiązywania problemów, jeśli się stanie, aby znaleźć to znany problem. Upewnij się, że używasz najnowszej wersji skryptu, aby zwiększyć szanse znalezienia znanych problemów.

> [!Note]  
> Zapoznaj się z tym GitHub [repozytorium](https://github.com/msazurestackworkloads/azurestack-gallery/tree/master/diagnosis) Aby uzyskać więcej informacji na temat skryptu dziennika modułu zbierającego.

## <a name="next-steps"></a>Kolejne kroki

[Wdrażanie rozwiązania Kubernetes do usługi Azure Stack](azure-stack-solution-template-kubernetes-deploy.md)

[Dodaj klaster Kubernetes w portalu Marketplace (dla operatora infrastruktury Azure Stack)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Kubernetes na platformie Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
