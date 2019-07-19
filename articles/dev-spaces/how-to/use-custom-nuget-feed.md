---
title: Jak używać niestandardowego źródła danych NuGet w Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Użyj niestandardowego źródła danych NuGet, aby uzyskać dostęp do pakietów NuGet i korzystać z nich w obszarze dev platformy Azure.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, containers
manager: gwallace
ms.openlocfilehash: 44a87491d276e09e1fa8fed3f5e6803648c3e4a2
ms.sourcegitcommit: 770b060438122f090ab90d81e3ff2f023455213b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2019
ms.locfileid: "68305396"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Używanie niestandardowego źródła danych NuGet w obszarze deweloperskim platformy Azure

Źródło danych NuGet zapewnia wygodny sposób dołączania źródeł pakietów do projektu. Azure Dev Spaces musi uzyskać dostęp do tego kanału informacyjnego, aby można było poprawnie zainstalować zależności w kontenerze platformy Docker.

## <a name="set-up-a-nuget-feed"></a>Skonfiguruj źródło danych NuGet

Dodaj [odwołanie do pakietu](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) dla zależności w `*.csproj` pliku `PackageReference` w węźle. Przykład:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Utwórz plik [NuGet. config](https://docs.microsoft.com/nuget/reference/nuget-config-file) w folderze projektu i ustaw `packageSources` sekcje i `packageSourceCredentials` dla źródła danych NuGet. `packageSources` Sekcja zawiera adres URL źródła danych, który musi być dostępny publicznie. `packageSourceCredentials` Są to poświadczenia do uzyskiwania dostępu do źródła danych. Na przykład:

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

Zaktualizuj wieloetapowe dockerfile, `NuGet.Config` aby skopiować plik do obrazu. Przykład:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> W systemie Windows `NuGet.Config`, `Nuget.Config`,, `nuget.config` i wszystkie działają jako prawidłowe nazwy plików. W systemie Linux jest `NuGet.Config` tylko prawidłową nazwą pliku dla tego pliku. Ponieważ Azure Dev Spaces używa platform Docker i Linux, ten plik musi mieć `NuGet.Config`nazwę. Nazwę można naprawić ręcznie lub przez uruchomienie `dotnet restore --configfile nuget.config`.


Jeśli używasz usługi git, nie musisz mieć poświadczeń dla źródła danych NuGet w kontroli wersji. Dodaj `NuGet.Config` `NuGet.Config` do projektu, tak aby plik nie został dodany do kontroli wersji. `.gitignore` Azure dev Spaces będzie potrzebował tego pliku podczas procesu kompilowania obrazu kontenera, ale domyślnie uwzględnia reguły zdefiniowane w `.gitignore` i `.dockerignore` podczas synchronizacji. Aby zmienić ustawienie domyślne i Zezwalaj Azure dev Spaces na synchronizowanie `NuGet.Config` pliku, `azds.yaml` Zaktualizuj plik:

```yaml
build:
useGitIgnore: true
ignore:
- “!NuGet.Config”
```

Jeśli nie korzystasz z usługi git, możesz pominąć ten krok.

Przy następnym uruchomieniu `azds up` lub trafieniu `F5` w programie Visual Studio Code lub Visual Studio `NuGet.Config` , Azure dev Spaces zsynchronizuje plik, użyje go do zainstalowania zależności pakietów.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [NuGet i sposobie jego działania](https://docs.microsoft.com/nuget/what-is-nuget).