---
title: Konfigurowanie usługi Azure Functions
description: W tym samouczku opisano sposób tworzenia aplikacji funkcji platformy Azure i konfigurowania jej do pracy z dostawcami niestandardowymi platformy Azure
author: jjbfour
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: b6984bfdf7ec078d02999bccf77f3592cfbcab27
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75649988"
---
# <a name="set-up-azure-functions-for-azure-custom-providers"></a>Konfigurowanie usług Azure Functions dla dostawców niestandardowych platformy Azure

Dostawca niestandardowy to umowa między platformą Azure a punktem końcowym. Za pomocą dostawców niestandardowych można zmieniać przepływy pracy na platformie Azure. W tym samouczku pokazano, jak skonfigurować aplikację funkcji platformy Azure do pracy jako punkt końcowy dostawcy niestandardowego.

## <a name="create-the-azure-function-app"></a>Tworzenie aplikacji funkcji platformy Azure

> [!NOTE]
> W tym samouczku utworzysz prosty punkt końcowy usługi, który używa aplikacji funkcji platformy Azure. Jednak dostawca niestandardowy można użyć dowolnego publicznie dostępnego punktu końcowego. Alternatywy obejmują usługi Azure Logic Apps, usługi Azure API Management i funkcję aplikacji sieci Web usługi Azure App Service.

Aby uruchomić ten samouczek, należy najpierw wykonać samouczek [Tworzenie pierwszej aplikacji funkcji platformy Azure w witrynie Azure portal](../../azure-functions/functions-create-first-azure-function.md). Ten samouczek tworzy funkcję elementu webhook .NET core, którą można zmodyfikować w witrynie Azure portal. Jest to również podstawa dla bieżącego samouczka.

## <a name="install-azure-table-storage-bindings"></a>Instalowanie powiązań magazynu tabel platformy Azure

Aby zainstalować powiązania magazynu tabel platformy Azure:

1. Przejdź do karty **Integruj** dla httptrigger.
1. Wybierz **+ Nowe wejście**.
1. Wybierz **usługę Azure Table Storage**.
1. Zainstaluj rozszerzenie Microsoft.Azure.WebJobs.Extensions.Storage, jeśli nie jest jeszcze zainstalowane.
1. W polu **Nazwa parametru Tabela** wprowadź **tabelęStorage**.
1. W polu **Nazwa tabeli** wprowadź **myCustomResources**.
1. Wybierz **pozycję Zapisz,** aby zapisać zaktualizowany parametr wejściowy.

![Omówienie dostawcy niestandardowego przedstawiające powiązania tabel](./media/create-custom-provider/azure-functions-table-bindings.png)

## <a name="update-restful-http-methods"></a>Aktualizowanie restful metod HTTP

Aby skonfigurować funkcję platformy Azure w celu uwzględnienia metod żądania restful dostawcy niestandardowego:

1. Przejdź do karty **Integruj** dla httptrigger.
1. W obszarze **Wybrane metody HTTP**wybierz pozycję **POBIERZ,** **POST**, **USUŃ**i **UMIEŚĆ**.

![Omówienie dostawcy niestandardowego z metodami HTTP](./media/create-custom-provider/azure-functions-http-methods.png)

## <a name="add-azure-resource-manager-nuget-packages"></a>Dodawanie pakietów NuGet usługi Azure Resource Manager

> [!NOTE]
> Jeśli w katalogu projektu brakuje pliku projektu w języku C#, można go dodać ręcznie. Lub pojawi się po microsoft.Azure.WebJobs.Extensions.Storage rozszerzenie jest zainstalowany w aplikacji funkcji.

Następnie zaktualizuj plik projektu języka C#, aby uwzględnić pomocne biblioteki NuGet. Te biblioteki ułatwiają analizowanie żądań przychodzących od dostawców niestandardowych. Wykonaj kroki, aby [dodać rozszerzenia z portalu](../../azure-functions/install-update-binding-extensions-manual.md) i zaktualizować plik projektu C#, aby uwzględnić następujące odwołania do pakietu:

```xml
<PackageReference Include="Microsoft.Azure.WebJobs.Extensions.Storage" Version="3.0.4" />
<PackageReference Include="Microsoft.Azure.Management.ResourceManager.Fluent" Version="1.22.2" />
<PackageReference Include="Microsoft.Azure.WebJobs.Script.ExtensionsMetadataGenerator" Version="1.1.*" />
```

Następujący element XML jest przykładowym plikiem projektu języka C#:

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

## <a name="next-steps"></a>Następne kroki

W tym samouczku skonfigurować aplikację funkcji platformy Azure do pracy jako punkt końcowy dostawcy niestandardowego platformy Azure.

Aby dowiedzieć się, jak tworzyć punkt końcowy dostawcy niestandardowego RESTful, zobacz [Samouczek: Tworzenie punktu końcowego dostawcy niestandardowego RESTful](./tutorial-custom-providers-function-authoring.md).

