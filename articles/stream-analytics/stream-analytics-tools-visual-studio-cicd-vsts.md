---
title: Wdrażanie zadania usługi Azure Stream Analytics z ciągłą integracją/ciągłym wdrażaniem przy użyciu usługi VSTS — samouczek
description: W tym artykule opisano, jak wdrożyć zadanie usługi Stream Analytics z ciągłą integracją/ciągłym wdrażaniem przy użyciu usługi VSTS.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 7/10/2018
ms.openlocfilehash: d4f1e188a1a145ba3be5fb45d2b0ea4d0bfd57a7
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/15/2018
ms.locfileid: "41920888"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-vsts"></a>Samouczek: wdrażanie zadania usługi Azure Stream Analytics z ciągłą integracją/ciągłym wdrażaniem przy użyciu usługi VSTS
W tym samouczku opisano konfigurowanie ciągłej integracji i ciągłego wdrażania na potrzeby zadania usługi Azure Stream Analytics przy użyciu usługi Visual Studio Team Services. 

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie kontroli źródła do projektu
> * Tworzenie definicji kompilacji w usłudze Team Services
> * Tworzenie definicji wydania w usłudze Team Services
> * Automatyczne wdrażanie i uaktualnianie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne
Przed rozpoczęciem upewnij się, że dysponujesz następującymi elementami:

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Zainstaluj program [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) i obciążenie **Programowanie na platformie Azure** lub **Magazynowanie i przetwarzanie danych**.
* Utwórz [projekt usługi Stream Analytics w programie Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-vs).
* Utwórz konto [Visual Studio Team Services](https://visualstudio.microsoft.com/team-services/).

## <a name="configure-nuget-package-dependency"></a>Konfigurowanie zależności pakietu NuGet
W celu wykonania automatycznej kompilacji i automatycznego wdrożenia na dowolnej maszynie, musisz użyć pakietu NuGet `Microsoft.Azure.StreamAnalytics.CICD`. Zapewnia on program MSBuild, uruchamianie lokalne oraz narzędzia wdrażania, które obsługują proces ciągłej integracji i ciągłego wdrażania projektów programu Visual Studio dla usługi Stream Analytics. Aby uzyskać więcej informacji, zobacz [Stream Analytics CI/CD tools (Narzędzia ciągłej integracji/ciągłego wdrażania usługi Stream Analytics)](stream-analytics-tools-for-visual-studio-cicd.md).

Dodaj plik **packages.config** do katalogu projektu.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>Udostępnianie rozwiązania programu Visual Studio w nowym repozytorium Git w usłudze Team Services
Udostępnij pliki źródłowe aplikacji w projekcie zespołowym usługi Team Services, aby umożliwić generowanie kompilacji.  

1. Utwórz nowe lokalne repozytorium Git dla projektu, wybierając pozycję **Dodaj do kontroli źródła**, a następnie pozycję **Git** na pasku stanu w prawym dolnym rogu programu Visual Studio. 

2. W widoku **Synchronizacja** w programie **Team Explorer** wybierz przycisk **Opublikuj repozytorium Git** w obszarze **Wypychanie do usługi Visual Studio Team Services**.

   ![Wypychanie repozytorium Git](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishgitrepo.png)

3. Sprawdź swój adres e-mail i z listy rozwijanej **Domena usługi Team Services** wybierz swoje konto. Wprowadź nazwę repozytorium i wybierz polecenie **Publikuj repozytorium**.

   ![Wypychanie repozytorium Git](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishcode.png)

    Opublikowanie repozytorium powoduje utworzenie nowego projektu zespołowego na Twoim koncie o takiej samej nazwie co lokalne repozytorium. Aby utworzyć repozytorium w istniejącym projekcie zespołowym, kliknij pozycję **Zaawansowane** obok pola **Nazwa repozytorium**, a następnie wybierz projekt zespołowy. Aby wyświetlić kod w przeglądarce, wybierz polecenie **Wyświetl w Internecie**.
 
## <a name="configure-continuous-delivery-with-vsts"></a>Konfigurowanie ciągłego dostarczania za pomocą usługi VSTS
Definicja kompilacji usługi Team Services zawiera opis przepływu pracy, który składa się z kroków kompilacji wykonywanych sekwencyjnie. Dowiedz się więcej na temat [definicji kompilacji usługi Team Services](https://www.visualstudio.com/docs/build/define/create). 

Definicja wydania usługi Team Services opisuje przepływ pracy, który wdraża pakiet aplikacji w klastrze. Jednoczesne użycie definicji kompilacji i definicji wydania powoduje wykonanie całego przepływu pracy, zaczynając od plików źródłowych, a kończąc na aplikacji uruchomionej w klastrze. Dowiedz się więcej na temat[definicji wydania](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition) usługi Team Services.

### <a name="create-a-build-definition"></a>Tworzenie definicji kompilacji
Otwórz przeglądarkę internetową i przejdź do nowo utworzonego projektu zespołowego w usłudze [Visual Studio Team Services](https://app.vsaex.visualstudio.com/). 

1. Na karcie **Kompilowanie i wydawanie** wybierz pozycję **Kompilacje**, a następnie opcję **+Nowa**.  Wybierz pozycję **VSTS Git** i przycisk **Kontynuuj**.
    
    ![Wybierz źródło](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source.png)

2. W obszarze **Wybierz szablon** kliknij pozycję **Pusty proces**, aby rozpocząć od pustej definicji.
    
    ![Wybieranie szablonu kompilacji](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template.png)

3. W obszarze **Wyzwalacze** włącz ciągłą integrację, zaznaczając stan wyzwalacza **Włącz ciągłą integrację**.  Wybierz pozycję **Zapisz i dodaj do kolejki**, aby ręcznie uruchomić kompilację. 
    
    ![Stan wyzwalacza](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger.png)

4. Kompilacje są również wyzwalane przez wypchnięcie lub zaewidencjonowanie. Aby sprawdzić postęp kompilacji, przejdź do karty **Kompilacje**.  Po upewnieniu się, że kompilacja jest wykonywana prawidłowo, musisz określić definicję wydania, która wdraża aplikację w klastrze. Kliknij prawym przyciskiem myszy wielokropek obok definicji kompilacji i wybierz polecenie **Edytuj**.

5.  W obszarze **Zadania** wprowadź wartość „Hostowana” w polu **Kolejka agentów**.
    
    ![Wybieranie kolejki agentów](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue.png) 

6. W obszarze **Faza 1** kliknij znak **+** i dodaj zadanie **NuGet**.
    
    ![Dodawanie zadania NuGet](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget.png)

7. Rozwiń sekcję **Zaawansowane** i dodaj ścieżkę `$(Build.SourcesDirectory)\packages` w polu **Katalog docelowy**. Pozostałe wartości domyślne konfiguracji NuGet pozostaw bez zmian.

   ![Konfigurowanie zadania NuGet](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-config.png)

8. W obszarze **Faza 1** kliknij znak **+** i dodaj zadanie **MSBuild**.

   ![Dodawanie zadania MSBuild](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild-task.png)

9. Zmień wartość w polu **Argumenty MSBuild** na następującą:

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![Konfigurowanie zadania MSBuild](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild.png)

10. W obszarze **Faza 1** kliknij znak **+** i dodaj zadanie **Wdrożenie grupy zasobów platformy Azure**. 
    
    ![Dodawanie zadania Wdrożenie grupy zasobów platformy Azure](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy.png)

11. Rozwiń pozycję **Szczegóły platformy Azure** i wypełnij konfigurację następującymi informacjami:
    
    |**Ustawienie**  |**Sugerowana wartość**  |
    |---------|---------|
    |Subskrypcja  |  Wybierz subskrypcję.   |
    |Akcja  |  Utwórz lub zaktualizuj grupę zasobów   |
    |Grupa zasobów  |  Wprowadź nazwę grupy zasobów.   |
    |Szablon  | [ścieżka do Twojego rozwiązania]\bin\Debug\Deploy\\[nazwa Twojego projektu].JobTemplate.json   |
    |Parametry szablonu  | [ścieżka do Twojego rozwiązania]\bin\Debug\Deploy\\[nazwa Twojego projektu].JobTemplate.parameters.json   |
    |Zastąp parametry szablonu  | W polu tekstowym wpisz parametry szablonu, które mają zostać zastąpione. Na przykład –storageName fabrikam –adminUsername $(vmusername) –adminPassword $(password) –azureKeyVaultName $(fabrikamFibre). Ta właściwość jest opcjonalna, ale Twoja kompilacja będzie powodować błędy, jeśli kluczowe parametry nie zostaną zastąpione.    |
    
    ![Ustawianie właściwości](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy-2.png)

12. Kliknij przycisk **Zapisz i dodaj do kolejki**, aby przetestować definicję kompilacji.
    
    ![Ustawianie zastępowania parametrów](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-queue.png)

### <a name="failed-build-process"></a>Niepowodzenie procesu kompilacji
Jeśli parametry szablonu nie zostały zastąpione w zadaniu **Wdrożenie grupy zasobów platformy Azure** Twojej definicji kompilacji, mogą wystąpić błędy dotyczące parametrów wdrożenia mających wartość null. Wróć do definicji kompilacji i zastąp parametry mające wartość null, aby naprawić błąd.

   ![Proces kompilacji nie powiódł się](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>Zatwierdzanie i wypychanie zmian w celu wyzwolenia wydania
Upewnij się, że potok ciągłej integracji działa prawidłowo, ewidencjonując jakieś zmiany kodu w usłudze Team Services.    

Podczas pisania kodu zmiany są automatycznie śledzone przez program Visual Studio. Zatwierdź zmiany w lokalnym repozytorium Git, wybierając ikonę oczekujących zmian na pasku stanu w prawym dolnym rogu.

1. W widoku **Zmiany** programu Team Explorer dodaj komunikat opisujący aktualizację i zatwierdź wprowadzone zmiany.

    ![Zatwierdzanie i wypychanie zmian](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes.png)

2. Wybierz ikonę paska stanu nieopublikowanych zmian lub widok synchronizacji w programie Team Explorer. Wybierz pozycję **Wypchnij**, aby zaktualizować kod w usłudze Team Services lub na serwerze TFS.

    ![Zatwierdzanie i wypychanie zmian](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-2.png)

Wypychanie zmian do usługi Team Services automatycznie wyzwala kompilację.  Po pomyślnym zakończeniu definicji kompilacji automatycznie tworzone jest wydanie, które rozpoczyna aktualizację zadania w klastrze.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy grupa zasobów, zadanie przesyłania strumieniowego i wszystkie pokrewne zasoby nie będą już potrzebne, usuń je. Usunięcie zadania pozwala uniknąć opłat za jednostki przesyłania strumieniowego zużywane przez zadanie. Jeśli planujesz użycie zadania w przyszłości, możesz zatrzymać je i uruchomić ponownie później w razie potrzeby. Jeśli nie zamierzasz w przyszłości korzystać z tego zadania, wykonaj następujące kroki, aby usunąć wszystkie zasoby utworzone w ramach tego samouczka:

1. W menu znajdującym się po lewej stronie w witrynie Azure Portal kliknij pozycję **Grupy zasobów**, a następnie kliknij nazwę utworzonego zasobu.  
2. Na stronie grupy zasobów kliknij pozycję **Usuń**, wpisz w polu tekstowym nazwę zasobu do usunięcia, a następnie kliknij pozycję **Usuń**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat konfigurowania procesu ciągłej integracji i ciągłego wdrażania przy użyciu narzędzi Azure Stream Analytics Tools for Visual Studio, przejdź do artykułu na temat konfigurowania potoku ciągłej integracji/ciągłego wdrażania:

> [!div class="nextstepaction"]
> [Ciągła integracja i ciągłe opracowywanie za pomocą narzędzi usługi Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)