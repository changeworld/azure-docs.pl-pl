---
title: Korzystanie z niestandardowego źródła danych NuGet
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Użyj niestandardowego źródła danych NuGet, aby uzyskać dostęp do pakietów NuGet i używać ich w obszarze deweloperów platformy Azure.
keywords: Docker, Kubernetes, Azure, AKS, Usługa kontenera azure, kontenery
manager: gwallace
ms.openlocfilehash: 39984a3b3a1be64a497fb8088559ccfcdee4f1c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325726"
---
# <a name="use-a-custom-nuget-feed-with-azure-dev-spaces"></a>Używanie niestandardowego źródła danych NuGet w usługach Azure Dev Spaces

Źródło danych NuGet zapewnia wygodny sposób, aby uwzględnić źródła pakietów w projekcie. Usługa Azure Dev Spaces musi uzyskać dostęp do tego źródła danych, aby zależności były poprawnie zainstalowane w kontenerze platformy Docker.

## <a name="set-up-a-nuget-feed"></a>Konfigurowanie źródła danych NuGet

Dodaj [odwołanie do pakietu](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) dla `*.csproj` zależności w `PackageReference` pliku w węźle. Przykład:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Utwórz plik [NuGet.Config](https://docs.microsoft.com/nuget/reference/nuget-config-file) w folderze `packageSources` `packageSourceCredentials` projektu i ustaw sekcje i sekcje dla źródła danych NuGet. Sekcja `packageSources` zawiera adres URL pliku danych, który musi być dostępny z klastra AKS. Są `packageSourceCredentials` poświadczenia dostępu do źródła danych. Przykład:

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

Zaktualizuj pliki dockerfiles, aby skopiować `NuGet.Config` plik do obrazu. Przykład:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> W systemie `NuGet.Config` `Nuget.Config`Windows `nuget.config` , i wszystkie działa jako prawidłowe nazwy plików. W systemie `NuGet.Config` Linux tylko prawidłowa nazwa pliku dla tego pliku. Ponieważ usługa Azure Dev Spaces korzysta z platformy `NuGet.Config`Docker i Linux, ten plik musi mieć nazwę . Nazewnictwo można naprawić ręcznie `dotnet restore --configfile nuget.config`lub po uruchomieniu .


Jeśli używasz Git, nie należy mieć poświadczenia dla źródła danych NuGet w kontroli wersji. Dodaj `NuGet.Config` do `.gitignore` projektu, aby `NuGet.Config` plik nie został dodany do kontroli wersji. Usługa Azure Dev Spaces będzie wymagać tego pliku podczas procesu kompilacji obrazu `.gitignore` `.dockerignore` kontenera, ale domyślnie jest zgodna z regułami zdefiniowanymi w synchronizacji i podczas synchronizacji. Aby zmienić wartość domyślną i zezwolić witrynie Azure Dev Spaces na `NuGet.Config` synchronizowanie pliku, zaktualizuj `azds.yaml` plik:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Jeśli nie używasz Git, możesz pominąć ten krok.

Przy następnym uruchomieniu `azds up` lub `F5` trafieniu w programie Visual Studio Code lub `NuGet.Config` Visual Studio usługa Azure Dev Spaces zsynchronizuje plik, aby zainstalować zależności pakietów.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [NuGet i jak to działa](https://docs.microsoft.com/nuget/what-is-nuget).