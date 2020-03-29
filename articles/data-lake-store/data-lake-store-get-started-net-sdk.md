---
title: 'Zestaw SDK .NET: operacje zarządzania kontami w usłudze Azure Data Lake Storage Gen1 | Dokumenty firmy Microsoft'
description: Użyj narzędzia Azure Data Lake Storage Gen1 .NET SDK do wykonywania operacji zarządzania kontami w usłudze Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: ea57d5a9-2929-4473-9d30-08227912aba7
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 8da40aa04381542c8c750c8d7e33c9a29879371d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65900882"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-net-sdk"></a>Operacje zarządzania kontami w usłudze Azure Data Lake Storage Gen1 przy użyciu sdk .NET
> [!div class="op_single_selector"]
> * [Zestaw SDK platformy .NET](data-lake-store-get-started-net-sdk.md)
> * [INTERFEJS API ODPOCZYNKU](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

W tym artykule dowiesz się, jak wykonywać operacje zarządzania kontami w usłudze Azure Data Lake Storage Gen1 przy użyciu pliku .NET SDK. Operacje zarządzania kontami obejmują tworzenie konta Usługi Data Lake Storage Gen1, wyświetlanie listy kont w ramach subskrypcji platformy Azure, usuwanie kont itp.

Aby uzyskać instrukcje dotyczące wykonywania operacji zarządzania danymi w programie Data Lake Storage Gen1 przy użyciu pliku .NET SDK, zobacz [Operacje systemu plików w programie Data Lake Storage Gen1 przy użyciu pliku .NET SDK](data-lake-store-data-operations-net-sdk.md).

## <a name="prerequisites"></a>Wymagania wstępne
* **Visual Studio 2013 lub wyższej**. Poniższe instrukcje używają programu Visual Studio 2019.

* **Subskrypcja platformy Azure**. Zobacz temat [Uzyskiwanie bezpłatnej wersji próbnej platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

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

6. Zadeklaruj zmienne i podaj wartości dla symboli zastępczych. Upewnij się również, że podana ścieżka lokalna i nazwa pliku istnieją na komputerze.

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
                    _adlsAccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net"; 
                    _resourceGroupName = "<RESOURCE-GROUP-NAME>"; 
                    _location = "East US 2";
                    _subId = "<SUBSCRIPTION-ID>";                    
                }
            }
        }

Z pozostałych akapitów tego artykułu możesz dowiedzieć się, w jaki sposób używać dostępnych metod .NET w celu wykonywania operacji, takich jak uwierzytelnianie, przekazywanie plików itp.

## <a name="authentication"></a>Uwierzytelnianie

* Aby uzyskać uwierzytelnianie użytkowników końcowych aplikacji, zobacz [Uwierzytelnianie użytkowników końcowych przy użyciu usługi Data Lake Storage Gen1 przy użyciu pliku .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Aby uzyskać uwierzytelnianie między usługami dla aplikacji, zobacz [Uwierzytelnianie usługi do usługi przy użyciu usługi Lake Storage Gen1 przy użyciu pliku .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Tworzenie obiektu klienta
Poniższy fragment kodu tworzy obiekt klienta konta Usługi Data Lake Storage Gen1, który jest używany do wystawiania żądań zarządzania kontem w usłudze, takich jak tworzenie konta, usuwanie konta itp.

    // Create client objects and set the subscription ID
    _adlsClient = new DataLakeStoreAccountManagementClient(armCreds) { SubscriptionId = _subId };
    
## <a name="create-a-data-lake-storage-gen1-account"></a>Tworzenie konta usługi Data Lake Storage Gen1
Poniższy fragment kodu tworzy konto Data Lake Storage Gen1 w subskrypcji platformy Azure, która została udostępniona podczas tworzenia obiektu klienta konta usługi Data Lake Storage Gen1.

    // Create Data Lake Storage Gen1 account
    var adlsParameters = new DataLakeStoreAccount(location: _location);
    _adlsClient.Account.Create(_resourceGroupName, _adlsAccountName, adlsParameters);

## <a name="list-all-data-lake-storage-gen1-accounts-within-a-subscription"></a>Wyświetlanie listy wszystkich kont Data Lake Storage Gen1 w ramach subskrypcji
Dodaj następującą metodę do swojej definicji klasy. Poniższy fragment kodu zawiera listę wszystkich kont Usługi Data Lake Storage Gen1 w ramach danej subskrypcji platformy Azure.

    // List all Data Lake Storage Gen1 accounts within the subscription
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

## <a name="delete-a-data-lake-storage-gen1-account"></a>Usuwanie konta Gen1 magazynu usługi Data Lake
Poniższy fragment kodu usuwa konto Usługi Data Lake Storage Gen1 utworzone wcześniej.

    // Delete Data Lake Storage Gen1 account
    _adlsClient.Account.Delete(_resourceGroupName, _adlsAccountName);

## <a name="see-also"></a>Zobacz też
* [Operacje systemu plików w u źródła danych Lake Storage Gen1 przy użyciu sdk .NET](data-lake-store-data-operations-net-sdk.md)
* [Odwołanie do sdk SDK usługi Data Lake Storage Gen1 .NET](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Następne kroki
* [Zabezpieczanie danych w usłudze Data Lake Storage 1. generacji](data-lake-store-secure-data.md)
