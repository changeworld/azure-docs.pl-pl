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
ms.openlocfilehash: d14420a363cfea23c86f63533a4ea89c5f2fd06f
ms.sourcegitcommit: 96f498de91984321614f09d796ca88887c4bd2fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/02/2018
ms.locfileid: "39412920"
---
# <a name="quickstart-create-and-deploy-a-web-app-to-azure-service-fabric-mesh"></a>Szybki Start: Tworzenie i wdrażanie aplikacji sieci web do usługi Azure Service Fabric siatki

Usługa Azure Service Fabric Mesh to w pełni zarządzana usługa, która pozwala deweloperom na wdrażanie aplikacji mikrousług bez zarządzania maszynami wirtualnymi, magazynem i siecią.

W tym przewodniku Szybki Start utworzysz nową aplikację usługi Service Fabric siatki składający się z aplikacji sieci web ASP.NET Core uruchom go na lokalny klaster projektowy, a następnie opublikować je do uruchomienia na platformie Azure.

Konieczna jest subskrypcja platformy Azure. Jeśli nie masz, można łatwo utworzyć bezpłatną subskrypcję platformy Azure, [Utwórz bezpłatne konto](https://azure.microsoft.com/free/) przed przystąpieniem do wykonywania. Będzie trzeba będzie również [Konfigurowanie środowiska dla deweloperów](service-fabric-mesh-howto-setup-developer-environment-sdk.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="create-a-service-fabric-mesh-project"></a>Tworzenie projektu usługi Service Fabric Mesh

Otwórz program Visual Studio i wybierz **pliku** > **New** > **projektu...**

W oknie dialogowym **Nowy projekt** w polu **Wyszukaj** u góry wpisz `mesh`. Wybierz szablon **Aplikacja Service Fabric Mesh**. Jeśli go nie widzisz, upewnij się, że zainstalowano zestaw SDK usługi Mesh i narzędzia VS w wersji zapoznawczej zgodnie z opisem w temacie [Konfigurowanie środowiska projektowego](service-fabric-mesh-howto-setup-developer-environment-sdk.md). 

W **nazwa** wpisz **ServiceFabricMesh1** i **lokalizacji** polu, Ustaw ścieżkę folderu, gdzie będą przechowywane pliki projektu.

Upewnij się, że opcja **Utwórz katalog dla rozwiązania** jest zaznaczona, a następnie kliknij przycisk **OK**, aby utworzyć projekt usługi Service Fabric Mesh.

![Okno dialogowe nowego projektu usługi Service Fabric Mesh w programie Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-project.png)

### <a name="create-a-service"></a>Tworzenie usługi

Po kliknięciu **OK**, **Nowa usługa Service Fabric** zostanie wyświetlone okno dialogowe. Wybierz **platformy ASP.NET Core** typ projektu, upewnij się, że **Container OS** jest ustawiona na **Windows** i kliknij przycisk **OK** do utworzenia projektu platformy ASP.NET Core . 

![Okno dialogowe nowego projektu usługi Service Fabric Mesh w programie Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-service-fabric-service.png)

**Nowa aplikacja internetowa ASP.NET Core** zostanie wyświetlone okno dialogowe. Wybierz **aplikacji sieci Web** a następnie kliknij przycisk **OK**.

![Nowa aplikacja platformy ASP.NET Core w programie Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-new-aspnetcore-app.png)

Program Visual Studio tworzy projekt aplikacji usługi Service Fabric siatki i projekt platformy ASP.NET Core.

## <a name="build-and-publish-to-your-local-cluster"></a>Tworzenie i publikowanie w klastrze lokalnym

Obraz platformy Docker jest wbudowana i automatycznie publikowane w klastrze lokalnym, zaraz po załadowaniu projektu. Ten proces może potrwać pewien czas. Możesz monitorować postęp narzędzi usługi Service Fabric w programie **dane wyjściowe** okna, wybierając **narzędzia usługi Service Fabric** elementu w **dane wyjściowe** okna listy rozwijanej. Możesz kontynuować pracę podczas wdrażania obrazu platformy docker.

Po utworzeniu projektu kliknij **F5** Aby debugować usługę lokalnie. Lokalne wdrożenie zostało zakończone, gdy program Visual Studio jest uruchomiony projektu, zostanie otwarte okno przeglądarki, za pomocą przykładowej strony sieci Web.

Po zakończeniu przeglądania wdrożonej usługi, Zatrzymaj debugowanie projektu przez naciśnięcie klawisza **Shift + F5** w programie Visual Studio.

## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Aby opublikować projekt usługi Service Fabric siatki na platformie Azure, kliknij prawym przyciskiem myszy **projekt usługi Service Fabric siatki** w programie Visual studio i wybierz pozycję **publikowania...**

![Program Visual studio kliknij prawym przyciskiem myszy projekt usługi Service Fabric siatki](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-right-click-publish.png)

Zostanie wyświetlony **publikowanie aplikacji usługi Service Fabric** okna dialogowego.

![Okno dialogowe publikowania usługi Service Fabric Mesh w programie Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-dialog.png)

Wybierz konto i subskrypcję platformy Azure. Wybierz **lokalizację**. W tym artykule jest używany region **Wschodnie stany USA**.

W obszarze **Grupa zasobów** wybierz pozycję **\<Utwórz nową grupę zasobów...>**. **Tworzenie grupy zasobów** zostanie wyświetlone okno dialogowe. Ustaw **nazwy grupy zasobów** i **lokalizacji**.  Ten przewodnik Szybki Start używa **wschodnie stany USA** lokalizacji i nazwy grupy **sfmeshTutorial1RG** (Jeśli Twoja organizacja ma wiele osób przy użyciu tej samej subskrypcji, wybierz unikatową nazwą grupy zasobów).  Kliknij przycisk **Utwórz** Utwórz grupę zasobów i powrócić do okna dialogowego publikowania.

![Okno dialogowe nowej grupy zasobów usługi Service Fabric Mesh w programie Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-resource-group-dialog.png)

Po powrocie do okna **Publikowanie aplikacji usługi Service Fabric** w obszarze **Azure Container Registry** wybierz pozycję **\<Utwórz nowy rejestr kontenerów...>**. W oknie dialogowym **Tworzenie rejestru kontenerów** użyj unikatowej nazwy w polu **Nazwa rejestru kontenerów**. Określ **lokalizacji** (korzysta z tego przewodnika Szybki Start **wschodnie stany USA**). Z listy rozwijanej wybierz **grupę zasobów** utworzoną w poprzednim kroku, na przykład **sfmeshTutorial1RG**. Ustaw **jednostki SKU** do **podstawowe** a następnie kliknij przycisk **Utwórz** aby powrócić do okna dialogowego publikowania.

![Okno dialogowe nowej grupy zasobów usługi Service Fabric Mesh w programie Visual Studio](media/service-fabric-mesh-quickstart-dotnet-core/visual-studio-publish-new-container-registry-dialog.png)

W oknie dialogowym publikowania kliknij **Publikuj** przycisk, aby wdrożyć aplikację usługi Service Fabric siatki na platformie Azure.

W przypadku publikowania na platformie Azure po raz pierwszy obraz platformy docker zostanie wypchnięty do usługi Azure Container Registry (ACR). Czas wykonywania tej czynności zależy od rozmiaru obrazu. Kolejne operacje publikowania tego samego projektu będą wykonywane szybciej. Możesz monitorować postęp wdrażania, wybierając **narzędzia usługi Service Fabric** w programie Visual Studio **dane wyjściowe** okna listy rozwijanej. Po zakończeniu wdrożenia w obszarze danych wyjściowych **Narzędzia usługi Service Fabric** adres IP i port aplikacji zostaną wyświetlone w postaci adresu URL.

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

Aby dowiedzieć się więcej na temat tworzenia i wdrażania aplikacji usługi Service Fabric Mesh, przejdź do samouczka.
> [!div class="nextstepaction"]
> [Tworzenie, debugowanie i wdrażanie aplikacji internetowej usługi Service Fabric Mesh dla wielu usług](service-fabric-mesh-tutorial-create-dotnetcore.md)