---
title: Samouczek — CI/CD z Jenkins do maszyn wirtualnych platformy Azure z Azure DevOps Services | Microsoft Docs
description: W tym samouczku dowiesz się, jak skonfigurować ciągłą integrację (CI) i ciągłe wdrażanie aplikacji node. js za pomocą Jenkins na maszynach wirtualnych platformy Azure z Release Management w Visual Studio Team Services lub Microsoft Team Foundation Server
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
ms.openlocfilehash: 42e1ede7c762fef1f33436ea4c757b0c0531a8f2
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828427"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-with-using-jenkins-and-azure-devops-services"></a>Samouczek: wdrażanie aplikacji na maszynach wirtualnych z systemem Linux na platformie Azure przy użyciu Jenkins i Azure DevOps Services

Ciągłej integracji (CI) i ciągłego wdrażania (CD) tworzą potok, za pomocą którego możesz kompilować, wydawania i wdrażać kod. Azure DevOps Services zawiera kompletny, w pełni funkcjonalny zestaw narzędzi do automatyzacji ciągłej integracji i ciągłego wdrażania na platformie Azure. Jenkins to popularne narzędzie oparte na serwerze ciągłej integracji/ciągłego dostarczania innej firmy, które zapewnia także automatyzację ciągłej integracji/ciągłego dostarczania. Możesz używać Azure DevOps Services i Jenkins razem, aby dostosowywać sposób dostarczania aplikacji lub usługi w chmurze.

W tym samouczku utworzysz aplikację sieci Web Node. js za pomocą Jenkins. Następnie należy użyć usługi Azure DevOps do jej wdrożenia

do [grupy wdrożenia](https://docs.microsoft.com/azure/devops/pipelines/release/deployment-groups/index?view=vsts) zawierającej maszyny wirtualne z systemem Linux. Dowiesz się, jak:

> [!div class="checklist"]
> * Pobierz przykładową aplikację.
> * Skonfiguruj wtyczki Jenkins.
> * Skonfiguruj projekt Jenkins Freestyle dla środowiska Node. js.
> * Skonfiguruj Jenkins dla integracji Azure DevOps Services.
> * Utwórz punkt końcowy usługi Jenkins.
> * Utwórz grupę wdrożenia dla maszyn wirtualnych platformy Azure.
> * Utwórz potok wydania Azure Pipelines.
> * Wykonaj wdrożenia inicjowane ręcznie i CI.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Wymagany jest dostęp do serwera Jenkins. Jeśli nie utworzono jeszcze serwera Jenkins, zobacz [Tworzenie wzorca Jenkins na maszynie wirtualnej platformy Azure](https://docs.microsoft.com/azure/jenkins/install-jenkins-solution-template). 

* Zaloguj się do swojej organizacji Azure DevOps Services (**https://{yourorganization}. VisualStudio. com**). 
  Możesz uzyskać [bezpłatną organizację Azure DevOps Services](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Aby uzyskać więcej informacji, zobacz [nawiązywanie połączenia z Azure DevOps Services](https://docs.microsoft.com/azure/devops/organizations/projects/connect-to-projects?view=vsts).

*  Wymagana jest maszyna wirtualna z systemem Linux dla celu wdrożenia.  Aby uzyskać więcej informacji, zobacz [Tworzenie maszyn wirtualnych z systemem Linux i zarządzanie nimi za pomocą interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm).

*  Otwórz port ruchu przychodzącego 80 dla maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Tworzenie sieciowych grup zabezpieczeń przy użyciu Azure Portal](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic).

## <a name="get-the-sample-app"></a>Pobieranie przykładowej aplikacji

Potrzebna jest aplikacja do wdrożenia przechowywanej w repozytorium git.
W tym samouczku zalecamy korzystanie [z tej przykładowej aplikacji dostępnej w](https://github.com/azure-devops/fabrikam-node)serwisie GitHub. Ten samouczek zawiera przykładowy skrypt służący do instalowania środowiska Node. js i aplikacji. Jeśli chcesz współpracować z własnym repozytorium, należy skonfigurować podobny przykład.

Utwórz rozwidlenie tej aplikacji i zanotuj lokalizację (adres URL) do użycia w kolejnych krokach tego samouczka. Aby uzyskać więcej informacji, zobacz [rozwidlenie repozytorium](https://help.github.com/articles/fork-a-repo/).    

> [!NOTE]
> Aplikacja została skompilowana za pomocą [Narzędzia Yeoman](https://yeoman.io/learning/index.html). Używa wyrażeń Express, Bower i grunt. I zawiera pewne pakiety npm jako zależności.
> Przykład zawiera również skrypt, który konfiguruje Nginx i wdraża aplikację. Jest ono wykonywane na maszynach wirtualnych. Skrypt:
> 1. Instaluje węzły Node, Nginx i PM2.
> 2. Konfiguruje Nginx i PM2.
> 3. Uruchamia aplikację węzła.

## <a name="configure-jenkins-plug-ins"></a>Konfigurowanie wtyczek Jenkins

Najpierw należy skonfigurować dwie wtyczki Jenkins: **NodeJS** i **vs Team Services ciągłego wdrażania**.

1. Otwórz konto Jenkins i wybierz pozycję **Zarządzaj Jenkins**.
2. Na stronie **Zarządzanie Jenkins** wybierz pozycję **Zarządzaj wtyczkami**.
3. Przefiltruj listę, aby znaleźć wtyczkę **NodeJS** , a następnie wybierz opcję **Zainstaluj bez ponownego uruchamiania** .
    ![Adding NodeJS wtyczki Jenkins @ no__t-1
4. Przefiltruj listę, aby znaleźć wtyczkę **ciągłego wdrażania usługi vs Team Services** i wybierz opcję **Zainstaluj bez ponownego uruchamiania** .
5. Wróć do pulpitu nawigacyjnego Jenkins i wybierz pozycję **Zarządzaj Jenkins**.
6. Wybierz pozycję **globalna konfiguracja narzędzia**. Znajdź **NodeJS** i wybierz **instalacje NodeJS**.
7. Wybierz opcję **Zainstaluj automatycznie** , a następnie wprowadź wartość w polu **Nazwa** .
8. Wybierz pozycję **Zapisz**.

## <a name="configure-a-jenkins-freestyle-project-for-nodejs"></a>Konfigurowanie projektu Jenkins Freestyle dla środowiska Node. js

1. Wybierz pozycję **nowy element**. Wprowadź nazwę elementu.
2. Wybierz **projekt Freestyle**. Wybierz **przycisk OK**.
3. Na karcie **Zarządzanie kodem źródłowym** wybierz pozycję **git** i wprowadź szczegóły repozytorium oraz gałąź zawierającą kod aplikacji.    
    @no__t — 0Add repozytorium do kompilacji @ no__t-1
4. Na karcie **kompilacje wyzwalaczy** wybierz pozycję **Menedżer SCM** i wprowadź harmonogram `H/03 * * * *`, aby sondować repozytorium Git pod kątem zmian co trzy minuty. 
5. Na karcie **środowisko kompilacji** wybierz pozycję **Dostarcz węzeł &amp; npm bin/folder ścieżka** i wybierz wartość **instalacji NodeJS** . Pozostaw **plik npmrc** , aby **użyć domyślnego ustawienia systemowego**.
6. Na karcie **kompilacja** wybierz pozycję **Wykonaj powłokę** i wprowadź polecenie `npm install`, aby upewnić się, że wszystkie zależności zostały zaktualizowane.


## <a name="configure-jenkins-for-azure-devops-services-integration"></a>Konfigurowanie Jenkins na potrzeby integracji Azure DevOps Services

> [!NOTE]
> Upewnij się, że osobisty token dostępu, który jest używany w następujących krokach, zawiera uprawnienie do *wykonywania (odczyt* , zapis, wykonywanie i zarządzanie) w Azure DevOps Services.
 
1.  Utwórz konto w organizacji Azure DevOps Services, jeśli jeszcze go nie masz. Jenkins wymaga tych informacji, aby uzyskać dostęp do organizacji Azure DevOps Services. Upewnij się, że przechowujesz informacje o tokenach dla nadchodzących kroków w tej sekcji.
  
    Aby dowiedzieć się, jak wygenerować token, Przeczytaj [Jak mogę utworzyć osobisty token dostępu dla Azure DevOps Services?](https://docs.microsoft.com/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate?view=vsts).
2. Na karcie **Akcje po kompilacji** wybierz pozycję **Dodaj akcję po kompilacji**. Wybierz pozycję **Archiwizuj artefakty**.
3. W przypadku **plików do archiwizacji**wprowadź `**/*`, aby uwzględnić wszystkie pliki.
4. Aby utworzyć kolejną akcję, wybierz pozycję **Dodaj akcję po kompilacji**.
5. Wybierz pozycję **Wywołaj w programie TFS/Team Services**. Wprowadź identyfikator URI organizacji Azure DevOps Services, na przykład **https://{Nazwa organizacji}. VisualStudio. com**.
6. Wprowadź nazwę **projektu** .
7. Wybierz nazwę potoku wydania. (Ten potok wydania można utworzyć później w Azure DevOps Services).
8. Wybierz poświadczenia, aby nawiązać połączenie ze środowiskiem Azure DevOps Services lub Team Foundation Server:
   - Jeśli używasz Azure DevOps Services, pozostaw pustą **nazwę użytkownika** . 
   - Wprowadź nazwę użytkownika i hasło, jeśli używasz lokalnej wersji Team Foundation Server.    
   ![Konfigurowanie akcji po kompilacji Jenkins](media/tutorial-build-deploy-jenkins/trigger-release-from-jenkins.png)
5. Zapisz projekt Jenkins.


## <a name="create-a-jenkins-service-endpoint"></a>Utwórz punkt końcowy usługi Jenkins

Punkt końcowy usługi zezwala Azure DevOps Services na łączenie się z usługą Jenkins.

1. Otwórz stronę **usługi** w Azure DevOps Services, Otwórz nową listę **punktów końcowych usługi** i wybierz pozycję **Jenkins**.
   ![Add Jenkins Endpoint @ no__t-1
2. Wprowadź nazwę połączenia.
3. Wprowadź adres URL serwera Jenkins, a następnie wybierz opcję **Akceptuj niezaufane certyfikaty SSL** . Przykładowy adres URL to **http://{YourJenkinsURL}. westcentralus. cloudapp. Azure. com**.
4. Wprowadź nazwę użytkownika i hasło do konta usługi Jenkins.
5. Wybierz pozycję **Weryfikuj połączenie** , aby sprawdzić, czy informacje są poprawne.
6. Wybierz **przycisk OK** , aby utworzyć punkt końcowy usługi.

## <a name="create-a-deployment-group-for-azure-virtual-machines"></a>Tworzenie grupy wdrożenia dla usługi Azure Virtual Machines

Do zarejestrowania agenta Azure DevOps Services potrzebna jest [Grupa wdrożenia](https://www.visualstudio.com/docs/build/concepts/definitions/release/deployment-groups/) , dzięki czemu można wdrożyć potok wydania na maszynie wirtualnej. Grupy wdrożeń ułatwiają Definiowanie logicznych grup maszyn docelowych na potrzeby wdrażania oraz Instalowanie agenta wymaganego na każdym komputerze.

   > [!NOTE]
   > W poniższej procedurze należy koniecznie zainstalować wymagania wstępne i *nie uruchamiać skryptu z uprawnieniami sudo.*

1. Otwórz kartę **wydania** w centrum **@no__t kompilacji** , Otwórz **grupy wdrożenia**i wybierz pozycję **+ Nowy**.
2. Wprowadź nazwę grupy wdrożenia i opcjonalny opis. Następnie wybierz pozycję **Utwórz**.
3. Wybierz system operacyjny dla maszyny wirtualnej wdrożenia docelowej. Na przykład wybierz pozycję **Ubuntu 16.04 +** .
4. Wybierz opcję **Użyj osobistego tokenu dostępu w skrypcie na potrzeby uwierzytelniania**.
5. Wybierz łącze **wymagania wstępne systemu** . Zainstaluj wymagania wstępne dla systemu operacyjnego.
6. Wybierz pozycję **Kopiuj skrypt do schowka** , aby skopiować skrypt.
7. Zaloguj się do docelowej maszyny wirtualnej wdrożenia i uruchom skrypt. Nie uruchamiaj skryptu z uprawnieniami sudo.
8. Po zakończeniu instalacji zostanie wyświetlony monit dotyczący tagów grupy wdrożenia. Zaakceptuj wartości domyślne.
9. W Azure DevOps Services Sprawdź, czy nowo zarejestrowana maszyna wirtualna znajduje się w obszarze **obiekty docelowe** w obszarze **grupy wdrożenia**.

## <a name="create-an-azure-pipelines-release-pipeline"></a>Tworzenie potoku wydania Azure Pipelines

Potok wersji określa proces, którego Azure Pipelines używa do wdrożenia aplikacji. W tym przykładzie wykonujesz skrypt powłoki.

Aby utworzyć potok wydania w Azure Pipelines:

1. Otwórz kartę **wydania w oknie** **kompilacja &amp; release** Hub, a następnie wybierz pozycję **Utwórz potok wersji**. 
2. Wybierz **pusty** szablon, wybierając opcję Rozpocznij od **pustego procesu**.
3. W sekcji **artefakty** wybierz pozycję **+ Dodaj artefakt** i wybierz pozycję **Jenkins** dla **typu źródła**. Wybierz połączenie punktu końcowego usługi Jenkins. Następnie wybierz zadanie źródłowe Jenkins i wybierz pozycję **Dodaj**.
4. Wybierz wielokropek obok **środowiska 1**. Wybierz pozycję **Dodaj fazę grupy wdrożenia**.
5. Wybierz grupę wdrożenia.
5. Wybierz **+** , aby dodać zadanie do **fazy grupy wdrożenia**.
6. Wybierz zadanie **skryptu powłoki** i wybierz pozycję **Dodaj**. Zadanie **skryptu powłoki** zapewnia konfigurację skryptu uruchamianego na każdym serwerze w celu zainstalowania środowiska Node. js i uruchomienia aplikacji.
8. W obszarze **ścieżka skryptu**wprowadź **$ (System. DefaultWorkingDirectory)/Fabrikam-Node/deployscript.sh**.
9. Wybierz pozycję **Zaawansowane**, a następnie włącz opcję **Określ katalog roboczy**.
10. W obszarze **katalog roboczy**wprowadź **$ (System. DefaultWorkingDirectory)/Fabrikam-Node**.
11. Edytuj nazwę potoku wydania pod nazwą określoną na karcie **Akcje po kompilacji** w Jenkins. Jenkins wymaga, aby ta nazwa mogła wyzwolić nową wersję, gdy artefakty źródłowe zostaną zaktualizowane.
12. Wybierz pozycję **Zapisz** i wybierz pozycję **OK** , aby zapisać potoku wydania.

## <a name="execute-manual-and-ci-triggered-deployments"></a>Wykonaj ręcznie i wyzwalane przez CI wdrożenia

1. Wybierz pozycję **+ wersja** i wybierz pozycję **Utwórz wydanie**.
2. Wybierz kompilację, która została ukończona na wyróżnionej liście rozwijanej, a następnie wybierz **kolejno pozycje Queue**.
3. W oknie podręcznym wybierz link Zwolnij. Na przykład: została utworzona wersja release **-1** .
4. Otwórz kartę **dzienniki** , aby obejrzeć dane wyjściowe konsoli wydania.
5. W przeglądarce Otwórz adres URL jednego z serwerów dodanych do grupy wdrożenia. Na przykład wprowadź **http://{adres IP-Server**
6. Przejdź do źródłowego repozytorium git i zmodyfikuj zawartość nagłówka **H1** w pliku App/views/index. Jade z niezmienionym tekstem.
7. Zatwierdź zmianę.
8. Po kilku minutach zostanie wyświetlona nowa wersja utworzona na stronie **wydania** usługi Azure DevOps. Otwórz wydanie, aby zobaczyć miejsce wdrożenia. Nabycia!

## <a name="troubleshooting-the-jenkins-plugin"></a>Rozwiązywanie problemów z wtyczką Jenkins

Jeśli wystąpią jakiekolwiek usterki z wtyczkami Jenkins, należy rozwiązać problem w [JIRA Jenkins](https://issues.jenkins-ci.org/) dla określonego składnika.

## <a name="next-steps"></a>Następne kroki

W tym samouczku wdrażasz wdrożenie aplikacji na platformie Azure przy użyciu Jenkins do kompilowania i Azure DevOps Services do wydania. Wiesz już, jak:

> [!div class="checklist"]
> * Kompiluj swoją aplikację w Jenkins.
> * Skonfiguruj Jenkins dla integracji Azure DevOps Services.
> * Utwórz grupę wdrożenia dla maszyn wirtualnych platformy Azure.
> * Utwórz potok wydania, który skonfiguruje maszyny wirtualne i wdroży aplikację.

Aby dowiedzieć się, jak wdrożyć stos LAMP (Linux, Apache, MySQL i PHP), przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Wdróż stos lampy](tutorial-lamp-stack.md)
