---
title: Jak używać niestandardowego NuGet źródła danych w obszarach deweloperów platformy Azure
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: johnsta
ms.author: johnsta
ms.date: 05/11/2018
ms.topic: conceptual
description: Za pomocą NuGet niestandardowego źródła danych do uzyskiwania dostępu i korzystania z pakietów NuGet przestrzeni deweloperów platformy Azure.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, containers
manager: ghogen
ms.openlocfilehash: 1a000e378a9b8ecfb09d778fd6444e3f24b3df7b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60686461"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Użyj niestandardowych NuGet kanału informacyjnego przestrzeni deweloperów platformy Azure

Kanał informacyjny NuGet zapewnia wygodny sposób obejmują źródeł pakietów w projekcie. Azure Dev spacje, należy mieć możliwość dostępu do tego źródła danych, w kolejności zależności jest prawidłowo zainstalowany w kontenerze platformy Docker.

## <a name="set-up-a-nuget-feed"></a>Konfigurowanie źródła danych NuGet

Aby skonfigurować NuGet źródła danych:
1. Dodaj [pakietu odwołania](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) w `*.csproj` plik `PackageReference` węzła.

   ```xml
   <ItemGroup>
       <!-- ... -->
       <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
       <!-- ... -->
   </ItemGroup>
   ```

2. Tworzenie [NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file) plik w folderze projektu.
     * Użyj `packageSources` sekcji, aby odwoływać się do usługi NuGet źródła danych lokalizacji. Ważne: Kanał informacyjny NuGet musi być dostępny publicznie.
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
    - Odwołanie `NuGet.Config` w swojej `.gitignore` pliku, dzięki czemu poświadczenia nie przypadkowo zatwierdzenia w repozytorium źródłowym.
    - Otwórz `azds.yaml` pliku w projekcie, a następnie zlokalizuj `build` sekcji i Wstaw następujący fragment kodu, aby upewnić się, że `NuGet.Config` plik zostanie zsynchronizowany na platformie Azure, tak że użyta podczas procesu kompilacji obrazu kontenera. (Domyślnie Azure Dev miejsca do magazynowania nie synchronizuje pliki, które pasują `.gitignore` i `.dockerignore` reguły.)

        ```yaml
        build:
        useGitIgnore: true
        ignore:
        - “!NuGet.Config”
        ```


## <a name="next-steps"></a>Kolejne kroki

Po wykonaniu powyższych kroków przy następnym uruchomieniu `azds up` (lub kliknij przycisk `F5` w VSCode lub Visual Studio), usługi Azure Dev miejsca do magazynowania będą synchronizować `NuGet.Config` plików na platformę Azure, który następnie jest wykorzystywany przez `dotnet restore` można zainstalować pakietu zależności w kontenerze.

