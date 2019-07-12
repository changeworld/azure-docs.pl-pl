---
title: Konfigurowanie usługi Azure Functions dla dostawców niestandardowych usługi Azure
description: W tym samouczku zostanie umieszczona w sposób tworzenia funkcji platformy Azure i ustawić go tak, aby pracować z dostawców niestandardowych usługi Azure
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: d7e4de43659db88bfd9aad40cc3b9f1753189bba
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799994"
---
# <a name="setup-azure-functions-for-azure-custom-providers"></a>Konfigurowanie usługi Azure Functions dla dostawców niestandardowych usługi Azure

Dostawcy niestandardowi umożliwiają dostosowywanie przepływów pracy na platformie Azure. Niestandardowy dostawca jest kontrakt między platformą Azure i `endpoint`. W tym samouczku zostanie przejść przez proces konfigurowania funkcji platformy Azure do działania w formie niestandardowego dostawcy `endpoint`.

W tym samouczku jest dzielony na następujące czynności:

- Tworzenie funkcji platformy Azure
- Zainstaluj powiązania tabeli platformy Azure
- Aktualizacja metod RESTful HTTP
- Dodaj pakiety NuGet usługi Azure Resource Manager

W tym samouczku zostanie skompilowany w ramach następujących samouczków:

- [Tworzenie pierwszej funkcji platformy Azure za pośrednictwem witryny Azure portal](../azure-functions/functions-create-first-azure-function.md)

## <a name="creating-the-azure-function"></a>Tworzenie funkcji platformy Azure

> [!NOTE]
> W tym samouczku utworzymy prostą usługę punktu końcowego, przy użyciu funkcji platformy Azure, ale wszystkie dostępne publiczne użyć niestandardowego dostawcy `endpoint`. Usługa Azure Logic Apps usługi Azure API Management i Azure Web Apps są niektóre świetnymi alternatywami.

Można uruchomić w tym samouczku, należy wykonać kroki samouczka, [tworzenie pierwszej funkcji platformy Azure w witrynie Azure portal](../azure-functions/functions-create-first-azure-function.md). Samouczek utworzy funkcji elementu webhook platformy .NET core, która może być modyfikowany w witrynie Azure portal.

## <a name="install-azure-table-bindings"></a>Zainstaluj powiązania tabeli platformy Azure

W tej sekcji będzie przejście przez Szybkie kroki dotyczące instalowania powiązania magazynu tabel Azure.

1. Przejdź do `Integrate` kartę HttpTrigger.
2. Kliknij pozycję `+ New Input`.
3. Wybierz pozycję `Azure Table Storage`.
4. Zainstaluj `Microsoft.Azure.WebJobs.Extensions.Storage` Jeśli nie jest już zainstalowany.
5. Aktualizacja `Table parameter name` do "tableStorage" i `Table name` do "myCustomResources".
6. Zapisz zaktualizowany parametr wejściowy.

![Omówienie niestandardowego dostawcy](./media/create-custom-providers/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Aktualizacja metod RESTful HTTP

W tej sekcji będzie przejście przez Szybkie kroki konfigurowania funkcji platformy Azure obejmujący metody żądania RESTful niestandardowego dostawcy.

1. Przejdź do `Integrate` kartę HttpTrigger.
2. Aktualizacja `Selected HTTP methods` do: Pobierz, POST, DELETE i PUT.

![Omówienie niestandardowego dostawcy](./media/create-custom-providers/azure-functions-http-methods.png)

## <a name="modifying-the-csproj"></a>Modyfikowanie pliku csproj

> [!NOTE]
> Brakuje pliku csproj katalogu, mogą być dodawane ręcznie czy pojawi się raz `Microsoft.Azure.WebJobs.Extensions.Storage` rozszerzenie jest zainstalowane w funkcji.

Następnie zaktualizujemy plik csproj, aby zawierają przydatne biblioteki NuGet, które ułatwi przeanalizować żądania przychodzące z niestandardowych dostawców. Wykonaj kroki opisane w temacie [Dodawanie rozszerzeń z portalu](../azure-functions/install-update-binding-extensions-manual.md) i zaktualizuj csproj, aby uwzględnić następujące odwołania do pakietu:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

Przykładowy plik csproj:

```xml
<Project Sdk="Microsoft.NET.Sdk">
  <PropertyGroup>
    <TargetFramework>netstandard2.0</TargetFramework>
    <WarningsAsErrors />
  </PropertyGroup>
  <ItemGroup>
    <PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
    <PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
    <PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
  </ItemGroup>
</Project>
```

## <a name="next-steps"></a>Kolejne kroki

W tym artykule będziemy konfigurować funkcji platformy Azure do pracy jako dostawcy niestandardowego usługi Azure `endpoint`. Przejdź do następnego artykułu, aby dowiedzieć się, jak tworzenie niestandardowego dostawcy typu RESTful `endpoint`.

- [Samouczek: Tworzenie punktu końcowego RESTful niestandardowego dostawcy](./tutorial-custom-providers-function-authoring.md)
