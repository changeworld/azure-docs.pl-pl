---
title: Łączenie się z Eksploratorem danych platformy Azure z usługi Azure Databricks przy użyciu języka Python
description: W tym temacie pokazano, jak używać biblioteki języka Python w usłudze Azure Databricks, aby uzyskać dostęp do danych z Usługi Azure Data Explorer przy użyciu jednej z dwóch metod uwierzytelniania.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 03dee0570faa863ca411ed91f2a6ec85a1e38380
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76985683"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-by-using-python"></a>Łączenie się z Eksploratorem danych platformy Azure z usługi Azure Databricks przy użyciu języka Python

[Usługa Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) to platforma analityczna oparta na platformie Apache Spark, zoptymalizowana pod kątem platformy Microsoft Azure. W tym artykule pokazano, jak używać biblioteki języka Python w usłudze Azure Databricks w celu uzyskania dostępu do danych z Eksploratora danych platformy Azure. Istnieje kilka sposobów uwierzytelniania za pomocą Eksploratora danych platformy Azure, w tym logowania urządzenia i aplikacji usługi Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Wymagania wstępne

- [Tworzenie klastra i bazy danych usługi Azure Data Explorer](/azure/data-explorer/create-cluster-database-portal).
- [Utwórz obszar roboczy usługi Azure Databricks](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace). W obszarze **Usługa Azure Databricks na**liście rozwijanej **Warstwy cenowej** wybierz pozycję **Premium**. Ten wybór umożliwia używanie wpisów tajnych usługi Azure Databricks do przechowywania poświadczeń i odwoływania się do nich w notesach i zadaniach.

- [Tworzenie klastra](https://docs.azuredatabricks.net/user-guide/clusters/create.html) w usłudze Azure Databricks z następującymi specyfikacjami (minimalne ustawienia potrzebne do uruchomienia przykładowych notesów):

   ![Specyfikacje dotyczące tworzenia klastra](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-the-python-library-on-your-azure-databricks-cluster"></a>Instalowanie biblioteki języka Python w klastrze usługi Azure Databricks

Aby zainstalować [bibliotekę języka Python](/azure/kusto/api/python/kusto-python-client-library) w klastrze usługi Azure Databricks:

1. Przejdź do obszaru roboczego usługi Azure Databricks i [utwórz bibliotekę](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library).
2. [Prześlij pakiet Python PyPI lub Python Egg](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg).
   - Przekaż, zainstaluj i dołącz bibliotekę do klastra Databricks.
   - Wprowadź nazwę PyPi: **azure-kusto-data**.

## <a name="connect-to-azure-data-explorer-by-using-a-device-login"></a>Łączenie się z Eksploratorem danych platformy Azure przy użyciu logowania urządzenia

[Importowanie notesu](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) przy użyciu notesu [query-ADX-device-login.](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) Następnie można połączyć się z Eksploratorem danych platformy Azure przy użyciu poświadczeń.

## <a name="connect-to-adx-by-using-an-azure-ad-app"></a>Łączenie się z usługą ADX przy użyciu aplikacji usługi Azure AD

1. Utwórz aplikację usługi Azure AD, [inicjując inicjowanie obsługi administracyjnej aplikacji usługi Azure AD.](/azure/kusto/management/access-control/how-to-provision-aad-app)
1. Udziel dostępu do aplikacji usługi Azure AD w bazie danych Usługi Azure Data Explorer w następujący sposób:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | nazwa bazy danych |
    | ```AAD App ID``` | identyfikator aplikacji usługi Azure AD |
    | ```AAD Tenant ID``` | identyfikator dzierżawy usługi Azure AD |

### <a name="find-your-azure-ad-tenant-id"></a>Znajdowanie identyfikatora dzierżawy usługi Azure AD

Aby uwierzytelnić aplikację, Usługa Azure Data Explorer używa identyfikatora dzierżawy usługi Azure AD. Aby znaleźć identyfikator dzierżawy, użyj następującego adresu URL. Zastąp domenę *yourdomain*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Jeśli na przykład Twoja domena to *contoso.com*, adres URL to [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Wybierz ten adres URL, aby wyświetlić wyniki. Pierwsza linia jest następująca: 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Identyfikatorem dzierżawy `6babcaad-604b-40ac-a9d7-9fd97c0b779f`jest . 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Przechowywanie i zabezpieczanie identyfikatora i klucza aplikacji usługi Azure AD 

Przechowuj i zabezpieczaj identyfikator i klucz aplikacji usługi Azure AD przy użyciu [wpisów tajnych](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) usługi Azure Databricks w następujący sposób:
1. [Konfigurowanie interfejsu wiersza polecenia](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli).
1. [Zainstaluj plik CLI](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 
1. [Skonfiguruj uwierzytelnianie](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Skonfiguruj [wpisy tajne](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) przy użyciu następujących przykładowych poleceń:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Importowanie notesu
[Importowanie notesu](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) przy użyciu notesu [Query-ADX-AAD-App](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) do łączenia się z Eksploratorem danych platformy Azure. Zaktualizuj wartości zastępcze za pomocą nazwy klastra, nazwy bazy danych i identyfikatora dzierżawy usługi Azure AD.
