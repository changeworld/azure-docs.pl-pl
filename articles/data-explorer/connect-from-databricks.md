---
title: Łączenie do Eksploratora danych platformy Azure z usługi Azure Databricks przy użyciu języka Python
description: W tym temacie dowiesz się, jak uzyskać dostęp do danych w Eksploratorze danych Azure przy użyciu jednej z dwóch metod uwierzytelniania za pomocą biblioteki Python w usłudze Azure Databricks.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 11/27/2018
ms.openlocfilehash: 55257d441916971b505432247f28033d6222c3be
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/05/2019
ms.locfileid: "59047759"
---
# <a name="connect-to-azure-data-explorer-from-azure-databricks-by-using-python"></a>Łączenie do Eksploratora danych platformy Azure z usługi Azure Databricks przy użyciu języka Python

[Usługa Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/what-is-azure-databricks) to platforma analizy oparta na platformie Apache Spark, która jest zoptymalizowana pod kątem platformy Microsoft Azure. Ten artykuł pokazuje, jak używać biblioteki Python w usłudze Azure Databricks dostępu do danych z Eksploratora danych usługi Azure. Istnieje kilka sposobów uwierzytelniania za pomocą Eksploratora danych platformy Azure, w tym logowanie do urządzenia i aplikacji usługi Azure Active Directory (Azure AD).

## <a name="prerequisites"></a>Wymagania wstępne

- [Tworzenie klastra Eksplorator danych platformy Azure i bazy danych](/azure/data-explorer/create-cluster-database-portal).
- [Tworzenie obszaru roboczego usługi Azure Databricks](/azure/azure-databricks/quickstart-create-databricks-workspace-portal#create-an-azure-databricks-workspace). W obszarze **usługa Azure Databricks**w **warstwy cenowej** listy rozwijanej wybierz **Premium**. Zaznacz to pole wyboru umożliwia przechowywanie poświadczeń i odwoływać się do nich w notesach i zadań za pomocą kluczy tajnych w usłudze Azure Databricks.

- [Tworzenie klastra](https://docs.azuredatabricks.net/user-guide/clusters/create.html) w usłudze Azure Databricks z następującymi specyfikacjami (potrzebnych do uruchomienia notesów przykładowe ustawienia minimalna):

   ![Specyfikacje dotyczące tworzenia klastra](media/connect-from-databricks/databricks-create-cluster.png)

## <a name="install-the-python-library-on-your-azure-databricks-cluster"></a>Zainstaluj bibliotekę języka Python w klastrze usługi Azure Databricks

Aby zainstalować [biblioteki Python](/azure/kusto/api/python/kusto-python-client-library) w klastrze usługi Azure Databricks:

1. Przejdź do obszaru roboczego usługi Azure Databricks i [utworzyć bibliotekę](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library).
2. [Przekazywanie pakietu PyPI języka Python lub Python Egg](https://docs.azuredatabricks.net/user-guide/libraries.html#upload-a-python-pypi-package-or-python-egg).
   - Przekaż, instalowanie i dołączanie biblioteki do klastra usługi Databricks.
   - Wprowadź nazwę PyPi: **azure kusto danych**.

## <a name="connect-to-azure-data-explorer-by-using-a-device-login"></a>Łączenie z Eksploratora danych platformy Azure za pomocą logowanie do urządzenia

[Importowanie notesów](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) przy użyciu [zapytania ADX — urządzenia — logowanie](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-device-login.ipynb) notesu. Następnie można łączyć z do Eksploratora danych Azure przy użyciu poświadczeń.

## <a name="connect-to-adx-by-using-an-azure-ad-app"></a>Łączenie z ADX za pomocą aplikacji usługi Azure AD

1. Tworzenie aplikacji usługi Azure AD przez [inicjowania obsługi administracyjnej aplikacji usługi Azure AD](/azure/kusto/management/access-control/how-to-provision-aad-app).
1. Udzielanie dostępu do aplikacji usługi Azure AD w bazie danych Azure Eksplorator danych w następujący sposób:

    ```kusto
    .set database <DB Name> users ('aadapp=<AAD App ID>;<AAD Tenant ID>') 'AAD App to connect Spark to ADX
    ```
    |   |   |
    | - | - |
    | ```DB Name``` | Nazwa bazy danych |
    | ```AAD App ID``` | Identyfikator aplikacji usługi Azure AD |
    | ```AAD Tenant ID``` | Identyfikator dzierżawy usługi Azure AD |

### <a name="find-your-azure-ad-tenant-id"></a>Znajdź swój identyfikator dzierżawy usługi Azure AD

W celu uwierzytelnienia aplikacji platformy Azure, Eksplorator danych używa identyfikatora dzierżawy usługi Azure AD. Aby znaleźć swój identyfikator dzierżawy, użyj następującego adresu URL. Zastąp domenę w poszukiwaniu *twojadomena*.

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

Jeśli na przykład Twoja domena to *contoso.com*, adres URL to [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/). Wybierz ten adres URL, aby wyświetlić wyniki. Pierwszy wiersz jest następująca: 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

Identyfikator dzierżawy `6babcaad-604b-40ac-a9d7-9fd97c0b779f`. 

### <a name="store-and-secure-your-azure-ad-app-id-and-key"></a>Store i zabezpieczanie usługi Azure AD, Identyfikatora aplikacji oraz klucza 

Store i zabezpieczanie usługi Azure AD, Identyfikatora aplikacji i klucz przy użyciu usługi Azure Databricks [wpisów tajnych](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) w następujący sposób:
1. [Konfigurowanie interfejsu wiersza polecenia](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-the-cli).
1. [Instalowanie interfejsu wiersza polecenia](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 
1. [Konfigurowanie uwierzytelniania](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#set-up-authentication).
1. Konfigurowanie [wpisów tajnych](https://docs.azuredatabricks.net/user-guide/secrets/index.html#secrets) przy użyciu następujących przykładowych poleceniach:

    ```databricks secrets create-scope --scope adx```

    ```databricks secrets put --scope adx --key myaadappid```

    ```databricks secrets put --scope adx --key myaadappkey```

    ```databricks secrets list --scope adx```

### <a name="import-a-notebook"></a>Importowanie notesów
[Importowanie notesów](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-a-notebook) przy użyciu [zapytania-ADX-AAD-App](https://github.com/Azure/azure-kusto-docs-samples/blob/master/Databricks_notebooks/Query-ADX-AAD-App.ipynb) Notes, aby nawiązać połączenie z Eksploratora danych usługi Azure. Zaktualizuj wartości symboli zastępczych z nazwą klastra, nazwę bazy danych i identyfikator dzierżawy usługi Azure AD
