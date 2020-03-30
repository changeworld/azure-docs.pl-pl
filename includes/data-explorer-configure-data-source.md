---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 11/03/2019
ms.author: orspodek
ms.openlocfilehash: 3cd9d017429b629acad39f5b902e842886c3c818
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "78305055"
---
## <a name="configure-the-data-source"></a>Konfigurowanie źródła danych

Wykonaj następujące kroki, aby skonfigurować Eksploratora danych platformy Azure jako źródło danych dla narzędzia pulpitu nawigacyjnego. Omówimy te kroki bardziej szczegółowo w tej sekcji:

1. Utwórz jednostkę usługi Azure Active Directory (Azure AD). Podmiot usługi jest używany przez narzędzie pulpitu nawigacyjnego, aby uzyskać dostęp do usługi Azure Data Explorer.

1. Dodaj jednostkę usługi Azure AD do roli *przeglądarki* w bazie danych Usługi Azure Data Explorer.

1. Określ właściwości połączenia narzędzia pulpitu nawigacyjnego na podstawie informacji z jednostki usługi Azure AD, a następnie przetestuj połączenie.

### <a name="create-a-service-principal"></a>Tworzenie nazwy głównej usługi

Można utworzyć jednostkę usługi w [witrynie Azure portal](#azure-portal) lub przy użyciu środowiska wiersza polecenia [interfejsu wiersza polecenia platformy Azure.](#azure-cli) Niezależnie od używanej metody po utworzeniu otrzymasz wartości dla czterech właściwości połączenia, które będą używane w kolejnych krokach.

#### <a name="azure-portal"></a>Portal Azure

1. Aby utworzyć jednostkę usługi, postępuj zgodnie z instrukcjami zawartymi w [dokumentacji portalu Azure.](/azure/active-directory/develop/howto-create-service-principal-portal)

    1. W sekcji [Przypisywanie aplikacji do roli](/azure/active-directory/develop/howto-create-service-principal-portal#assign-a-role-to-the-application) przypisz typ roli **programu Reader** do klastra usługi Azure Data Explorer.

    1. W sekcji [Pobierz wartości do logowania](/azure/active-directory/develop/howto-create-service-principal-portal#get-values-for-signing-in) skopiuj trzy wartości właściwości opisane w krokach: Identyfikator katalogu (identyfikator **dzierżawy),** **identyfikator aplikacji**i **hasło**.

1. W witrynie Azure portal wybierz **subskrypcje,** a następnie skopiuj identyfikator subskrypcji, w której utworzono jednostkę usługi.

    ![Identyfikator subskrypcji — portal](media/data-explorer-configure-data-source/subscription-id-portal.png)

#### <a name="azure-cli"></a>Interfejs wiersza polecenia platformy Azure

1. Tworzenie jednostki usługi. Ustaw odpowiedni zakres i typ `reader`roli .

    ```azurecli
    az ad sp create-for-rbac --name "https://{UrlToYourDashboard}:{PortNumber}" --role "reader" \
                             --scopes /subscriptions/{SubID}/resourceGroups/{ResourceGroupName}
    ```

    Aby uzyskać więcej informacji, zobacz [Tworzenie jednostki usługi platformy Azure za pomocą interfejsu wiersza polecenia platformy Azure.](/cli/azure/create-an-azure-service-principal-azure-cli)

1. Polecenie zwraca zestaw wyników, podobnie jak poniżej. Skopiuj trzy wartości właściwości: **appID**, **hasło**i **dzierżawa**.


    ```json
    {
      "appId": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "displayName": "{UrlToYourDashboard}:{PortNumber}",
      "name": "https://{UrlToYourDashboard}:{PortNumber}",
      "password": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX",
      "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
    }
    ```

1. Pobierz listę swoich subskrypcji.

    ```azurecli
    az account list --output table
    ```

    Skopiuj odpowiedni identyfikator subskrypcji.

    ![Identyfikator subskrypcji — cli](media/data-explorer-configure-data-source/subscription-id-cli.png)

### <a name="add-the-service-principal-to-the-viewers-role"></a>Dodawanie jednostki usługi do roli przeglądarki

Teraz, gdy masz jednostkę usługi, należy dodać go do roli *przeglądarki* w bazie danych Usługi Azure Data Explorer. To zadanie można wykonać w obszarze **Uprawnienia** w witrynie Azure Portal lub w obszarze **Kwerenda** przy użyciu polecenia zarządzania.

#### <a name="azure-portal---permissions"></a>Portal Azure — uprawnienia

1. W witrynie Azure portal przejdź do klastra usługi Azure Data Explorer.

1. W sekcji **Przegląd** wybierz bazę danych z przykładowymi danymi StormEvents.

    ![Wybieranie bazy danych](media/data-explorer-configure-data-source/select-database.png)

1. Wybierz **pozycję Uprawnienia,** a następnie **dodaj**.

    ![Uprawnienia bazy danych](media/data-explorer-configure-data-source/database-permissions.png)

1. W obszarze **Dodawanie uprawnień bazy danych**wybierz rolę **Podgląd,** a następnie **wybierz główne podmioty**.

    ![Dodawanie uprawnień do bazy danych](media/data-explorer-configure-data-source/add-permission.png)

1. Wyszukaj utworzony podmiot usługi. Wybierz podmiot, a następnie **wybierz**.

    ![Zarządzanie uprawnieniami w witrynie Azure portal](media/data-explorer-configure-data-source/new-principals.png)

1. Wybierz **pozycję Zapisz**.

    ![Zarządzanie uprawnieniami w witrynie Azure portal](media/data-explorer-configure-data-source/save-permission.png)

#### <a name="management-command---query"></a>Polecenie Zarządzanie — kwerenda

1. W witrynie Azure portal przejdź do klastra Usługi Azure Data Explorer i wybierz pozycję **Zapytanie**.

    ![Zapytanie](media/data-explorer-configure-data-source/query.png)

1. Uruchom następujące polecenie w oknie kwerendy. Użyj identyfikatora aplikacji i identyfikatora dzierżawy z witryny Azure portal lub interfejsu wiersza polecenia.

    ```kusto
    .add database {TestDatabase} viewers ('aadapp={ApplicationID};{TenantID}')
    ```

    Polecenie zwraca zestaw wyników, podobnie jak poniżej. W tym przykładzie pierwszy wiersz jest dla istniejącego użytkownika w bazie danych, a drugi wiersz jest dla jednostki usługi, który został właśnie dodany.

    ![Zestaw wyników](media/data-explorer-configure-data-source/result-set.png)
