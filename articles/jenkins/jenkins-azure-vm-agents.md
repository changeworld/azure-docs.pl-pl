---
title: Skalowanie wdrożeń Wpięć z agentami maszyny Wirtualnej platformy Azure.
description: Dodaj dodatkowej pojemności do Twojej potoki Wpięć przy użyciu maszyn wirtualnych platformy Azure przy użyciu agenta maszyny Wirtualnej Azure Wpięć wtyczki.
services: multiple
documentationcenter: ''
author: rloutlaw
manager: justhe
ms.service: multiple
ms.workload: multiple
ms.topic: article
ms.date: 8/25/2017
ms.author: mlearned
ms.custom: Jenkins
ms.openlocfilehash: 4d45ed14be499ed927f1433e134a029066146eea
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/21/2018
ms.locfileid: "29392644"
---
# <a name="scale-your-jenkins-deployments-to-meet-demand-with-azure-vm-agents"></a>Skalowanie wdrożeń Wpięć aby spełnić wymagania z agentami maszyny Wirtualnej Azure

Ten samouczek przedstawia sposób użycia Wpięć [wtyczkę Azure VM agentów](https://plugins.jenkins.io/azure-vm-agents) można dodać pojemności na żądanie z maszyn wirtualnych systemu Linux działających na platformie Azure.

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Instalowanie wtyczki agentów maszyny Wirtualnej Azure
> * Skonfiguruj dodatek plug-in, aby utworzyć zasobów w Twojej subskrypcji platformy Azure
> * Ustaw zasobów obliczeniowych dostępnych do każdego agenta
> * Ustaw systemu operacyjnego i zainstalowanym każdego agenta
> * Utwórz nowe zadanie stylu Wpięć
> * Uruchom zadanie Agenta maszyny Wirtualnej Azure

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-Integration-with-Jenkins-Using-Azure-VM-Agents/player]

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure
* Wpięć serwera głównego. Jeśli nie masz, wyświetlać [szybkiego startu](install-jenkins-solution-template.md) do jednego na platformie Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="install-azure-vm-agents-plugin"></a>Instalowanie wtyczki agentów maszyn wirtualnych platformy Azure

> [!TIP]
> Jeśli wdrożono Wpięć w Azure przy użyciu [szablon rozwiązania](install-jenkins-solution-template.md), wtyczki Agent maszyny Wirtualnej jest już zainstalowana.

1. Na pulpicie nawigacyjnym Wpięć wybierz **Zarządzanie Wpięć**, a następnie wybierz pozycję **Zarządzanie wtyczkami**.
2. Wybierz **dostępne** , a następnie wyszukaj **agentów maszyny Wirtualnej Azure**. Zaznacz pole wyboru obok wpisu dla wtyczki i wybierz **zainstalować bez ponownego uruchomienia** w dolnej części pulpitu nawigacyjnego.

## <a name="configure-the-azure-vm-agents-plugin"></a>Skonfiguruj wtyczkę Azure VM agentów

1. Na pulpicie nawigacyjnym Wpięć wybierz **Zarządzanie Wpięć**, następnie **skonfigurować System**.
2. Przewiń w dół strony i Znajdź **chmury** sekcji z **Dodaj nowe chmury** listy rozwijanej i wybierz polecenie **Microsoft Azure VM Agents**.
3. Wybierz istniejącą nazwę główną usługi z **Dodaj** listy rozwijanej w **poświadczenia Azure** sekcji. Jeśli żaden nie jest wyświetlane, wykonaj następujące kroki, aby [Tworzenie nazwy głównej usługi](/cli/azure/create-an-azure-service-principal-azure-cli?toc=%2fazure%2fazure-resource-manager) kont platformy Azure i dodaj go do konfiguracji Wpięć:   

    a. Wybierz **Dodaj** obok **poświadczenia Azure** i wybierz polecenie **Wpięć**.   
    b. W **dodać poświadczenia** okno dialogowe, wybierz opcję **główną usługi Microsoft Azure** z **rodzaj** listy rozwijanej.   
    c. Utworzyć podmiot zabezpieczeń usługi Active Directory z wiersza polecenia platformy Azure lub [powłoki chmury](/azure/cloud-shell/overview).
    
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
    d. Wprowadź poświadczenia z usługą główną w **Dodawanie poświadczeń** okna dialogowego. Jeśli nie znasz identyfikator subskrypcji platformy Azure, można wykonać zapytanie, z poziomu interfejsu wiersza polecenia:
     
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

    Należy użyć nazwy głównej usługi zakończone `id` dla pole **identyfikator subskrypcji**, `appId` wartość **identyfikator klienta**, `password` dla **klucz tajny klienta**, i `tenant` dla **Identyfikatorem dzierżawy**. Wybierz **Dodaj** dodać nazwy głównej usługi, a następnie skonfiguruj dodatek plug-in, aby użyć nowo utworzonego poświadczenia.

    ![Konfigurowanie nazwy głównej usługi Azure](./media/jenkins-azure-vm-agents/new-service-principal.png)

    

4. W **Nazwa grupy zasobów** sekcji, pozostaw **Utwórz nowy** zaznaczone, a następnie wprowadź `myJenkinsAgentGroup`.
5. Wybierz **konfiguracji Sprawdź** do nawiązania połączenia platformy Azure, aby przetestować ustawienia profilu.
6. Wybierz **Zastosuj** można zaktualizować konfiguracji wtyczki.

## <a name="configure-agent-resources"></a>Konfigurowanie zasobów agenta

Konfigurowanie szablonu w celu używania do definiowania agenta maszyny Wirtualnej platformy Azure. Ten szablon określa zasoby obliczeniowe każdego agenta ma podczas obliczania utworzony.

1. Wybierz **Dodaj** obok **dodać szablon maszyny wirtualnej Azure**.
2. Wprowadź `defaulttemplate` dla **nazwy**
3. Wprowadź `ubuntu` dla **etykiety**
4. Wybierz żądaną [regionu Azure](https://azure.microsoft.com/regions/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) w polu kombi.
5. Wybierz [rozmiar maszyny Wirtualnej](/azure/virtual-machines/linux/sizes) z listy rozwijanej w obszarze **rozmiar maszyny wirtualnej**. Ogólnego przeznaczenia `Standard_DS1_v2` rozmiar jest odpowiednia dla tego samouczka.   
6. Pozostaw **czas przechowywania** na `60`. To ustawienie określa liczbę minut oczekiwania Wpięć można przed jej alokację bezczynności agentów. Określ 0, jeśli nie chcesz bezczynności agentów zostało usunięte automatycznie.

   ![Konfiguracja maszyny Wirtualnej ogólne](./media/jenkins-azure-vm-agents/general-config.png)

## <a name="configure-agent-operating-system-and-tools"></a>Konfigurowanie agenta systemu operacyjnego i narzędzia

W **konfiguracji obrazu** sekcji konfiguracji wtyczki, wybierz opcję **Ubuntu 16.04 LTS**. Zaznacz pola wyboru obok pozycji **zainstalować Git (Najnowsza wersja)**, **zainstalować Maven (V3.5.0)**, i **zainstalować Docker** do zainstalowania tych narzędzi na nowo utworzony agentów.

![Skonfiguruj system operacyjny maszyny Wirtualnej i narzędzia](./media/jenkins-azure-vm-agents/jenkins-os-config.png)

Wybierz **Dodaj** obok **poświadczeń administratora**, a następnie wybierz pozycję **Wpięć**. Wprowadź nazwę użytkownika i hasło używane do logowania do agentów, upewniając się, że spełniają one [zasad użytkownika i hasło](/azure/virtual-machines/linux/faq#what-are-the-username-requirements-when-creating-a-vm) dla konta z uprawnieniami administracyjnymi na maszynach wirtualnych Azure.

Wybierz **Sprawdź szablon** Sprawdź konfigurację, a następnie wybierz **zapisać** Aby zapisać zmiany i wróć do pulpitu nawigacyjnego Wpięć.

## <a name="create-a-job-in-jenkins"></a>Tworzenie zadania w usłudze Jenkins

1. Na pulpicie nawigacyjnym usługi Jenkins kliknij **New Item** (Nowy element). 
2. Wprowadź `demoproject1` dla nazwy i wybierz **stylu projektu**, a następnie wybierz pozycję **OK**.
3. W **ogólne** , wybierz pozycję **Ogranicz, w którym można uruchomić projektu** i typ `ubuntu` w **wyrażenie etykiety**. Zostanie wyświetlony komunikat z potwierdzeniem, że etykiety jest obsługiwana przez konfigurację chmury utworzony w poprzednim kroku. 
   ![Konfigurowanie zadania](./media/jenkins-azure-vm-agents/job-config.png)
4. W **zarządzania kodem źródłowym** wybierz opcję **Git** i Dodaj następujący adres URL do **adres URL repozytorium** pola: `https://github.com/spring-projects/spring-petclinic.git`
5. W **kompilacji** wybierz opcję **kroku kompilacji Dodaj**, następnie **wywołania najwyższego poziomu celów Maven**. Wprowadź `package` w **cele** pola.
6. Wybierz **zapisać** można zapisać definicji zadania.

## <a name="build-the-new-job-on-an-azure-vm-agent"></a>Tworzenie nowego zadania agenta maszyny Wirtualnej Azure

1. Wróć do pulpitu nawigacyjnego usługi Jenkins.
2. Wybierz zadanie utworzone w poprzednim kroku, a następnie kliknij przycisk **kompilacji teraz**. Nową kompilację znajduje się w kolejce, ale nie uruchamia się dopiero po utworzeniu agenta maszyny Wirtualnej w ramach subskrypcji platformy Azure.
3. Po ukończeniu kompilacji przejdź do obszaru **Console output** (Dane wyjściowe konsoli). Możesz sprawdzić, czy Kompilacja została wykonana zdalnie agenta platformy Azure.

![Dane wyjściowe konsoli](./media/jenkins-azure-vm-agents/console-output.png)

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [CI/CD w usłudze Azure App Service](java-deploy-webapp-tutorial.md)
