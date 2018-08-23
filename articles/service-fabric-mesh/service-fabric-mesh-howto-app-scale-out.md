---
title: Skalowanie usług w aplikacji usługi Azure Service Fabric siatki | Dokumentacja firmy Microsoft
description: Dowiedz się, jak i niezależne skalowanie usług w ramach aplikacji uruchomionej na usługi Service Fabric siatki, przy użyciu wiersza polecenia platformy Azure.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/08/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: e68bcd135c33c7fd83908b8fed0fd098a698fd36
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/10/2018
ms.locfileid: "42061363"
---
# <a name="scale-services-within-an-application-running-on-service-fabric-mesh"></a>Skalowanie usług w ramach aplikacji uruchomionej na usługi Service Fabric siatki

W tym artykule przedstawiono sposób niezależne skalowanie mikrousług w aplikacji. W tym przykładzie aplikacja obiektów wizualnych składa się z dwóch mikrousług: `web` i `worker`.

`web` Usługi jest aplikacją programu ASP.NET Core przy użyciu strony sieci web, który pokazuje trójkąty w przeglądarce. W przeglądarce pojawi się jeden trójkąt dla każdego wystąpienia `worker` usługi.

`worker` Usługa przenosi trójkąt na wstępnie zdefiniowanego przedziału czasu, w obszarze i wysyła lokalizacji trójkąt `web` usługi. Używa DNS do rozpoznania adresu `web` usługi.

## <a name="set-up-service-fabric-mesh-cli"></a>Konfigurowanie interfejsu wiersza polecenia usługi Service Fabric Mesh

Aby zakończyć to zadanie, można użyć usługi Azure Cloud Shell lub lokalnej instalacji interfejsu wiersza polecenia platformy Azure. Zainstaluj moduł rozszerzenia interfejsu wiersza polecenia usługi Azure Service Fabric Mesh, korzystając z tych [instrukcji](service-fabric-mesh-howto-setup-cli.md).

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do platformy Azure i wybierz swoją subskrypcję.

```azurecli-interactive
az login
az account set --subscription "<subscriptionID>"
```

## <a name="create-resource-group"></a>Tworzenie grupy zasobów

Utwórz grupę zasobów, w której zostanie wdrożona aplikacja.

```azurecli-interactive
az group create --name myResourceGroup --location eastus 
```

## <a name="deploy-the-application-with-one-worker-service"></a>Wdrażanie aplikacji za pomocą jednego procesu roboczego usługi

Tworzenie aplikacji w grupie zasobów przy użyciu `deployment create` polecenia.

Poniższy przykład służy do wdrażania aplikacji systemu Linux za pomocą [szablonu mesh_rp.base.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json). Aby wdrożyć aplikację Windows, użyj [[szablonu mesh_rp.base.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.windows.json). Obrazy kontenerów systemu Windows są większe niż obrazy kontenerów systemu Linux i ich wdrożenie może zająć więcej czasu.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.base.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
```

W ciągu kilku minut polecenie powinny zostać zwrócone przy użyciu:

`visualObjectsApp has been deployed successfully on visualObjectsNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Otwieranie aplikacji

Polecenie wdrożenia zwróci publiczny adres IP punktu końcowego usługi. Po pomyślnym wdrożeniu aplikacji, Uzyskaj publiczny adres IP punktu końcowego usługi, a następnie otwórz go w przeglądarce. Wyświetli stronę sieci web z trójkątem przenoszenia.

Nazwa zasobu sieci dla tej aplikacji jest `visualObjectsNetwork`. Informacje na temat aplikacji, takie jak jej opis, lokalizację, grupę zasobów, itp. można wyświetlić za pomocą następującego polecenia:

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name visualObjectsNetwork
```

## <a name="scale-worker-service"></a>Skala `worker` usługi

Skala `worker` usługi, aby uzyskać trzy wystąpienia, używając następującego polecenia. Poniższy przykład służy do wdrażania aplikacji systemu Linux za pomocą [szablonu mesh_rp.scaleout.linux.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json). Aby wdrożyć aplikację Windows, należy użyć [szablonu mesh_rp.scaleout.windows.json](https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.windows.json). Należy pamiętać, czy większych obrazów kontenerów może potrwać dłużej wdrożenia.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/visualobjects/mesh_rp.scaleout.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}}"
  
```

Po pomyślnym wdrożeniu aplikacji w przeglądarce powinna zostać wyświetlona strony sieci web z trzema trójkąty przenoszenie.

## <a name="delete-the-resources"></a>Usuń zasoby

Często usunąć zasoby, których już używasz na platformie Azure. Aby usunąć zasoby związane z tym przykładzie, należy usunąć grupę zasobów, w którym zostały wdrożone (co spowoduje usunięcie wszystkich skojarzonych z tą grupą zasobów) za pomocą następującego polecenia:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

## <a name="next-steps"></a>Kolejne kroki

- Wyświetlanie obiektów wizualnych przykładowej aplikacji na [GitHub](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/visualobjects).
- Aby dowiedzieć się więcej o modelu zasobów sieci szkieletowej usług, zobacz [siatki modelu zasobów usługi Service Fabric](service-fabric-mesh-service-fabric-resources.md).
- Aby dowiedzieć się więcej na temat usługi Service Fabric siatki, przeczytaj [Omówienie usługi Service Fabric siatki](service-fabric-mesh-overview.md).