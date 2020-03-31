---
title: Tworzenie potoku ciągłej integracji/ciągłego wdrażania dla programu PWA z projektami GatsbyJS i Azure DevOps
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2020
ms.locfileid: "78209071"
---
#  <a name="quickstart-create-a-cicd-pipeline-in-azure-pipelines-for-nodejs-with-azure-devops-projects"></a>Szybki start: tworzenie potoku ciągłej integracji/ciągłego wdrażania w potokach platformy Azure dla aplikacji Node.js za pomocą projektów usługi Azure DevOps
W tym przewodniku Szybki start utworzysz progresywną aplikację sieci web NodeJS (PWA) przy użyciu [usługi GatsbyJS](https://www.gatsbyjs.org/) i uproszczone środowisko tworzenia projektu Azure DevOps. Po zakończeniu masz ciągłej integracji (CI) i ciągłego dostarczania (CD) potoku dla programu PWA w potokach platformy Azure. Projekty programu Azure DevOps konfigurują, czego potrzebujesz do tworzenia, wdrażania i monitorowania.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto za darmo](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). 
- Organizacja [Azure DevOps.](https://azure.microsoft.com/services/devops/)

## <a name="sign-in-to-the-azure-portal"></a>Logowanie się do witryny Azure Portal

Usługa DevOps Projects tworzy potok ciągłej integracji/ciągłego wdrażania w usłudze Azure Pipelines. Możesz utworzyć nową organizację usługi Azure DevOps lub użyć istniejącej organizacji. Usługa DevOps Projects tworzy również zasoby platformy Azure w wybranej subskrypcji platformy Azure.

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com)i w lewym okienku wybierz pozycję **Utwórz zasób**. 

   ![Tworzenie zasobu platformy Azure w witrynie Azure portal](_img/azure-devops-project-nodejs/create-azure-resource.png)

2. Wyszukaj i wybierz pozycję **Projekty DevOps**, a następnie wybierz pozycję **Utwórz**.

 ![Tworzenie projektu DevOps](_img/azure-devops-project-nodejs/create-devops-project.png) 

## <a name="select-a-sample-application-and-azure-service"></a>Wybieranie przykładowej aplikacji i usługi platformy Azure

1. Wybierz przykładową aplikację środowiska Node.js.   

 ![Wybierz przykład Node.js](_img/azure-devops-project-nodejs/select-nodejs-devops-project.png) 

2. Domyślną platformą przykładową jest **Express.js**. Zmień zaznaczenie na **Simple Node.js App,** a następnie wybierz pozycję **Dalej**. 

 ![Wybierz aplikację Simple Node.js](_img/azure-devops-project-nodejs/simple-nodejs-project.png) 

3. Obiekty docelowe wdrażania dostępne w tym kroku są podyktowane strukturą aplikacji wybraną w kroku 2.  W tym przykładzie **aplikacja Windows Web App** jest domyślnym obiektem docelowym wdrożenia.  Pozostaw **aplikację Sieci Web dla kontenerów** i wybierz pozycję **Dalej**.

 ![Wybierz cel wdrożenia](_img/azure-devops-project-nodejs/select-web-server.png) 

## <a name="configure-a-project-name-and-an-azure-subscription"></a>Konfigurowanie nazwy projektu i subskrypcji platformy Azure

1. W ostatnim kroku przepływu pracy tworzenia projektu DevOps należy przypisać nazwę projektu, wybrać subskrypcję platformy Azure i wybrać opcję **Gotowe**.  

 ![Przypisywanie nazwy projektu i wybieranie subskrypcji](_img/azure-devops-project-nodejs/assign-project-name.png) 

2. Strona podsumowania jest wyświetlana podczas tworzenia projektu i wdrażania aplikacji na platformie Azure. Po krótkim czasie tworzony jest projekt w [organizacji Usługi Azure DevOps,](https://dev.azure.com/) który zawiera repozytorium git, tablicę Kanban, potok wdrażania, plany testów i artefakty wymagane przez aplikację.  

## <a name="managing-your-project"></a>Zarządzanie projektem

1. Przejdź do **pozycji Wszystkie zasoby** i znajdź projekt DevOps. Wybierz projekt **DevOps**.

![Pulpit nawigacyjny usługi Azure DevOps na liście zasobów](_img/azure-devops-project-nodejs/azure-devops-project-in-resource-list.png)

2. Użytkownik jest kierowany do pulpitu nawigacyjnego, który zapewnia wgląd w stronę główną projektu, repozytorium kodu, potok ciągłej integracji/ciągłego wdrażania oraz łącze do uruchomionej aplikacji. Wybierz **stronę główną programu Project,** aby wyświetlić aplikację w **usłudze Azure DevOps,** a na innej karcie przeglądarki wybierz **punkt końcowy aplikacji,** aby wyświetlić przykładową aplikację na żywo.  Możemy zmienić ten przykład później używać GatsbyJS generowane PWA.

![Pulpit nawigacyjny usługi Azure DevOps](_img/azure-devops-project-nodejs/devops-projects-dashboard.png) 

3. W projekcie Azure DevOps możesz zaprosić członków zespołu do współpracy i ustanowić tablicę Kanban, aby rozpocząć śledzenie pracy.  Aby uzyskać więcej informacji, zobacz [tutaj](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops?view=azure-devops).

![Omówienie usługi Azure DevOps](_img/azure-devops-project-nodejs/azure-devops-overview.png)

## <a name="clone-the-repo-and-install-your-gatsby-pwa"></a>Klonuj repozytorium i zainstaluj Gatsby PWA

DevOps Projects tworzy repozytorium git w repozytorium platformy Azure lub usłudze GitHub. W tym przykładzie utworzono usługę Azure Repo.  Następnym krokiem jest sklonowanie repozytorium i wprowadzenie zmian.

1. Wybierz **pozycję Repos** z **projektu DevOps,** a następnie kliknij przycisk **Klonuj**.  Istnieją różne mechanizmy klonowania repozytorium git na pulpicie.  Wybierz ten, który pasuje do Twojego środowiska programistycznego.  

![Klonowanie repozytorium](_img/azure-devops-project-nodejs/clone-the-repo.png)

2. Po sklonowanie repozytorium na pulpicie należy wprowadzić pewne zmiany w szablonie startowym. Zacznij od zainstalowania interfejsu wiersza polecenia GatsbyJS z terminala.
```powershell
npm install -g gatsby
```

3. Z terminala przejdź do katalogu głównego repozytorium. Powinien zawierać trzy foldery, które wyglądają następująco:
```powershell
Mode                LastWriteTime         Length Name
----                -------------         ------ ----
d-----        2/23/2020  10:42 PM                Application
d-----        2/23/2020   3:05 PM                ArmTemplates
d-----        2/23/2020   3:05 PM                Tests
```

4. Nie chcemy wszystkich plików w folderze Aplikacji, ponieważ mamy zamiar zastąpić go rozrusznikiem Gatsby.We do not want all of the files in the Application folder because we are going to replace it with a Gatsby starter. Uruchom następujące polecenia, w kolejności, aby przyciąć go w dół.
```powershell
cp .\Application\Dockerfile .
rmdir Application
```

5. Użyj interfejsu wiersza polecenia Gatsby do wygenerowania przykładowego pwa. Uruchom `gatsby new` z terminala, aby rozpocząć `gatsby-starter-blog` kreatora PWA i wybrać dla szablonu startowego. Powinien on przypominać tę próbkę:
```powershell
c:\myproject> gatsby new
√ What is your project called? ... my-gatsby-project
? What starter would you like to use? » - Use arrow-keys. Return to submit.
    gatsby-starter-default
    gatsby-starter-hello-world
>   gatsby-starter-blog
    (Use a different starter)
```

6. Masz teraz folder `my-gatsby-project`o nazwie . Zmień jego `Application` nazwę i `Dockerfile` skopiuj do niego.
```powershell
mv my-gatsby-project Application
mv Dockerfile Application
```

7. W ulubionym edytorze otwórz plik Dockerfile i `FROM node:8` `FROM node:12`zmień pierwszy wiersz z . Ta zmiana gwarantuje, że kontener używa node.js w wersji 12.x zamiast wersji 8.x. GatsbyJS wymaga bardziej nowoczesnych wersji Node.js.

8. Następnie otwórz plik package.json w folderze Aplikacji i edytuj [pole skrypty,](https://docs.npmjs.com/files/package.json#scripts) aby upewnić się, że serwery programistyczne i produkcyjne nasłuchują wszystkich dostępnych interfejsów sieciowych (na przykład 0.0.0.0) i portu 80. Bez tych ustawień usługa aplikacji kontenera nie może kierować ruchu do aplikacji Node.js uruchomionej wewnątrz kontenera. Pole `scripts` powinno przypominać to, co znajduje się poniżej. W szczególności chcesz zmienić `develop` `serve`, `start` i cele z ich wartości domyślnych.
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

1. Przed zatwierdzeniem kodu w poprzedniej sekcji, należy wprowadzić pewne zmiany w kompilacji i wydania potoków. Edytuj potok "Kompilacja" i zaktualizuj zadanie Node, aby używać pliku Node.js w wersji 12.x. Ustaw pole **Wersja zadania** na 1.x, a pole **Wersja** na 12.x.
![Aktualizacja pliku Node.js do 12.x](_img/azure-devops-project-nodejs/build-pipeline-update-node.png)

2. W tym przewodniku Szybki start nie tworzymy testów jednostkowych i wyłączamy te kroki w naszym potoku kompilacji. Podczas pisania testów można ponownie włączyć te kroki. Kliknij prawym przyciskiem myszy, aby wybrać zadania oznaczone jako **Zależności testu instalacji** i **Uruchom testy jednostkowe** i wyłącz je.

![Wyłącz testy kompilacji](_img/azure-devops-project-nodejs/disable-build-unittests.png)

3. Edytuj potok wersji.
![Edytowanie potoku wydania](_img/azure-devops-project-nodejs/edit-release-pipeline.png)

4. Podobnie jak w przypadku potoku kompilacji, zmień zadanie Node, aby użyć 12.x i wyłącz dwa zadania testowe. Wydanie powinno przypominać ten zrzut ekranu.

![Ukończony potok wydania](_img/azure-devops-project-nodejs/release-pipeline-complete.png)

1. Przejdź do pliku **views/index.pug** z lewej strony okna przeglądarki.

1. Wybierz pozycję **Edytuj** i wprowadź zmianę w nagłówku h2.  
    Na przykład wprowadź **wprowadzenie od razu z usługi Azure DevOps projects** lub wprowadzić inne zmiany.

1. Wybierz pozycję **Zatwierdź**, a następnie zapisz zmiany.

1. W przeglądarce przejdź do pulpitu nawigacyjnego usługi DevOps Projects.   
W tym momencie powinna być widoczna trwająca kompilacja. Wprowadzone zmiany są automatycznie szykowane i wdrażane za pośrednictwem potoku ciągłej integracji/ciągłego wdrażania.

## <a name="commit-your-changes-and-examine-the-azure-cicd-pipeline"></a>Zatwierdzanie zmian i badanie potoku ciągłej integracji/ciągłego wdrażania platformy Azure

W poprzednich dwóch krokach dodano Gatsby generowane PWA do repozytorium git i edytowane potoki do tworzenia i wdrażania kodu. Możemy zatwierdzić kod i obserwować jego postęp w potoku kompilacji i wydania.

1. Z katalogu głównego repozytorium git projektu w terminalu uruchom następujące polecenia, aby wypchnąć kod do projektu programu Azure DevOps:
```powershell
git add .
git commit -m "My first Gatsby PWA"
git push
```

2. Kompilacja jest uruchamiana `git push` natychmiast po zakończeniu. Postęp można śledzić na **pulpicie nawigacyjnym programu Azure DevOps**.

![Pulpit nawigacyjny usługi Azure DevOps na liście zasobów](_img/azure-devops-project-nodejs/azure-devops-project-in-resource-list.png)

3. Po kilku minutach potoki kompilacji i zwalniania powinny zakończyć się i program PWA powinny zostać wdrożone w kontenerze. Kliknij **łącze Punkt końcowy aplikacji** z pulpitu nawigacyjnego powyżej i powinien zostać wyświetlony projekt startowy Gatsby dla blogów.



## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Usługę Azure App Service i inne powiązane zasoby utworzone, gdy zasoby nie są już potrzebne. Użyj funkcji **Usuń** na pulpicie nawigacyjnym usługi DevOps Projects.


## <a name="next-steps"></a>Następne kroki

Podczas konfigurowania procesu ciągłej integracji/ciągłego wdrażania tworzone są automatycznie potoki kompilacji i zwalniania. Można zmienić te potoki kompilacji i wydania, aby spełnić potrzeby zespołu. Aby dowiedzieć się więcej na temat potoku ciągłej integracji/ciągłego wdrażania, zobacz:

> [!div class="nextstepaction"]
> [Dostosowywanie procesu ciągłego wdrażania](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts)

