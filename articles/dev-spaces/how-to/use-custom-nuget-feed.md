---
title: Jak używać niestandardowych NuGet źródła danych w funkcji miejsca do deweloperów platformy Azure | Dokumentacja firmy Microsoft
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.component: azds-kubernetes
author: johnsta
ms.author: johnsta
ms.date: 05/11/2018
ms.topic: article
description: Użyj niestandardowych NuGet źródła danych dostęp i używanie pakietów NuGet w miejsce deweloperów usługi Azure.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, containers
manager: ghogen
ms.openlocfilehash: 3badd15bcfd09c97b43744a20c5df05f4ff57e84
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/16/2018
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Użyj niestandardowych NuGet źródła danych w miejscu deweloperów platformy Azure

Źródło danych NuGet oferują wygodny sposób uwzględnienie źródła pakietów w projekcie. Spacje deweloperów platformy Azure musi być w stanie uzyskać dostępu do tego źródła danych, aby zależności jest prawidłowo zainstalowany w kontenerze Docker.

## <a name="set-up-a-nuget-feed"></a>Konfigurowanie NuGet źródła danych

Aby skonfigurować NuGet źródła danych:
1. Dodaj [odwołania pakować](https://docs.microsoft.com/en-us/nuget/consume-packages/package-references-in-project-files) w `*.csproj` plików w obszarze `PackageReference` węzła.

   ```xml
   <ItemGroup>
       <!-- ... -->
       <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
       <!-- ... -->
   </ItemGroup>
   ```

2. Utwórz [NuGet.Config](https://docs.microsoft.com/en-us/nuget/reference/nuget-config-file) plików w folderze projektu.
     * Użyj `packageSources` sekcji, aby odwołać programu NuGet źródła lokalizacji. Ważne: Źródło NuGet musi być dostępny publicznie.
     * Użyj `packageSourceCredentials` sekcji, aby skonfigurować poświadczenia użytkownika i hasło. 

   ```xml
   <packageSources>
       <add key="Contoso" value="https://contoso.com/packages/" />
   </packageSources>

   <packageSourceCredentials>
       <Contoso>
           <add key="Username" value="user@contoso.com" />
           <add key="ClearTextPassword" value="33f!!lloppa" />
       </Contoso>
   </packageSourceCredentials>
   ```

3. Jeśli używasz kontroli kodu źródłowego:
    - Odwołanie `NuGet.Config` w Twojej `.gitignore` pliku, poświadczenia nie przypadkowo zatwierdzenia w repozytorium źródła.
    - Otwórz `azds.yaml` pliku w projekcie, a następnie zlokalizuj `build` sekcji i Wstaw następujący fragment kodu w celu zapewnienia, że `NuGet.Config` plik zostanie zsynchronizowany na platformie Azure, tak aby użyć podczas procesu tworzenia obrazu kontenera. (Domyślnie Azure deweloperów spacje nie synchronizuje pliki, które spełniają `.gitignore` i `.dockerignore` reguły.)

        ```yaml
        build:
        useGitIgnore: true
        ignore:
        - “!NuGet.Config”
        ```


## <a name="next-steps"></a>Kolejne kroki

Po wykonaniu powyższych czynności, przy następnym uruchomieniu `azds up` (lub kliknij przycisk `F5` VSCode lub Visual Studio), spacji deweloperów platformy Azure będzie synchronizować `NuGet.Config` plików na platformie Azure, który następnie jest wykorzystywany przez `dotnet restore` można zainstalować pakietu zależności w kontenerze.

