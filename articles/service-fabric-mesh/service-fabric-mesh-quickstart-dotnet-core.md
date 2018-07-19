---
title: Przewodnik Szybki Start — tworzenie i wdrażanie aplikacji sieci web do usługi Azure Service Fabric siatki | Dokumentacja firmy Microsoft
description: Ten przewodnik Szybki Start dowiesz się, jak utworzyć witrynę sieci Web platformy ASP.NET Core i opublikowania go w usłudze Azure Service Fabric siatki.
services: service-fabric-mesh
documentationcenter: .net
author: tylermsft
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: ad8920ac01ce62eb676b495dcde2aae6b076cbe2
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125507"
---
# <a name="quickstart-create-and-deploy-a-web-app-to-azure-service-fabric-mesh"></a>Szybki Start: Tworzenie i wdrażanie aplikacji sieci web do usługi Azure Service Fabric siatki

Usługa Azure Service Fabric siatki jest w pełni zarządzana usługa, która pozwala deweloperom na wdrażanie aplikacji mikrousług bez zarządzania maszynami wirtualnymi, magazynu i sieci.

W tym przewodniku Szybki Start utworzysz nową aplikację usługi Service Fabric siatki składający się z aplikacji sieci web ASP.NET Core uruchom go na lokalny klaster projektowy, a następnie opublikować je do uruchomienia na platformie Azure.

Konieczna jest subskrypcja platformy Azure. Jeśli nie masz, można łatwo utworzyć bezpłatną subskrypcję platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed przystąpieniem do wykonywania. Będzie trzeba będzie również [Konfigurowanie środowiska dla deweloperów](service-fabric-mesh-howto-setup-developer-environment-sdk.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="create-a-service-fabric-mesh-project"></a>Utwórz projekt usługi Service Fabric siatki

Otwórz program Visual Studio i wybierz **pliku** > **New** > **projektu...**

W **nowy projekt** okna dialogowego **wyszukiwania** polu u góry typu `mesh`. Wybierz **aplikacji usługi Service Fabric siatki** szablonu. (Jeśli nie widzisz szablonu, upewnij się, że zainstalowano zestaw SDK siatki i narzędzia VS tools w wersji zapoznawczej zgodnie z opisem w [Konfigurowanie środowiska projektowego](service-fabric-mesh-howto-setup-developer-environment-sdk.md). 

W **nazwa** wpisz **ServiceFabricMesh1** i **lokalizacji** polu, Ustaw ścieżkę folderu, gdzie będą przechowywane pliki projektu.

Upewnij się, że **Utwórz katalog rozwiązania** jest zaznaczone, a następnie kliknij przycisk **OK** Aby utworzyć projekt usługi Service Fabric siatki.

![Program Visual studio usługi Service Fabric siatki okna dialogowego Nowy projekt](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-project.png)

### <a name="create-a-service"></a>Tworzenie usługi

Po kliknięciu **OK**, **Nowa usługa Service Fabric** zostanie wyświetlone okno dialogowe. Wybierz **platformy ASP.NET Core** typ projektu, upewnij się, że **Container OS** jest ustawiona na **Windows** i kliknij przycisk **OK** do utworzenia projektu platformy ASP.NET Core . 

![Program Visual studio usługi Service Fabric siatki okna dialogowego Nowy projekt](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-service-fabric-service.png)

**Nowa aplikacja internetowa ASP.NET Core** zostanie wyświetlone okno dialogowe. Wybierz **aplikacji sieci Web** a następnie kliknij przycisk **OK**.

![Nowa aplikacja platformy ASP.NET core programu Visual studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-aspnetcore-app.png)

Program Visual Studio tworzy projekt aplikacji usługi Service Fabric siatki i projekt platformy ASP.NET Core.

## <a name="build-and-publish-to-your-local-cluster"></a>Tworzenie i publikowanie w klastrze lokalnym

Obraz platformy Docker jest wbudowana i automatycznie publikowane w klastrze lokalnym, zaraz po załadowaniu projektu. Ten proces może potrwać pewien czas. Możesz monitorować postęp narzędzi usługi Service Fabric w programie **dane wyjściowe** okna, wybierając **narzędzia usługi Service Fabric** elementu w **dane wyjściowe** okna listy rozwijanej. Możesz kontynuować pracę podczas wdrażania obrazu platformy docker.

Po utworzeniu projektu kliknij **F5** Aby debugować usługę lokalnie. Lokalne wdrożenie zostało zakończone, gdy program Visual Studio jest uruchomiony projektu, zostanie otwarte okno przeglądarki, za pomocą przykładowej strony sieci Web.

Po zakończeniu przeglądania wdrożonej usługi, Zatrzymaj debugowanie projektu przez naciśnięcie klawisza **Shift + F5** w programie Visual Studio.

## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Aby opublikować projekt usługi Service Fabric siatki na platformie Azure, kliknij prawym przyciskiem myszy **projekt usługi Service Fabric siatki** w programie Visual studio i wybierz pozycję **publikowania...**

![Program Visual studio kliknij prawym przyciskiem myszy projekt usługi Service Fabric siatki](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-right-click-publish.png)

Zostanie wyświetlony **publikowanie aplikacji usługi Service Fabric** okna dialogowego.

![Okno dialogowe publikowanie w programie Visual studio, Usługa Service Fabric siatki](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-dialog.png)

Wybierz konto platformy Azure i subskrypcji. Wybierz **lokalizacji**. W tym artykule wykorzystano **wschodnie stany USA**.

W obszarze **grupy zasobów**, wybierz opcję  **\<tworzenia nowej grupy zasobów... >**. **Tworzenie grupy zasobów** zostanie wyświetlone okno dialogowe. Ustaw **nazwy grupy zasobów** i **lokalizacji**.  Ten przewodnik Szybki Start używa **wschodnie stany USA** lokalizacji i nazwy grupy **sfmeshTutorial1RG** (Jeśli Twoja organizacja ma wiele osób przy użyciu tej samej subskrypcji, wybierz unikatową nazwą grupy zasobów).  Kliknij przycisk **Utwórz** Utwórz grupę zasobów i powrócić do okna dialogowego publikowania.

![Program Visual studio usługi Service Fabric siatki nowego zasobu grupy okna dialogowego](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-resource-group-dialog.png)

Ponownie **publikowanie aplikacji usługi Service Fabric** okna dialogowego, w obszarze **usługi Azure Container Registry**, wybierz opcję  **\<Utwórz nowy rejestr kontenerów... >**. W **Tworzenie rejestru kontenerów** okno dialogowe, Użyj unikatowej nazwy dla **nazwy rejestru kontenerów**. Określ **lokalizacji** (korzysta z tego przewodnika Szybki Start **wschodnie stany USA**). Wybierz **grupy zasobów** utworzonego w poprzednim kroku na liście rozwijanej, na przykład **sfmeshTutorial1RG**. Ustaw **jednostki SKU** do **podstawowe** a następnie kliknij przycisk **Utwórz** aby powrócić do okna dialogowego publikowania.

Jeśli wystąpi błąd, który nie został zarejestrowany dostawca zasobów dla subskrypcji, należy zarejestrować go. Najpierw sprawdź, czy dostawca zasobów jest dostępna dla Twojej subskrypcji:

```Powershell
Connect-AzureRmAccount
Get-AzureRmResourceProvider -ListAvailable
```

Jeśli dostawca rejestru kontenerów (`Microsoft.ContainerRegistry`) jest dostępny, zarejestruj go za pomocą programu Powershell:

```Powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ContainerRegistry
```

![Program Visual studio usługi Service Fabric siatki nowego zasobu grupy okna dialogowego](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-container-registry-dialog.png)

W oknie dialogowym publikowania kliknij **Publikuj** przycisk, aby wdrożyć aplikację usługi Service Fabric siatki na platformie Azure.

Podczas publikowania na platformie Azure po raz pierwszy obraz platformy docker zostanie przypisany do usługi Azure Container Registry (ACR), która zajmuje trochę czasu w zależności od rozmiaru obrazu. Kolejne publikuje tego samego projektu będzie przebiegać szybciej. Możesz monitorować postęp wdrażania, wybierając **narzędzia usługi Service Fabric** w programie Visual Studio **dane wyjściowe** okna listy rozwijanej. Po zakończeniu wdrożenia **narzędzia usługi Service Fabric** dane wyjściowe będą wyświetlane adresu IP i portu aplikacji w postaci adresu URL.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMesh1\Web1\bin\Any CPU\Release\netcoreapp2.0\Web1.dll
Uploading the images to Azure Container Registry...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://...
```

Otwórz przeglądarkę internetową i przejdź do adresu URL, aby wyświetlić witrynę sieci Web, działającej na platformie Azure:

![Uruchamianie aplikacji usługi Service Fabric siatki w sieci Web](media/service-fabric-mesh-tutorial-deploy-dotnetcore/deployed-web-project.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy nie będą już potrzebne, Usuń wszystkie zasoby utworzone w tym przewodniku Szybki Start. Od momentu utworzenia nowej grupy zasobów do hostowania zasobów usługi ACR i usługi Service Fabric siatki można bezpiecznie usunąć tej grupy zasobów i jest to prosty sposób, aby usunąć wszystkie skojarzone z nią zasoby.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Connect-AzureRmAccount
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Alternatywnie możesz usunąć grupę zasobów [w witrynie Azure portal](https://portal.azure.com).

## <a name="next-steps"></a>Kolejne kroki

Aby dowiedzieć się więcej na temat tworzenia i wdrażania aplikacji usługi Service Fabric siatki, przejdź do samouczka.
> [!div class="nextstepaction"]
> [Tworzenie, debugowanie i wdrażanie aplikacji internetowej z wieloma usługami usługi Service Fabric siatki](service-fabric-mesh-tutorial-create-dotnetcore.md)