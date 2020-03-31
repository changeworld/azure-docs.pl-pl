---
title: Zadanie azure devops dla Eksploratora danych platformy Azure
description: W tym temacie nauczysz się tworzyć potok wersji i wdrażać
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 1e44a7e71858f028b798720c5505eacbfe8c2332
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472047"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Zadanie azure devops dla Eksploratora danych platformy Azure

[Usługa Azure DevOps Services](https://azure.microsoft.com/services/devops/) udostępnia narzędzia do współpracy deweloperów, takie jak potoki o wysokiej wydajności, bezpłatne prywatne repozytoria Git, konfigurowalne karty Kanban oraz rozbudowane funkcje automatycznego i ciągłego testowania. [Usługa Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) to funkcja Azure DevOps, która umożliwia zarządzanie ciągłą integracją/dyskiem CD w celu wdrożenia kodu za pomocą potoków o wysokiej wydajności, które działają z dowolnym językiem, platformą i chmurą.
[Usługa Azure Data Explorer — polecenia administratora](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) to zadanie potoków platformy Azure, które umożliwia tworzenie potoków wersji i wdrażanie zmian bazy danych w bazach danych usługi Azure Data Explorer. Jest on dostępny za darmo w [visual studio marketplace](https://marketplace.visualstudio.com/).

W tym dokumencie opisano prosty przykład użycia zadania **Eksploratora danych platformy Azure — polecenia administracyjne** w celu wdrożenia zmian schematu w bazie danych. Aby uzyskać kompletne potoki ciągłej integracji/ciągłego wdrażania, zapoznaj się z [dokumentacją usługi Azure DevOps](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts).

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/) przed rozpoczęciem.
* Konfiguracja klastra usługi Azure Data Explorer:
    * [Klaster i baza danych usługi Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal)
    * Utwórz aplikację usługi Azure Active Directory (Azure AD) przez [inicjowanie obsługi administracyjnej aplikacji usługi Azure AD.](/azure/kusto/management/access-control/how-to-provision-aad-app)
    * Udziel dostępu do aplikacji usługi Azure AD w bazie danych Usługi Azure Data Explorer, [zarządzając uprawnieniami bazy danych Usługi Azure Data Explorer.](/azure/data-explorer/manage-database-permissions)
* Konfiguracja programu Azure DevOps:
    * [Zarejestruj się w bezpłatnej organizacji](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Tworzenie organizacji](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Tworzenie projektu w usłudze Azure DevOps](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Kod z Git](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Tworzenie folderów

Utwórz następujące przykładowe foldery (*Funkcje*, *Zasady*, *Tabele*) w repozytorium Git. Skopiuj pliki [z tego miejsca](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) do odpowiednich folderów, jak widać poniżej i zaobję zmiany. Przykładowe pliki są dostarczane do wykonania następującego przepływu pracy.

![Tworzenie folderów](media/devops/create-folders.png)

> [!TIP]
> Podczas tworzenia własnego przepływu pracy, zaleca się, aby kod idempotent. Na przykład zamiast [tabeli tworzenia i scalania](/azure/kusto/management/create-table-command#create-merge-table) należy użyć funkcji .create-merge zamiast funkcji [.create table](/azure/kusto/management/create-table-command) [.create-merge.](/azure/kusto/management/create-function) [.create-or-alter](/azure/kusto/management/create-alter-function)

## <a name="create-a-release-pipeline"></a>Tworzenie potoku wydania

1. Zaloguj się do [organizacji Azure DevOps](https://dev.azure.com/).
1. Wybierz **pozycję Wersje potoków** > **Releases** z menu po lewej stronie i wybierz pozycję **Nowy potok**.

    ![Nowy potok](media/devops/new-pipeline.png)

1. Zostanie otwarte okno **Potok nowej wersji.** Na karcie **Potoki** w okienku **Wybieranie szablonu** wybierz pozycję **Puste zadanie**.

     ![Wybieranie szablonu](media/devops/select-template.png)

1. Wybierz przycisk **Stołu montażego.** W **okienku Stół montażowy** dodaj **nazwę stołu montażowego**. Wybierz **pozycję Zapisz,** aby zapisać potok.

    ![Nadawanie nazwy scenie](media/devops/stage-name.png)

1. Wybierz pozycję Dodaj przycisk **artefaktu.** W okienku **Dodawanie artefaktów** wybierz repozytorium, w którym istnieje kod, wypełnij odpowiednie informacje i kliknij przycisk **Dodaj**. Wybierz **pozycję Zapisz,** aby zapisać potok.

    ![Dodawanie artefaktu](media/devops/add-artifact.png)

1. Na karcie **Zmienne** wybierz pozycję **+ Dodaj,** aby utworzyć zmienną dla **adresu URL punktu końcowego,** która będzie używana w zadaniu. Wpisz **nazwę** i **wartość** punktu końcowego. Wybierz **pozycję Zapisz,** aby zapisać potok. 

    ![Tworzenie zmiennej](media/devops/create-variable.png)

    Aby znaleźć Endpoint_URL, strona przeglądu **klastra usługi Azure Data Explorer** w witrynie Azure portal zawiera identyfikator URI klastra usługi Azure Data Explorer. Skonstruuj identyfikator `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`URI w następującym formacie .  Na przykład https:\//kustodocs.westus.kusto.windows.net?DatabaseName=SampleDB

    ![Identyfikator URI klastra usługi Azure Data Explorer](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Tworzenie zadań do wdrożenia

1. Na karcie **Potok** kliknij **1 zadanie, 0,** aby dodać zadania. 

    ![Dodawanie zadań](media/devops/add-task.png)

1. Utwórz trzy zadania, aby wdrożyć **tabele,** **funkcje**i **zasady**, w tej kolejności. 

1. Na karcie **Zadania** **+** wybierz pozycję Według **zadania agenta**. Wyszukaj pozycję **Azure Data Explorer**. W **portalu Marketplace**zainstaluj rozszerzenie Azure Data Explorer – Admin **Commands.** Następnie wybierz pozycję **Dodaj** w **poleceniu Eksploratora danych platformy Azure**.

     ![Dodawanie poleceń administratora](media/devops/add-admin-commands.png)

1. Kliknij na **Kusto Command** po lewej stronie i zaktualizuj zadanie następującymi informacjami:
    * **Nazwa wyświetlana**: Nazwa zadania
    * **Ścieżka pliku**: W zadaniu **Tabele** określ */Tables/*.csl, ponieważ pliki tworzenia tabeli znajdują się w folderze *Tabela.*
    * **Adres URL punktu końcowego:** wprowadź zmienną utworzoną `EndPoint URL`w poprzednim kroku.
    * Wybierz **pozycję Użyj punktu końcowego usługi** i wybierz pozycję + **Nowy**.

    ![Aktualizuj zadanie polecenia Kusto](media/devops/kusto-command-task.png)

1. Wykonaj następujące informacje w oknie **Dodawania połączenia usługi Usługi Usługi Azure Data Explorer:**

    |Ustawienie  |Sugerowana wartość  |
    |---------|---------|
    |**Nazwa połączenia**     |    Wprowadź nazwę, aby zidentyfikować ten punkt końcowy usługi     |
    |**Adres URL klastra**    |    Wartość można znaleźć w sekcji przeglądowej klastra eksploratora danych platformy Azure w witrynie Azure portal | 
    |**Identyfikator jednostki usługi**    |    Wprowadź identyfikator aplikacji AAD (utworzony jako warunek wstępny)     |
    |**Klucz aplikacji głównego usługi**     |    Wprowadź klucz aplikacji AAD (utworzony jako warunek wstępny)    |
    |**Identyfikator dzierżawy usługi AAD**    |      Wprowadź dzierżawę usługi AAD (na przykład microsoft.com, contoso.com...)    |

    Wybierz **opcję Zezwalaj wszystkim potokom na użycie tego pola wyboru połączenia.** Kliknij przycisk **OK**.

    ![Dodawanie połączenia usługi](media/devops/add-service-connection.png)

1. Powtórz kroki 1-5 jeszcze dwa razy, aby wdrożyć pliki z folderów *Funkcje* i *zasady.* Wybierz **pozycję Zapisz**. Na karcie **Zadania** zobacz trzy utworzone zadania: **Wdrażanie tabel,** **Wdrażanie funkcji**i **Wdrażanie zasad**.

    ![Wdrażanie wszystkich folderów](media/devops/deploy-all-folders.png)

1. Wybierz **+ Zwolnij** > **utwórz zwolnienie,** aby utworzyć wydanie.

    ![Tworzenie wydania](media/devops/create-release.png)

1. Na karcie **Dzienniki** sprawdź, czy stan wdrożenia jest pomyślny.

    ![Wdrożenie zakończyło się pomyślnie](media/devops/deployment-successful.png)

Ukończono już tworzenie potoku wersji do wdrożenia trzech zadań do przedprodukcji.