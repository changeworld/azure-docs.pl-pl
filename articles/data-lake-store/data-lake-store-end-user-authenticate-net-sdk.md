---
title: 'Uwierzytelnianie użytkowników końcowych: zestaw SDK platformy .NET z usługą Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak osiągnąć uwierzytelnianie użytkowników końcowych za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory za pomocą narzędzia .NET SDK
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
ms.openlocfilehash: 215b839c21c2590c08ac2f4250086eaf97914ce1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "66243709"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Uwierzytelnianie użytkowników końcowych przy użyciu usługi Azure Data Lake Storage Gen1 przy użyciu sdk .NET
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-end-user-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-end-user-authenticate-rest-api.md)
> 
>  

W tym artykule dowiesz się, jak używać sdk .NET do uwierzytelniania użytkowników końcowych za pomocą usługi Azure Data Lake Storage Gen1. Aby uzyskać uwierzytelnianie między usługami przy użyciu usługi Data Lake Storage Gen1 przy użyciu pliku .NET SDK, zobacz [Uwierzytelnianie usługi do usługi przy użyciu usługi Lake Storage Gen1 przy użyciu pliku .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne
* **Visual Studio 2013 lub wyższej**. Poniższe instrukcje używają programu Visual Studio 2019.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Utwórz "natywną" aplikację usługi Azure Active Directory**. Kroki w uwierzytelnianiu użytkowników końcowych za [pomocą usługi Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

## <a name="create-a-net-application"></a>Tworzenie aplikacji .NET
1. W programie Visual Studio wybierz menu **Plik** **Nowy**, a następnie **pozycję Project**.
2. Wybierz pozycję **Aplikacja konsoli (.NET Framework),** a następnie wybierz pozycję **Dalej**.
3. W obszarze Nazwa `CreateADLApplication` **projektu**wprowadź , a następnie wybierz pozycję **Utwórz**.

4. Dodaj pakiety NuGet do swojego projektu.

   1. Kliknij prawym przyciskiem myszy nazwę projektu w Eksploratorze rozwiązań i kliknij polecenie **Zarządzaj pakietami NuGet**.
   2. Na karcie **Menedżer pakietów NuGet** upewnij się, że **źródło pakietu** jest ustawione na **nuget.org** i że pole wyboru Dołącz wydanie wstępne jest **zaznaczone.**
   3. Wyszukaj i zainstaluj następujące pakiety NuGet:

      * `Microsoft.Azure.Management.DataLake.Store` — w tym samouczku jest używana wersja v2.1.3-preview.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` — w tym samouczku jest używana wersja v2.2.12.

        ![Dodawanie źródła NuGet](./media/data-lake-store-get-started-net-sdk/data-lake-store-install-nuget-package.png "Tworzenie nowego konta usługi Azure Data Lake")
   4. Zamknij **Menedżera pakietów NuGet**.

5. Otwórz **Program.cs**
6. Zastąp instrukcje using następującymi wierszami:

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
Dodaj ten fragment kodu w aplikacji klienckiej platformy .NET. Zastąp wartości zastępcze wartościami pobranymi z aplikacji natywnej usługi Azure AD (wymienione jako warunek wstępny). Ten fragment kodu umożliwia **interaktywne** uwierzytelnienie aplikacji za pomocą usługi Data Lake Storage Gen1, co oznacza, że zostanie wyświetlony monit o wprowadzenie poświadczeń platformy Azure.

Aby ułatwić korzystanie z poniższego fragmentu kodu, używane są wartości domyślne dla identyfikatora klienta i przekierowywania identyfikatora URI, które są prawidłowe dla dowolnej subskrypcji platformy Azure. W poniższym urywek wystarczy podać wartość identyfikatora dzierżawy. Identyfikator dzierżawy można pobrać, korzystając z instrukcji podanych w [pliku Get the tenant ID](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
    
- Zastąp funkcję Main() następującym kodem:

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

Kilka rzeczy, które należy wiedzieć o poprzednim urywek:

* Poprzedni fragment kodu używa funkcji `GetTokenCache` pomocnika i `GetCreds_User_Popup`. Kod dla tych funkcji pomocnika jest dostępny [tutaj na GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#gettokencache).
* Aby ułatwić szybsze ukończenie samouczka, fragment kodu używa identyfikatora klienta aplikacji natywnej, który jest domyślnie dostępny dla wszystkich subskrypcji platformy Azure. Dzięki temu można **użyć tego fragmentu w aplikacji w niezmienionej formie**.
* Jeśli jednak chcesz użyć własnej domeny usługi Azure AD i własnego identyfikatora klienta aplikacji, musisz utworzyć natywną aplikację usługi Azure AD, a następnie użyć identyfikatora dzierżawy usługi Azure AD, identyfikatora klienta i identyfikatora URI przekierowania utworzonej aplikacji. Aby uzyskać instrukcje, zobacz [Tworzenie aplikacji usługi Active Directory do uwierzytelniania użytkowników końcowych za pomocą usługi Data Lake Storage Gen1.](data-lake-store-end-user-authenticate-using-active-directory.md)

  
## <a name="next-steps"></a>Następne kroki
W tym artykule dowiesz się, jak używać uwierzytelniania użytkowników końcowych do uwierzytelniania przy użyciu usługi Azure Data Lake Storage Gen1 przy użyciu pliku .NET SDK. Teraz można spojrzeć na następujące artykuły, które mówią o tym, jak używać .NET SDK do pracy z usługi Azure Data Lake Storage Gen1.

* [Operacje zarządzania kontami w umiań magazynowania danych w uł.1 przy użyciu sdk .NET](data-lake-store-get-started-net-sdk.md)
* [Operacje danych w umiań przechowywania danych w uł.1 przy użyciu sdk .NET](data-lake-store-data-operations-net-sdk.md)

