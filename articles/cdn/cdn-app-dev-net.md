---
title: Wprowadzenie do biblioteki usługi Azure CDN dla platformy .NET | Dokumentacja firmy Microsoft
description: Dowiedz się, jak pisać aplikacje .NET pod kątem zarządzać usługą CDN Azure przy użyciu programu Visual Studio.
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
ms.openlocfilehash: 838c76e6a383b61ff465f3ed7506af34c8cd01d4
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916618"
---
# <a name="get-started-with-azure-cdn-development"></a>Rozpoczynanie pracy z wdrażaniem usługi Azure CDN
> [!div class="op_single_selector"]
> * [Node.js](cdn-app-dev-node.md)
> * [.NET](cdn-app-dev-net.md)
> 
> 

Możesz użyć [biblioteki usługi Azure CDN dla platformy .NET](/dotnet/api/overview/azure/cdn) zautomatyzować tworzenie i zarządzanie profilami usługi CDN i punktów końcowych.  Ten samouczek przedstawia kroki tworzenia prostą aplikację konsolową .NET, który pokazuje niektóre z dostępnych operacji.  W tym samouczku nie jest przeznaczona do szczegółowego opisywania, wszystkie aspekty biblioteki usługi Azure CDN dla platformy .NET.

Potrzebujesz programu Visual Studio 2015 do ukończenia tego samouczka.  [Program Visual Studio Community 2015](https://www.visualstudio.com/products/visual-studio-community-vs.aspx) jest dostępne bezpłatnie do pobrania.

> [!TIP]
> [Ukończone projektu z tego samouczka](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c) jest dostępny do pobrania w witrynie MSDN.
> 
> 

[!INCLUDE [cdn-app-dev-prep](../../includes/cdn-app-dev-prep.md)]

## <a name="create-your-project-and-add-nuget-packages"></a>Tworzenie projektu i dodawanie pakietów Nuget
Teraz, gdy opracowaliśmy grupę zasobów dla naszych profilów usługi CDN i naszych uprawnienia aplikacji usługi Azure AD do zarządzania profilami usługi CDN i punkty końcowe w ramach tej grupy, Zaczniemy tworzenie naszej aplikacji.

W programie Visual Studio 2015, kliknij **pliku**, **New**, **projektu...**  aby otworzyć okno dialogowe nowego projektu.  Rozwiń **Visual C#**, a następnie wybierz **Windows** w okienku po lewej stronie.  Kliknij przycisk **aplikację Konsolową** w środkowym okienku.  Nazwij swój projekt, a następnie kliknij przycisk **OK**.  

![Nowy projekt](./media/cdn-app-dev-net/cdn-new-project.png)

Nasz projekt będzie używać niektórych bibliotek platformy Azure zawartych w pakietach Nuget.  Dodajmy tych do projektu.

1. Kliknij przycisk **narzędzia** menu **Menedżera pakietów Nuget**, następnie **Konsola Menedżera pakietów**.
   
    ![Zarządzaj pakietami Nuget](./media/cdn-app-dev-net/cdn-manage-nuget.png)
2. W konsoli Menedżera pakietów, wykonaj następujące polecenie, aby zainstalować **Active Directory Authentication Library (ADAL)**:
   
    `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory`
3. Wykonaj następujące czynności, aby zainstalować **biblioteki zarządzania usługi Azure CDN**:
   
    `Install-Package Microsoft.Azure.Management.Cdn`

## <a name="directives-constants-main-method-and-helper-methods"></a>Dyrektywy, stałe, Metoda główna i metody pomocnicze
Uzyskajmy podstawowa struktura nasz program napisany.

1. W karcie pliku Program.cs Zastąp `using` dyrektywę w górnej następującym kodem:
   
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
2. Należy zdefiniować kilka stałych, używanymi przez naszych metod.  W `Program` klasy, lecz przed `Main` metody, Dodaj następujący kod.  Koniecznie Zastąp symbole zastępcze, w tym  **&lt;nawiasy kątowe&gt;**, przy użyciu własnych wartości, zgodnie z potrzebami.
   
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
3. Również na poziomie klasy, należy zdefiniować te dwie zmienne.  Użyjemy tych później Aby określić, jeśli nasz profilu i punktu końcowego już istnieje.
   
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
5. Niektóre z naszych innych metod zamierza monit z pytaniami "Yes/No".  Dodaj następującą metodę do nieco ułatwia:
   
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

Teraz, gdy podstawowa struktura nasz program został napisany, możemy utworzyć metody wywoływane przez `Main` metody.

## <a name="authentication"></a>Authentication
Firma Microsoft może korzystać z biblioteki zarządzania usługi Azure CDN, musimy uwierzytelniania naszych nazwy głównej usługi i uzyskiwanie tokenu uwierzytelniania.  Ta metoda korzysta z biblioteki ADAL do pobrania tokenu.

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

Jeśli używasz uwierzytelniania użytkownika `GetAccessToken` metoda będzie wyglądać nieco inaczej.

> [!IMPORTANT]
> Ten przykładowy kod należy używać tylko, gdy decyduje się zastosować uwierzytelnianie poszczególnych użytkowników, zamiast nazwy głównej usługi.
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

Koniecznie Zastąp `<redirect URI>` przy użyciu przekierowania URI wprowadzona podczas rejestrowania aplikacji w usłudze Azure AD.

## <a name="list-cdn-profiles-and-endpoints"></a>Lista profilów usługi CDN i punktów końcowych
Teraz jesteśmy gotowi do wykonywania operacji usługi CDN.  Pierwszą rzeczą, jaką naszych metody jest lista wszystkich profilów i punktów końcowych w naszej grupy zasobów, a jeśli znajdzie dopasowanie dla nazwy profilu i punktu końcowego, określone w naszym stałe sprawia, że zapamiętać, że na później, dzięki czemu firma Microsoft nie należy próbować tworzyć duplikaty.

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

## <a name="create-cdn-profiles-and-endpoints"></a>Tworzenie profilów CDN i punktów końcowych
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

Po utworzeniu profilu, utworzymy punkt końcowy.

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
> W powyższym przykładzie przypisuje punktu końcowego o nazwie pochodzi *Contoso* z nazwą hosta `www.contoso.com`.  Należy zmienić ten element, aby wskazać nazwę hosta własnego źródła.
> 
> 

## <a name="purge-an-endpoint"></a>Przeczyszczanie punktu końcowego usługi
Przy założeniu, że utworzono punkt końcowy, co typowe zadanie, które chcemy przeprowadzić w naszym programie jest przeczyszczanie zawartości w naszej punktu końcowego.

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
> W przykładzie powyżej ciąg `/*` oznacza, że chcę, aby przeczyścić wszystkie elementy w folderze głównym ścieżkę punktu końcowego.  Jest to równoważne sprawdzanie **Przeczyść wszystkie** w oknie dialogowym "Wyczyść" w witrynie Azure portal. W `CreateCdnProfile` metody, po utworzeniu naszej profil jako **Azure CDN from Verizon** profilowania za pomocą kodu `Sku = new Sku(SkuName.StandardVerizon)`, więc to zakończy się pomyślnie.  Jednak **Azure CDN from Akamai** nie obsługują profile **Przeczyść wszystkie**, więc jeśli zastosowaniu profilu firmy Akamai w ramach tego samouczka, czy muszę dołączyć określonej ścieżki do przeczyszczenia.
> 
> 

## <a name="delete-cdn-profiles-and-endpoints"></a>Usuń profile CDN i punktów końcowych
Spowoduje to usunięcie ostatniej metody naszego punktu końcowego i profilu.

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
Firma Microsoft teraz skompilować i uruchomić program, klikając **Start** przycisku w programie Visual Studio.

![Uruchomiony program](./media/cdn-app-dev-net/cdn-program-running-1.png)

Gdy program osiągnie wiersz powyżej, można powrócić do grupy zasobów w witrynie Azure portal i zobacz, czy profil został utworzony.

![To wszystko!](./media/cdn-app-dev-net/cdn-success.png)

Następnie możemy potwierdzić monitami, aby uruchomić pozostałej części programu.

![Kończenie programu](./media/cdn-app-dev-net/cdn-program-running-2.png)

## <a name="next-steps"></a>Następne kroki
Aby wyświetlić ukończone projekt z tego przewodnika [pobrać przykład](https://code.msdn.microsoft.com/Azure-CDN-Management-1f2fba2c).

Aby uzyskać dodatkową dokumentację biblioteki zarządzania usługi Azure CDN dla platformy .NET, należy wyświetlić [odwołania w witrynie MSDN](/dotnet/api/overview/azure/cdn).

Zarządzanie zasobami sieci CDN, za pomocą [PowerShell](cdn-manage-powershell.md).

