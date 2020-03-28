---
title: Skalowanie wdrożeń serwera Jenkins przy użyciu agentów maszyn wirtualnych platformy Azure
description: Dodaj dodatkową pojemność do potoków narzędzia Jenkins przy użyciu maszyn wirtualnych platformy Azure za pomocą wtyczki agenta maszyny wirtualnej platformy Azure usługi Jenkins.
keywords: jenkins, azure, devops, maszyna wirtualna, agenci
ms.topic: tutorial
ms.date: 07/31/2018
ms.openlocfilehash: 2e811d628c017316a5bc50a8ddc22ee24d6f744e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74158531"
---
# <a name="scale-your-jenkins-deployments-to-meet-demand-with-azure-vm-agents"></a>Skalowanie wdrożeń serwera Jenkins w celu spełnienia wymagań przy użyciu agentów maszyn wirtualnych platformy Azure

W tym samouczku pokazano, jak używać [wtyczki agentów maszyn wirtualnych platformy Azure](https://plugins.jenkins.io/azure-vm-agents) narzędzia Jenkins w celu dodania pojemności na żądanie na przykładzie maszyn wirtualnych z systemem Linux działających na platformie Azure.

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Instalowanie wtyczki agentów maszyn wirtualnych platformy Azure
> * Konfigurowanie wtyczki w celu utworzenia zasobów w ramach subskrypcji platformy Azure
> * Ustawianie zasobów obliczeniowych dostępnych dla każdego agenta
> * Ustawianie systemu operacyjnego i narzędzi zainstalowanych na każdym agencie
> * Tworzenie nowego zadania freestyle usługi Jenkins
> * Uruchamianie zadania na agencie maszyny wirtualnej platformy Azure

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents/player]

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure
* Serwer główny Jenkins. Jeśli nie masz serwera głównego, przejrzyj samouczek [Szybki start](install-jenkins-solution-template.md), aby skonfigurować taki serwer na platformie Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-vm-agents-plugin"></a>Instalowanie wtyczki agentów maszyn wirtualnych platformy Azure

> [!TIP]
> Jeśli usługa Jenkins została wdrożona na platformie Azure przy użyciu [szablonu rozwiązania](install-jenkins-solution-template.md), wtyczka agentów maszyn wirtualnych platformy Azure jest już zainstalowana.

1. Na pulpicie nawigacyjnym narzędzia Jenkins wybierz pozycję **Manage Jenkins** (Zarządzaj narzędziem Jenkins), a następnie wybierz pozycję **Manage Plugins** (Zarządzaj wtyczkami).
1. Wybierz kartę **Available** (Dostępne), a następnie wyszukaj **Azure VM Agents** (Agenci maszyn wirtualnych platformy Azure). Zaznacz pole wyboru obok wpisu dla wtyczki, a następnie wybierz pozycję **Install without restart** (Instaluj bez ponownego uruchamiania) w dolnej części pulpitu nawigacyjnego.

## <a name="configure-the-azure-vm-agents-plugin"></a>Konfigurowanie wtyczki agentów maszyn wirtualnych platformy Azure

1. Na pulpicie nawigacyjnym narzędzia Jenkins wybierz pozycję **Manage Jenkins** (Zarządzaj narzędziem Jenkins), a następnie pozycję **Configure System** (Konfiguruj system).
1. Przewiń ekran do dolnej części strony i znajdź sekcję **Cloud** (Chmura) z listą rozwijaną **Add new cloud** (Dodaj nową chmurę) i wybierz z niej polecenie **Microsoft Azure VM Agents** (Agenci maszyn wirtualnych platformy Microsoft Azure).
1. Wybierz istniejącą jednostkę usługi z listy rozwijanej **Add** (Dodaj) w sekcji **Azure Credentials** (Poświadczenia platformy Azure). Jeśli na liście nie ma żadnej pozycji, wykonaj następujące kroki, aby [utworzyć jednostkę usługi](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager) dla konta platformy Azure i dodać ją do konfiguracji usługi Jenkins:   

    a. Wybierz pozycję **Add** (Dodaj) obok sekcji **Azure Credentials** (Poświadczenia platformy Azure) i wybierz pozycję **Jenkins**.   
    b. W oknie dialogowym **Add Credentials** (Dodawanie poświadczeń) wybierz opcję **Microsoft Azure Service Principal** (Jednostka usługi Microsoft Azure) z listy rozwijanej **Kind** (Rodzaj).   
    d. Utwórz jednostkę usługi Active Directory za pomocą wiersza polecenia platformy Azure lub usługi [Cloud Shell](/azure/cloud-shell/overview).
    
    ```azurecli-interactive
    az ad sp create-for-rbac --name jenkins_sp --password secure_password
    ```

    ```json
    {
        "appId": "BBBBBBBB-BBBB-BBBB-BBBB-BBBBBBBBBBB",
        "displayName": "jenkins_sp",
        "name": "http://jenkins_sp",
        "password": "secure_password",
        "tenant": "CCCCCCCC-CCCC-CCCC-CCCCCCCCCCC"
    }
    ```
    d. Wprowadź poświadczenia jednostki usługi w oknie dialogowym **Add credentials** (Dodawanie poświadczeń). Jeśli nie znasz identyfikatora subskrypcji platformy Azure, możesz go uzyskać za pomocą zapytania interfejsu wiersza polecenia:
     
     ```azurecli-interactive
     az account list
     ```

     ```json
        {
            "cloudName": "AzureCloud",
            "id": "AAAAAAAA-AAAA-AAAA-AAAA-AAAAAAAAAAAA",
            "isDefault": true,
            "name": "Visual Studio Enterprise",
            "state": "Enabled",
            "tenantId": "CCCCCCCC-CCCC-CCCC-CCCC-CCCCCCCCCCC",
            "user": {
            "name": "raisa@fabrikam.com",
            "type": "user"
            }
     ```

    Ukończona jednostka usługi powinna używać pola `id` dla **identyfikatora subskrypcji**, wartości `appId` dla **identyfikatora klienta**, wartości `password` dla **klucza tajnego klienta** i wartości `tenant` dla **identyfikatora dzierżawy**. Wybierz pozycję **Add** (Dodaj) w celu dodania jednostki usługi, a następnie skonfiguruj wtyczkę, aby używała nowo utworzonego poświadczenia.

    ![Konfigurowanie jednostki usługi platformy Azure](./media/jenkins-azure-vm-agents/new-service-principal.png)

    

1. W sekcji **Resource Group Name** (Nazwa grupy zasobów) pozostaw zaznaczoną opcję **Create new** (Utwórz nową) i wprowadź nazwę `myJenkinsAgentGroup`.
1. Wybierz pozycję **Verify configuration** (Sprawdź konfigurację), aby nawiązać połączenie z platformą Azure w celu przetestowania ustawień profilu.
1. Wybierz pozycję **Apply** (Zastosuj), aby zaktualizować konfigurację wtyczki.

## <a name="configure-agent-resources"></a>Konfigurowanie zasobów agenta

Skonfiguruj szablon używany do definiowania agenta maszyny wirtualnej platformy Azure. Ten szablon definiuje zasoby obliczeniowe, które ma każdy agent po utworzeniu.

1. Wybierz pozycję **Add** (Dodaj) obok pozycji **Add Azure Virtual Machine Template** (Dodaj szablon maszyny wirtualnej platformy Azure).
1. Wprowadź `defaulttemplate` w polu **Name** (Nazwa).
1. Wprowadź `ubuntu` w polu **Label** (Etykieta).
1. Wybierz żądany [region świadczenia usługi Azure](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) z pola kombi.
1. Wybierz pozycję [VM size](/azure/virtual-machines/linux/sizes) (Rozmiar maszyny wirtualnej) z listy rozwijanej w obszarze **Virtual Machine Size** (Rozmiar maszyny wirtualnej). Rozmiar ogólnego przeznaczenia `Standard_DS1_v2` wystarczy na potrzeby tego samouczka.   
1. Pozostaw dla opcji **Retention time** (Czas przechowywania) wartość `60`. To ustawienie określa liczbę minut, po jakiej usługa Jenkins cofnie przydział dla bezczynnych agentów. Wpisz 0, jeśli nie chcesz, aby bezczynne agenty były automatycznie usuwane.

   ![Ogólna konfiguracja maszyny wirtualnej](./media/jenkins-azure-vm-agents/general-config.png)

## <a name="configure-agent-operating-system-and-tools"></a>Konfigurowanie systemu operacyjnego i narzędzi agenta

W sekcji **Image Configuration** (Konfiguracja obrazu) konfiguracji wtyczki wybierz opcję **Ubuntu 16.04 LTS**. Zaznacz pola wyboru obok pozycji **Install Git (Latest)** (Zainstaluj najnowszą wersję narzędzia Git), **Install Maven (V3.5.0)** (Zainstaluj narzędzie Maven 3.5.0) i **Install Docker** (Zainstaluj platformę Docker), aby zainstalować te narzędzia na nowo utworzonych agentach.

![Konfigurowanie systemu operacyjnego i narzędzi maszyny wirtualnej](./media/jenkins-azure-vm-agents/jenkins-os-config.png)

Wybierz pozycję **Add** (Dodaj) obok opcji **Admin Credentials** (Poświadczenia administratora), a następnie wybierz pozycję **Jenkins**. Wprowadź nazwę użytkownika i hasło używane do logowania się w agentach, upewniając się, że spełniają one [zasady nazw użytkownika i haseł](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm) dla kont z uprawnieniami administracyjnymi na maszynach wirtualnych platformy Azure.

Wybierz pozycję **Verify Template** (Sprawdź szablon), aby sprawdzić konfigurację, a następnie wybierz pozycję **Save** (Zapisz), aby zapisać zmiany i powrócić do pulpitu nawigacyjnego usługi Jenkins.

## <a name="create-a-job-in-jenkins"></a>Tworzenie zadania w usłudze Jenkins

1. Na pulpicie nawigacyjnym usługi Jenkins kliknij **New Item** (Nowy element). 
1. Wprowadź ciąg `demoproject1` jako nazwę i wybierz pozycję **Freestyle project** (Projekt Freestyle), a następnie wybierz przycisk **OK**.
1. Na karcie **General** (Ogólne) wybierz opcję **Restrict where project can be run** (Ogranicz miejsca uruchomienia projektu) i wpisz `ubuntu` w polu **Label Expression** (Wyrażenie etykiety). Zostanie wyświetlony komunikat potwierdzający, że etykieta jest obsługiwana przez konfigurację chmury utworzoną w poprzednim kroku. 
   ![Konfigurowanie zadania](./media/jenkins-azure-vm-agents/job-config.png)
1. Na karcie **Source Code Management** (Zarządzanie kodem źródłowym) wybierz pozycję **Git** i dodaj następujący adres URL w polu **Repository URL** (Adres URL repozytorium): `https://github.com/spring-projects/spring-petclinic.git`
1. Na karcie **Build** (Kompilacja) wybierz pozycję **Add build step** (Dodaj krok kompilacji), a następnie pozycję **Invoke top-level Maven targets** (Wywołaj elementy docelowe najwyższego poziomu narzędzia Maven). Wprowadź `package` w polu **Goals** (Cele).
1. Wybierz pozycję **Save** (Zapisz), aby zapisać definicję zadania.

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>Tworzenie nowego zadania na agencie maszyny wirtualnej platformy Azure

1. Wróć do pulpitu nawigacyjnego usługi Jenkins.
1. Wybierz zadanie utworzone w poprzednim kroku, a następnie kliknij pozycję **Build now** (Kompiluj teraz). Nowa kompilacja jest umieszczana w kolejce, ale nie jest uruchamiana do czasu utworzenia agenta maszyny wirtualnej w ramach subskrypcji platformy Azure.
1. Po ukończeniu kompilacji przejdź do obszaru **Console output** (Dane wyjściowe konsoli). Będzie widać, że kompilacja została wykonana zdalnie na agencie platformy Azure.

![Dane wyjściowe konsoli](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="troubleshooting-the-jenkins-plugin"></a>Rozwiązywanie problemów z wtyczką narzędzia Jenkins

Jeśli wystąpią jakiekolwiek błędy z wtyczkami jenkins, zgładź problem w [JIRA usługi Jenkins](https://issues.jenkins-ci.org/) dla określonego składnika.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Ciągła integracja/ciągłe wdrażanie w usłudze Azure App Service](java-deploy-webapp-tutorial.md)