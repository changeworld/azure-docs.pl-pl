---
title: Zadanie usługi Azure DevOps w Eksploratorze danych platformy Azure
description: W tym temacie dowiesz się utworzyć potok tworzenia wersji i wdrożyć
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 0628d5c07d7258cc4d68727c364e65bd81c78e8e
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/30/2019
ms.locfileid: "66388990"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Zadanie usługi Azure DevOps w Eksploratorze danych platformy Azure

[Usługi Azure DevOps](https://azure.microsoft.com/services/devops/) zapewnia narzędzi do współpracy, takie jak potoki o wysokiej wydajności, bezpłatne prywatne repozytoria Git, konfigurowalne tablice Kanban i rozbudowane możliwości testowania automatycznego i ciągłego programowania. [Potoki usługi Azure](https://azure.microsoft.com/services/devops/pipelines/) jest możliwość DevOps platformy Azure, która pozwala na zarządzanie ciągłej integracji/ciągłego wdrażania umożliwia wdrażanie kodu w taki sposób, w przypadku potoków o wysokiej wydajności tę pracę za pomocą dowolnego języka, platformy i chmury.
[Eksplorator danych platformy Azure — polecenia administratora](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) jest zadanie potoki usługi Azure, która pozwala na tworzenie potoków wydania i wdrażanie bazy danych zmienia się na bazach danych Eksploratora danych usługi Azure. Jest dostępna bezpłatnie w [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

W tym dokumencie opisano prosty przykład korzystający z **Eksplorator danych platformy Azure — polecenia administratora** zadanie, aby wdrożyć schemat zmienia się z bazą danych. Pełne potoków ciągłej integracji/ciągłego wdrażania, można znaleźć [dokumentacji usługi Azure DevOps](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts).

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* Konfiguracja klastra Eksploratora danych usługi Azure:
    * [Klastra Azure Eksploratora danych i bazy danych](/azure/data-explorer/create-cluster-database-portal)
    * Tworzenie aplikacji usługi Azure Active Directory (Azure AD), [inicjowania obsługi administracyjnej aplikacji usługi Azure AD](/azure/kusto/management/access-control/how-to-provision-aad-app).
    * Udzielić dostępu do aplikacji usługi Azure AD w bazie danych przez Eksploratora usługi Azure Data [Zarządzanie uprawnieniami bazy danych Azure Eksplorator danych](/azure/data-explorer/manage-database-permissions).
* Konfiguracja usługi Azure DevOps:
    * [Załóż bezpłatne organizacji](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Utwórz organizację](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Tworzenie projektu w DevOps platformy Azure](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Kod za pomocą narzędzia Git](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Tworzenie folderów

Utwórz następujące foldery przykładowe (*funkcje*, *zasady*, *tabel*) w repozytorium Git. Skopiuj pliki z [tutaj](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) do odpowiednich folderów wyświetlanego poniżej i zatwierdź zmiany. Przykładowe pliki znajdują się wykonać poniższy przepływ pracy.

![Tworzenie folderów](media/devops/create-folders.png)

> [!TIP]
> Podczas tworzenia własnego przepływu pracy, firma Microsoft zaleca, dzięki czemu idempotentne Twojego kodu. Na przykład użyć [tabeli scalania .create](/azure/kusto/management/tables#create-merge-tables) zamiast [tabeli .create](/azure/kusto/management/tables#create-table)i użyj [.create lub alter](/azure/kusto/management/functions#create-or-alter-function) zamiast funkcji [.create](/azure/kusto/management/functions#create-function) Funkcja.

## <a name="create-a-release-pipeline"></a>Tworzenie potoku tworzenia wersji

1. Zaloguj się do Twojej [DevOps platformy Azure organizacji](https://dev.azure.com/).
1. Wybierz **potoki** > **wersji** z menu po lewej stronie i wybierz przycisk **nowy potok**.

    ![Nowy potok](media/devops/new-pipeline.png)

1. **Potoku wydania nowe** zostanie otwarte okno. W **potoki** na karcie **wybierz szablon** okienku wybierz **puste zadanie**.

     ![Wybierz szablon](media/devops/select-template.png)

1. Wybierz **etapu** przycisku. W **etapu** okienko, Dodaj **Nazwa etapu**. Wybierz **Zapisz** można zapisać swój potok.

    ![Nazwa etapu](media/devops/stage-name.png)

1. Wybierz **Dodawanie artefaktu** przycisku. W **Dodawanie artefaktu** okienku wybierz repozytorium, w której istnieje kodu, wprowadź odpowiednie informacje i kliknij przycisk **Dodaj**. Wybierz **Zapisz** można zapisać swój potok.

    ![Dodawanie artefaktu](media/devops/add-artifact.png)

1. W **zmienne** zaznacz **+ Dodaj** utworzyć zmienną **adresu URL punktu końcowego** który będzie używany w zadaniu. Zapis **nazwa** i **wartość** punktu końcowego. Wybierz **Zapisz** można zapisać swój potok. 

    ![Utwórz zmienną](media/devops/create-variable.png)

    Można znaleźć usługi Endpoint_URL na stronie Przegląd usługi **klastra Eksploratora danych usługi Azure** w witrynie Azure portal zawiera identyfikator URI klastra Eksploratora danych usługi Azure. Konstruowania identyfikatorów URI w następującym formacie `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`.  Na przykład https:\//kustodocs.westus.kusto.windows.net?DatabaseName=SampleDB

    ![Usługa Azure identyfikator URI klastra Eksplorator danych](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Tworzenie zadania w celu wdrożenia

1. W **potoku** kartę, kliknij pozycję **zadanie 1, 0 zadanie** można dodać zadania. 

    ![Dodaj zadania](media/devops/add-task.png)

1. Utwórz trzy zadania w celu wdrożenia **tabel**, **funkcje**, i **zasady**, w następującej kolejności. 

1. W **zadania** zaznacz **+** przez **zadania agenta**. Wyszukaj pozycję **Azure Data Explorer**. W **Marketplace**, zainstaluj **Eksplorator danych platformy Azure — polecenia administratora** rozszerzenia. Następnie wybierz **Dodaj** w **Uruchom polecenie Eksploratora danych usługi Azure**.

     ![Dodano polecenia administratora](media/devops/add-admin-commands.png)

1. Kliknij pozycję **polecenia Kusto** po lewej i aktualizacji zadań z poniższymi informacjami:
    * **Nazwa wyświetlana**: Nazwa zadania
    * **Ścieżka pliku**: W **tabel** zadań, określić */Tables/* .csl, ponieważ pliki tworzenia tabeli znajdują się w *tabeli* folderu.
    * **Adres URL punktu końcowego**: wprowadź `EndPoint URL`zmiennej utworzonej w poprzednim kroku.
    * Wybierz **punktu końcowego usługi** i wybierz **+ nowy**.

    ![Aktualizacja zadania polecenia Kusto](media/devops/kusto-command-task.png)

1. Podaj następujące informacje w **połączenia z usługą Dodaj Eksploratora usługi Azure Data** okna:

    |Ustawienie  |Sugerowana wartość  |
    |---------|---------|
    |**Nazwa połączenia**     |    Wprowadź nazwę identyfikującą ten punkt końcowy usługi     |
    |**Adres Url klastra**    |    Wartość można znaleźć w sekcji Przegląd klastra Eksploratora danych usługi Azure w witrynie Azure portal | 
    |**Identyfikator jednostki usługi**    |    Wprowadź identyfikator aplikacji usługi AAD (utworzonego jako warunek wstępny)     |
    |**Klucz aplikacji jednostki usługi**     |    Wprowadź klucz aplikacji usługi AAD (utworzonego jako warunek wstępny)    |
    |**Identyfikator dzierżawy usługi AAD**    |      Wprowadź dzierżawy usługi AAD (np. microsoft.com, contoso.com...)    |

    Wybierz **Zezwalaj na wszystkie potoki, aby korzystać z tego połączenia** pola wyboru. Kliknij przycisk **OK**.

    ![Dodawanie połączenia z usługą](media/devops/add-service-connection.png)

1. Powtórz kroki od 1 do 5 innego dwa razy, aby wdrożyć pliki z *funkcje* i *zasady* folderów. Wybierz pozycję **Zapisz**. W **zadania** kartę, zobacz te trzy zadania utworzone: **Wdrażanie tabel**, **wdrażanie funkcji**, i **wdrożenie zasad**.

    ![Wdrażanie wszystkich folderów](media/devops/deploy-all-folders.png)

1. Wybierz **+ wydanie** > **utworzyć wersję** tworzenie wydań.

    ![Tworzenie wydania](media/devops/create-release.png)

1. W **dzienniki** kartę, należy sprawdzić stan wdrożenia zakończy się pomyślnie.

    ![Wdrożenie powiodło się](media/devops/deployment-successful.png)

Ukończono tworzenie potoku wydania na potrzeby wdrażania trzy zadania podrzędne do produkcji wstępnej.