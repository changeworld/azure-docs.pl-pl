---
title: Użyj Centrum wdrażania w usłudze Azure App Service
description: Centrum wdrażania w infrastrukturze DevOps platformy Azure upraszcza konfigurowanie niezawodnego potoku DevOps platformy Azure dla aplikacji
ms.author: puagarw
ms.manager: douge
ms.prod: devops
ms.technology: devops-cicd
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
monikerRange: vsts
ms.openlocfilehash: 8d1e467906b74c97c8b4f4e5c14af0814dd098f7
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/12/2019
ms.locfileid: "67854638"
---
# <a name="deployment-center-launcher"></a>Uruchom Centrum wdrażania

Centrum wdrażania w infrastrukturze DevOps platformy Azure upraszcza ustawienie zapasowej niezawodnego potoku metodyki DevOps dla aplikacji. Domyślnie Konfiguruje potok DevOps, aby wdrożyć aktualizacje aplikacji w klastrze usługi kubernetes. Można rozszerzyć domyślnej skonfigurowanych DevOps potoku i Dodaj bardziej rozbudowane możliwości środowiska DevOps — zatwierdzenia przed przystąpieniem do wdrażania, aprowizowanie dodatkowych zasobów platformy Azure, uruchamianie skryptów, uaktualniania aplikacji lub nawet uruchomionych dodatkowe testy weryfikacji.

W tym samouczku wykonasz następujące czynności:

> [!div class="checklist"]
> * Konfigurowanie potoku metodyki DevOps, aby wdrażać aktualizacje aplikacji do klastra k8s
> * Badanie potoku ciągłej integracji
> * Badanie potoku ciągłego wdrażania
> * Oczyszczanie zasobów

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Możesz uzyskać jeden oferowana bezpłatnie do [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/)

* Klaster usługi Azure Kubernetes Service (AKS)

## <a name="create-aks-cluster"></a>Tworzenie klastra AKS

1. Zaloguj się do Twojej [witryny Azure portal](https://portal.azure.com/)

1. Wybierz [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) przycisk menu w prawym górnym rogu witryny Azure portal.

1. Aby utworzyć klaster usługi AKS, uruchom następujące polecenia.

    ```cmd
    # The below command creates Resource Group in the south india location

    az group create --name azooaks --location southindia

    # The below command creates a cluster named azookubectl with one node. 

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-k8s-cluster"></a>Wdrażanie aktualizacji aplikacji w klastrze K8s

1. Przejdź do powyższego, utworzyć grupę zasobów.

1. Wybierz klaster usługi AKS i kliknij pozycję w obszarze Ustawienia po lewej stronie bloku **Centrum wdrażania (wersja zapoznawcza)** . Kliknij pozycję **wprowadzenie**.

   ![settings](media/deployment-center-launcher/settings.png)

1. Wybierz lokalizację kodu, a następnie kliknij przycisk **dalej**. Obecnie są repozytoriów obsługiwane **[repozytoriów Azure](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)** i **GitHub**. Aby wykonać poniższe kroki w zależności od wyboru repozytorium.

    Repozytoriów platformy Azure to zestaw narzędzi do kontroli wersji, które służą do zarządzania kodem. Czy projekt oprogramowania jest duże lub małe, jak najszybciej korzystanie z kontroli wersji jest dobrym pomysłem.

    - **Azure repozytoriów**: Wybierz repozytorium z istniejącego projektu i organizacji.

        ![Azure Repos](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: Autoryzować, a następnie wybierz repozytorium dla Twojego konta usługi GitHub.

        ![GitHub](media/deployment-center-launcher/github.gif)


1. Będziemy analizować repozytorium i wykrywać z pliku Dockerfile. Jeśli chcesz go zaktualizować, można edytować numer portu zidentyfikowane.

    ![Ustawienia aplikacji](media/deployment-center-launcher/application-settings.png)

    Jeśli repozytorium nie zawiera pliku Dockerfile, system wyświetli komunikat do jednego zatwierdzenia. 

    ![Dockerfile](media/deployment-center-launcher/dockerfile.png)

1. Wybierz lub Utwórz istniejącego rejestru kontenera i kliknij pozycję **Zakończ**. Potok zostanie utworzony automatycznie i kolejkować kompilację w [potoki Azure](https://docs.microsoft.com/azure/devops/pipelines/index?view=azure-devops).

    Potoki usługi Azure to usługa w chmurze, która służy do automatycznego tworzenia i testowania projektu kodu i udostępnić go innym użytkownikom. Potoki usługi Azure łączy ciągłej integracji (CI) i ciągłe dostarczanie (CD), aby stale i spójne testów i kompilacji kodu i wyślij go do dowolnej docelowej.

    ![Container Registry](media/deployment-center-launcher/container-registry.png)

1. Kliknij link, aby wyświetlić bieżące potoku.

1. Widoczne będą dzienniki pomyślne pokazany, gdy wdrożenie jest ukończone.

    ![Dzienniki](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>Badanie potoku ciągłej integracji

Centrum wdrażania konfiguruje organizacji usługi Azure DevOps ciągłej integracji / ciągłego wdrażania potoku automatycznie. Można przeglądać i dostosowywać potok. 

1. Przejdź do pulpitu nawigacyjnego Centrum wdrażania.  

1. Kliknij numer kompilacji na liście pomyślne dzienniki, aby wyświetlić potoku kompilacji dla projektu. 

1. Kliknij przycisk ellipsis(...) w prawym górnym rogu. Menu zawiera kilka opcji, takich jak kolejkowania nowej kompilacji, zachowywanie kompilacji i potoku kompilacji do edycji. Wybierz **potoku edycji**. 

1. Potok kompilacji w tym okienku, można sprawdzić różne zadania. Kompilacja wykonuje różne zadania, takie jak zbieranie źródła z repozytorium Git, tworzenie obrazu, wypychanie obrazu do rejestru kontenerów i publikowanie danych wyjściowych, które są używane do wdrożenia.

1. Wybierz nazwę potoku kompilacji, w górnej części procesu kompilacji.

1. Zmień nazwę potoku kompilacji na coś bardziej opisowe, wybierz opcję **Zapisz k & olejką**, a następnie wybierz pozycję **Zapisz**.

1. Wybierz **historii** w ramach potoku kompilacji. W tym okienku przedstawia dziennik inspekcji ostatnie zmiany kompilacji. DevOps platformy Azure monitoruje wszelkie zmiany wprowadzone do potoku kompilacji i porównywanie wersji.

1. Wybierz **wyzwalaczy**. Opcjonalnie gałęzie mogą być dołączone lub wykluczone z procesu ciągłej integracji.

1. Wybierz **przechowywania**. Można określić zasady, aby zachować lub usunąć numer kompilacji, w zależności od scenariusza.

## <a name="examine-the-cd-pipeline"></a>Badanie potoku ciągłego wdrażania

Centrum wdrażania tworzy i automatycznie konfiguruje niezbędne kroki z Twojej organizacji DevOps platformy Azure z subskrypcją platformy Azure. Te kroki obejmują Konfigurowanie połączenia z usługą platformy Azure do uwierzytelniania Twojej subskrypcji platformy Azure DevOps platformy Azure. W ramach automatyzacji jest też tworzony potok wydania, który zapewnia ciągłe wdrażanie na platformie Azure.

1. Wybierz **potoki**, a następnie wybierz **wersji**.

1. Aby edytować potoku tworzenia wersji, kliknij pozycję **Edytuj** .

1. Wybierz **porzucić** z **artefaktów**. W poprzednich krokach potoku konstrukcji, które można zbadać generuje dane wyjściowe, w używane artefaktu. 

1. Wybierz **ciągłe wdrażanie** wyzwalacz po prawej stronie **porzucić** ikony. Ten potok wersji ma włączone wyzwalacz ciągłego wdrażania, który uruchamia wdrożenie, zawsze wtedy, gdy nowe artefakt kompilacji jest dostępny. Opcjonalnie można wyłączyć wyzwalacza, aby wymagać ręcznego wykonywania w celu wdrożenia.

1. Aby sprawdzić wszystkie zadania dla potoku, kliknij polecenie **zadania**. Wydanie ustawia środowisko tiller, konfiguruje imagePullSecrets, instaluje narzędzia Helm i wdraża klaster K8s wykresów rozwiązania Helm.

1. Aby wyświetlić historię wersji, wybierz polecenie **Wyświetl wersje**. 

1. Aby wyświetlić podsumowanie, kliknij **wersji**. Kliknij dowolny etap, aby zapoznać się z wielu menu, takich jak podsumowanie wydania skojarzone elementy robocze i testy. 

1. Wybierz pozycję **Zatwierdzenia**. Ten widok przedstawia zatwierdzenia kodu powiązane z tym wdrożeniem. Porównanie wersji, aby wyświetlić zatwierdzenia różnice między wdrożeniami.

1. Wybierz pozycję **Dzienniki**. Dzienniki zawierają przydatne informacje. Podczas i po jego zakończeniu można je wyświetlić.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Możesz usunąć powiązane zasoby, które zostały utworzone podczas możesz nie są już potrzebne. Na pulpicie nawigacyjnym DevOps Projects, należy użyć funkcji usuwania.

## <a name="next-steps"></a>Następne kroki

Opcjonalnie możesz zmodyfikować potoki kompilacji i wydania, aby zaspokoić potrzeby swojego zespołu. Możesz także użyć wzorca ciągłej integracji/ciągłego wdrażania jako szablonu podczas pracy z innymi potokami. W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Konfigurowanie potoku metodyki DevOps, aby wdrażać aktualizacje aplikacji do klastra k8s
> * Badanie potoku ciągłej integracji
> * Badanie potoku ciągłego wdrażania
> * Oczyszczanie zasobów
