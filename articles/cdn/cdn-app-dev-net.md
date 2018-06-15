---
title: Rozpoczynanie pracy z biblioteki usługi Azure CDN dla platformy .NET | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pisać aplikacje .NET do zarządzania usługi Azure CDN przy użyciu programu Visual Studio.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 63cf4101-92e7-49dd-a155-a90e54a792ca
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 5379586355ece98af6295236d6cbd09cb31c742b
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "23843049"
---
# <a name="get-started-with-azure-cdn-development"></a>Rozpoczynanie pracy z wdrażaniem usługi Azure CDN
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Można użyć [biblioteki usługi Azure CDN dla platformy .NET](https://msdn.microsoft.com/library/mt657769.aspx) można zautomatyzować tworzenie i zarządzanie profilami sieci CDN i punktów końcowych.  Ten samouczek przeprowadza przez tworzenie prostej aplikacji konsoli .NET, która przedstawia niektóre z dostępnych operacji.  W tym samouczku nie ma na celu opis wszystkich aspektów z biblioteki usługi Azure CDN dla platformy .NET szczegółowo.

Potrzebujesz programu Visual Studio 2015 do ukończenia tego samouczka.  [Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) jest dostępny do pobrania.

> [!TIP]
> [Ukończone projektu z tego samouczka](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) jest dostępny do pobrania w witrynie MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Tworzenie projektu i dodawanie pakietów Nuget
Teraz, opracowaliśmy grupę zasobów dla naszych profilów CDN i naszych uprawnienia aplikacji usługi Azure AD do zarządzania profilami sieci CDN i punkty końcowe w ramach tej grupy, możemy rozpocząć tworzenie aplikacji.

W programie Visual Studio 2015, kliknij **pliku**, **nowy**, **projektu...**  aby otworzyć okno dialogowe nowego projektu.  Rozwiń węzeł **Visual C#**, a następnie wybierz pozycję **Windows** w okienku po lewej stronie.  Kliknij przycisk **aplikacji konsoli** w środkowym okienku.  Nazwij swój projekt, a następnie kliknij przycisk **OK**.  

![Nowy projekt](./media/cdn-app-dev-net/cdn-new-project.png)

Nasze projektu będzie korzystać z niektórych bibliotek Azure zawartych w pakietach Nuget.  Dodajmy tych do projektu.

1. Kliknij przycisk **narzędzia** menu **Menedżera pakietów Nuget**, następnie **Konsola Menedżera pakietów**.
   
    ![Zarządzaj pakietami Nuget](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. W konsoli Menedżera pakietów, wykonaj następujące polecenie, aby zainstalować **Active Directory Authentication Library (ADAL)**:
   
    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. Wykonaj następujące czynności, aby zainstalować **biblioteki zarządzania usługi Azure CDN**:
   
    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Dyrektywy, Metoda główna, stałe i metody pomocnicze
Załóż podstawowej struktury nasz program zapisywane.

1. Wróć na karcie Program.cs Zastąp `using` dyrektywy u góry, z następujących czynności:
   
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
2. Musimy zdefiniować niektóre stałe, używanego przez naszych metod.  W `Program` klasy, ale przed wysłaniem `Main` metody, Dodaj następujący kod.  Koniecznie Zastąp symbole zastępcze w tym  **&lt;nawiasy&gt;**, z własne wartości zgodnie z potrzebami.
   
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
3. Także zdefiniować te dwie zmienne na poziomie klasy.  Użyjemy tych później do określenia, czy naszych profilu i punktu końcowego już istnieje.
   
    ```csharp
    static bool profileAlreadyExists = false;
    static bool endpointAlreadyExists = false;
    ```
4. Zastąp `Main` metody w następujący sposób:
   
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
5. Niektóre z naszych innych metod mają być monit z pytaniami "Yes/No".  Dodaj następującą metodę, aby ułatwić który nieco:
   
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

Teraz, gdy podstawowa struktura nasz program napisano, powinien utworzyć metody wywoływane przez `Main` metody.

## <a name="authentication"></a>Authentication
Zanim możemy użyć biblioteki zarządzania usługi Azure CDN, musimy uwierzytelniania naszych nazwy głównej usługi i uzyskiwanie tokenu uwierzytelniania.  Ta metoda używa biblioteki ADAL do pobrania tokenu.

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

Jeśli używasz uwierzytelniania użytkownika `GetAccessToken` metody będą różnić się nieznacznie.

> [!IMPORTANT]
> Ten przykładowy kod należy używać, tylko jeśli wybierzesz do uwierzytelniania indywidualnych użytkowników zamiast nazwy głównej usługi.
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

Pamiętaj zastąpić `<redirect URI>` z przekierowania URI wprowadzona podczas rejestrowania aplikacji w usłudze Azure AD.

## <a name="list-cdn-profiles-and-endpoints"></a>Lista profilów CDN i punkty końcowe
Teraz już wszystko gotowe do wykonywania operacji CDN.  Przede wszystkim naszym — metoda nie jest lista wszystkich profilów i punktów końcowych w naszej grupy zasobów, a w przypadku odnalezienia pasuje do nazwy profilu i punktu końcowego określona w naszym stałe odnotowuje tego na później, firma Microsoft nie należy próbować tworzyć duplikaty.

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

## <a name="create-cdn-profiles-and-endpoints"></a>Tworzenie profilów sieci CDN i punkty końcowe
Następnie utworzymy profilu.

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

Po utworzeniu profilu, utworzymy punktu końcowego.

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
> W powyższym przykładzie przypisuje punktu końcowego o nazwie początek *Contoso* z nazwą hosta `www.contoso.com`.  Należy zmienić ten wskazywał hosta własne źródła.
> 
> 

## <a name="purge-an-endpoint"></a>Przeczyszczanie punktu końcowego
Zakładając, że utworzono punkt końcowy, jeden typowe zadania, które firma Microsoft może być wykonanie w nasz program jest przeczyszczanie zawartości w naszym punktu końcowego.

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
> W przykładzie przedstawionym powyżej ciąg `/*` oznacza, że chcę przeczyścić wszystkie elementy w folderze głównym ścieżkę punktu końcowego.  Jest to równoważne sprawdzanie **przeczyścić wszystkie** w oknie dialogowym "przeczyścić" w portalu Azure. W `CreateCdnProfile` metody, po utworzeniu profilu naszych jako **Azure CDN from Verizon** profilu, używając kodu `Sku = new Sku(SkuName.StandardVerizon)`, więc to zakończy się pomyślnie.  Jednak **Azure CDN from Akamai** nie obsługują profile **przeczyścić wszystkie**, więc jeśli profil Akamai zastosowaniu w tym samouczku, I należy dołączyć określonej ścieżki do przeczyszczenia.
> 
> 

## <a name="delete-cdn-profiles-and-endpoints"></a>Usuwanie punktów końcowych i profilów usługi CDN
Ostatni metody usunie naszych punktu końcowego i profilu.

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

## <a name="running-the-program"></a>Uruchomienie programu
Firma Microsoft może teraz skompilować i uruchomić program klikając **Start** przycisku w programie Visual Studio.

![Program uruchomiony](./media/cdn-app-dev-net/cdn-program-running-1.png)

Gdy program osiągnie wiersz powyżej, można powrócić do tej grupy zasobów w portalu Azure i zobaczyć, czy profil został utworzony.

![To wszystko!](./media/cdn-app-dev-net/cdn-success.png)

Następnie można potwierdzamy z monitami, aby uruchomić całego programu.

![Kończenie pracy programu](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Następne kroki
Aby wyświetlić ukończone projektu z tego przewodnika [Pobierz przykład](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

Aby uzyskać dodatkową dokumentację z biblioteki usługi Azure CDN zarządzania dla platformy .NET, Wyświetl [odwołania w witrynie MSDN](https://msdn.microsoft.com/library/mt657769.aspx).

Zarządzanie zasobami CDN za pomocą [PowerShell](cdn-manage-powershell.md).

