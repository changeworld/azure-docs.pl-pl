---
title: Wdrażanie aplikacji logiki przy użyciu szablonów usługi Azure Resource Manager — Azure Logic Apps
description: Wdrażanie aplikacji logiki przy użyciu szablonów usługi Azure Resource Manager
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.assetid: 7574cc7c-e5a1-4b7c-97f6-0cffb1a5d536
ms.date: 10/15/2017
ms.openlocfilehash: 7543859a916de97d471db2894887e640db51dfc2
ms.sourcegitcommit: 0a3efe5dcf56498010f4733a1600c8fe51eb7701
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2019
ms.locfileid: "58893428"
---
# <a name="deploy-logic-apps-with-azure-resource-manager-templates"></a>Wdrażanie aplikacji logiki przy użyciu szablonów usługi Azure Resource Manager

Po utworzeniu szablonu usługi Azure Resource Manager do wdrażania aplikacji logiki, można wdrożyć szablon, w następujący sposób:

* [Azure Portal](#portal)
* [Azure PowerShell](#powershell)
* [Interfejs wiersza polecenia platformy Azure](#cli)
* [Interfejs API REST usługi Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy-rest.md)
* [Potoki usługi Azure DevOps platformy Azure](#azure-pipelines)

<a name="portal"></a>

## <a name="deploy-through-azure-portal"></a>Wdrażanie za pośrednictwem witryny Azure portal

Aby automatycznie wdrożyć szablon aplikacji logiki na platformie Azure, możesz wybrać następujące **Wdróż na platformie Azure** przycisk, który zaloguje Cię do witryny Azure portal i wyświetli monit o podanie informacji o aplikacji logiki. Następnie można wprowadzić wymagane zmiany do szablonu aplikacji logiki lub parametrów.

[![Deploy na platformie Azure](./media/logic-apps-create-deploy-azure-resource-manager-templates/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

Na przykład zostanie wyświetlony monit o te informacje po zalogowaniu się do witryny Azure portal:

* Nazwa subskrypcji platformy Azure
* Grupy zasobów, której chcesz użyć
* Lokalizacja aplikacji logiki
* Nazwa aplikacji logiki
* Test identyfikatora URI
* Akceptacja określonego warunków i postanowień

Aby uzyskać więcej informacji, zobacz [wdrażanie zasobów za pomocą szablonów usługi Azure Resource Manager i witryny Azure portal](../azure-resource-manager/resource-group-template-deploy-portal.md).

## <a name="authorize-oauth-connections"></a>Autoryzowania połączeń protokołu OAuth

Po wdrożeniu aplikacja logiki działa end-to-end przy użyciu prawidłowych parametrów. Należy jednak nadal autoryzować połączenia protokołu OAuth do wygenerowania tokenu dostępu nie jest ważna. W przypadku zautomatyzowanych wdrożeń, możesz użyć skryptu, który wyraża zgodę na każde połączenie protokołu OAuth, takich jak to [przykładowy skrypt w projekcie usługi GitHub LogicAppConnectionAuth](https://github.com/logicappsio/LogicAppConnectionAuth). Możesz również autoryzować połączenia OAuth za pomocą witryny Azure portal lub w programie Visual Studio, otwierając aplikację logiki w Projektancie aplikacji logiki.

<a name="powershell"></a>

## <a name="deploy-with-azure-powershell"></a>Wdrażanie przy użyciu programu Azure PowerShell

Do wdrożenia z konkretnym *grupy zasobów platformy Azure*, użyj tego polecenia:

```powershell
New-AzResourceGroupDeployment -ResourceGroupName <Azure-resource-group-name> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

Aby wdrożyć w określonej subskrypcji platformy Azure, użyj tego polecenia:

```powershell
New-AzDeployment -Location <location> -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json 
```

* [Deploy resources with Resource Manager templates and Azure PowerShell (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i programu Azure PowerShell)](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy)
* [`New-AzResourceGroupDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroupdeployment)
* [`New-AzDeployment`](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azdeployment)

<a name="cli"></a>

## <a name="deploy-with-azure-cli"></a>Wdrażanie przy użyciu interfejsu wiersza polecenia platformy Azure

Do wdrożenia z konkretnym *grupy zasobów platformy Azure*, użyj tego polecenia:

```azurecli
az group deployment create -g <Azure-resource-group-name> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Aby wdrożyć w określonej subskrypcji platformy Azure, użyj tego polecenia:

```azurecli
az deployment create --location <location> --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json
```

Aby uzyskać więcej informacji zobacz następujące tematy: 

* [Deploy resources with Resource Manager templates and Azure CLI (Wdrażanie zasobów za pomocą szablonów usługi Resource Manager i interfejsu wiersza polecenia platformy Azure)](../azure-resource-manager/resource-group-template-deploy-cli.md) 
* [`az group deployment create`](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create)
* [`az deployment create`](https://docs.microsoft.com/cli/azure/deployment?view=azure-cli-latest#az-deployment-create)

<a name="azure-pipelines"></a>

## <a name="deploy-with-azure-devops"></a>Wdrażanie przy użyciu usługi Azure DevOps

Aby wdrożyć szablony aplikacji logiki i zarządzać środowiskami, zespoły często użyć narzędzia takie jak [potoki Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/what-is-azure-pipelines) w [DevOps platformy Azure](https://docs.microsoft.com/azure/devops/user-guide/what-is-azure-devops-services). Potoki usługi Azure udostępnia [zadania wdrożenia grupy zasobów platformy Azure](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzureResourceGroupDeploymentV2) można dodać do dowolnej kompilacji lub potoku wydania.
Na potrzeby autoryzacji do wdrażania i generowanie potoku tworzenia wersji należy również usługi Azure Active Directory (AD) [nazwy głównej usługi](../active-directory/develop/app-objects-and-service-principals.md). Dowiedz się więcej o [używania jednostek usług za pomocą potoków Azure](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure). 

Poniżej przedstawiono ogólne kroki wysokiego poziomu przy użyciu potoków usługi Azure:

1. W potokach Azure utworzyć pustym potoku.

1. Wybierz zasoby, czego potrzebujesz do potoku, takie jak szablon aplikacji logiki i plików parametrów szablonów, które generują ręcznie lub jako część procesu kompilacji.

1. Dla zadania agenta, znajduj i dodawaj **wdrożenie grupy zasobów Azure** zadania.

   ![Dodaj zadanie "Wdrożenie grupy zasobów platformy Azure"](./media/logic-apps-create-deploy-template/add-azure-resource-group-deployment-task.png)

1. Konfigurowanie przy użyciu [nazwy głównej usługi](https://docs.microsoft.com/azure/devops/pipelines/library/connect-to-azure). 

1. Dodaj odwołania do szablonu aplikacji logiki i plików parametrów szablonu.

1. Nadal nad kroki procesu tworzenia wersji dla dowolnego środowiska, testów automatycznych lub osób zatwierdzających, zgodnie z potrzebami.

## <a name="get-support"></a>Uzyskiwanie pomocy technicznej

Jeśli masz pytania, odwiedź [forum usługi Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).

## <a name="next-steps"></a>Kolejne kroki

> [!div class="nextstepaction"]
> [Monitorowanie aplikacji logiki](../logic-apps/logic-apps-monitor-your-logic-apps.md)
