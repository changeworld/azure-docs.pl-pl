---
title: Utwórz potok ciągłej integracji/ciągłego wdrażania dla programu PWA z GatsbyJS i Azure DevOps Projects
description: Usługa DevOps Projects ułatwia rozpoczęcie pracy na platformie Azure. Umożliwia uruchomienie aplikacji w wybranej usłudze platformy Azure w kilku prostych krokach.
ms.prod: devops
ms.technology: devops-cicd
services: vsts
documentationcenter: vs-devops-build
author: arob98
manager: angrobe
editor: ''
ms.assetid: ''
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 02/24/2020
ms.author: angrobe
ms.custom: mvc
monikerRange: vsts
ms.openlocfilehash: 508a61d6bbb00692855e09601aed67ab3be9cc8d
ms.sourcegitcommit: 5192c04feaa3d1bd564efe957f200b7b1a93a381
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2020
ms.locfileid: "78209071"
---
#  <a name="quickstart-create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-projects"></a>Szybki Start: Tworzenie potoku ciągłej integracji/ciągłego wdrażania w Azure Pipelines dla środowiska Node. js z Azure DevOps Projects
W tym przewodniku szybki start utworzysz NodeJS progresywną aplikację sieci Web (PWA) przy użyciu [GatsbyJS](https://www.gatsbyjs.org/) i uproszczonego środowiska tworzenia projektów DevOps platformy Azure. Po zakończeniu masz potok ciągłej integracji (CI) i ciągłe dostarczanie (CD) dla programu PWA w Azure Pipelines. Azure DevOps Projects konfiguruje to, czego potrzebujesz do tworzenia, wdrażania i monitorowania.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Organizacja [usługi Azure DevOps](https://azure.microsoft.com/services/devops/) .

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Usługa DevOps Projects tworzy potok ciągłej integracji/ciągłego wdrażania w usłudze Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. Usługa DevOps Projects tworzy również zasoby platformy Azure w wybranej subskrypcji platformy Azure.

1. Zaloguj się do [Azure Portal](https://portal.azure.com), a następnie w okienku po lewej stronie wybierz pozycję **Utwórz zasób**. 

   ![Tworzenie zasobu platformy Azure w Azure Portal](_img/azure-devops-project-nodejs/create-azure-resource.png)

2. Wyszukaj i wybierz pozycję **DevOps projects**, a następnie wybierz pozycję **Utwórz**.

 ![Tworzenie projektu DevOps](_img/azure-devops-project-nodejs/create-devops-project.png) 

## <a name="select-a-sample-application-and-azure-service"></a>Wybieranie przykładowej aplikacji i usługi platformy Azure

1. Wybierz przykładową aplikację środowiska Node.js.   

 ![Wybieranie przykładu Node. js](_img/azure-devops-project-nodejs/select-nodejs-devops-project.png) 

2. Domyślna struktura przykładowej aplikacji to struktura **Express.js**. Zmień zaznaczenie na **prostą aplikację Node. js** , a następnie wybierz przycisk **dalej**. 

 ![Wybierz prostą aplikację Node. js](_img/azure-devops-project-nodejs/simple-nodejs-project.png) 

3. Cele wdrożenia dostępne w tym kroku są podyktowane przez strukturę aplikacji wybraną w kroku 2.  W tym przykładzie **aplikacja sieci Web systemu Windows** jest domyślnym miejscem docelowym wdrożenia.  Pozostaw zestaw **Web App for Containers** i wybierz pozycję **dalej**.

 ![Wybierz cel wdrożenia](_img/azure-devops-project-nodejs/select-web-server.png) 

## <a name="configure-a-project-name-and-an-azure-subscription"></a>Skonfiguruj nazwę projektu i subskrypcję platformy Azure

1. W ostatnim kroku przepływu pracy tworzenia projektu DevOps przypiszesz nazwę projektu, wybierz subskrypcję platformy Azure, a następnie wybierz pozycję **gotowe**.  

 ![Przypisz nazwę projektu i wybierz subskrypcję](_img/azure-devops-project-nodejs/assign-project-name.png) 

2. Podczas kompilowania projektu zostanie wyświetlona strona podsumowania, a aplikacja jest wdrażana na platformie Azure. Po krótkim czasie projekt zostanie utworzony w [organizacji usługi Azure DevOps](https://dev.azure.com/) , która obejmuje repozytorium git, tablicę Kanban, potok wdrożenia, plany testów i artefakty wymagane przez aplikację.  

## <a name="managing-your-project"></a>Zarządzanie projektem

1. Przejdź do **wszystkich zasobów** i Znajdź projekt DevOps. Wybierz **projekt DevOps**.

![Pulpit nawigacyjny usługi Azure DevOps na liście zasobów](_img/azure-devops-project-nodejs/azure-devops-project-in-resource-list.png)

2. Nastąpi przekierowanie do pulpitu nawigacyjnego, który zapewnia wgląd w stronę główną projektu, repozytorium kodu, potok ciągłej integracji/ciągłego wdrażania oraz link do uruchomionej aplikacji. Wybierz **stronę główną projektu** , aby wyświetlić aplikację w **usłudze Azure DevOps** , a następnie w innej karcie przeglądarki wybierz **punkt końcowy aplikacji** , aby wyświetlić przykładową aplikację na żywo.  Zmienimy ten przykład później, aby użyć GatsbyJS wygenerowanego PWA.

![Pulpit nawigacyjny usługi Azure DevOps](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

3. W projekcie usługi Azure DevOps możesz zapraszać członków zespołu do współpracy i nawiązywać tablicę Kanban, aby zacząć śledzić swoją pracę.  Aby uzyskać więcej informacji, zobacz [tutaj](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops?view=azure-devops).

![Azure DevOps — Omówienie](_img/azure-devops-project-nodejs/azure-devops-overview.png)

## <a name="clone-the-repo-and-install-your-gatsby-pwa"></a>Klonowanie repozytorium i Instalowanie programu Gatsby PWA

DevOps Projects tworzy repozytorium Git w usłudze Azure Repos lub GitHub. Ten przykład utworzył repozytorium platformy Azure.  Następnym krokiem jest klonowanie repozytorium i wprowadzanie zmian.

1. Wybierz pozycję **repozytoria** z **projektu DevOps** , a następnie kliknij pozycję **Klonuj**.  Istnieją różne mechanizmy klonowania repozytorium Git na pulpicie.  Wybierz ten, który pasuje do Twojego środowiska programistycznego.  

![Klonowanie repozytorium](_img/azure-devops-project-nodejs/clone-the-repo.png)

2. Po sklonowaniu repozytorium do pulpitu wprowadź pewne zmiany w szablonie początkowym. Zacznij od zainstalowania interfejsu wiersza polecenia GatsbyJS z poziomu terminalu.
```powershell
npm install -g gatsby
```

3. Z poziomu terminalu przejdź do katalogu głównego repozytorium. Powinien on zawierać trzy foldery, które wyglądają następująco:
```powershell
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        2/23/2020  10:42 PM                Application
d-----        2/23/2020   3:05 PM                ArmTemplates
d-----        2/23/2020   3:05 PM                Tests
```

4. Nie chcemy, aby wszystkie pliki w folderze aplikacji zostały zamienione na Gatsby Starter. Uruchom następujące polecenia w kolejności, aby przyciąć je w dół.
```powershell
cp .\Application\Dockerfile .
rmdir Application
```

5. Użyj interfejsu wiersza polecenia Gatsby, aby wygenerować przykład PWA. Uruchom `gatsby new` z terminalu, aby rozpocząć pracę kreatora PWA i wybrać `gatsby-starter-blog` dla początkowego szablonu. Powinien wyglądać podobnie do tego przykładu:
```powershell
c:\myproject> gatsby new
√ What is your project called? ... my-gatsby-project
? What starter would you like to use? » - Use arrow-keys. Return to submit.
    gatsby-starter-default
    gatsby-starter-hello-world
>   gatsby-starter-blog
    (Use a different starter)
```

6. Masz teraz folder o nazwie `my-gatsby-project`. Zmień jej nazwę na `Application` i skopiuj `Dockerfile` do niej.
```powershell
mv my-gatsby-project Application
mv Dockerfile Application
```

7. W ulubionym edytorze Otwórz pliku dockerfile i Zmień pierwszy wiersz z `FROM node:8` na `FROM node:12`. Ta zmiana gwarantuje, że kontener używa środowiska Node. js w wersji 12. x zamiast wersji 8. x. GatsbyJS wymaga więcej nowoczesnych wersji środowiska Node. js.

8. Następnie otwórz plik Package. JSON w folderze Application i edytuj [pole skrypty](https://docs.npmjs.com/files/package.json#scripts) , aby upewnić się, że serwery deweloperskie i produkcyjne nasłuchują wszystkich dostępnych interfejsów sieciowych (na przykład 0.0.0.0) i portu 80. Bez tych ustawień usługa App Service nie może kierować ruchu do aplikacji node. js działającej w kontenerze. Pole `scripts` powinno wyglądać podobnie do poniższego. W tym celu należy zmienić wartości domyślne `develop`, `serve`i `start`.
```json
  "scripts": {
    "build": "gatsby build",
    "develop": "gatsby develop  -H 0.0.0.0 -p 80",
    "format": "prettier --write \"**/*.{js,jsx,json,md}\"",
    "start": "npm run serve",
    "serve": "npm run build && gatsby serve -H 0.0.0.0 -p 80",
    "clean": "gatsby clean",
    "test": "echo \"Write tests! -> https://gatsby.dev/unit-testing\" && exit 1"
  }
```

## <a name="edit-your-cicd-pipelines"></a>Edytowanie potoków ciągłej integracji/ciągłego wdrażania

1. Przed zatwierdzeniem kodu w poprzedniej sekcji wprowadź pewne zmiany w kompilacjach i potokach wydań. Edytuj "potok kompilacji" i zaktualizuj zadanie węzła, aby użyć środowiska Node. js w wersji 12. x. W polu **wersja zadania** ustaw wartość 1. x i pole **wersja** na 12. x.
![zaktualizować Node. js do 12. x](_img/azure-devops-project-nodejs/build-pipeline-update-node.png)

2. W tym przewodniku Szybki Start nie są tworzone testy jednostkowe i wyłączamy te kroki w naszym potoku kompilacji. Podczas pisania testów można ponownie włączyć te kroki. Kliknij prawym przyciskiem myszy, aby wybrać zadania z etykietą **Zainstaluj zależności testów** i **uruchomić testy jednostkowe** i wyłączyć je.

![Wyłącz testy kompilacji](_img/azure-devops-project-nodejs/disable-build-unittests.png)

3. Edytuj potoku wydania.
![edytować potoku wydania](_img/azure-devops-project-nodejs/edit-release-pipeline.png)

4. Podobnie jak w przypadku potoku kompilacji, należy zmienić zadanie węzła, aby użyć 12. x, i wyłączyć dwa zadania testowe. Twoja wersja powinna wyglądać podobnie do tego zrzutu ekranu.

![Ukończony potok wydania](_img/azure-devops-project-nodejs/release-pipeline-complete.png)

1. Przejdź do pliku **views/index.pug** z lewej strony okna przeglądarki.

1. Wybierz pozycję **Edytuj** i wprowadź zmianę w nagłówku h2.  
    Na przykład wprowadź od razu rozpocząć **pracę z Azure DevOps projects** lub wprowadź inne zmiany.

1. Wybierz pozycję **Zatwierdź**, a następnie zapisz zmiany.

1. W przeglądarce przejdź do pulpitu nawigacyjnego usługi DevOps Projects.   
W tym momencie powinna być widoczna trwająca kompilacja. Wprowadzone zmiany są automatycznie kompilowane i wdrażane za pomocą potoku ciągłej integracji/ciągłego wdrażania.

## <a name="commit-your-changes-and-examine-the-azure-cicd-pipeline"></a>Zatwierdź zmiany i sprawdź potok ciągłej integracji/ciągłego wdrażania platformy Azure

W poprzednich dwóch krokach dodano Gatsby wygenerowany PWA do repozytorium git i edytowano potoki w celu skompilowania i wdrożenia kodu. Możemy zatwierdzić kod i obserwować postępy w potoku kompilowania i wydawania.

1. Z poziomu katalogu głównego repozytorium git projektu w terminalu uruchom następujące polecenia, aby wypchnąć kod do projektu usługi Azure DevOps:
```powershell
git add .
git commit -m "My first Gatsby PWA"
git push
```

2. Kompilacja została rozpoczęta zaraz po zakończeniu `git push`. Postęp można wykonać przy użyciu **pulpitu nawigacyjnego usługi Azure DevOps**.

![Pulpit nawigacyjny usługi Azure DevOps na liście zasobów](_img/azure-devops-project-nodejs/azure-devops-project-in-resource-list.png)

3. Po kilku minutach kompilacje i potoki wydań powinny zostać zakończone, a Twoje PWA należy wdrożyć do kontenera. Kliknij link do **punktu końcowego aplikacji** z poziomu pulpitu nawigacyjnego powyżej i powinien zostać wyświetlony projekt Gatsby Start dla blogów.



## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Można usuwać Azure App Service i inne powiązane zasoby, które zostały utworzone, gdy nie są już potrzebne zasoby. Użyj funkcji **Usuń** na pulpicie nawigacyjnym usługi DevOps Projects.


## <a name="next-steps"></a>Następne kroki

Podczas konfigurowania procesu ciągłej integracji/ciągłego tworzenia potoki kompilacji i wydania są tworzone automatycznie. Możesz zmienić te potoki kompilacji i wydań, aby zaspokoić potrzeby zespołu. Aby dowiedzieć się więcej na temat potoku ciągłej integracji/ciągłego wdrażania, zobacz:

> [!div class="nextstepaction"]
> [Dostosowywanie procesu ciągłego wdrażania](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

