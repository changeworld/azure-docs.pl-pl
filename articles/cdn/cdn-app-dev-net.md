---
title: Wprowadzenie do biblioteki usługi Azure CDN dla platformy .NET | Dokumenty firmy Microsoft
description: Dowiedz się, jak pisać aplikacje platformy .NET do zarządzania usługą Azure CDN przy użyciu programu Visual Studio.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 63cf4101-92e7-49dd-a155-a90e54a792ca
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 7e3ad3a5928b36c221bb83b1c4012c3c9e14f35d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67594178"
---
# <a name="get-started-with-azure-cdn-development"></a>Rozpoczynanie pracy z wdrażaniem usługi Azure CDN
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

[Biblioteka usługi Azure CDN dla platformy .NET](/dotnet/api/overview/azure/cdn) umożliwia automatyzację tworzenia profilów i punktów końcowych sieci CDN i zarządzania nimi.  W tym samouczku przedstawiono tworzenie prostej aplikacji konsoli .NET, która pokazuje kilka dostępnych operacji.  Ten samouczek nie jest przeznaczony do szczegółowego opisu wszystkich aspektów biblioteki usługi Azure CDN dla platformy .NET.

Aby ukończyć ten samouczek, potrzebujesz programu Visual Studio 2015.  [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) jest bezpłatnie dostępny do pobrania.

> [!TIP]
> [Ukończony projekt z tego samouczka](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) jest dostępny do pobrania na MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Tworzenie projektu i dodawanie pakietów Nuget
Teraz, gdy utworzyliśmy grupę zasobów dla naszych profili usługi CDN i mamy uprawnienia aplikacji usługi Azure AD do zarządzania profilami i punktami końcowymi usługi CDN w tej grupie, możemy rozpocząć tworzenie naszej aplikacji.

W programie Visual Studio 2015 kliknij pozycję **Plik**, **Nowy**, **Projekt...,** aby otworzyć nowe okno dialogowe projektu.  Rozwiń **pozycję Visual C#**, a następnie wybierz pozycję **Windows** w okienku po lewej stronie.  Kliknij pozycję **Aplikacja konsoli** w środkowym okienku.  Nadaj nazwę projektowi, a następnie kliknij przycisk **OK**.  

![Nowy projekt](./media/cdn-app-dev-net/cdn-new-project.png)

Nasz projekt będzie używać niektórych bibliotek platformy Azure zawartych w pakietach Nuget.  Dodajmy je do projektu.

1. Kliknij menu **Narzędzia,** **Menedżer pakietów Nuget**, a następnie **konsolę Menedżera pakietów**.
   
    ![Zarządzanie pakietami Nuget](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. W konsoli Menedżera pakietów wykonaj następujące polecenie, aby zainstalować **bibliotekę uwierzytelniania usługi Active Directory (ADAL)**:
   
    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. Wykonaj następujące czynności, aby zainstalować **bibliotekę zarządzania siecią CDN platformy Azure:**
   
    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Dyrektywy, stałe, metoda główna i metody pomocnicze
Przejdźmy do podstawowej struktury naszego programu.

1. Powrót na kartę Program.cs, zastąp `using` dyrektywy u góry następującymi:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using Microsoft.Azure.Management.Cdn;
    using Microsoft.Azure.Management.Cdn.Models;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Rest;
    ```
2. Musimy zdefiniować pewne stałe, z których będą korzystać nasze metody.  W `Program` klasie, ale `Main` przed metodą dodaj następujące.  Pamiętaj, aby w razie potrzeby zastąpić symbole zastępcze, w tym ** &lt;wsporniki kątowe,&gt;** własnymi wartościami.
   
    ```csharp
    //Tenant app constants
    private const string clientID = "<YOUR CLIENT ID>";
    private const string clientSecret = "<YOUR CLIENT AUTHENTICATION KEY>"; //Only for service principals
    private const string authority = "https://login.microsoftonline.com/<YOUR TENANT ID>/<YOUR TENANT DOMAIN NAME>";
   
    //Application constants
    private const string subscriptionId = "<YOUR SUBSCRIPTION ID>";
    private const string profileName = "CdnConsoleApp";
    private const string endpointName = "<A UNIQUE NAME FOR YOUR CDN ENDPOINT>";
    private const string resourceGroupName = "CdnConsoleTutorial";
    private const string resourceLocation = "<YOUR PREFERRED AZURE LOCATION, SUCH AS Central US>";
    ```
3. Również na poziomie klasy zdefiniuj te dwie zmienne.  Użyjemy ich później, aby ustalić, czy nasz profil i punkt końcowy już istnieją.
   
    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```
4. Zastąp metodę w `Main` następujący sposób:
   
   ```csharp
   static void Main(string[] args)
   {
       //Get a token
       AuthenticationResult authResult = GetAccessToken();
   
       // Create CDN client
       CdnManagementClient cdn = new CdnManagementClient(new TokenCredentials(authResult.AccessToken))
           { SubscriptionId = subscriptionId };
   
       ListProfilesAndEndpoints(cdn);
   
       // Create CDN Profile
       CreateCdnProfile(cdn);
   
       // Create CDN Endpoint
       CreateCdnEndpoint(cdn);
   
       Console.WriteLine();
   
       // Purge CDN Endpoint
       PromptPurgeCdnEndpoint(cdn);
   
       // Delete CDN Endpoint
       PromptDeleteCdnEndpoint(cdn);
   
       // Delete CDN Profile
       PromptDeleteCdnProfile(cdn);
   
       Console.WriteLine("Press Enter to end program.");
       Console.ReadLine();
   }
   ```
5. Niektóre z naszych innych metod będą monitować użytkownika z "Tak/ Nie" pytania.  Dodaj następującą metodę, aby to trochę ułatwić:
   
    ```csharp
    private static bool PromptUser(string Question)
    {
        Console.Write(Question + " (Y/N): ");
        var response = Console.ReadKey();
        Console.WriteLine();
        if (response.Key == ConsoleKey.Y)
        {
            return true;
        }
        else if (response.Key == ConsoleKey.N)
        {
            return false;
        }
        else
        {
            // They pressed something other than Y or N.  Let's ask them again.
            return PromptUser(Question);
        }
    }
    ```

Teraz, gdy podstawowa struktura naszego programu jest napisana, `Main` powinniśmy stworzyć metody wywoływane przez metodę.

## <a name="authentication"></a>Uwierzytelnianie
Zanim będziemy mogli korzystać z biblioteki zarządzania usługą Azure CDN, musimy uwierzytelnić naszego podmiotu usługi i uzyskać token uwierzytelniania.  Ta metoda używa ADAL do pobierania tokenu.

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority); 
    ClientCredential credential = new ClientCredential(clientID, clientSecret);
    AuthenticationResult authResult = 
        authContext.AcquireTokenAsync("https://management.core.windows.net/", credential).Result;

    return authResult;
}
```

Jeśli używasz uwierzytelniania poszczególnych użytkowników, `GetAccessToken` metoda będzie wyglądać nieco inaczej.

> [!IMPORTANT]
> Użyj tego przykładu kodu tylko wtedy, gdy zdecydujesz się na uwierzytelnianie poszczególnych użytkowników zamiast jednostki usługi.
> 
> 

```csharp
private static AuthenticationResult GetAccessToken()
{
    AuthenticationContext authContext = new AuthenticationContext(authority);
    AuthenticationResult authResult = authContext.AcquireTokenAsync("https://management.core.windows.net/",
        clientID, new Uri("http://<redirect URI>"), new PlatformParameters(PromptBehavior.RefreshSession)).Result;

    return authResult;
}
```

Pamiętaj, aby `<redirect URI>` zastąpić identyfikator URI przekierowania wprowadzony podczas rejestracji aplikacji w usłudze Azure AD.

## <a name="list-cdn-profiles-and-endpoints"></a>Wyświetlanie profilu i punktów końcowych sieci CDN
Teraz jesteśmy gotowi do wykonywania operacji CDN.  Pierwszą rzeczą, którą robi nasza metoda, jest lista wszystkich profili i punktów końcowych w naszej grupie zasobów, a jeśli znajdzie dopasowanie do nazw profilu i punktów końcowych określonych w naszych stałych, zwraca uwagę na to na później, więc nie staramy się tworzyć duplikatów.

```csharp
private static void ListProfilesAndEndpoints(CdnManagementClient cdn)
{
    // List all the CDN profiles in this resource group
    var profileList = cdn.Profiles.ListByResourceGroup(resourceGroupName);
    foreach (Profile p in profileList)
    {
        Console.WriteLine("CDN profile {0}", p.Name);
        if (p.Name.Equals(profileName, StringComparison.OrdinalIgnoreCase))
        {
            // Hey, that's the name of the CDN profile we want to create!
            profileAlreadyExists = true;
        }

        //List all the CDN endpoints on this CDN profile
        Console.WriteLine("Endpoints:");
        var endpointList = cdn.Endpoints.ListByProfile(p.Name, resourceGroupName);
        foreach (Endpoint e in endpointList)
        {
            Console.WriteLine("-{0} ({1})", e.Name, e.HostName);
            if (e.Name.Equals(endpointName, StringComparison.OrdinalIgnoreCase))
            {
                // The unique endpoint name already exists.
                endpointAlreadyExists = true;
            }
        }
        Console.WriteLine();
    }
}
```

## <a name="create-cdn-profiles-and-endpoints"></a>Tworzenie profili i punktów końcowych sieci CDN
Następnie utworzymy profil.

```csharp
private static void CreateCdnProfile(CdnManagementClient cdn)
{
    if (profileAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating profile {0}.", profileName);
        ProfileCreateParameters profileParms =
            new ProfileCreateParameters() { Location = resourceLocation, Sku = new Sku(SkuName.StandardVerizon) };
        cdn.Profiles.Create(profileName, profileParms, resourceGroupName);
    }
}
```

Po utworzeniu profilu utworzymy punkt końcowy.

```csharp
private static void CreateCdnEndpoint(CdnManagementClient cdn)
{
    if (endpointAlreadyExists)
    {
        Console.WriteLine("Profile {0} already exists.", profileName);
    }
    else
    {
        Console.WriteLine("Creating endpoint {0} on profile {1}.", endpointName, profileName);
        EndpointCreateParameters endpointParms =
            new EndpointCreateParameters()
            {
                Origins = new List<DeepCreatedOrigin>() { new DeepCreatedOrigin("Contoso", "www.contoso.com") },
                IsHttpAllowed = true,
                IsHttpsAllowed = true,
                Location = resourceLocation
            };
        cdn.Endpoints.Create(endpointName, endpointParms, profileName, resourceGroupName);
    }
}
```

> [!NOTE]
> Powyższy przykład przypisuje punkt końcowy pochodzenia o nazwie *Contoso* z nazwa hosta `www.contoso.com`.  Należy to zmienić, aby wskazać własną nazwa hosta pochodzenia.
> 
> 

## <a name="purge-an-endpoint"></a>Czyszczenie punktu końcowego
Zakładając, że punkt końcowy został utworzony, jednym z typowych zadań, które możemy chcieć wykonać w naszym programie, jest czyszczenie zawartości w naszym punkcie końcowym.

```csharp
private static void PromptPurgeCdnEndpoint(CdnManagementClient cdn)
{
    if (PromptUser(String.Format("Purge CDN endpoint {0}?", endpointName)))
    {
        Console.WriteLine("Purging endpoint. Please wait...");
        cdn.Endpoints.PurgeContent(endpointName, profileName, resourceGroupName, new List<string>() { "/*" });
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

> [!NOTE]
> W powyższym przykładzie `/*` ciąg oznacza, że chcę przeczyścić wszystko w katalogu głównym ścieżki punktu końcowego.  Jest to równoważne sprawdzanie **przeczyścić wszystko** w witrynie Azure portal "przeczyścić" okna dialogowego. W `CreateCdnProfile` metodzie utworzyłem nasz profil jako **azure cdn** `Sku = new Sku(SkuName.StandardVerizon)`z profilu Verizon przy użyciu kodu, więc będzie to udane.  Jednak **azure CDN z profili Akamai** nie obsługuje **Purge All**, więc jeśli używałem profilu Akamai dla tego samouczka, musiałbym dołączyć konkretne ścieżki do przeczyszczenie.
> 
> 

## <a name="delete-cdn-profiles-and-endpoints"></a>Usuwanie profili i punktów końcowych sieci CDN
Ostatnie metody spowoduje usunięcie naszego punktu końcowego i profilu.

```csharp
private static void PromptDeleteCdnEndpoint(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN endpoint {0} on profile {1}?", endpointName, profileName)))
    {
        Console.WriteLine("Deleting endpoint. Please wait...");
        cdn.Endpoints.DeleteIfExists(endpointName, profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}

private static void PromptDeleteCdnProfile(CdnManagementClient cdn)
{
    if(PromptUser(String.Format("Delete CDN profile {0}?", profileName)))
    {
        Console.WriteLine("Deleting profile. Please wait...");
        cdn.Profiles.DeleteIfExists(profileName, resourceGroupName);
        Console.WriteLine("Done.");
        Console.WriteLine();
    }
}
```

## <a name="running-the-program"></a>Uruchamianie programu
Możemy teraz skompilować i uruchomić program, klikając przycisk **Start** w programie Visual Studio.

![Program uruchomiony](./media/cdn-app-dev-net/cdn-program-running-1.png)

Gdy program osiągnie powyższy monit, powinieneś być w stanie powrócić do grupy zasobów w witrynie Azure portal i zobaczyć, że profil został utworzony.

![To wszystko!](./media/cdn-app-dev-net/cdn-success.png)

Następnie możemy potwierdzić monity o uruchomienie pozostałej części programu.

![Ukończenie programu](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Następne kroki
Aby zobaczyć ukończony projekt z tego [przewodnika, pobierz przykład .](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c)

Aby znaleźć dodatkową dokumentację dotyczącą biblioteki zarządzania usługą Azure CDN dla platformy .NET, wyświetl [odwołanie w witrynie MSDN](/dotnet/api/overview/azure/cdn).

Zarządzanie zasobami sieci CDN za pomocą programu [PowerShell](cdn-manage-powershell.md).

