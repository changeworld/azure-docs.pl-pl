---
title: Samouczek — wdrażanie aplikacji usługi Service Fabric Mesh w usłudze Service Fabric Mesh | Microsoft Docs
description: Dowiedz się, jak opublikować aplikację usługi Azure Service Fabric Mesh składającą się z witryny internetowej platformy ASP.NET Core, która komunikuje się z usługą internetową zaplecza.
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: f9dea759f6556bc521dda4efbd27176f1e06452b
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/18/2018
ms.locfileid: "39126579"
---
# <a name="tutorial-deploy-a-service-fabric-mesh-web-application"></a>Samouczek: wdrażanie internetowej aplikacji usługi Service Fabric Mesh

Ten samouczek to trzecia część serii. Przedstawiono w nim sposób publikowania internetowej aplikacji usługi Azure Service Fabric Mesh bezpośrednio z poziomu programu Visual Studio.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Publikowanie aplikacji na platformie Azure
> * Sprawdzanie stanu wdrożenia aplikacji
> * Wyświetlanie wszystkich aplikacji aktualnie wdrożonych w subskrypcji
> * Wyświetlanie dzienników aplikacji
> * Czyszczenie zasobów używanych przez aplikację

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Kompilowanie internetowej aplikacji usługi Service Fabric Mesh](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * [Lokalne debugowanie aplikacji](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md)
> * Publikowanie aplikacji na platformie Azure

Dowiesz się, jak utworzyć aplikację usługi Azure Service Fabric Mesh z frontonem internetowym platformy ASP.NET i usługą zaplecza internetowego interfejsu API platformy ASP.NET Core. Następnie przeprowadzisz debugowanie aplikacji w lokalnym klastrze projektowym i opublikujesz ją na platformie Azure. Po zakończeniu będziesz mieć prostą aplikację z zadaniami do wykonania, która przedstawia przeprowadzanie wywołań między usługami w internetowej aplikacji Service Fabric Mesh.

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem możesz utworzyć [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Upewnij się, że masz [skonfigurowane środowisko projektowe](service-fabric-mesh-howto-setup-developer-environment-sdk.md) z zainstalowanym środowiskiem uruchomieniowym usługi Service Fabric, zestawem SDK, platformą Docker i programem Visual Studio 2017.

## <a name="download-the-to-do-sample-application"></a>Pobieranie przykładowej aplikacji z zadaniami do wykonania

Jeśli nie skompilowano przykładowej aplikacji z zadaniami do wykonania w [drugiej części tej serii samouczków](service-fabric-mesh-tutorial-debug-service-fabric-mesh-app.md), można ją pobrać. W oknie polecenia uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

Aplikacja znajduje się w katalogu `src\todolistapp`.

## <a name="publish-to-azure"></a>Publikowanie na platformie Azure

Aby opublikować projekt usługi Service Fabric Mesh na platformie Azure, kliknij prawym przyciskiem myszy pozycję **ServiceFabricMeshApp** w programie Visual Studio i wybierz pozycję **Opublikuj...**

Następnie zobaczysz okno dialogowe **Publikowanie aplikacji usługi Service Fabric**.

![Okno dialogowe publikowania usługi Service Fabric Mesh w programie Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-dialog.png)

Wybierz konto i subskrypcję platformy Azure. Wybierz **lokalizację**. W tym artykule jest używany region **Wschodnie stany USA**.

W obszarze **Grupa zasobów** wybierz pozycję **\<Utwórz nową grupę zasobów...>**. Zostanie wyświetlone okno dialogowe umożliwiające utworzenie nowej grupy zasobów. W tym artykule jest używana lokalizacja **Wschodnie stany USA** i nazwa grupy **sfmeshTutorial1RG** (jeśli w Twojej organizacji wiele osób korzysta z tej samej subskrypcji, wybierz unikatową nazwę grupy).  Naciśnij pozycję **Utwórz**, aby utworzyć grupę zasobów i wrócić do okna dialogowego publikowania.

![Okno dialogowe nowej grupy zasobów usługi Service Fabric Mesh w programie Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-resource-group-dialog.png)

Po powrocie do okna **Publikowanie aplikacji usługi Service Fabric** w obszarze **Azure Container Registry** wybierz pozycję **\<Utwórz nowy rejestr kontenerów...>**. W oknie dialogowym **Tworzenie rejestru kontenerów** użyj unikatowej nazwy w polu **Nazwa rejestru kontenerów**. Określ **lokalizację** (w tym samouczku jest używany region **Wschodnie stany USA**). Z listy rozwijanej wybierz **grupę zasobów** utworzoną w poprzednim kroku, na przykład **sfmeshTutorial1RG**. Ustaw **jednostkę SKU** na **Podstawowa**, a następnie naciśnij pozycję **Utwórz**, aby wrócić do okna dialogowego publikowania.

![Okno dialogowe nowej grupy zasobów usługi Service Fabric Mesh w programie Visual Studio](./media/service-fabric-mesh-tutorial-deploy-dotnetcore/visual-studio-publish-new-container-registry-dialog.png)

Jeśli zostanie wyświetlony błąd wskazujący, że dostawca zasobów nie został zarejestrowany, możesz go zarejestrować. Najpierw sprawdź, czy dostawca zasobów jest dostępny w przypadku Twojej subskrypcji:

```Powershell
Get-AzureRmResourceProvider -ListAvailable
```

Jeśli dostawca rejestru kontenerów (`Microsoft.ContainerRegistry`) jest dostępny, zarejestruj go z poziomu programu PowerShell:

```Powershell
Connect-AzureRmAccount
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.ContainerRegistry
```

W oknie dialogowym publikowania naciśnij przycisk **Opublikuj**, aby wdrożyć aplikację usługi Service Fabric na platformie Azure.

W przypadku publikowania na platformie Azure po raz pierwszy obraz platformy docker zostanie wypchnięty do usługi Azure Container Registry (ACR). Czas wykonywania tej czynności zależy od rozmiaru obrazu. Kolejne operacje publikowania tego samego projektu będą wykonywane szybciej. Aby monitorować postęp wdrażania, możesz wybrać okienko **Narzędzia usługi Service Fabric** w oknie **Dane wyjściowe** programu Visual Studio. Po zakończeniu wdrożenia w obszarze danych wyjściowych **Narzędzia usługi Service Fabric** adres IP i port aplikacji zostaną wyświetlone w postaci adresu URL.

```json
Packaging Application...
Building Images...
Web1 -> C:\Code\ServiceFabricMeshApp\ToDoService\bin\Any CPU\Release\netcoreapp2.0\ToDoService.dll
Uploading the images to Azure Container Registy...
Deploying application to remote endpoint...
The application was deployed successfully and it can be accessed at http://10.000.38.000:20000.
```

Otwórz przeglądarkę internetową i przejdź do adresu URL, aby wyświetlić witrynę internetową działającą na platformie Azure.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Podczas wykonywania pozostałych kroków możesz użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure.

Jeśli zdecydujesz się zainstalować interfejs wiersza polecenia i korzystać z niego lokalnie, ten samouczek będzie wymagać interfejsu wiersza polecenia platformy Azure w wersji 2.0.35 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Aby zainstalować najnowszą wersję interfejsu wiersza polecenia lub uaktualnić do niej, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure 2.0][azure-cli-install].

## <a name="install-the-az-mesh-cli"></a>Instalowanie interfejsu wiersza polecenia az mesh
W oknie interfejsu wiersza polecenia

1) Usuń wszystkie poprzednie instalacje modułu interfejsu wiersza polecenia usługi Azure Service Fabric Mesh.

```cli
az extension remove --name mesh
```

2)  Zainstaluj moduł rozszerzenia interfejsu wiersza polecenia usługi Azure Service Fabric Mesh. W wersji zapoznawczej interfejs wiersza polecenia usługi Azure Service Fabric Mesh jest pisany jako rozszerzenie interfejsu wiersza polecenia platformy Azure, ale w publicznej wersji zapoznawczej będzie on dostarczany jako część interfejsu wiersza polecenia platformy Azure.

```cli
az extension add --source https://sfmeshcli.blob.core.windows.net/cli/mesh-0.8.1-py2.py3-none-any.whl
```

## <a name="check-application-deployment-status"></a>Sprawdzanie stanu wdrożenia aplikacji

Na tym etapie Twoja aplikacja została wdrożona. Jej stan możesz sprawdzić za pomocą polecenia `app show`. 

Nazwa aplikacji w samouczku to `ServiceMeshApp`. Zbierz szczegółowe informacje dotyczące aplikacji za pomocą następującego polecenia:

```azurecli-interactive
az mesh app show --resource-group $rg --name ServiceMeshApp
```

## <a name="see-all-applications-currently-deployed-to-your-subscription"></a>Wyświetlanie wszystkich aplikacji aktualnie wdrożonych w subskrypcji

Możesz użyć polecenia „app list” w celu uzyskania listy aplikacji wdrożonych w subskrypcji.

```cli
az mesh app list --output table
```

## <a name="see-the-application-logs"></a>Wyświetlanie dzienników aplikacji

Sprawdź dzienniki wdrożonej aplikacji:

```azurecli-interactive
az mesh code-package-log get --resource-group $rg --application-name ServiceMeshApp --service-name todoservice --replica-name 0 --code-package-name ServiceMeshApp
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli już nie potrzebujesz żadnych utworzonych zasobów, usuń je. Ponieważ utworzono nową grupę zasobów w celu hostowania zasobów usług ACR i Service Fabric Mesh, można bezpiecznie usunąć tę grupę zasobów. Spowoduje to usunięcie wszystkich skojarzonych zasobów.

```azurecli
az group delete --resource-group sfmeshTutorial1RG
```

```powershell
Remove-AzureRmResourceGroup -Name sfmeshTutorial1RG
```

Alternatywnie możesz usunąć grupę zasobów [z poziomu portalu](../azure-resource-manager/resource-group-portal.md#delete-resource-group-or-resources). 

## <a name="next-steps"></a>Następne kroki

W tej części samouczka przedstawiono informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * Publikowanie aplikacji na platformie Azure
> * Sprawdzanie stanu wdrożenia aplikacji
> * Wyświetlanie wszystkich aplikacji, które zostały aktualnie wdrożone w subskrypcji
> * Wyświetlanie dzienników aplikacji
> * Czyszczenie zasobów używanych przez aplikację

Teraz, po zakończeniu publikowania aplikacji usługi Service Fabric Mesh na platformie Azure, spróbuj wykonać następujące czynności:

* Zapoznaj się z [przykładową aplikacją do głosowania](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp) — innym przykładem komunikacji między usługami
* Przeczytaj temat [Zasoby usługi Service Fabric](service-fabric-mesh-service-fabric-resources.md)
* Przeczytaj więcej o usłudze [Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview)

[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest