---
title: 'Uwierzytelnianie użytkowników końcowych: zestawu SDK platformy .NET przy użyciu usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak wykonać uwierzytelnianie użytkowników końcowych za pomocą usługi Azure Data Lake Storage Gen1 za pomocą usługi Azure Active Directory przy użyciu zestawu .NET SDK
services: data-lake-store
documentationcenter: ''
author: twooley
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 78a290d8136f8804e853d36a9bc95571625ed89c
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58876772"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Uwierzytelnianie użytkowników końcowych za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu zestawu .NET SDK
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-end-user-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

W tym artykule dowiesz się o tym, jak wykonać uwierzytelnianie użytkowników końcowych za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu zestawu .NET SDK. Do usługi do uwierzytelniania przy użyciu Data Lake Storage Gen1 przy użyciu zestawu .NET SDK, zobacz [Service to service uwierzytelnianie za pomocą programu Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne
* **Program Visual Studio w wersji 2013, 2015 lub 2017**. Poniższe instrukcje korzystają z programu Visual Studio 2017.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Tworzenie aplikacji usługi Azure Active Directory "Natywnego"**. Zostały wykonane kroki opisane w [uwierzytelnianie użytkowników końcowych za pomocą programu Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Tworzenie aplikacji .NET
1. Otwórz program Visual Studio i utwórz aplikację konsolową.
2. W menu **Plik** kliknij pozycję **Nowy**, a następnie kliknij pozycję **Projekt**.
3. W obszarze **Nowy projekt** wpisz lub wybierz następujące wartości:

   | Właściwość | Wartość |
   | --- | --- |
   | Kategoria |Szablony/Visual C#/Windows |
   | Szablon |Aplikacja konsolowa |
   | Name (Nazwa) |CreateADLApplication |

4. Kliknij przycisk **OK**, aby utworzyć projekt.

5. Dodaj pakiety NuGet do swojego projektu.

   1. Kliknij prawym przyciskiem myszy nazwę projektu w Eksploratorze rozwiązań i kliknij polecenie **Zarządzaj pakietami NuGet**.
   2. Na karcie **Menedżer pakietów NuGet** upewnij się, że **Źródło pakietów** jest ustawione na wartość **nuget.org** i że zaznaczone jest pole wyboru **Uwzględnij wersję wstępną**.
   3. Wyszukaj i zainstaluj następujące pakiety NuGet:

      * `Microsoft.Azure.Management.DataLake.Store` — W tym samouczku v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` — W tym samouczku używana wersja v2.2.12.

        ![Dodawanie źródła pakietów NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Tworzenie nowego konta usługi Azure Data Lake")
   4. Zamknij **Menedżera pakietów NuGet**.

6. Otwórz **Program.cs**
7. Zamień przy użyciu instrukcji z następującymi wierszami:

    ```csharp
    using System;
    using System.IO;
    using System.Linq;
    using System.Text;
    using System.Threading;
    using System.Collections.Generic;
            
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.Management.DataLake.Store;
    using Microsoft.Azure.Management.DataLake.Store.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```     

## <a name="end-user-authentication"></a>Uwierzytelnianie użytkowników końcowych
Dodaj następujący fragment kodu w aplikacji klienckiej .NET. Zastąp wartości zastępcze wartościami pobranymi z natywną aplikację Azure AD (wymienione jako warunek wstępny). Ten fragment kodu umożliwia uwierzytelnianie aplikacji **interaktywnie** z Data Lake Storage Gen1, co oznacza, że zostanie wyświetlony monit o wprowadzenie poświadczeń platformy Azure.

W celu ułatwienia poniższy fragment kodu używa wartości domyślnych Identyfikatora klienta oraz identyfikatora URI, które są prawidłowe dla każdej subskrypcji platformy Azure przekierowania. W poniższym fragmencie kodu wystarczy podać wartość identyfikatora dzierżawy. Możesz pobrać identyfikator dzierżawy wykonując instrukcje podane w [uzyskanie Identyfikatora dzierżawy](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-id).
    
- Zastąp funkcji Main() następującym kodem:

    ```csharp
    private static void Main(string[] args)
    {
        //User login via interactive popup
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "1950a258-227b-4e31-a9cf-717495945fc2";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string MY_DOCUMENTS = System.Environment.GetFolderPath(System.Environment.SpecialFolder.MyDocuments);
        string TOKEN_CACHE_PATH = System.IO.Path.Combine(MY_DOCUMENTS, "my.tokencache");
        var tokenCache = GetTokenCache(TOKEN_CACHE_PATH);
        var armCreds = GetCreds_User_Popup(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, tokenCache);
        var adlCreds = GetCreds_User_Popup(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, tokenCache);
    }
    ```

Kilka rzeczy, aby dowiedzieć się o poprzednim fragmencie kodu:

* Poprzedni fragment kodu używa funkcji pomocnika `GetTokenCache` i `GetCreds_User_Popup`. Kod dla tych funkcji pomocnika jest dostępny [tutaj w serwisie GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* Aby ułatwić szybsze ukończenie tego samouczka, fragment kodu używa Identyfikatora klienta aplikacji natywnej, która jest domyślnie dostępny dla wszystkich subskrypcji platformy Azure. Dzięki temu można **użyć tego fragmentu w aplikacji w niezmienionej formie**.
* Jeśli jednak chcesz użyć własnej domeny usługi Azure AD i własnego identyfikatora klienta aplikacji, musisz utworzyć natywną aplikację usługi Azure AD, a następnie użyć identyfikatora dzierżawy usługi Azure AD, identyfikatora klienta i identyfikatora URI przekierowania utworzonej aplikacji. Zobacz [tworzenie aplikacji usługi Active Directory uwierzytelnianie użytkowników końcowych za pomocą programu Data Lake Storage Gen1](data-lake-store-end-user-authenticate-using-active-directory.md) instrukcje.

  
## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób uwierzytelniania za pomocą usługi Azure Data Lake Storage Gen1 za pomocą uwierzytelniania użytkowników końcowych przy użyciu zestawu .NET SDK. Możesz teraz przejrzeć następujące artykuły, które porozmawiać na temat sposobu pracy z usługi Azure Data Lake Storage Gen1 przy użyciu zestawu SDK platformy .NET.

* [Operacje zarządzania kontem w Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Operacje na danych na Data Lake Storage Gen1 przy użyciu zestawu .NET SDK](data-lake-store-data-operations-net-sdk.md)

