---
title: 'Uwierzytelnianie użytkownika końcowego: zestaw .NET SDK z usługą Data Lake Store za pomocą usługi Azure Active Directory | Dokumentacja firmy Microsoft'
description: Dowiedz się, jak osiągnięcie uwierzytelniania użytkownika końcowego przy użyciu usługi Azure Active Directory przy użyciu zestawu .NET SDK usługi Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 4b5ab13904fad4319a8a5baf48dd27a716c3c6d0
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
# <a name="end-user-authentication-with-data-lake-store-using-net-sdk"></a>Uwierzytelnianie użytkownika końcowego przy użyciu zestawu .NET SDK usługi Data Lake Store
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-end-user-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

W tym artykule opisano sposób użycia zestawu .NET SDK w celu uwierzytelniania użytkowników końcowych za pomocą usługi Azure Data Lake Store. Do uwierzytelnienia do usługi Data Lake Store przy użyciu zestawu .NET SDK, zobacz [do usługi uwierzytelniania przy użyciu zestawu .NET SDK usługi Data Lake Store](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne
* **Program Visual Studio w wersji 2013, 2015 lub 2017**. Poniższe instrukcje korzystają z programu Visual Studio 2017.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Tworzenie aplikacji usługi Azure Active Directory "Native"**. Należy wykonać czynności opisane w [uwierzytelniania użytkowników końcowych za pomocą usługi Data Lake Store za pomocą usługi Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

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

      * `Microsoft.Azure.Management.DataLake.Store` — w tym samouczku jest używana wersja v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` — w tym samouczku jest używana wersja v2.2.12.

        ![Dodawanie źródła pakietów NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Tworzenie nowego konta usługi Azure Data Lake")
   4. Zamknij **Menedżera pakietów NuGet**.

6. Otwórz **Program.cs**
7. Zastąp za pomocą instrukcji następujące wiersze:

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
Dodaj następujący fragment kodu w aplikacji klienta .NET. Zastąp symbole zastępcze wartości pobierane z usługi Azure AD aplikacji natywnych (wymienionym wymagań wstępnych). Ta Wstawka kodu umożliwia uwierzytelnianie aplikacji **interaktywnie** z usługi Data Lake Store, co oznacza, że zostanie wyświetlony monit o wprowadzenie poświadczeń platformy Azure.

Łatwość użycia, aby uzyskać następujący fragment kodu używa domyślnych wartości dla Identyfikatora klienta oraz identyfikatora URI, które są prawidłowe dla dowolnej subskrypcji platformy Azure przekierowania. W poniższy fragment kodu wystarczy podać wartość dla identyfikatora dzierżawcy Można pobrać Identyfikatora dzierżawcy przy użyciu instrukcji podanych w [uzyskanie Identyfikatora dzierżawy](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
    
- Funkcja Main() Zastąp następujący kod:

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

Kilka rzeczy, informacje o poprzednim fragment kodu:

* Fragment poprzedzających używa funkcji pomocnika `GetTokenCache` i `GetCreds_User_Popup`. Kod dla tych funkcji pomocnika jest dostępny [tutaj w serwisie Github](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* Aby ułatwić ukończenie samouczka szybciej, fragment kodu używa Identyfikatora klienta aplikacji natywnej, który jest domyślnie dostępne dla wszystkich subskrypcji platformy Azure. Dzięki temu można **użyć tego fragmentu w aplikacji w niezmienionej formie**.
* Jeśli jednak chcesz użyć własnej domeny usługi Azure AD i własnego identyfikatora klienta aplikacji, musisz utworzyć natywną aplikację usługi Azure AD, a następnie użyć identyfikatora dzierżawy usługi Azure AD, identyfikatora klienta i identyfikatora URI przekierowania utworzonej aplikacji. Instrukcje można znaleźć w temacie [Create an Active Directory Application for end-user authentication with Data Lake Store](data-lake-store-end-user-authenticate-using-active-directory.md) (Tworzenie aplikacji usługi Active Directory na potrzeby uwierzytelniania użytkownika końcowego przy użyciu usługi Data Lake Store).

  
## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób uwierzytelniania użytkowników końcowych do uwierzytelniania w usłudze Azure Data Lake Store przy użyciu zestawu .NET SDK. Teraz można przeglądać następujące artykuły, które opisano sposób użycia zestawu .NET SDK do pracy z usługą Azure Data Lake Store.

* [Account management operations on Data Lake Store using .NET SDK (Operacje zarządzania kontem w usłudze Data Lake Store przy użyciu zestawu SDK platformy .NET)](data-lake-store-get-started-net-sdk.md)
* [Operacje na danych na przy użyciu zestawu .NET SDK usługi Data Lake Store](data-lake-store-data-operations-net-sdk.md)

