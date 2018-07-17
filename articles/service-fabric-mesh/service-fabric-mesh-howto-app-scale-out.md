---
title: Skalowanie usług w aplikacji usługi Azure Service Fabric siatki | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i niezależne skalowanie usług w ramach aplikacji uruchomionej na usługi Service Fabric siatki, przy użyciu wiersza polecenia platformy Azure.
services: service-fabric
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
ms.date: 06/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: c0350b767b65aee0c4611bb8fa6f635a651d33dc
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076306"
---
# <a name="scale-services-within-an-application-running-on-service-fabric-mesh"></a>Skalowanie usług w ramach aplikacji uruchomionej na usługi Service Fabric siatki

W tym artykule przedstawiono sposób niezależne skalowanie mikrousług w aplikacji. W tym przykładzie aplikacja obiektów wizualnych składa się z dwóch mikrousług; `web` i `worker`. 

`web` Usługi jest aplikacją programu ASP.NET Core przy użyciu strony sieci web, który pokazuje trójkąty w przeglądarce. W przeglądarce pojawi się jeden trójkąt dla każdego wystąpienia `worker` usługi. 

`worker` Usługa przenosi trójkąt na wstępnie zdefiniowanego przedziału czasu, w obszarze i wysyła lokalizacji trójkąt `web` usługi. Używa DNS do rozpoznania adresu `web` usługi.

## <a name="set-up-service-fabric-mesh-cli"></a>Konfigurowanie usługi Service Fabric siatki interfejsu wiersza polecenia 
Aby zakończyć to zadanie, można użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Instalowanie modułu rozszerzenia usługi Service Fabric siatki wiersza polecenia platformy Azure, wykonując te [instrukcje](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure
Logowanie do platformy Azure i ustawić subskrypcję.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów
Utwórz grupę zasobów, aby wdrożyć aplikację. Można użyć istniejącej grupy zasobów i pominąć ten krok. 

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application-with-one-worker-service"></a>Wdrażanie aplikacji za pomocą jednego procesu roboczego usługi
Tworzenie aplikacji w grupie zasobów przy użyciu `deployment create` polecenia.

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```
Poprzednie polecenie wdraża Linux przy użyciu [szablonu mesh_rp.base.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json). Jeśli chcesz wdrożyć aplikację Windows, użyj [szablonu mesh_rp.base.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.windows.json). Obrazy kontenerów Windows są większe niż obrazy kontenerów systemu Linux i może zająć więcej czasu, aby wdrożyć.

W ciągu kilku minut polecenia powinny zostać zwrócone przy użyciu:

`visualObjectsApp has been deployed successfully on visualObjectsNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Otwórz aplikację
Po pomyślnym wdrożeniu aplikacji, Uzyskaj publiczny adres IP punktu końcowego usługi, a następnie otwórz go w przeglądarce. Powinna zostać wyświetlona strona sieci web za pomocą jednego trójkąt przechodzenia przez miejsce.

Polecenie wdrożenia zwraca publiczny adres IP punktu końcowego usługi. Można także badać zasobu sieciowego, aby znaleźć publiczny adres IP punktu końcowego usługi.
 
Nazwa zasobu sieci dla tej aplikacji jest `visualObjectsNetwork`, pobierania informacji o nim przy użyciu następującego polecenia. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name visualObjectsNetwork
```

## <a name="scale-worker-service"></a>Skala `worker` usługi

Skala `worker` usługi, aby uzyskać trzy wystąpienia, używając następującego polecenia. 

```azurecli-interactive
az mesh deployment create --resource-group <resourceGroupName> --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```
Poprzednie polecenie wdraża Linux przy użyciu [szablonu mesh_rp.scaleout.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json). Jeśli chcesz wdrożyć aplikację Windows, użyj [szablonu mesh_rp.scaleout.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.windows.json). Obrazy kontenerów Windows są większe niż obrazy kontenerów systemu Linux i może zająć więcej czasu, aby wdrożyć.

Po pomyślnym wdrożeniu aplikacji przeglądarki powinien wyświetlanie strony sieci web za pomocą trzech trójkąty przechodzenia przez miejsce.

## <a name="delete-the-resources"></a>Usuń zasoby

Aby zaoszczędzić ograniczonych zasobów przydzielonych do programu wersji zapoznawczej, Usuń zasoby często. Aby usunąć zasoby związane z tym przykładzie, Usuń grupę zasobów, w którym zostały wdrożone.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>Kolejne kroki
- Wyświetlanie obiektów wizualnych przykładowej aplikacji na [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects).
- Aby dowiedzieć się więcej o modelu zasobów sieci szkieletowej usług, zobacz [siatki modelu zasobów usługi Service Fabric](service-fabric-mesh-service-fabric-resources.md).
- Aby dowiedzieć się więcej na temat usługi Service Fabric siatki, przeczytaj [Omówienie usługi Service Fabric siatki](service-fabric-mesh-overview.md).