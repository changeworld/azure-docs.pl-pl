---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/03/2019
ms.author: orspodek
ms.openlocfilehash: f8b3e541dfd55bbd613af2791329a08402cf8670
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581975"
---
## <a name="configure-the-data-source"></a>Konfigurowanie źródła danych

Aby skonfigurować Eksplorator danych platformy Azure jako źródło danych dla narzędzia pulpitu nawigacyjnego, wykonaj następujące czynności. Te kroki zostaną omówione bardziej szczegółowo w tej sekcji:

1. Utwórz nazwę główną usługi Azure Active Directory (Azure AD). Nazwa główna usługi jest używana przez narzędzie pulpitu nawigacyjnego do uzyskiwania dostępu do usługi Azure Eksplorator danych.

1. Dodaj nazwę główną usługi Azure AD do roli *osoby przeglądające* w bazie danych Azure Eksplorator danych.

1. Określ właściwości połączenia narzędzia pulpitu nawigacyjnego na podstawie informacji z jednostki usługi Azure AD, a następnie przetestuj połączenie.

### <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Nazwę główną usługi można utworzyć w [Azure Portal](#azure-portal) lub przy użyciu interfejsu wiersza polecenia [platformy Azure](#azure-cli) . Niezależnie od używanej metody, po utworzeniu uzyskasz wartości czterech właściwości połączenia, które będą używane w dalszych krokach.

#### <a name="azure-portal"></a>Azure Portal

1. Aby utworzyć jednostkę usługi, postępuj zgodnie z instrukcjami w [dokumentacji Azure Portal](/azure/active-directory/develop/howto-create-service-principal-portal).

    1. W sekcji [przypisywanie aplikacji do roli](/azure/active-directory/develop/howto-create-service-principal-portal#assign-the-application-to-a-role) Przypisz typ roli **czytnik** do klastra usługi Azure Eksplorator danych.

    1. W sekcji [pobieranie wartości dla logowania](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) Skopiuj trzy wartości właściwości omówione w krokach: **Identyfikator katalogu** (identyfikator dzierżawy), **Identyfikator aplikacji**i **hasło**.

1. W Azure Portal wybierz pozycję **subskrypcje** , a następnie skopiuj Identyfikator subskrypcji, w której utworzono nazwę główną usługi.

    ![Identyfikator subskrypcji — Portal](media/data-explorer-configure-data-source/subscription-id-portal.png)

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Tworzenie jednostki usługi. Ustaw odpowiedni zakres i typ roli `reader`.

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourDashboard}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Aby uzyskać więcej informacji, zobacz [Tworzenie jednostki usługi platformy Azure przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Polecenie zwraca zestaw wyników podobny do poniższego. Skopiuj trzy wartości właściwości: **Identyfikator aplikacji**, **hasło**i **dzierżawca**.


    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourDashboard}:{PortNumber}",
      "name": "https://{UrlToYourDashboard}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Pobierz listę subskrypcji.

    ```azurecli
    az account list --output table
    ```

    Skopiuj odpowiedni identyfikator subskrypcji.

    ![Identyfikator subskrypcji — interfejs wiersza polecenia](media/data-explorer-configure-data-source/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Dodawanie jednostki usługi do roli osoby przeglądające

Teraz, gdy masz nazwę główną usługi, Dodaj ją do roli *osoby przeglądające* w bazie danych Azure Eksplorator danych. To zadanie można wykonać w obszarze **uprawnienia** w Azure Portal lub w obszarze **zapytanie** przy użyciu polecenia zarządzania.

#### <a name="azure-portal---permissions"></a>Azure Portal — uprawnienia

1. W Azure Portal przejdź do klastra usługi Azure Eksplorator danych.

1. W sekcji **Przegląd** wybierz bazę danych z przykładowymi danymi StormEvents.

    ![Wybierz bazę danych](media/data-explorer-configure-data-source/select-database.png)

1. Wybierz pozycję **uprawnienia** , a następnie **Dodaj**.

    ![Uprawnienia bazy danych](media/data-explorer-configure-data-source/database-permissions.png)

1. W obszarze **Dodawanie uprawnień do bazy danych**wybierz rolę **przeglądarki** , a następnie **Wybierz pozycję podmioty zabezpieczeń**.

    ![Dodawanie uprawnień do bazy danych](media/data-explorer-configure-data-source/add-permission.png)

1. Wyszukaj utworzoną jednostkę usługi. Wybierz podmiot zabezpieczeń, a następnie **Wybierz pozycję**.

    ![Zarządzanie uprawnieniami w Azure Portal](media/data-explorer-configure-data-source/new-principals.png)

1. Wybierz pozycję **Zapisz**.

    ![Zarządzanie uprawnieniami w Azure Portal](media/data-explorer-configure-data-source/save-permission.png)

#### <a name="management-command---query"></a>Polecenie zarządzania — zapytanie

1. W Azure Portal przejdź do klastra usługi Azure Eksplorator danych, a następnie wybierz pozycję **zapytanie**.

    ![Zapytanie](media/data-explorer-configure-data-source/query.png)

1. Uruchom następujące polecenie w oknie zapytania. Użyj identyfikatora aplikacji i identyfikatora dzierżawy z Azure Portal lub interfejsu wiersza polecenia.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    Polecenie zwraca zestaw wyników podobny do poniższego. W tym przykładzie pierwszy wiersz dotyczy istniejącego użytkownika w bazie danych, a drugi wiersz jest dla jednostki usługi, która została właśnie dodana.

    ![Zestaw wyników](media/data-explorer-configure-data-source/result-set.png)
