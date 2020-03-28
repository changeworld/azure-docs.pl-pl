---
title: Centrum wdrażania platformy Azure Kubernetes
description: Centrum wdrażania w usłudze Azure DevOps upraszcza konfigurowanie niezawodnego potoku programu Azure DevOps dla aplikacji
ms.author: puagarw
ms.topic: tutorial
ms.date: 07/12/2019
author: pulkitaggarwl
ms.openlocfilehash: b0a9597e370648faab3787218c7d038798dbd455
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048109"
---
# <a name="deployment-center-for-azure-kubernetes"></a>Centrum wdrażania platformy Azure Kubernetes

Centrum wdrażania w usłudze Azure DevOps upraszcza konfigurowanie niezawodnego potoku programu Azure DevOps dla aplikacji. Domyślnie Centrum wdrażania konfiguruje potok programu Azure DevOps w celu wdrożenia aktualizacji aplikacji w klastrze usługi Kubernetes. Można rozszerzyć domyślny skonfigurowany potok Azure DevOps, a także dodać bogatsze możliwości: możliwość uzyskania zatwierdzenia przed wdrożeniem, aprowizować dodatkowe zasoby platformy Azure, uruchamiać skrypty, uaktualniać aplikację, a nawet uruchamiać więcej testów sprawdzania poprawności.

W tym samouczku zostaną wykonane następujące czynności:

> [!div class="checklist"]
> * Skonfiguruj potok programu Azure DevOps, aby wdrożyć aktualizacje aplikacji w klastrze kubernetes.
> * Sprawdź potok ciągłej integracji (CI).
> * Sprawdź potok ciągłego dostarczania (CD).
> * Oczyść zasoby.

## <a name="prerequisites"></a>Wymagania wstępne

* Subskrypcja platformy Azure. Możesz uzyskać ją bezpłatnie za pośrednictwem programu [Visual Studio Dev Essentials](https://visualstudio.microsoft.com/dev-essentials/).

* Klaster usługi Kubernetes platformy Azure (AKS).

## <a name="create-an-aks-cluster"></a>Tworzenie klastra AKS

1. Zaloguj się do [witryny Azure portal](https://portal.azure.com/).

1. Wybierz opcję [Powłoka w chmurze](https://docs.microsoft.com/azure/cloud-shell/overview) po prawej stronie paska menu w witrynie Azure portal.

1. Aby utworzyć klaster AKS, uruchom następujące polecenia:

    ```azurecli
    # Create a resource group in the South India location:

    az group create --name azooaks --location southindia

    # Create a cluster named azookubectl with one node.

    az aks create --resource-group azooaks --name azookubectl --node-count 1 --enable-addons monitoring --generate-ssh-keys
    ```

## <a name="deploy-application-updates-to-a-kubernetes-cluster"></a>Wdrażanie aktualizacji aplikacji w klastrze usługi Kubernetes

1. Przejdź do grupy zasobów utworzonej w poprzedniej sekcji.

1. Wybierz klaster AKS, a następnie wybierz **pozycję Centrum wdrażania (podgląd)** na lewym bloku. Wybierz **pozycję Wprowadzenie**.

   ![settings](media/deployment-center-launcher/settings.png)

1. Wybierz lokalizację kodu i wybierz pozycję **Dalej**. Następnie wybierz jedno z aktualnie obsługiwanych repozytoriów: **[Repozytoria platformy Azure](https://docs.microsoft.com/azure/devops/repos/index?view=azure-devops)** lub **GitHub**.

    Repozytorium platformy Azure to zestaw narzędzi do kontroli wersji, które ułatwiają zarządzanie kodem. Dobrym pomysłem jest to, czy projekt oprogramowania jest duży, czy mały, korzystanie z kontroli wersji tak wcześnie, jak to możliwe.

    - **Repozytorium platformy Azure:** wybierz repozytorium z istniejącego projektu i organizacji.

        ![Azure Repos](media/deployment-center-launcher/azure-repos.gif)

    - **GitHub**: Autoryzuj i wybierz repozytorium dla swojego konta GitHub.

        ![GitHub](media/deployment-center-launcher/github.gif)


1. Centrum wdrażania analizuje repozytorium i wykrywa plik Dockerfile. Jeśli chcesz zaktualizować plik dockerfile, możesz edytować zidentyfikowany numer portu.

    ![Ustawienia aplikacji](media/deployment-center-launcher/application-settings.png)

    Jeśli repozytorium nie zawiera pliku Dockerfile, system wyświetla komunikat o jego zatwierdzeniu.

    ![Plik dockerfile](media/deployment-center-launcher/dockerfile.png)

1. Wybierz istniejący rejestr kontenerów lub utwórz go, a następnie wybierz pozycję **Zakończ**. Potok jest tworzony automatycznie i kolejkuje kompilację w [usłudze Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/index?view=azure-devops).

    Usługa Azure Pipelines to usługa w chmurze, której można użyć do automatycznego tworzenia i testowania projektu kodu i udostępniania go innym użytkownikom. Usługa Azure Pipelines łączy ciągłą integrację i ciągłe dostarczanie, aby stale i konsekwentnie testować i tworzyć kod i wysyłać go do dowolnego obiektu docelowego.

    ![Container Registry](media/deployment-center-launcher/container-registry.png)

1. Wybierz łącze, aby wyświetlić trwający potok.

1. Zobaczysz pomyślne dzienniki po zakończeniu wdrażania.

    ![Dzienniki](media/deployment-center-launcher/logs.png)

## <a name="examine-the-ci-pipeline"></a>Badanie potoku ciągłej integracji

Centrum wdrażania automatycznie konfiguruje potok ciągłej integracji/dysku CD organizacji Azure DevOps. Potok można zbadać i dostosować.

1. Przejdź do pulpitu nawigacyjnego Centrum wdrażania.  

1. Wybierz numer kompilacji z listy dzienników pomyślnego, aby wyświetlić potok kompilacji dla projektu.

1. Wybierz wielokropek (...) w prawym górnym rogu. W menu przedstawiono kilka opcji, takich jak kolejkowanie nowej kompilacji, zachowywanie kompilacji i edytowanie potoku kompilacji. Wybierz **edytuj potok**. 

1. Można sprawdzić różne zadania dla potoku kompilacji w tym okienku. Kompilacja wykonuje różne zadania, takie jak zbieranie źródeł z repozytorium Git, tworzenie obrazu, wypychanie obrazu do rejestru kontenerów i publikowanie danych wyjściowych, które są używane do wdrożeń.

1. Wybierz nazwę potoku kompilacji w górnej części potoku.

1. Zmień nazwę potoku kompilacji na coś bardziej opisowego, wybierz **pozycję Zapisz & kolejki**, a następnie wybierz pozycję **Zapisz**.

1. W obszarze potoku kompilacji wybierz pozycję **Historia**. W tym okienku jest wyświetlana ścieżka inspekcji ostatnich zmian kompilacji. Usługa Azure DevOps monitoruje wszelkie zmiany wprowadzone w potoku kompilacji i umożliwia porównywanie wersji.

1. Wybierz pozycję **Wyzwalacze**. Można uwzględnić lub wykluczyć gałęzie z procesu ciągłej integracji.

1. Wybierz pozycję **Przechowywanie**. Można określić zasady, aby zachować lub usunąć liczbę kompilacji, w zależności od scenariusza.

## <a name="examine-the-cd-pipeline"></a>Badanie potoku ciągłego wdrażania

Centrum wdrażania automatycznie tworzy i konfiguruje relację między organizacją Azure DevOps a subskrypcją platformy Azure. Kroki te obejmują konfigurowanie połączenia usługi platformy Azure w celu uwierzytelnienia subskrypcji platformy Azure za pomocą usługi Azure DevOps. Zautomatyzowany proces tworzy również potok wersji, który zapewnia ciągłe dostarczanie do platformy Azure.

1. Wybierz **pozycję Potoki**, a następnie wybierz pozycję **Zwalnia**.

1. Aby edytować potok wydania, wybierz pozycję **Edytuj**.

1. Wybierz **pozycję Upuść** z listy **Artefakty.** W poprzednich krokach potoku budowy, który zbadałeś produkuje dane wyjściowe używane dla artefaktu. 

1. Wybierz wyzwalacz **ciągłego wdrażania** po prawej stronie opcji **Upuszczanie.** Ten potok wydania ma włączony wyzwalacz dysku CD, który uruchamia wdrożenie, gdy dostępny jest nowy artefakt kompilacji. Można również wyłączyć wyzwalacz, aby wymagać ręcznego wykonywania wdrożeń.

1. Aby sprawdzić wszystkie zadania dotyczące potoku, wybierz pozycję **Zadania**. Wydanie ustawia środowisko kultywatyzatora, konfiguruje `imagePullSecrets` parametr, instaluje narzędzia Helm i wdraża wykresy Helm w klastrze Kubernetes.

1. Aby wyświetlić historię wydań, wybierz **pozycję Wyświetl wydania**.

1. Aby wyświetlić podsumowanie, wybierz pozycję **Zwolnij**. Wybierz dowolny z etapów, aby eksplorować wiele menu, takich jak podsumowanie wydania, skojarzone elementy robocze i testy. 

1. Wybierz pozycję **Zatwierdzenia**. W tym widoku przedstawiono zatwierdzenia kodu związane z tym wdrożeniem. Porównaj wersje, aby zobaczyć różnice zatwierdzania między wdrożeniami.

1. Wybierz **dzienniki**. Dzienniki zawierają przydatne informacje o wdrożeniu, które można wyświetlić podczas i po wdrożeniach.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Powiązane zasoby można usunąć, gdy nie są już potrzebne. Użyj funkcji usuwania na pulpicie nawigacyjnym Projektów DevOps.

## <a name="next-steps"></a>Następne kroki

Możesz zmodyfikować potoki kompilacji i wydania, aby dopasować je do potrzeb swojego zespołu. Można też użyć tego modelu ciągłej integracji/ciągłego wdrażania jako szablonu dla innych potoków.
