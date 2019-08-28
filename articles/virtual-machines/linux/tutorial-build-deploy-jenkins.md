---
title: Samouczek — ciągła integracja/ciągłe wdrażanie (CI/CD) z usługi Jenkins na maszynach wirtualnych platformy Azure za pomocą usług Azure DevOps Services | Microsoft Docs
description: Z tego samouczka dowiesz się jak skonfigurować ciągłą integrację (CI) i ciągłe wdrażanie (CD) aplikacji Node.js przy użyciu usługi Jenkins dla maszyn wirtualnych platformy Azure z narzędzia zarządzania wydaniami usługi Visual Studio Team Services lub serwera Microsoft Team Foundation Server
author: tomarchermsft
manager: jpconnock
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: jenkins
ms.workload: infrastructure
ms.date: 07/31/2018
ms.author: tarcher
ms.custom: jenkins
ms.openlocfilehash: a62a302748cb7e60b6a857c686d1833ad499cc7a
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70081652"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-with-using-jenkins-and-azure-devops-services"></a>Samouczek: wdrażanie aplikacji na maszynach wirtualnych systemu Linux na platformie Azure przy użyciu usług Jenkins i Azure DevOps Services

Ciągła integracja (CI) i ciągłe wdrażanie (CD) tworzą potok, za pomocą którego możesz kompilować, wydawać i wdrażać swój kod. Usługi Azure DevOps Services udostępniają kompletny, rozbudowany zestaw narzędzi automatyzacji CI/CD do wdrażania na platformie Azure. Usługa Jenkins to popularne serwerowe narzędzie CI/CD innej firmy, które udostępnia również automatyzację CI/CD. Usług Azure DevOps Services i Jenkins można używać razem, aby dostosować sposób dostarczania aplikacji lub usługi w chmurze.

W tym samouczku użyjesz usługi Jenkins do skompilowania aplikacji internetowej Node.js. Następnie użyjesz usług Azure DevOps do jej wdrożenia

do [grupy wdrożenia](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups/index?view=vsts) zawierającej maszyny wirtualne (VM) systemu Linux. Omawiane kwestie:

> [!div class="checklist"]
> * Pobieranie przykładowej aplikacji.
> * Konfigurowanie wtyczek usługi Jenkins.
> * Konfigurowanie projektu Jenkins Freestyle dla środowiska Node.js.
> * Konfigurowanie usługi Jenkins do integracji z usługami Azure DevOps Services.
> * Tworzenie punktu końcowego usługi Jenkins.
> * Tworzenie grupy wdrożenia dla maszyn wirtualnych platformy Azure.
> * Utwórz potok wydania Azure Pipelines.
> * Przeprowadzanie wdrożeń wyzwalanych ręcznie i za pomocą CI.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Wymagany jest dostęp do serwera usługi Jenkins. Jeśli jeszcze nie utworzono serwera usługi Jenkins, zobacz [Create a Jenkins master on an Azure virtual machine (Tworzenie wzorca usługi Jenkins na maszynie wirtualnej platformy Azure)](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* Zaloguj się do swojej organizacji Azure DevOps Services (**https://{twojaorganizacja}.visualstudio.com**). 
  Możesz uzyskać [bezpłatną organizację Azure DevOps Services](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Aby uzyskać więcej informacji, zobacz [Nawiązywanie połączenia z usługami Azure DevOps Services](https://docs.microsoft.com/azure/devops/organizations/projects/connect-to-projects?view=vsts).

*  Maszyna wirtualna systemu Linux jest potrzebna jako cel wdrożenia.  Aby uzyskać więcej informacji, zobacz [Create and manage Linux VMs with the Azure CLI (Tworzenie maszyn wirtualnych systemu Linux i zarządzanie nimi za pomocą interfejsu wiersza polecenia platformy Azure)](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm).

*  Otwórz port wejściowy 80 dla maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Create network security groups using the Azure portal (Tworzenie sieciowych grup zabezpieczeń przy użyciu witryny Azure Portal)](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic).

## <a name="get-the-sample-app"></a>Pobieranie przykładowej aplikacji

Do wdrożenia potrzebujesz aplikacji przechowywanej w repozytorium usługi Git.
W tym samouczku zalecamy używanie [tej przykładowej aplikacji dostępnej w serwisie GitHub](https://github.com/azooinmyluggage/fabrikam-node). Ten samouczek zawiera przykładowy skrypt używany do instalowania platformy Node.js i aplikacji. Jeśli chcesz pracować z własnym repozytorium, musisz skonfigurować podobny przykład.

Utwórz rozwidlenie tej aplikacji i zanotuj lokalizację (adres URL) do użycia w dalszych krokach tego samouczka. Aby uzyskać więcej informacji, zobacz [Fork a repo (Tworzenie rozwidlenia repozytorium)](https://help.github.com/articles/fork-a-repo/).    

> [!NOTE]
> Aplikacja została skompilowana przy użyciu [narzędzia Yeoman](https://yeoman.io/learning/index.html). Używa ona narzędzi Express, Bower i Grunt. I ma niektóre pakiety npm jako zależności.
> Przykład zawiera także skrypt, który konfiguruje serwer Nginx, a następnie wdraża aplikację. Jest on wykonywany na maszynach wirtualnych. W szczególności skrypt:
> 1. Instaluje rozwiązania Node, Nginx i PM2.
> 2. Konfiguruje rozwiązania Nginx i PM2.
> 3. Uruchamia aplikację Node.

## <a name="configure-jenkins-plug-ins"></a>Konfigurowanie wtyczek usługi Jenkins

Najpierw należy skonfigurować dwie wtyczki usługi Jenkins: **NodeJS** i **VS Team Services Continuous Deployment**.

1. Otwórz konto usługi Jenkins i wybierz pozycję **Manage Jenkins** (Zarządzaj usługą Jenkins).
2. Na stronie **Manage Jenkins** (Zarządzanie usługą Jenkins) wybierz pozycję **Manage Plugins** (Zarządzanie wtyczkami).
3. Odfiltruj listę, aby zlokalizować wtyczkę **NodeJS**, a następnie wybierz opcję **Install without restart** (Instaluj bez ponownego uruchamiania).
    ![Dodawanie wtyczki NodeJS do usługi Jenkins](media/tutorial-build-deploy-jenkins/jenkins-nodejs-plugin.png)
4. Odfiltruj listę, aby odnaleźć wtyczkę **VS Team Services Continuous Deployment**, a następnie wybierz opcję **Install without restart** (Instaluj bez ponownego uruchamiania).
5. Wróć do pulpitu nawigacyjnego usługi Jenkins i wybierz pozycję **Manage Jenkins** (Zarządzaj usługą Jenkins).
6. Wybierz pozycję **Global Tool Configuration** (Globalna konfiguracja narzędzi). Znajdź wtyczkę **NodeJS** i wybierz pozycję **NodeJS installations** (Instalacje wtyczki NodeJS).
7. Wybierz opcję **Install automatically** (Instaluj automatycznie), a następnie wprowadź wartość **Name** (Nazwa).
8. Wybierz pozycję **Zapisz**.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Konfigurowanie projektu Jenkins Freestyle dla środowiska Node.js

1. Wybierz pozycję **New Item** (Nowy element). Wprowadź nazwę elementu.
2. Wybierz pozycję **Freestyle project** (Projekt Freestyle). Kliknij przycisk **OK**.
3. Na karcie **Source Code Management** (Zarządzanie kodem źródłowym) wybierz pozycję **Git**, a następnie wprowadź szczegóły repozytorium i gałęzi zawierającej kod aplikacji.    
    ![Dodawanie repozytorium do kompilacji](media/tutorial-build-deploy-jenkins/jenkins-git.png)
4. Na karcie **Build Triggers** (Wyzwalacze kompilacji) wybierz pozycję **Poll SCM** (Sondowanie SCM), a następnie wprowadź harmonogram `H/03 * * * *`, aby przeprowadzać sondowanie repozytorium Git pod kątem zmian co trzy minuty. 
5. Na karcie **Build Environment** (Środowisko kompilacji) wybierz pozycję **Provide Node &amp; npm bin/ folder PATH** (Podaj ścieżke folderu bin/ środowiska Node i menedżera npm), a następnie wybierz wartość **NodeJS Installation** (Instalacja wtyczki NodeJS). Pozostaw pozycję **npmrc file** (plik npmrc) ustawioną na **use system default** (użyj wartości domyślnej systemu).
6. Na karcie **Build** (Kompilacja) wybierz pozycję **Execute shell** (Wykonaj powłokę), a następnie wprowadź polecenie `npm install`, aby upewnić się, że wszystkie zależności będą aktualizowane.


## <a name="configure-jenkins-for-azure-devops-services-integration"></a>Konfigurowanie usługi Jenkins do integracji z usługami Azure DevOps Services

> [!NOTE]
> Upewnij się, że osobisty token dostępu używany w następnych krokach zawiera uprawnienie *Wydanie* (odczyt, zapis, wykonanie i zarządzanie) w usługach Azure DevOps Services.
 
1.  Utwórz osobisty token dostępu w swojej organizacji Azure DevOps Services, jeśli jeszcze go nie masz. Usługa Jenkins wymaga tych informacji, aby uzyskać dostęp do organizacji Azure DevOps Services. Pamiętaj, aby przechować informacje o tokenie na potrzeby kolejnych kroków w tej sekcji.
  
    Aby dowiedzieć się, jak wygenerować token, przeczytaj temat [Jak mogę utworzyć osobisty token dostępu dla usług Azure DevOps Services?](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=vsts).
2. Na karcie **Post-build Actions** (Akcje po kompilacji) wybierz pozycję **Add post-build action** (Dodaj akcję po kompilacji). Wybierz pozycję **Archive the artifacts** (Archiwizuj artefakty).
3. Dla pozycji **Files to archive** (Pliki do archiwizacji) wprowadź `**/*`, aby uwzględnić wszystkie pliki.
4. Aby utworzyć inną akcję, wybierz pozycję **Add post-build action** (Dodaj akcję po kompilacji).
5. Wybierz pozycję **Trigger release in TFS/Team Services** (Wyzwalanie wydania w TFS/Team Services). Wprowadź identyfikator URI swojej organizacji Azure DevOps Services, taki jak **https://{nazwa-organizacji}.visualstudio.com**.
6. Wprowadź nazwę **Projektu**.
7. Wybierz nazwę potoku wydania. (Ten potok wydania utworzysz później w usługach Azure DevOps Services).
8. Wybierz poświadczenia niezbędne do połączenia ze środowiskiem usług Azure DevOps Services lub serwera Team Foundation Server:
   - Pozostaw puste pole **Nazwa użytkownika**, jeśli używasz usług Azure DevOps Services. 
   - Wprowadź nazwę użytkownika i hasło, jeśli korzystasz z lokalnej wersji serwera Team Foundation Server.    
   ![Konfigurowanie akcji postkompilacyjnych usługi Jenkins](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. Zapisz projekt usługi Jenkins.


## <a name="create-a-jenkins-service-endpoint"></a>Tworzenie punktu końcowego usługi Jenkins

Punkt końcowy usługi umożliwia usługom Azure DevOps Services nawiązanie połączenia z usługą Jenkins.

1. Otwórz stronę **Usługi** w usługach Azure DevOps Services, otwórz listę **Nowy punkt końcowy usługi**, a następnie wybierz pozycję **Jenkins**.
   ![Dodawanie punktu końcowego usługi Jenkins](media/tutorial-build-deploy-jenkins/add-jenkins-endpoint.png)
2. Wprowadź nazwę połączenia.
3. Wprowadź adres URL serwera Jenkins, a następnie wybierz opcję **Zaakceptuj niezaufane certyfikaty SSL**. Przykładowy adres URL to **http://{Adres_URL_usługi_Jenkins}.westcentralus.cloudapp.azure.com**.
4. Wprowadź nazwę użytkownika i hasło konta usługi Jenkins.
5. Wybierz pozycję **Sprawdź połączenie**, aby sprawdzić, czy informacje są poprawne.
6. Wybierz przycisk **OK**, aby utworzyć punkt końcowy usługi.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Tworzenie grupy wdrożenia dla maszyn wirtualnych platformy Azure

Potrzebujesz [grupy wdrożenia](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/), aby zarejestrować agenta usług Azure DevOps Services, co umożliwi wdrażanie potoku wydania na maszynie wirtualnej. Grupy wdrożenia ułatwiają określenie grup logicznych docelowych komputerów dla wdrożenia i zainstalowanie wymaganego agenta na każdej maszynie.

   > [!NOTE]
   > W poniższej procedurze zainstaluj wymagania wstępne i *nie uruchamiaj skryptu z uprawnieniami programu sudo.*

1. Otwórz kartę **Wydania** centrum **Kompilacja &amp; wydanie**, otwórz pozycję **Grupy wdrożenia** i wybierz pozycję **+ Nowy**.
2. Wprowadź nazwę grupy wdrożenia i opcjonalny opis. Następnie wybierz przycisk **Utwórz**.
3. Wybierz system operacyjny docelowej maszyny wirtualnej wdrożenia. Na przykład wybierz pozycję **Ubuntu 16.04+** .
4. Wybierz pozycję **Do uwierzytelnienia użyj osobistego tokenu dostępu w skrypcie**.
5. Wybierz link **Wymagania wstępne systemu**. Zainstaluj wymagania wstępne swojego systemu operacyjnego.
6. Wybierz pozycję **Kopiuj skrypt do schowka**, aby skopiować skrypt.
7. Zaloguj się do docelowej maszyny wirtualnej wdrożenia i uruchom skrypt. Nie uruchamiaj skryptu przy użyciu uprawnień programu sudo.
8. Po zakończeniu instalacji zostanie wyświetlony monit o tagi grupy wdrożenia. Zaakceptuj wartości domyślne.
9. W usługach Azure DevOps Services sprawdź nowo zarejestrowaną maszynę wirtualną w pozycji **Miejsca docelowe** w obszarze **Grupy wdrożenia**.

## <a name="create-an-azure-pipelines-release-pipeline"></a>Tworzenie potoku wydania Azure Pipelines

Potok wydania określa proces, którego usługa Azure Pipelines używa do wdrażania aplikacji. W tym przykładzie wykonasz skrypt powłoki.

W celu utworzenia potoku wydania w usłudze Azure Pipelines:

1. Otwórz kartę **Wydania** centrum **Kompilacja i wydanie**, a następnie wybierz pozycję **Utwórz potok wydania**. 
2. Wybierz szablon **Pusty**, wybierając rozpoczęcie od pozycji **Pusty proces**.
3. W sekcji **Artefakty** wybierz pozycję **+ Dodaj artefakt**, a następnie wybierz wartość **Jenkins** dla pozycji **Typ źródła**. Wybierz połączenie punktu końcowego usługi Jenkins. Następnie wybierz zadanie źródłowe usługi Jenkins, po czym wybierz pozycję **Dodaj**.
4. Wybierz symbol wielokropka obok pozycji **Środowisko 1**. Wybierz pozycję **Dodaj fazę grupy wdrożenia**.
5. Wybierz swoją grupę wdrożenia.
5. Wybierz symbol **+** , aby dodać zadanie do pozycji **Faza grupy wdrożenia**.
6. Wybierz zadanie **Skrypt powłoki**, a następnie wybierz pozycję **Dodaj**. Zadanie **Skrypt powłoki** zapewnia, że konfiguracja skryptu jest uruchamiana na każdym serwerze w celu zainstalowania środowiska Node.js i uruchomienia aplikacji.
8. W pozycji **Ścieżka skryptu** wprowadź **$(System.DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh**.
9. Wybierz pozycję **Zaawansowane**, a następnie włącz pozycję **Określ katalog roboczy**.
10. W obszarze **Katalog roboczy** wprowadź **$(System.DefaultWorkingDirectory)/Fabrikam-Node**.
11. Zmień nazwę potoku wydania na nazwę określoną na karcie **Post-build Actions** (Akcje po kompilacji) kompilacji w usłudze Jenkins. Usługa Jenkins wymaga tej nazwy, aby mogła wyzwolić nowe wydanie po zaktualizowaniu artefaktów źródła.
12. Wybierz pozycję **Zapisz**, a następnie wybierz przycisk **OK**, aby zapisać potok wydania.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Przeprowadzanie wdrożeń wyzwalanych ręcznie i za pomocą CI

1. Wybierz pozycję **+ Wydanie**, a następnie wybierz pozycję **Utwórz wydanie**.
2. Wybierz ukończoną kompilację na wyróżnionej liście rozwijanej, a następnie wybierz pozycję **Kolejka**.
3. Wybierz link wydania w komunikacie podręcznym. Na przykład: „Wydanie **Wydanie 1** zostało utworzone”.
4. Otwórz kartę **Dzienniki**, aby obejrzeć dane wyjściowe konsoli wydania.
5. W przeglądarce otwórz adres URL jednego z serwerów, które zostały dodane do grupy wdrożenia. Na przykład wprowadź **http://{adres_IP_serwera}** .
6. Przejdź do źródłowego repozytorium Git i zmodyfikuj zawartość nagłówka **h1** w pliku app/views/index.jade, zmieniając tekst.
7. Zatwierdź wprowadzone zmiany.
8. Po kilku minutach nowo utworzone wydanie pojawi się na stronie **Wydania** usług Azure DevOps Services. Otwórz wydanie, aby zobaczyć trwające wdrożenie. Gratulacje!

## <a name="troubleshooting-the-jenkins-plugin"></a>Rozwiązywanie problemów z wtyczką narzędzia Jenkins

Jeśli napotkasz jakiekolwiek usterki we wtyczkach narzędzia Jenkins, prześlij zgłoszenie za pomocą narzędzia [Jenkins JIRA](https://issues.jenkins-ci.org/) dla określonego składnika.

## <a name="next-steps"></a>Następne kroki

W tym samouczku zautomatyzowano wdrażanie aplikacji na platformie Azure przy użyciu usługi Jenkins na potrzeby kompilacji i usług Azure DevOps Services na potrzeby wydania. W tym samouczku omówiono:

> [!div class="checklist"]
> * Kompilowanie aplikacji w usłudze Jenkins.
> * Konfigurowanie usługi Jenkins do integracji z usługami Azure DevOps Services.
> * Tworzenie grupy wdrożenia dla maszyn wirtualnych platformy Azure.
> * Tworzenie potoku wydania, który konfiguruje maszyny wirtualne, a następnie wdraża aplikację.

Aby dowiedzieć się więcej o sposobie wdrażania stosu LAMP (Linux, Apache MySQL i PHP), przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Wdrażanie stosu LAMP](tutorial-lamp-stack.md)
