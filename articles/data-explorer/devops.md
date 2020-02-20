---
title: Zadanie usługi Azure DevOps dla platformy Azure Eksplorator danych
description: W tym temacie dowiesz się, jak utworzyć potok wydania i wdrożyć
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: jasonh
ms.service: data-explorer
ms.topic: conceptual
ms.date: 05/05/2019
ms.openlocfilehash: 1e44a7e71858f028b798720c5505eacbfe8c2332
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77472047"
---
# <a name="azure-devops-task-for-azure-data-explorer"></a>Zadanie usługi Azure DevOps dla platformy Azure Eksplorator danych

[Azure DevOps Services](https://azure.microsoft.com/services/devops/) udostępnia narzędzia do współpracy deweloperskiej, takie jak potoki o wysokiej wydajności, bezpłatne prywatne repozytoria Git, konfigurowalne tablice Kanban i zaawansowane możliwości testowania automatycznego i ciągłego. [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/) to funkcja platformy Azure DevOps, która umożliwia zarządzanie ciągłej integracji/ciągłego wdrażania kodu przy użyciu potoków o wysokiej wydajności, które działają z dowolnym językiem, platformą i chmurą.
[Azure Eksplorator danych — polecenia administratora](https://marketplace.visualstudio.com/items?itemName=Azure-Kusto.PublishToADX) to Azure Pipelines zadanie, które umożliwia tworzenie potoków wydań i wdrażanie zmian bazy danych w bazach danych usługi Azure Eksplorator danych. Jest ona dostępna bezpłatnie w [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

W tym dokumencie opisano prosty przykład użycia zadania **Eksplorator danych platformy Azure — administrator** do wdrażania zmian schematu w bazie danych programu. Aby uzyskać kompletne potoki ciągłej integracji/ciągłego wdrażania, zapoznaj się z [dokumentacją usługi Azure DevOps](/azure/devops/user-guide/what-is-azure-devops?view=azure-devops#vsts).

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto platformy Azure](https://azure.microsoft.com/free/).
* Konfiguracja klastra usługi Azure Eksplorator danych:
    * [Klaster Eksplorator danych i baza danych platformy Azure](/azure/data-explorer/create-cluster-database-portal)
    * Tworzenie aplikacji Azure Active Directory (Azure AD) przez [zainicjowanie obsługi aplikacji usługi Azure AD](/azure/kusto/management/access-control/how-to-provision-aad-app).
    * Przyznaj dostęp do aplikacja usługi Azure AD w usłudze Azure Eksplorator danych Database, [zarządzając uprawnieniami azure Eksplorator danych Database](/azure/data-explorer/manage-database-permissions).
* Konfiguracja usługi Azure DevOps:
    * [Utwórz konto w bezpłatnej organizacji](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops)
    * [Tworzenie organizacji](/azure/devops/organizations/accounts/create-organization?view=azure-devops)
    * [Tworzenie projektu na platformie Azure DevOps](/azure/devops/organizations/projects/create-project?view=azure-devops)
    * [Kod w usłudze git](/azure/devops/user-guide/code-with-git?view=azure-devops)

## <a name="create-folders"></a>Tworzenie folderów

Utwórz następujące przykładowe foldery (*funkcje*, *zasady*, *tabele*) w repozytorium git. Skopiuj pliki z tego [miejsca](https://github.com/Azure/azure-kusto-docs-samples/tree/master/DevOps_release_pipeline) do odpowiednich folderów, jak pokazano poniżej, i zatwierdź zmiany. Pliki przykładowe są dostarczane w celu wykonania poniższego przepływu pracy.

![Tworzenie folderów](media/devops/create-folders.png)

> [!TIP]
> Podczas tworzenia własnego przepływu pracy zalecamy utworzenie kodu idempotentne. Można na przykład użyć [. Create-Scale Table](/azure/kusto/management/create-table-command#create-merge-table) zamiast [. Create Table](/azure/kusto/management/create-table-command)i USE [. Create-lub-ALTER](/azure/kusto/management/create-alter-function) zamiast. [Create](/azure/kusto/management/create-function) .

## <a name="create-a-release-pipeline"></a>Tworzenie potoku wydania

1. Zaloguj się do swojej [organizacji usługi Azure DevOps](https://dev.azure.com/).
1. Wybierz pozycję **potoki** > **wersje** z menu po lewej stronie i wybierz pozycję **Nowy potok**.

    ![Nowy potok](media/devops/new-pipeline.png)

1. Zostanie otwarte okno **nowe potoku wydania** . Na karcie **potoki** w okienku **Wybierz szablon** wybierz pozycję **puste zadanie**.

     ![Wybierz szablon](media/devops/select-template.png)

1. Wybierz przycisk **etapu** . W okienku **etap** Dodaj **nazwę etapu**. Wybierz pozycję **Zapisz** , aby zapisać potok.

    ![Nadaj nazwę etapowi](media/devops/stage-name.png)

1. Wybierz przycisk **Dodaj artefakt** . W okienku **Dodawanie artefaktu** wybierz repozytorium, w którym znajduje się kod, podaj odpowiednie informacje i kliknij przycisk **Dodaj**. Wybierz pozycję **Zapisz** , aby zapisać potok.

    ![Dodawanie artefaktu](media/devops/add-artifact.png)

1. Na karcie **zmienne** wybierz pozycję **+ Dodaj** , aby utworzyć zmienną dla **adresu URL punktu końcowego** , który będzie używany w zadaniu. Napisz **nazwę** i **wartość** punktu końcowego. Wybierz pozycję **Zapisz** , aby zapisać potok. 

    ![Utwórz zmienną](media/devops/create-variable.png)

    Aby znaleźć Endpoint_URL, Strona przegląd **klastra Eksplorator danych platformy Azure** w Azure Portal zawiera identyfikator URI klastra Eksplorator danych platformy Azure. Konstruowanie identyfikatora URI w następującym formacie `https://<Azure Data Explorer cluster URI>?DatabaseName=<DBName>`.  Na przykład https:\//kustodocs.westus.Kusto.Windows.NET? DatabaseName = SampleDB

    ![Identyfikator URI klastra usługi Azure Eksplorator danych](media/devops/adx-cluster-uri.png)

## <a name="create-tasks-to-deploy"></a>Utwórz zadania do wdrożenia

1. Na karcie **potok** kliknij **1 zadanie, 0 zadanie,** aby dodać zadania. 

    ![Dodaj zadania](media/devops/add-task.png)

1. Utwórz trzy zadania do wdrożenia **tabel**, **funkcji**i **zasad**w tej kolejności. 

1. Na karcie **zadania** wybierz pozycję **+** według **zadania agenta**. Wyszukaj pozycję **Azure Data Explorer**. W **witrynie Marketplace**Zainstaluj rozszerzenie **polecenia platformy Azure Eksplorator danych — administrator** . Następnie wybierz pozycję **Dodaj** w obszarze **Uruchom Eksplorator danych platformy Azure**.

     ![Dodawanie poleceń administratora](media/devops/add-admin-commands.png)

1. Kliknij **polecenie Kusto** po lewej stronie i zaktualizuj zadanie przy użyciu następujących informacji:
    * **Nazwa wyświetlana**: Nazwa zadania
    * **Ścieżka pliku**: w obszarze zadania w **tabelach** należy określić */Tables/* . CSL, ponieważ pliki tworzenia tabel znajdują się w folderze *tabeli* .
    * **Adres URL punktu końcowego**: wprowadź zmienną `EndPoint URL`utworzoną w poprzednim kroku.
    * Wybierz pozycję **Użyj punktu końcowego usługi** i wybierz pozycję **+ Nowy**.

    ![Aktualizuj zadanie polecenia Kusto](media/devops/kusto-command-task.png)

1. Wykonaj następujące informacje w oknie **Dodawanie połączenia z usługą Azure Eksplorator danych** :

    |Ustawienie  |Sugerowana wartość  |
    |---------|---------|
    |**Nazwa połączenia**     |    Wprowadź nazwę identyfikującą ten punkt końcowy usługi     |
    |**Adres URL klastra**    |    Wartość można znaleźć w sekcji Przegląd klastra Eksplorator danych platformy Azure w Azure Portal | 
    |**Identyfikator jednostki usługi**    |    Wprowadź identyfikator aplikacji usługi AAD (utworzony jako warunek wstępny)     |
    |**Klucz aplikacji głównej usługi**     |    Wprowadź klucz aplikacji usługi AAD (utworzony jako warunek wstępny)    |
    |**Identyfikator dzierżawy usługi AAD**    |      Wprowadź dzierżawcę usługi AAD (na przykład microsoft.com, contoso.com...)    |

    Zaznacz pole wyboru **Zezwalaj wszystkim potokom na korzystanie z tego połączenia** . Kliknij przycisk **OK**.

    ![Dodaj połączenie usługi](media/devops/add-service-connection.png)

1. Powtórz kroki 1-5 jeszcze dwa razy, aby wdrożyć pliki z folderów *funkcje* i *zasady* . Wybierz pozycję **Zapisz**. Na karcie **zadania** zapoznaj się z trzema utworzonymi zadaniami: **Wdróż tabele**, **Wdróż funkcje**i **Wdróż zasady**.

    ![Wdróż wszystkie foldery](media/devops/deploy-all-folders.png)

1. Wybierz pozycję **+ wersja** > **Utwórz wydanie** , aby utworzyć wydanie.

    ![Tworzenie wersji](media/devops/create-release.png)

1. Na karcie **dzienniki** Sprawdź, czy stan wdrożenia zakończył się pomyślnie.

    ![Wdrożenie zakończyło się pomyślnie](media/devops/deployment-successful.png)

Teraz ukończono tworzenie potoku wydania dla wdrożenia trzech zadań do przedprodukcyjnego.