---
title: 'Do usługi uwierzytelniania: zestaw .NET SDK z usługą Data Lake Store za pomocą usługi Azure Active Directory | Dokumentacja firmy Microsoft'
description: Informacje o sposobie uzyskania do usługi uwierzytelniania przy użyciu usługi Azure Active Directory przy użyciu zestawu .NET SDK usługi Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 388b84024a031a181625404ec1429087982dffbe
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2018
ms.locfileid: "34625495"
---
# <a name="service-to-service-authentication-with-data-lake-store-using-net-sdk"></a>Aby usługa uwierzytelniania za pomocą przy użyciu zestawu .NET SDK usługi Data Lake Store
> [!div class="op_single_selector"]
> * [Korzystanie z języka Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Korzystanie z zestawu SDK dla platformy .NET](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Korzystanie z języka Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Korzystanie z interfejsu API REST](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
>  

W tym artykule informacje o sposobie używania zestawu .NET SDK w celu usługi do uwierzytelniania za pomocą usługi Azure Data Lake Store. Do uwierzytelnienia użytkownika końcowego przy użyciu zestawu .NET SDK usługi Data Lake Store, zobacz [uwierzytelniania użytkowników końcowych za pomocą usługi Data Lake Store przy użyciu zestawu .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).


## <a name="prerequisites"></a>Wymagania wstępne
* **Program Visual Studio w wersji 2013, 2015 lub 2017**. Poniższe instrukcje korzystają z programu Visual Studio 2017.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Tworzenie aplikacji usługi Azure Active Directory "Web"**. Należy wykonać czynności opisane w [do usługi uwierzytelniania za pomocą usługi Data Lake Store za pomocą usługi Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

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

6. Otwórz plik **Program.cs**, usuń istniejący kod, a następnie dołącz poniższe instrukcje, aby dodać odwołania do przestrzeni nazw.

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

## <a name="service-to-service-authentication-with-client-secret"></a>Aby usługa uwierzytelniania za pomocą klucza tajnego klienta
Dodaj następujący fragment kodu w aplikacji klienta .NET. Zastąp symbole zastępcze wartości pobierane z aplikacji sieci web usługi Azure AD (wyświetlane jako warunek wstępny).  Ta Wstawka kodu umożliwia uwierzytelnianie aplikacji **nieinteraktywnie** z Data Lake Store za pomocą klucza tajnego/klucz klienta dla usługi Azure AD aplikacji sieci web. 

    private static void Main(string[] args)
    {    
        // Service principal / appplication authentication with client secret / key
        // Use the client ID of an existing AAD "Web App" application.
        string TENANT = "<AAD-directory-domain>";
        string CLIENTID = "<AAD_WEB_APP_CLIENT_ID>";
        System.Uri ARM_TOKEN_AUDIENCE = new System.Uri(@"https://management.core.windows.net/");
        System.Uri ADL_TOKEN_AUDIENCE = new System.Uri(@"https://datalake.azure.net/");
        string secret_key = "<AAD_WEB_APP_SECRET_KEY>";
        var armCreds = GetCreds_SPI_SecretKey(TENANT, ARM_TOKEN_AUDIENCE, CLIENTID, secret_key);
        var adlCreds = GetCreds_SPI_SecretKey(TENANT, ADL_TOKEN_AUDIENCE, CLIENTID, secret_key);
    }

Fragment poprzedzających korzysta z funkcji pomocnika `GetCreds_SPI_SecretKey`. Kod dla tej funkcji pomocnika jest dostępny [tutaj w serwisie Github](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_secretkey).

## <a name="service-to-service-authentication-with-certificate"></a>Aby usługa uwierzytelniania za pomocą certyfikatu

Dodaj następujący fragment kodu w aplikacji klienta .NET. Zastąp symbole zastępcze wartości pobierane z aplikacji sieci web usługi Azure AD (wyświetlane jako warunek wstępny). Ta Wstawka kodu umożliwia uwierzytelnianie aplikacji **nieinteraktywnie** z Data Lake Store przy użyciu certyfikatu dla usługi Azure AD aplikacji sieci web. Aby uzyskać instrukcje dotyczące tworzenia aplikacji usługi Azure AD, zobacz [Tworzenie nazwy głównej usługi z certyfikatami](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-self-signed-certificate).

    
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

Fragment poprzedzających korzysta z funkcji pomocnika `GetCreds_SPI_Cert`. Kod dla tej funkcji pomocnika jest dostępny [tutaj w serwisie Github](https://github.com/Azure-Samples/data-lake-analytics-dotnet-auth-options#getcreds_spi_cert).

## <a name="next-steps"></a>Kolejne kroki
W tym artykule przedstawiono sposób uwierzytelniania za pomocą usługi Azure Data Lake Store za pomocą usługi do uwierzytelniania przy użyciu zestawu .NET SDK. Teraz można przeglądać następujące artykuły, które opisano sposób użycia zestawu .NET SDK do pracy z usługą Azure Data Lake Store.

* [Account management operations on Data Lake Store using .NET SDK (Operacje zarządzania kontem w usłudze Data Lake Store przy użyciu zestawu SDK platformy .NET)](data-lake-store-get-started-net-sdk.md)
* [Operacje na danych na przy użyciu zestawu .NET SDK usługi Data Lake Store](data-lake-store-data-operations-net-sdk.md)


