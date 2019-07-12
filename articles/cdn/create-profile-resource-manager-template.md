---
title: Przewodnik Szybki Start — Tworzenie usługi Azure Content Delivery Network profilu i punktu końcowego przy użyciu szablonów usługi Resource Manager | Dokumentacja firmy Microsoft
description: Dowiedz się, jak utworzyć profil usługi Azure Content Delivery Network i punktu końcowego przy użyciu szablonów usługi Resource Manager
services: cdn
documentationcenter: ''
author: senthuransivananthan
manager: danielgi
editor: ''
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: azure-cdn
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: cbde4c7fd568e6d9ff9a0d90332da96926e08077
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593133"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint-using-resource-manager-template"></a>Szybki start: Tworzenie usługi Azure CDN profilu i punktu końcowego przy użyciu szablonu usługi Resource Manager

W tym przewodniku Szybki Start wdrożysz szablonu usługi Azure Resource Manager przy użyciu interfejsu wiersza polecenia. Tworzony szablon wdraża profil usługi CDN i punktu końcowego usługi CDN do aplikacji sieci web frontonu.
Aby wykonać te kroki powinno zająć około 10 minut.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prequisites"></a>Wymagania wstępne

Na potrzeby tego przewodnika Szybki Start konieczne jest posiadanie aplikacji sieci Web do użycia jako źródło. Przykład, aplikacja sieci Web używane w tym przewodniku Szybki Start została wdrożona do https://cdndemo.azurewebsites.net

Aby uzyskać więcej informacji, zobacz [Tworzenie statycznej aplikacji sieci web w języku HTML na platformie Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html).

## <a name="create-a-resource-group"></a>Tworzenie grupy zasobów

Wszystkie zasoby, musi zostać wdrożony w tej samej grupie zasobów.

Utwórz grupę zasobów w wybranej lokalizacji. W tym przykładzie pokazano tworzenie grupy zasobów o nazwie usługę cdn w lokalizacji wschodnie stany USA.

```bash
az group create --name cdn --location eastus
```

![Nowa grupa zasobów](./media/create-profile-resource-manager-template/cdn-create-resource-group.png)

## <a name="create-the-resource-manager-template"></a>Tworzenie szablonu usługi Resource Manager

W tym kroku utworzysz plik szablonu, który służy do wdrażania zasobów.

Chociaż w tym przykładzie przedstawiono scenariusz ogólne przyspieszania witryny sieci Web, istnieje wiele ustawień, które można skonfigurować. Te ustawienia są dostępne w odwołaniu do szablonu usługi Azure Resource Manager. Zobacz odwołania do [profil CDN](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles) i [punktu końcowego profilu CDN](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints).

Należy pamiętać, że Microsoft CDN nie obsługuje modyfikowania lista typów zawartości.

Zapisz szablon jako **cdn.json-resource-manager**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "cdnProfileSku": {
            "type": "string",
            "allowedValues": [
                "Standard_Microsoft",
                "Standard_Akamai",
                "Standard_Verizon",
                "Premium_Verizon"
            ]
        },
        "endpointOriginHostName": {
            "type": "string"
        }
    },
    "variables": {
        "profile": {
            "name": "[replace(toLower(parameters('cdnProfileSku')), '_', '-')]"
        },
        "endpoint": {
            "name": "[replace(toLower(parameters('endpointOriginHostName')), '.', '-')]",
            "originHostName": "[parameters('endpointOriginHostName')]"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Cdn/profiles",
            "apiVersion": "2017-10-12",
            "location": "[resourceGroup().location]",
            "name": "[variables('profile').name]",
            "sku": {
                "name": "[parameters('cdnProfileSku')]"
            }
        },
        {
            "dependsOn": [
                "[resourceId('Microsoft.Cdn/profiles', variables('profile').name)]"
            ],
            "type": "Microsoft.Cdn/profiles/endpoints",
            "apiVersion": "2017-10-12",
            "location": "[resourceGroup().location]",
            "name": "[concat(variables('profile').name, '/', variables('endpoint').name)]",
            "properties": {
                "hostName": "[concat(variables('endpoint').name, '.azureedge.net')]",
                "originHostHeader": "[variables('endpoint').originHostName]",
                "isHttpAllowed": true,
                "isHttpsAllowed": true,
                "queryStringCachingBehavior": "IgnoreQueryString",
                "origins": [
                    {
                        "name": "[replace(variables('endpoint').originHostName, '.', '-')]",
                        "properties": {
                            "hostName": "[variables('endpoint').originHostName]",
                            "httpPort": 80,
                            "httpsPort": 443
                        }
                    }
                ],
                "contentTypesToCompress": [
                    "application/eot",
                    "application/font",
                    "application/font-sfnt",
                    "application/javascript",
                    "application/json",
                    "application/opentype",
                    "application/otf",
                    "application/pkcs7-mime",
                    "application/truetype",
                    "application/ttf",
                    "application/vnd.ms-fontobject",
                    "application/xhtml+xml",
                    "application/xml",
                    "application/xml+rss",
                    "application/x-font-opentype",
                    "application/x-font-truetype",
                    "application/x-font-ttf",
                    "application/x-httpd-cgi",
                    "application/x-javascript",
                    "application/x-mpegurl",
                    "application/x-opentype",
                    "application/x-otf",
                    "application/x-perl",
                    "application/x-ttf",
                    "font/eot",
                    "font/ttf",
                    "font/otf",
                    "font/opentype",
                    "image/svg+xml",
                    "text/css",
                    "text/csv",
                    "text/html",
                    "text/javascript",
                    "text/js",
                    "text/plain",
                    "text/richtext",
                    "text/tab-separated-values",
                    "text/xml",
                    "text/x-script",
                    "text/x-component",
                    "text/x-java-source"
                ],
                "isCompressionEnabled": true,
                "optimizationType": "GeneralWebDelivery"
            }
        }
    ],
    "outputs": {
        "cdnUrl": {
            "type": "string",
            "value": "[concat('https://', variables('endpoint').name, '.azureedge.net')]"
        }
    }
}
```

## <a name="create-the-resources"></a>Tworzenie zasobów

Wdrażanie szablonu przy użyciu wiersza polecenia platformy Azure. Zostanie wyświetlony monit dla danych wejściowych 2:

**cdnProfileSku** -u dostawcy sieci CDN, którego chcesz używać. Dostępne opcje to:

* Standard_Microsoft
* Standard_Akamai
* Standard_Verizon
* Premium_Verizon.

**endpointOriginHostName** — punkt końcowy, który zostanie dostarczony za pośrednictwem usługi CDN, na przykład cdndemo.azurewebsites.net.

```bash
az group deployment create --resource-group cdn --template-file arm-cdn.json
```

![Wdrażanie szablonu usługi Resource Manager](./media/create-profile-resource-manager-template/cdn-deploy-resource-manager.png)

## <a name="view-the-cdn-profile"></a>Wyświetl profil CDN

```bash
az cdn profile list --resource-group cdn -o table
```

![Wyświetl profil CDN](./media/create-profile-resource-manager-template/cdn-view-profile.png)

## <a name="view-the-cdn-endpoint-for-the-profile-standard-microsoft"></a>Wyświetl punktu końcowego usługi CDN dla profilu standard firmy microsoft

```bash
az cdn endpoint list --profile-name standard-microsoft --resource-group cdn -o table
```

![Punkt końcowy usługi CDN widoku](./media/create-profile-resource-manager-template/cdn-view-endpoint.png)

Użyj nazwy hosta, aby wyświetlić zawartość. Na przykład uzyskać dostęp https://cdndemo-azurewebsites-net.azureedge.net przy użyciu przeglądarki.

## <a name="clean-up"></a>Czyszczenie

Usunięcie grupy zasobów spowoduje automatyczne usunięcie wszystkich zasobów, które zostały wdrożone w niej.

```bash
az group delete --name cdn
```

![Usuń grupę zasobów](./media/create-profile-resource-manager-template/cdn-delete-resource-group.png)

## <a name="references"></a>Odwołania

* Profil CDN - [dokumentacja szablonu usługi Azure Resource Manager](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles)
* Punkt końcowy usługi CDN - [dokumentacji dokumentacja szablonu usługi Azure Resource Manager](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints)

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat dodawania domeny niestandardowej do punktu końcowego usługi CDN, zapoznaj się z następującym samouczkiem:

> [!div class="nextstepaction"]
> [Samouczek: Dodawanie domeny niestandardowej do punktu końcowego usługi Azure CDN](cdn-map-content-to-custom-domain.md)
