---
title: Wdrażanie aplikacji w usłudze Azure Service Fabric siatki za pomocą szablonu | Dokumentacja firmy Microsoft
description: Dowiedz się, jak wdrożyć aplikację platformy .NET Core do usługi Service Fabric siatki z szablonu przy użyciu wiersza polecenia platformy Azure.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/12/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 356e8019f9a4a64cb1c1c113d0f44990aa4e0f95
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2018
ms.locfileid: "42054548"
---
# <a name="deploy-a-service-fabric-mesh-application-to-service-fabric-mesh-using-a-template"></a>Wdrażanie aplikacji usługi Service Fabric siatki w usłudze Service Fabric siatki, przy użyciu szablonu
W tym artykule przedstawiono sposób wdrażania aplikacji .NET Core do usługi Service Fabric siatki, przy użyciu szablonu. Gdy skończysz, masz aplikację do głosowania z internetową frontonu ASP.NET Core, zapisuje wyniki głosowania w usłudze zaplecza w klastrze. Frontonu przy użyciu serwera DNS do rozpoznawania adresów usługi zaplecza.

## <a name="set-up-service-fabric-mesh-cli"></a>Konfigurowanie interfejsu wiersza polecenia usługi Service Fabric Mesh 
Aby zakończyć to zadanie, można użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Zainstaluj moduł rozszerzenia interfejsu wiersza polecenia usługi Azure Service Fabric Mesh, korzystając z tych [instrukcji](service-fabric-mesh-howto-setup-cli.md).

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
Tworzenie aplikacji w grupie zasobów przy użyciu `deployment create` polecenia.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

Poprzednie polecenie służy do wdrażania aplikacji Windows za pomocą [szablonu mesh_rp.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.windows.json). Jeśli chcesz wdrożyć aplikację systemu Linux, użyj [szablonu mesh_rp.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/voting/mesh_rp.linux.json). Obrazy kontenerów systemu Windows są większe niż obrazy kontenerów systemu Linux i ich wdrożenie może zająć więcej czasu.

W ciągu kilku minut polecenia powinny zostać zwrócone przy użyciu:

`VotingApp has been deployed successfully on VotingAppNetwork with public ip address <IP address>` 

## <a name="open-the-application"></a>Otwieranie aplikacji
Po pomyślnym wdrożeniu aplikacji, Uzyskaj publiczny adres IP punktu końcowego usługi, a następnie otwórz go w przeglądarce. Wyświetla następujące strony sieci web. 

![Aplikacja do głosowania](./media/service-fabric-mesh-howto-deploy-app-template/VotingApplication.png)

Możesz teraz dodać Opcje głosowania do aplikacji i oddawać głosy na nim lub usunąć opcji głosowania.

Polecenie wdrożenia zwraca publiczny adres IP punktu końcowego usługi. Opcjonalnie można także badać zasobu sieciowego, aby znaleźć publiczny adres IP punktu końcowego usługi. 

Nazwa zasobu sieci dla tej aplikacji jest `VotingAppNetwork`, pobierania informacji o nim przy użyciu następującego polecenia. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name VotingAppNetwork
```

## <a name="check-the-application-details"></a>Sprawdzanie szczegółów aplikacji
Stan aplikacji możesz sprawdzić przy użyciu polecenia `app show`. Nazwa aplikacji do wdrożonej aplikacji jest "VotingApp", więc pobrać jego szczegóły. 

```azurecli-interactive
az mesh app show --resource-group myResourceGroup --name VotingApp
```

## <a name="list-the-deployed-applications"></a>Wyświetlanie listy wdrożonych aplikacji
Możesz użyć polecenia „app list” w celu uzyskania listy aplikacji wdrożonych w subskrypcji. 

```azurecli-interactive
az mesh app list -o table
```

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Gdy już nie potrzebujesz aplikacji i jest powiązana zasobów, Usuń grupę zasobów zawierającą je. 

```azurecli-interactive
az group delete --resource-group myResourceGroup  
``` 

## <a name="next-steps"></a>Kolejne kroki
- Wyświetl przykładowej aplikacji do głosowania na [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/votingapp).
- Aby dowiedzieć się więcej na temat usługi Service Fabric siatki, przeczytaj [Omówienie usługi Service Fabric siatki](service-fabric-mesh-overview.md).


