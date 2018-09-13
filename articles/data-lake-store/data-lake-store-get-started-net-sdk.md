---
title: 'Zestaw SDK platformy .NET: operacje zarządzania kontem w usłudze Azure Data Lake Store | Microsoft Docs'
description: Używanie zestawu SDK platformy .NET usługi Azure Data Lake Store do wykonywania operacji zarządzania kontem w usłudze Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: ee3c528345232090227c413aebaebe7cc265bc76
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/12/2018
ms.locfileid: "35648229"
---
# <a name="account-management-operations-on-azure-data-lake-store-using-net-sdk"></a>Operacje zarządzania kontem w usłudze Azure Data Lake Store przy użyciu zestawu SDK platformy .NET
> [!div class="op_single_selector"]
> * [Zestaw SDK platformy .NET](data-lake-store-get-started-net-sdk.md)
> * [Interfejs API REST](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Z tego artykułu dowiesz się, jak wykonywać operacje zarządzania kontem w usłudze Data Lake Store przy użyciu zestawu SDK platformy .NET. Operacje zarządzania kontem obejmują tworzenie konta usługi Data Lake Store, wyświetlanie listy kont w subskrypcji platformy Azure, usuwanie kont itp.

Aby uzyskać instrukcje na temat sposobu wykonywania operacji zarządzania danymi w usłudze Data Lake Store przy użyciu zestawu SDK platformy .NET, zobacz [Filesystem operations on Data Lake Store using .NET SDK (Operacje systemu plików w usłudze Data Lake Store przy użyciu zestawu SDK platformy .NET)](data-lake-store-data-operations-net-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne
* **Program Visual Studio w wersji 2013, 2015 lub 2017**. Poniższe instrukcje korzystają z programu Visual Studio 2017.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

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

7. Zadeklaruj zmienne i podaj wartości dla symboli zastępczych. Upewnij się również, że podana ścieżka lokalna i nazwa pliku istnieją na komputerze.

        namespace SdkSample
        {
            class Program
            {
                private static DataLakeStoreAccountManagementClient _adlsClient;
                
                private static string _adlsAccountName;
                private static string _resourceGroupName;
                private static string _location;
                private static string _subId;

                private static void Main(string[] args)
                {
                    _adlsAccountName = "<DATA-LAKE-STORE-NAME>.azuredatalakestore.net"; 
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";                    
                }
            }
        }

Z pozostałych akapitów tego artykułu możesz dowiedzieć się, w jaki sposób używać dostępnych metod .NET w celu wykonywania operacji, takich jak uwierzytelnianie, przekazywanie plików itp.

## <a name="authentication"></a>Authentication

* Aby dowiedzieć się więcej o uwierzytelnianiu użytkowników końcowych w aplikacji, zobacz [End-user authentication with Data Lake Store using .NET SDK (Uwierzytelnianie użytkowników końcowych w usłudze Data Lake Store przy użyciu zestawu SDK platformy .NET)](data-lake-store-end-user-authenticate-net-sdk.md).
* Aby dowiedzieć się więcej o uwierzytelnianiu „usługa-usługa” w aplikacji, zobacz [Service-to-service authentication with Data Lake Store using .NET SDK (Uwierzytelnianie „usługa-usługa” w usłudze Data Lake Store przy użyciu zestawu SDK platformy .NET)](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Tworzenie obiektu klienta
Poniższy fragment kodu tworzy obiekt klienta konta usługi Data Lake Store, który jest używany do wysyłania do usługi żądań zarządzania kontem, takich jak tworzenie konta, usuwanie konta itp.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
    
## <a name="create-a-data-lake-store-account"></a>Tworzenie konta usługi Data Lake Store
Poniższy fragment kodu tworzy konto usługi Data Lake Store w subskrypcji platformy Azure udostępnionej przez Ciebie podczas tworzenia obiektu klienta konta usługi Data Lake Store.

    // Create Data Lake Store account
    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

## <a name="list-all-data-lake-store-accounts-within-a-subscription"></a>Wyświetlanie listy wszystkich kont usługi Data Lake Store w ramach subskrypcji
Dodaj następującą metodę do swojej definicji klasy. Poniższy fragment kodu umożliwia wyświetlenie listy wszystkich kont usługi Data Lake Store należących do danej subskrypcji platformy Azure.

    // List all Data Lake Store accounts within the subscription
    public static List<DataLakeStoreAccountBasic> ListAdlStoreAccounts()
    {
        var response = _adlsClient.Account.List(_adlsAccountName);
        var accounts = new List<DataLakeStoreAccountBasic>(response);

        while (response.NextPageLink != null)
        {
            response = _adlsClient.Account.ListNext(response.NextPageLink);
            accounts.AddRange(response);
        }

        return accounts;
    }

## <a name="delete-a-data-lake-store-account"></a>Usuwanie konta usługi Data Lake Store
Poniższy fragment kodu usuwa wcześniej utworzone konto usługi Data Lake Store.

    // Delete Data Lake Store account
    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);

## <a name="see-also"></a>Zobacz także
* [Operacje systemu plików w usłudze Data Lake Store przy użyciu zestawu SDK platformy .NET](data-lake-store-data-operations-net-sdk.md)
* [Dokumentacja zestawu SDK .NET usługi Data Lake Store](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Kolejne kroki
* [Zabezpieczanie danych w usłudze Data Lake Store](data-lake-store-secure-data.md)
