---
title: Samouczek — maszyny wirtualne ciągłej integracji/ciągłego wdrażania do platformy Azure przy użyciu potoków platformy Azure
description: W tym samouczku dowiesz się, jak skonfigurować ciągłą integrację (CI) i ciągłe wdrażanie (CD) aplikacji Node.js na maszynach wirtualnych platformy Azure przy użyciu potoku platformy Azure opartego na yaml.
author: ushan
tags: azure-devops-pipelines
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: tutorial
ms.tgt_pltfrm: azure-pipelines
ms.workload: infrastructure
ms.date: 1/3/2020
ms.author: ushan
ms.custom: devops
ms.openlocfilehash: bb7c773d02c5da5c115af79cd9e90c78e71eb6bf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76988332"
---
# <a name="tutorial-deploy-your-app-to-linux-virtual-machines-in-azure-using-azure-devops-services-and-azure-pipelines"></a>Samouczek: Wdrażanie aplikacji na maszynach wirtualnych systemu Linux na platformie Azure przy użyciu usług Azure DevOps i potoków platformy Azure

Ciągła integracja (CI) i ciągłe wdrażanie (CD) tworzą potok, za pomocą którego można tworzyć, zwalniać i wdrażać kod po każdym zatwierdzeniu kodu. Ten dokument zawiera kroki związane z konfigurowaniem potoku ciągłej integracji/ciągłego wdrażania dla wykonywania wdrożeń wielu komputerów przy użyciu usługi Azure Pipelines.

Usługa Azure Pipelines udostępnia kompletny, w pełni funkcjonalny zestaw narzędzi automatyzacji ciągłej integracji/ciągłego wdrażania na maszynach wirtualnych, zarówno w przedm, jak i w dowolnej chmurze.

W tym samouczku skonfigurujesz potok ciągłej integracji/dysku CD oparty na YAML, aby wdrożyć aplikację w [środowisku](https://docs.microsoft.com/azure/devops/pipelines/process/environments?view=azure-devops) potoków platformy Azure z maszynami wirtualnymi systemu Linux jako zasobami, z których każdy służy jako serwery sieci web do uruchamiania aplikacji.

Omawiane kwestie:

> [!div class="checklist"]
> * Pobierz przykładową aplikację.
> * Utwórz potok ci oparty na usłudze Azure Pipelines oparty na YAML do tworzenia przykładowej aplikacji.
> * Tworzenie środowiska potoków platformy Azure dla maszyn wirtualnych platformy Azure
> * Utwórz potok dysku CD potoku potoku potoku usługi Azure Pipelines.
> * Przeprowadzanie wdrożeń wyzwalanych ręcznie i za pomocą CI.

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Zaloguj się do organizacji usługi Azure**https://dev.azure.com/** DevOps ( ). 
  Możesz uzyskać [bezpłatną organizację Azure DevOps Services](https://go.microsoft.com/fwlink/?LinkId=307137&clcid=0x409&wt.mc_id=o~msft~vscom~home-vsts-hero~27308&campaign=o~msft~vscom~home-vsts-hero~27308).

  > [!NOTE]
  > Aby uzyskać więcej informacji, zobacz [Nawiązywanie połączenia z usługami Azure DevOps Services](https://docs.microsoft.com/azure/devops/organizations/projects/connect-to-projects?view=vsts).

*  Maszyna wirtualna systemu Linux jest potrzebna jako cel wdrożenia.  Aby uzyskać więcej informacji, zobacz [Create and manage Linux VMs with the Azure CLI (Tworzenie maszyn wirtualnych systemu Linux i zarządzanie nimi za pomocą interfejsu wiersza polecenia platformy Azure)](https://docs.microsoft.com/azure/virtual-machines/linux/tutorial-manage-vm).

*  Otwórz port wejściowy 80 dla maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Create network security groups using the Azure portal (Tworzenie sieciowych grup zabezpieczeń przy użyciu witryny Azure Portal)](https://docs.microsoft.com/azure/virtual-network/tutorial-filter-network-traffic).

## <a name="get-your-sample-app-code"></a>Pobierz przykładowy kod aplikacji

Jeśli masz już aplikację w usłudze GitHub, którą chcesz wdrożyć, możesz spróbować utworzyć potok dla tego kodu.

Jeśli jednak jesteś nowym użytkownikiem, możesz lepiej zacząć, korzystając z naszego przykładowego kodu. W takim przypadku rozwidlić to repozytorium w usłudze GitHub:

#### <a name="java"></a>[Java](#tab/java)

```
https://github.com/spring-projects/spring-petclinic
```

> [!NOTE]
> Petclinic to aplikacja [Java Spring Boot](https://spring.io/guides/gs/spring-boot) zbudowana przy użyciu [Maven.](https://spring.io/guides/gs/maven/)

#### <a name="javascript"></a>[Javascript](#tab/java-script)

```
https://github.com/azure-devops/fabrikam-node
```

> [!NOTE]
> Ta aplikacja Node.js została zbudowana przez [Yeoman](https://yeoman.io/learning/index.html). Używa ona narzędzi Express, Bower i Grunt. I ma niektóre pakiety npm jako zależności.
> Przykład zawiera także skrypt, który konfiguruje serwer Nginx, a następnie wdraża aplikację. Jest on wykonywany na maszynach wirtualnych. W szczególności skrypt:
> 1. Instaluje rozwiązania Node, Nginx i PM2.
> 2. Konfiguruje rozwiązania Nginx i PM2.
> 3. Uruchamia aplikację Node.

* * * 

## <a name="prerequisites-for-the-linux-vm"></a>Wymagania wstępne dla maszyny Wirtualnej z systemem Linux

Przykładowe aplikacje wymienione powyżej zostały przetestowane na Ubuntu 16.04 i zalecamy użycie tej samej wersji maszyny Wirtualnej z systemem Linux dla tego szybkiego startu.
Wykonaj dodatkowe kroki opisane poniżej na podstawie stosu środowiska wykonawczego używanego dla aplikacji.

#### <a name="java"></a>[Java](#tab/java)

- Aby wdrożyć aplikacje java spring boot i spring cloud, utwórz maszynę wirtualną z systemem Linux na platformie Azure przy użyciu [tego](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) szablonu, który zapewnia w pełni obsługiwany środowisko uruchomieniowe oparte na openjdk.
- Aby wdrożyć serwery Java na serwerze Tomcat, utwórz maszynę wirtualną z systemem Linux z oprogramowaniem Java 8 przy użyciu [tego](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) szablonu platformy Azure i [skonfiguruj tomcat 9.x jako usługę.](https://tomcat.apache.org/tomcat-9.0-doc/setup.html)
- Do wdrażania aplikacji opartej na języku Java EE użyj szablonu platformy Azure, aby utworzyć [maszynę wirtualną z systemem Linux + Java + WebSphere 9.x](https://azuremarketplace.microsoft.com/marketplace/apps/midvision.websphere-application-server-nde-90) lub [maszynę wirtualną z systemem Linux + Java + WebLogic 12.x](https://azuremarketplace.microsoft.com/marketplace/apps/oracle.20191009-arm-oraclelinux-wls-admin) lub [maszynę wirtualną z systemem Linux +Java](https://azuremarketplace.microsoft.com/marketplace/apps/azul.azul-zulu8-ubuntu-1804) + WildFly/JBoss 14 

#### <a name="javascript"></a>[Javascript](#tab/java-script)

Aby zainstalować aplikację javascript lub aplikację Node.js, do wdrożenia aplikacji potrzebna jest maszyna wirtualna z systemem Linux z serwerem sieci Web Nginx.
Jeśli nie masz jeszcze maszyny Wirtualnej z systemem Linux z Nginx, utwórz go teraz na platformie Azure, wykonując kroki opisane w [tym przykładzie.](/azure/virtual-machines/linux/quick-create-cli)

* * * 

## <a name="create-an-azure-pipelines-environment-with-azure-virtual-machines"></a>Tworzenie środowiska potoków platformy Azure za pomocą maszyn wirtualnych platformy Azure

Maszyny wirtualne mogą być dodawane jako zasoby w [środowiskach](https://docs.microsoft.com/azure/devops/pipelines/process/environments) i mogą być przeznaczone dla wdrożeń wielu komputerów. Widoki historii wdrażania w środowisku zapewniają identyfikowalność od maszyny Wirtualnej do potoku, a następnie do zatwierdzenia.

Środowisko można utworzyć w centrum "**Środowiska**" w sekcji "**Potoki**".
1.  Zaloguj się do organizacji Azure DevOps i przejdź do projektu.
2.  W projekcie przejdź do strony **Potoki.** Następnie wybierz pozycję **Środowiska** i kliknij pozycję **Utwórz środowisko**. Określ **nazwę** (wymaganą) dla środowiska i **opis**.
3.  Wybierz pozycję **Maszyny wirtualne** jako **zasób,** który ma zostać dodany do środowiska, a następnie kliknij przycisk **Dalej**.
4.  Wybierz system operacyjny (Windows/Linux) i **skopiuj skrypt rejestracji PS**. 
5.  Teraz uruchom skopiowany skrypt z wiersza polecenia programu PowerShell administratora na każdej z docelowych maszyn wirtualnych, które mają być zarejestrowane w tym środowisku.
    > [!NOTE]
    > - Osobisty token dostępu zalogowanego użytkownika jest wstępnie wstawiany do skryptu, który wygasa tego samego dnia, co sprawia, że skopiowany skrypt nie nadaje się do jego użytecznego.
    > - Jeśli maszyna wirtualna ma już żadnego agenta działającego na nim, podaj unikatową nazwę dla "agenta", aby zarejestrować się w środowisku.
6.  Po zarejestrowaniu maszyny Wirtualnej rozpocznie się pojawianie się jako zasób środowiska w zakładce "zasoby" środowiska.

    ![VMcreation (Tworzenia maszyn wirtualnych)](media/tutorial-deploy-vms-azure-pipelines/vm-creation.png)

7.  Aby dodać więcej maszyn wirtualnych, można ponownie wyświetlić i skopiować skrypt, klikając na "Dodaj zasób" i wybierając "Maszyny wirtualne" jako zasób. Ten skrypt pozostanie taki sam dla wszystkich maszyn wirtualnych, które mają zostać dodane do tego środowiska. 
8.  Każda maszyna współdziała z usługi Azure Pipelines w celu koordynowania wdrażania aplikacji.

    ![VMresource_view](media/tutorial-deploy-vms-azure-pipelines/vm-resourceview.png)

9. Tagi można dodać do maszyny Wirtualnej jako część interaktywnego skryptu rejestracji PS (lub) można również dodać/usunąć to samo z widoku zasobów, klikając potrójne kropki na końcu każdego zasobu maszyny Wirtualnej w widoku zasobów.

   Przypisane tagi umożliwiają ograniczenie wdrażania do określonych maszyn wirtualnych, gdy środowisko jest używane w zadaniu wdrażania. Tagi są ograniczone do 256 znaków, ale nie ma limitu liczby tagów, których można użyć.

   ![Tagi wirtualne](media/tutorial-deploy-vms-azure-pipelines/vm-tags.png)

* * * 

## <a name="define-your-ci-build-pipeline"></a>Definiowanie potoku kompilacji ciągłej integracji

Potrzebny jest potok kompilacji ciągłej integracji (CI), który publikuje aplikację sieci web, a także skrypt wdrażania, który można uruchomić lokalnie na serwerze Ubuntu. Skonfiguruj potok kompilacji ciągłej integracji na podstawie środowiska wykonawczego, którego chcesz użyć. 

1. Zaloguj się do organizacji Azure DevOps i przejdź do projektu.

1. W projekcie przejdź do strony **Potoki.** Następnie wybierz akcję, aby utworzyć nowy potok.

1. Przejdź przez kroki kreatora, najpierw wybierając **GitHub** jako lokalizację kodu źródłowego.

1. Możesz zostać przekierowany do gitHub, aby się zalogować. Jeśli tak, wprowadź swoje poświadczenia GitHub.

1. Po wyświetleniu listy repozytoriów wybierz żądane przykładowe repozytorium aplikacji.

1. Usługa Azure Pipelines przeanalizuje repozytorium i zaleci odpowiedni szablon potoku.

#### <a name="java"></a>[Java](#tab/java)

Wybierz szablon **startowy** i skopiuj poniższy fragment kodu YAML, który tworzy projekt Java i uruchamia testy z Apache Maven:

```YAML
- job: Build
  displayName: Build Maven Project
  steps:
  - task: Maven@3
    displayName: 'Maven Package'
    inputs:
      mavenPomFile: 'pom.xml'
  - task: CopyFiles@2
    displayName: 'Copy Files to artifact staging directory'
    inputs:
      SourceFolder: '$(System.DefaultWorkingDirectory)'
      Contents: '**/target/*.?(war|jar)'
      TargetFolder: $(Build.ArtifactStagingDirectory)
  - upload: $(Build.ArtifactStagingDirectory)
    artifact: drop
```

Aby uzyskać więcej wskazówek, wykonaj kroki wymienione w [Tworzenie aplikacji Java z Maven](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/java).

#### <a name="javascript"></a>[Javascript](#tab/java-script)

Wybierz szablon **startowy** i skopiuj poniższy fragment kodu YAML, który tworzy ogólny projekt Node.js z npm.

```YAML
- stage: Build
  displayName: Build stage
  jobs:  
  - job: Build
    displayName: Build
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    - script: |
        npm install
        npm run build --if-present
        npm run test --if-present
      displayName: 'npm install, build and test'
    - task: ArchiveFiles@2
      displayName: 'Archive files'
      inputs:
        rootFolderOrFile: '$(System.DefaultWorkingDirectory)'
        includeRootFolder: false
        archiveType: zip
        archiveFile: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
        replaceExistingArchive: true
    - upload: $(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip
      artifact: drop
```

Aby uzyskać więcej wskazówek, wykonaj kroki opisane w [tworzenie aplikacji Node.js za pomocą łyka.](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/javascript)

- Spójrz na potoku, aby zobaczyć, co robi. Upewnij się, że wszystkie domyślne dane wejściowe są odpowiednie dla kodu.

- Wybierz **pozycję Zapisz i uruchom**, a następnie wybierz pozycję **Zatwierdź bezpośrednio do gałęzi wzorcowej**, a następnie wybierz pozycję **Zapisz i uruchom** ponownie.

- Rozpoczyna się nowy bieg. Poczekaj na zakończenie biegu.

* * * 

## <a name="define-cd-steps-to-deploy-to-the-linux-vm"></a>Definiowanie kroków cd do wdrożenia na maszynie Wirtualnej z systemem Linux

1. Edytuj powyższy potok i dołącz [zadanie wdrożenia,](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs) odwołując się do środowiska i zasobów maszyny Wirtualnej, które zostały wcześniej przy użyciu składni YAML poniżej:

   ```YAML
   jobs:  
   - deployment: VMDeploy
     displayName: web
     environment:
       name:  <environment name>
       resourceType: VirtualMachine
       tags: web1
     strategy:
   ```
2. Można wybrać określone zestawy maszyn wirtualnych ze środowiska, aby otrzymać wdrożenie, określając **znaczniki zdefiniowane** dla każdej maszyny wirtualnej w środowisku.
[Oto](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=azure-devops&tabs=schema#deployment-job) kompletny schemat YAML dla zadania wdrożenia.

3. Można określić eithor `runOnce` lub `rolling` jako strategii wdrażania. 

   `runOnce`jest najprostszą strategią wdrażania, w której wszystkie `preDeploy` `deploy`haki cyklu życia, a mianowicie , `routeTraffic`i `postRouteTraffic`, są wykonywane raz. Następnie `on:` `success` albo `on:` `failure` jest wykonywany.

   Poniżej znajduje się przykładowy fragment `runOnce` kodu YAML dla:
   ```YAML
   jobs:
   - deployment: VMDeploy
     displayName: web
     pool:
       vmImage: 'Ubuntu-16.04'
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         runOnce:
           deploy:
             steps:
             - script: echo my first deployment
   ```

4. Poniżej znajduje się przykład fragmentu kodu YAML, który służy do definiowania strategii stopniowej dla maszyn wirtualnych aktualizuje do 5 obiektów docelowych w każdej iteracji. `maxParallel`określi liczbę celów, które można wdrożyć równolegle. Wybór uwzględnia bezwzględną liczbę lub procent celów, które muszą pozostać dostępne w dowolnym momencie, z wyłączeniem celów, które są wdrażane. Jest również używany do określenia warunków sukcesu i awarii podczas wdrażania.

   ```YAML
   jobs: 
   - deployment: VMDeploy
     displayName: web
     environment:
       name: <environment name>
       resourceType: VirtualMachine
     strategy:
         rolling:
           maxParallel: 5  #for percentages, mention as x%
           preDeploy:
             steps:
             - download: current
               artifact: drop
             - script: echo initialize, cleanup, backup, install certs
           deploy:
             steps:
             - task: Bash@3
               inputs:
                 targetType: 'inline'
                 script: |
                   # Modify deployment script based on the app type
                   echo "Starting deployment script run"
                   sudo java -jar '$(Pipeline.Workspace)/drop/**/target/*.jar'
           routeTraffic:
             steps:
             - script: echo routing traffic
           postRouteTraffic:
             steps:
             - script: echo health check post-route traffic
           on:
             failure:
               steps:
               - script: echo Restore from backup! This is on failure
             success:
               steps:
               - script: echo Notify! This is on success
   ```

   Przy każdym uruchomieniu tego zadania historia wdrażania `<environment name>` jest rejestrowana w środowisku, które zostało utworzone i zarejestrowane maszyny wirtualne.

## <a name="run-your-pipeline-and-get-traceability-views-in-environment"></a>Uruchamianie potoku i uzyskanie widoków identyfikowalności w środowisku
Widok wdrożeń środowiska zapewnia pełną identyfikowalność zatwierdzeń i elementów roboczych oraz historię wdrażania między potokami na środowisko/zasób.

![VMDeployments_view](media/tutorial-deploy-vms-azure-pipelines/vm-deployments.png)
  
![VMjobs_view](media/tutorial-deploy-vms-azure-pipelines/vm-jobsview.png)

## <a name="next-steps"></a>Następne kroki
- Można przystąpić do [dostosowywania potoku,](https://docs.microsoft.com/azure/devops/pipelines/customize-pipeline) który właśnie utworzono.
- Aby dowiedzieć się, co jeszcze można zrobić w potokach YAML, zobacz [odwołanie do schematu YAML](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema).
- Aby dowiedzieć się więcej o sposobie wdrażania stosu LAMP (Linux, Apache MySQL i PHP), przejdź do następnego samouczka.

> [!div class="nextstepaction"]
> [Wdrażanie stosu LAMP](tutorial-lamp-stack.md)
