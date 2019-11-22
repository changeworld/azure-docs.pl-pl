---
title: Jak używać niestandardowego źródła danych NuGet w Azure Dev Spaces
services: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 07/17/2019
ms.topic: conceptual
description: Użyj niestandardowego źródła danych NuGet, aby uzyskać dostęp do pakietów NuGet i korzystać z nich w obszarze dev platformy Azure.
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, containers
manager: gwallace
ms.openlocfilehash: ee14d999872f6e739321c144831d60a4ae6f9388
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2019
ms.locfileid: "74279949"
---
#  <a name="use-a-custom-nuget-feed-in-an-azure-dev-space"></a>Używanie niestandardowego źródła danych NuGet w obszarze deweloperskim platformy Azure

Źródło danych NuGet zapewnia wygodny sposób dołączania źródeł pakietów do projektu. Azure Dev Spaces musi uzyskać dostęp do tego kanału informacyjnego, aby można było poprawnie zainstalować zależności w kontenerze platformy Docker.

## <a name="set-up-a-nuget-feed"></a>Skonfiguruj źródło danych NuGet

Dodaj [odwołanie do pakietu](https://docs.microsoft.com/nuget/consume-packages/package-references-in-project-files) dla zależności w pliku `*.csproj` w węźle `PackageReference`. Na przykład:

```xml
<ItemGroup>
    <!-- ... -->
    <PackageReference Include="Contoso.Utility.UsefulStuff" Version="3.6.0" />
    <!-- ... -->
</ItemGroup>
```

Utwórz plik [NuGet. config](https://docs.microsoft.com/nuget/reference/nuget-config-file) w folderze projektu i ustaw `packageSources` i `packageSourceCredentials` sekcje dla źródła danych NuGet. Sekcja `packageSources` zawiera adres URL źródła danych, który musi być dostępny z klastra AKS. `packageSourceCredentials` są poświadczeniami do uzyskiwania dostępu do źródła danych. Na przykład:

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

Zaktualizuj wieloetapowe dockerfile, aby skopiować plik `NuGet.Config` do obrazu. Na przykład:

```console
COPY ["<project folder>/NuGet.Config", "./NuGet.Config"]
```

> [!TIP]
> W systemie Windows `NuGet.Config`, `Nuget.Config`i `nuget.config` wszystkie działają jako prawidłowe nazwy plików. W systemie Linux tylko `NuGet.Config` jest prawidłową nazwą pliku dla tego pliku. Ponieważ Azure Dev Spaces używa platform Docker i Linux, ten plik musi mieć nazwę `NuGet.Config`. Nazwę można naprawić ręcznie lub przez uruchomienie `dotnet restore --configfile nuget.config`.


Jeśli używasz usługi git, nie musisz mieć poświadczeń dla źródła danych NuGet w kontroli wersji. Dodaj `NuGet.Config` do `.gitignore` dla projektu, tak aby plik `NuGet.Config` nie został dodany do kontroli wersji. Azure Dev Spaces będzie potrzebował tego pliku podczas procesu kompilowania obrazu kontenera, ale domyślnie uwzględnia reguły zdefiniowane w `.gitignore` i `.dockerignore` podczas synchronizacji. Aby zmienić ustawienie domyślne i Zezwalaj Azure Dev Spaces na synchronizowanie pliku `NuGet.Config`, zaktualizuj plik `azds.yaml`:

```yaml
build:
useGitIgnore: true
ignore:
- "!NuGet.Config"
```

Jeśli nie korzystasz z usługi git, możesz pominąć ten krok.

Przy następnym uruchomieniu `azds up` lub trafienia `F5` w Visual Studio Code lub programie Visual Studio Azure Dev Spaces zsynchronizuje plik `NuGet.Config` przy użyciu go do zainstalowania zależności pakietu.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [NuGet i sposobie jego działania](https://docs.microsoft.com/nuget/what-is-nuget).