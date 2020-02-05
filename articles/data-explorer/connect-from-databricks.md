---
title: Nawiązywanie połączenia z usługą Azure Eksplorator danych z Azure Databricks przy użyciu języka Python
description: W tym temacie pokazano, jak używać biblioteki języka Python w Azure Databricks do uzyskiwania dostępu do danych z usługi Azure Eksplorator danych przy użyciu jednej z dwóch metod uwierzytelniania.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 03dee0570faa863ca411ed91f2a6ec85a1e38380
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76985683"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-by-using-python"></a>Nawiązywanie połączenia z usługą Azure Eksplorator danych z poziomu Azure Databricks przy użyciu języka Python

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) to oparta na Apache Spark platforma analityczna zoptymalizowana pod kątem platformy Microsoft Azure. W tym artykule pokazano, jak używać biblioteki języka Python w Azure Databricks, aby uzyskać dostęp do danych z usługi Azure Eksplorator danych. Istnieje kilka sposobów uwierzytelniania przy użyciu usługi Azure Eksplorator danych, w tym logowania do urządzenia i aplikacji Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Wymagania wstępne

- [Utwórz klaster Eksplorator danych i bazę danych platformy Azure](/azure/data-explorer/create-cluster-database-portal).
- [Utwórz obszar roboczy Azure Databricks](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace). W obszarze **Azure Databricks usługi**, na liście rozwijanej **warstwa cenowa** wybierz pozycję **Premium**. Wybranie tej opcji umożliwia używanie Azure Databricks wpisów tajnych do przechowywania poświadczeń i odwoływania się do nich w notesach i zadaniach.

- [Utwórz klaster](https://docs.azuredatabricks.net/user-guide/clusters/create.html) w Azure Databricks z następującymi specyfikacjami (minimalne ustawienia niezbędne do uruchomienia przykładowych notesów):

   ![Specyfikacje dotyczące tworzenia klastra](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-the-python-library-on-your-azure-databricks-cluster"></a>Instalowanie biblioteki języka Python w klastrze Azure Databricks

Aby zainstalować [bibliotekę języka Python](/azure/kusto/api/python/kusto-python-client-library) w klastrze Azure Databricks:

1. Przejdź do obszaru roboczego Azure Databricks i [Utwórz bibliotekę](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library).
2. [Przekaż pakiet języka Python PyPI lub jaja języka Python](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg).
   - Przekaż, zainstaluj i Dołącz bibliotekę do klastra datakostks.
   - Wprowadź nazwę PyPi: **Azure-Kusto-Data**.

## <a name="connect-to-azure-data-explorer-by-using-a-device-login"></a>Nawiązywanie połączenia z usługą Azure Eksplorator danych przy użyciu logowania do urządzenia

[Zaimportuj Notes](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) przy użyciu notesu [Query-ADX-Device-login](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) . Następnie możesz nawiązać połączenie z usługą Azure Eksplorator danych przy użyciu swoich poświadczeń.

## <a name="connect-to-adx-by-using-an-azure-ad-app"></a>Nawiązywanie połączenia z usługą ADX przy użyciu aplikacji usługi Azure AD

1. Utwórz aplikację usługi Azure AD, udostępniając [aplikację usługi Azure AD](/azure/kusto/management/access-control/how-to-provision-aad-app).
1. Przyznaj dostęp do aplikacji usługi Azure AD w usłudze Azure Eksplorator danych Database w następujący sposób:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | Nazwa bazy danych |
    | ```AAD App ID``` | Identyfikator aplikacji usługi Azure AD |
    | ```AAD Tenant ID``` | Identyfikator dzierżawy usługi Azure AD |

### <a name="find-your-azure-ad-tenant-id"></a>Znajdź swój identyfikator dzierżawy usługi Azure AD

Aby można było uwierzytelnić aplikację, usługa Azure Eksplorator danych używa identyfikatora dzierżawy usługi Azure AD. Aby znaleźć swój identyfikator dzierżawy, użyj następującego adresu URL. Zastąp domenę do *twojadomena*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Jeśli na przykład Twoja domena to *contoso.com*, adres URL to [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Wybierz ten adres URL, aby wyświetlić wyniki. Pierwszy wiersz jest następujący: 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Identyfikator dzierżawy jest `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Przechowywanie i zabezpieczanie identyfikatora i klucza aplikacji usługi Azure AD 

Przechowuj i zabezpieczaj identyfikator i klucz aplikacji usługi Azure AD, używając Azure Databricks wpisów [tajnych](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) w następujący sposób:
1. [Skonfiguruj interfejs wiersza polecenia](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli).
1. [Zainstaluj interfejs wiersza polecenia](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 
1. [Skonfiguruj uwierzytelnianie](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Skonfiguruj wpisy [tajne](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) przy użyciu następujących przykładowych poleceń:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Importowanie notesu
[Zaimportuj Notes](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) przy użyciu notesu [Query-ADX-AAD-App](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) , aby nawiązać połączenie z usługą Azure Eksplorator danych. Zaktualizuj wartości symboli zastępczych nazwą klastra, nazwą bazy danych i IDENTYFIKATORem dzierżawy usługi Azure AD.
