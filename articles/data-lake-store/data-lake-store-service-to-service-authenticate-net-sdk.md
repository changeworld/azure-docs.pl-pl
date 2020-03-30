---
title: 'Uwierzytelnianie usługi do usługi: zestaw SDK .NET z usługą Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory | Dokumenty firmy Microsoft'
description: Dowiedz się, jak uzyskać uwierzytelnianie między usługami za pomocą usługi Azure Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory przy użyciu narzędzia .NET SDK
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
ms.openlocfilehash: 96c496ef67e26a3079577bf52e9d019d963467b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265534"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-net-sdk"></a>Uwierzytelnianie usługi do usługi przy użyciu usługi Azure Data Lake Storage Gen1 przy użyciu sdk .NET
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>

W tym artykule dowiesz się, jak używać sdk .NET do uwierzytelniania usługi do usługi za pomocą usługi Azure Data Lake Storage Gen1. Aby uzyskać uwierzytelnianie użytkownika końcowego przy użyciu usługi Data Lake Storage Gen1 przy użyciu pliku .NET SDK, zobacz [Uwierzytelnianie użytkowników końcowych przy użyciu usługi Data Lake Storage Gen1 przy użyciu pliku .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne
* **Visual Studio 2013 lub wyższej**. Poniższe instrukcje używają programu Visual Studio 2019.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Tworzenie aplikacji sieci Web usługi Azure Active Directory**. Kroki w [uwierzytelnianiu usługi do usługi za pomocą usługi Data Lake Storage Gen1 przy użyciu usługi Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

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

5. Otwórz plik **Program.cs**, usuń istniejący kod, a następnie dołącz poniższe instrukcje, aby dodać odwołania do przestrzeni nazw.

```csharp
using System;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading;
using System.Collections.Generic;
using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates

using Microsoft.Rest;
using Microsoft.Rest.Azure.Authentication;
using Microsoft.Azure.Management.DataLake.Store;
using Microsoft.Azure.Management.DataLake.Store.Models;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
```

## <a name="service-to-service-authentication-with-client-secret"></a>Uwierzytelnianie usługi do usługi z kluczem tajnym klienta
Dodaj ten fragment kodu w aplikacji klienckiej platformy .NET. Zastąp wartości zastępcze wartościami pobranymi z aplikacji sieci Web usługi Azure AD (wymienionych jako warunek wstępny). Ten fragment kodu umożliwia uwierzytelnienie aplikacji **nieinteraktywnie** przy użyciu usługi Data Lake Storage Gen1 przy użyciu klucza tajnego/klucza klienta dla aplikacji sieci Web usługi Azure AD.

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with client secret / key
    // Use the client ID of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
    var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
    var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
}
```

Poprzedni fragment kodu używa funkcji `GetCreds_SPI_SecretKey`pomocnika . Kod tej funkcji pomocnika jest dostępny [tutaj na GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey).

## <a name="service-to-service-authentication-with-certificate"></a>Uwierzytelnianie usługi do usługi z certyfikatem

Dodaj ten fragment kodu w aplikacji klienckiej platformy .NET. Zastąp wartości zastępcze wartościami pobranymi z aplikacji sieci Web usługi Azure AD (wymienionych jako warunek wstępny). Ten fragment kodu umożliwia uwierzytelnienie aplikacji **nieinteraktywnie** przy użyciu usługi Data Lake Storage Gen1 przy użyciu certyfikatu dla aplikacji sieci web usługi Azure AD. Aby uzyskać instrukcje dotyczące tworzenia aplikacji usługi Azure AD, zobacz [Tworzenie jednostki usługi z certyfikatami.](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate)

```csharp
private static void Main(string[] args)
{
    // Service principal / application authentication with certificate
    // Use the client ID and certificate of an existing AAD "Web App" application.
    string TENANT = "<AAD-directory-domain>";
    string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
    System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
    System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
    var cert = new X509Certificate2(@"d:\cert.pfx", "<certpassword>");
    var armCreds = GetCreds_SPI_Cert(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, cert);
    var adlCreds = GetCreds_SPI_Cert(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, cert);
}
```

Poprzedni fragment kodu używa funkcji `GetCreds_SPI_Cert`pomocnika . Kod tej funkcji pomocnika jest dostępny [tutaj na GitHub](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert).

## <a name="next-steps"></a>Następne kroki
W tym artykule dowiesz się, jak używać uwierzytelniania usługi do usługi do uwierzytelniania przy użyciu usługi Lake Storage Gen1 przy użyciu .NET SDK. Teraz można przeglądać następujące artykuły, które mówią o tym, jak używać pliku .NET SDK do pracy z gen1 magazynu usługi Data Lake.

* [Operacje zarządzania kontami w umiań magazynowania danych w uł.1 przy użyciu sdk .NET](data-lake-store-get-started-net-sdk.md)
* [Operacje danych w umiań przechowywania danych w uł.1 przy użyciu sdk .NET](data-lake-store-data-operations-net-sdk.md)
