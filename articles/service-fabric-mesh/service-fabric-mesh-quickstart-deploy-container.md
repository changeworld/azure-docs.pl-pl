---
title: Przewodnik Szybki start — wdrażanie aplikacji Hello World w usłudze Azure Service Fabric Mesh | Microsoft Docs
description: W tym przewodniku Szybki start przedstawiono sposób wdrażania aplikacji usługi Service Fabric Mesh w usłudze Azure Service Fabric Mesh.
services: service-fabric-mesh
keywords: Nie dodawaj ani nie edytuj słów kluczowych bez konsultacji z ekspertem SEO.
author: rwike77
ms.author: ryanwi
ms.date: 11/27/2018
ms.topic: quickstart
ms.service: service-fabric-mesh
manager: timlt
ms.openlocfilehash: ce3001a2984726332b036eea69d4e18e3d7d300b
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/05/2018
ms.locfileid: "52890436"
---
# <a name="quickstart-deploy-hello-world-to-service-fabric-mesh"></a>Szybki start: wdrażanie aplikacji Hello World w usłudze Service Fabric Mesh

Usługa [Service Fabric Mesh](service-fabric-mesh-overview.md) ułatwia tworzenie aplikacji mikrousług i zarządzanie nimi na platformie Azure bez konieczności aprowizowania maszyn wirtualnych. W tym przewodniku Szybki start utworzysz aplikację Hello World na platformie Azure i udostępnisz ją w Internecie. Ta operacja jest wykonywana za pomocą jednego polecenia. W ciągu kilku minut ten widok zostanie wyświetlony w przeglądarce:

![Aplikacja Hello World w przeglądarce][sfm-app-browser]

[!INCLUDE [preview note](./includes/include-preview-note.md)]

Jeśli nie masz jeszcze konta platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="set-up-service-fabric-mesh-cli"></a>Konfigurowanie interfejsu wiersza polecenia usługi Service Fabric Mesh 
Podczas pracy z tym przewodnikiem Szybki start możesz użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Zainstaluj moduł rozszerzenia interfejsu wiersza polecenia usługi Azure Service Fabric Mesh, korzystając z tych [instrukcji](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Zaloguj się do platformy Azure i wybierz swoją subskrypcję.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów
Utwórz grupę zasobów, w której zostanie wdrożona aplikacja. Możesz użyć istniejącej grupy zasobów i pominąć ten krok. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application"></a>Wdrażanie aplikacji
Utwórz aplikację w grupie zasobów przy użyciu polecenia `az mesh deployment create`.  Uruchom następujące polecenie:

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://raw.githubusercontent.com/Azure-Samples/service-fabric-mesh/master/templates/helloworld/mesh_rp.linux.json --parameters "{'location': {'value': 'eastus'}}" 
```

Poprzednie polecenie wdraża aplikację systemu Linux przy użyciu [szablonu mesh_rp.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.linux.json). Jeśli chcesz wdrożyć aplikację systemu Windows, użyj [szablonu mesh_rp.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.windows.json). Obrazy kontenerów systemu Windows są większe niż obrazy kontenerów systemu Linux i ich wdrożenie może zająć więcej czasu.

To polecenie powoduje wygenerowanie fragmentu kodu JSON, który pokazano poniżej. W sekcji ```outputs``` danych wyjściowych JSON skopiuj właściwość ```publicIPAddress```.

```json
"outputs": {
    "publicIPAddress": {
    "type": "String",
    "value": "40.83.78.216"
    }
}
```

Te dane pochodzą z sekcji ```outputs``` szablonu ARM. Jak pokazano poniżej, ta sekcja przywołuje zasób bramy w celu pobrania publicznego adresu IP. 

```json
  "outputs": {
    "publicIPAddress": {
      "value": "[reference('helloWorldGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>Otwieranie aplikacji
Po pomyślnym wdrożeniu aplikacji skopiuj publiczny adres IP punktu końcowego usługi z danych wyjściowych interfejsu wiersza polecenia. Otwórz adres IP w przeglądarce internetowej. Zostanie wyświetlona strona internetowa z logo usługi Azure Service Fabric Mesh.

## <a name="check-the-application-details"></a>Sprawdzanie szczegółów aplikacji
Stan aplikacji możesz sprawdzić przy użyciu polecenia `az mesh app show`. To polecenie udostępnia przydatne informacje, na które można zareagować.

W tym przewodniku Szybki start nazwa aplikacji to`helloWorldApp`. Aby zebrać szczegółowe informacje dotyczące aplikacji, wykonaj następujące polecenie:

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name helloWorldApp
```

## <a name="see-the-application-logs"></a>Wyświetlanie dzienników aplikacji

Sprawdź dzienniki wdrożonej aplikacji przy użyciu polecenia `az mesh code-package-log get`:

```azurecli-interactive
az mesh code-package-log get --resource-group myResourceGroup --application-name helloWorldApp --service-name helloWorldService --replica-name 0 --code-package-name helloWorldCode
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Gdy wszystko będzie gotowe do usunięcia aplikacji, uruchom polecenie [az group delete][az-group-delete], aby usunąć grupę zasobów oraz zawarte w niej zasoby aplikacji i sieci.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat tworzenia i wdrażania aplikacji usługi Service Fabric Mesh, przejdź do samouczka.
> [!div class="nextstepaction"]
> [Tworzenie i wdrażanie aplikacji internetowej dla wielu usług](service-fabric-mesh-tutorial-create-dotnetcore.md)

<!-- Images -->
[sfm-app-browser]: ./media/service-fabric-mesh-quickstart-deploy-container/HelloWorld.png

<!-- Links / Internal -->
[az-group-delete]: /cli/azure/group#az_group_delete
[azure-cli-install]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest
