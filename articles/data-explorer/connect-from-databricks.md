---
title: Łączenie do Eksploratora danych platformy Azure z usługi Azure Databricks przy użyciu języka Python
description: W tym temacie zostanie pokazują, jak używać biblioteki Python w usłudze Azure Databricks dostępu do danych z usługi Azure Data Explorer (ADX) przy użyciu jednej z dwóch metod uwierzytelniania.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 53f51db8d1b495f9a6faec86450d2b4e08a4fb72
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/27/2018
ms.locfileid: "52428522"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-using-python"></a>Łączenie do Eksploratora danych platformy Azure z usługi Azure Databricks przy użyciu języka Python

[Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) to platforma analizy oparta na usłudze Apache Spark i zoptymalizowana pod kątem platformy usług w chmurze Microsoft Azure. W tym artykule pokazano, jak za pomocą biblioteki Python w usłudze Azure Databricks dostęp do danych z usługi Azure Data Explorer (ADX). Istnieje kilka sposobów uwierzytelniania za pomocą ADX tym logowanie do urządzenia i aplikacji usługi Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Wymagania wstępne

- [Tworzenie klastra Eksplorator danych platformy Azure i bazy danych](/azure/data-explorer/create-cluster-database-portal)
- [Tworzenie obszaru roboczego usługi Azure Databricks](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace)

    W obszarze **usługa Azure Databricks**w **warstwy cenowej** listy rozwijanej wybierz **Premium**. Pozwala na przechowywanie poświadczeń i odwoływać się do nich w notesach i zadań za pomocą kluczy tajnych w usłudze Azure Databricks.

- [Tworzenie klastra](https://docs.azuredatabricks.net/user-guide/clusters/create.html) w usłudze Azure Databricks z następującymi specyfikacjami (potrzebnych do uruchomienia notesów przykładowe ustawienia minimalna):

![Tworzenie klastra](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-python-library-on-your-azure-databricks-cluster"></a>Zainstaluj bibliotekę języka Python w klastrze usługi Azure Databricks

Aby zainstalować [biblioteki Python](/azure/kusto/api/python/kusto-python-client-library) w klastrze usługi Azure Databricks:

1. Przejdź do obszaru roboczego usługi Azure Databricks i [utworzyć bibliotekę](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)
2. [Przekazywanie pakietu języka Python PyPI lub Egg języka Python](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg)
    - Przekaż, instalowanie i dołączanie biblioteki do klastra usługi Databricks.
    - Wprowadź nazwę PyPi: *azure kusto danych*

## <a name="connect-to-adx-using-device-login"></a>Nawiązać połączenie z ADX za pomocą logowanie do urządzenia

[Importowanie notesów](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) przy użyciu [zapytania ADX — urządzenia — logowanie](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) Notes, aby nawiązać połączenie z ADX przy użyciu poświadczeń.

## <a name="connect-to-adx-using-azure-ad-app"></a>Nawiązać połączenie z ADX za pomocą aplikacji usługi Azure AD

1. Tworzenie aplikacji usługi Azure AD przez [inicjowania obsługi administracyjnej aplikacji usługi AAD](/azure/kusto/management/access-control/how-to-provision-aad-app).
1. Udzielanie dostępu do aplikacji usługi Azure AD w bazie danych Azure Eksplorator danych w następujący sposób:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | Nazwa bazy danych |
    | ```AAD App ID``` | Identyfikator aplikacji usługi Azure AD |
    | ```AAD Tenant ID``` | Twój identyfikator dzierżawy usługi Azure AD |

### <a name="find-your-azure-ad-tenant-id"></a>Znajdź swój identyfikator dzierżawy usługi Azure AD

W celu uwierzytelnienia aplikacji platformy Azure, Eksplorator danych używa identyfikatora dzierżawy usługi Azure AD. Aby odnaleźć identyfikator dzierżawy, użyj następującego adresu URL, zastępując ciąg *YourDomain* nazwą domeny.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Jeśli na przykład Twoja domena to *contoso.com*, adres URL to [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Kliknij ten adres URL, aby wyświetlić wyniki. Pierwszy wiersz wygląda w następujący sposób. 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Identyfikator dzierżawy `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Store i zabezpieczanie aplikacja usługi Azure AD identyfikator i klucz usługi 

Store i zabezpieczanie identyfikator aplikacji usługi Azure AD oraz klucza przy użyciu usługi Azure Databricks [wpisów tajnych](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) w następujący sposób:
1. [Konfigurowanie interfejsu wiersza polecenia](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli)
1. [Instalowanie interfejsu wiersza polecenia](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli) 
1. [Konfigurowanie uwierzytelniania](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Konfigurowanie [wpisów tajnych](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) przy użyciu następujących przykładowych poleceniach:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Importowanie notesów
[Importowanie notesów](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) przy użyciu [zapytania-ADX-AAD-App](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) Notes, aby nawiązać połączenie z ADX. Zaktualizuj wartości symboli zastępczych z nazwą klastra, nazwę bazy danych i identyfikator dzierżawy usługi Azure AD.