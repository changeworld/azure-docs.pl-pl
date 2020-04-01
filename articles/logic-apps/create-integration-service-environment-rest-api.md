---
title: Tworzenie środowisk usług integracji (ISE) za pomocą interfejsu API REST aplikacji logiki
description: Tworzenie środowiska usługi integracji (ISE) przy użyciu interfejsu API REST aplikacji logiki, dzięki czemu można uzyskać dostęp do sieci wirtualnych platformy Azure (VNETs) z usługi Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: conceptual
ms.date: 03/11/2020
ms.openlocfilehash: 0670331d2338b4b6419ffbff1452b5fbac91029f
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478832"
---
# <a name="create-an-integration-service-environment-ise-by-using-the-logic-apps-rest-api"></a>Tworzenie środowiska usługi integracji (ISE) przy użyciu interfejsu API REST aplikacji logiki

W tym artykule pokazano, jak utworzyć [ *środowisko usługi integracji* (ISE)](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) za pośrednictwem interfejsu API REST aplikacji logiki dla scenariuszy, w których aplikacje logiki i konta integracji potrzebują dostępu do [sieci wirtualnej platformy Azure.](../virtual-network/virtual-networks-overview.md) Środowisko ISE jest izolowanym środowiskiem, które używa dedykowanego magazynu i innych zasobów, które są oddzielone od "globalnej" usługi aplikacji logiki z wieloma dzierżawami. Ta separacja zmniejsza również wpływ innych dzierżaw platformy Azure na wydajność aplikacji. Ise zapewnia również własne statyczne adresy IP. Te adresy IP są oddzielone od statycznych adresów IP, które są współużytkowane przez aplikacje logiki w publicznej, usługi wielu dzierżawców.

Można również utworzyć ise przy użyciu [przykładowego szablonu szybkiego startu usługi Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/201-integration-service-environment) lub za pomocą [witryny Azure portal](../logic-apps/connect-virtual-network-vnet-isolated-environment.md).

> [!IMPORTANT]
> Aplikacje logiki, wbudowane wyzwalacze, wbudowane akcje i łączniki uruchamiane w interfejsie ISE używają planu cenowego innego niż plan cenowy oparty na zużyciu. Aby dowiedzieć się, jak działają ceny i rozliczenia dla firm ISE, zobacz [model cenowy Aplikacji logiki](../logic-apps/logic-apps-pricing.md#fixed-pricing). Aby uzyskać informacje o cenach, zobacz [Ceny aplikacji logiki](../logic-apps/logic-apps-pricing.md).

## <a name="prerequisites"></a>Wymagania wstępne

* Te same [wymagania wstępne](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#prerequisites) i [wymagania, aby włączyć dostęp do ise](../logic-apps/connect-virtual-network-vnet-isolated-environment.md#enable-access) jak podczas tworzenia ise w witrynie Azure portal

* Narzędzie, którego można użyć do utworzenia interfejsu ISE, wywołując interfejs API REST aplikacji logiki z żądaniem HTTPS PUT. Na przykład można użyć [postman](https://www.getpostman.com/downloads/)lub można utworzyć aplikację logiki, która wykonuje to zadanie.

## <a name="send-the-request"></a>Wysyłanie żądania

Aby utworzyć interfejs ISE, wywołując interfejs API REST aplikacji logiki, należy wykonać to żądanie HTTPS PUT:

`PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Logic/integrationServiceEnvironments/{integrationServiceEnvironmentName}?api-version=2019-05-01`

> [!IMPORTANT]
> Wersja interfejsu API INTERFEJSU API 2019-05-01 aplikacji logic apps wymaga wykonania własnego żądania HTTP PUT dla łączników ISE.

Wdrożenie zwykle trwa w ciągu dwóch godzin, aby zakończyć. Od czasu do czasu wdrożenie może potrwać do czterech godzin. Aby sprawdzić stan wdrożenia, w [witrynie Azure portal](https://portal.azure.com), na pasku narzędzi platformy Azure wybierz ikonę powiadomień, która otwiera okienko powiadomień.

> [!NOTE]
> Jeśli wdrożenie zakończy się niepowodzeniem lub usuniesz ise, platforma Azure może potrwać do godziny przed zwolnieniem podsieci. To opóźnienie oznacza, że może być trzeba poczekać przed ponownym użyciem tych podsieci w innym ise.
>
> Jeśli usuniesz sieć wirtualną, platforma Azure zwykle trwa do dwóch godzin przed zwolnieniem podsieci, ale ta operacja może potrwać dłużej. 
> Podczas usuwania sieci wirtualnych upewnij się, że żadne zasoby nie są nadal połączone. 
> Zobacz [Usuwanie sieci wirtualnej](../virtual-network/manage-virtual-network.md#delete-a-virtual-network).

## <a name="request-header"></a>Nagłówek żądania

W nagłówku żądania uwzględnij następujące właściwości:

* `Content-type`: Ustaw tę `application/json`wartość właściwości na .

* `Authorization`: Ustaw tę wartość właściwości na token nośny dla klienta, który ma dostęp do subskrypcji platformy Azure lub grupy zasobów, której chcesz użyć.

### <a name="request-body-syntax"></a>Składnia treści żądania

Oto składnia treści żądania, która opisuje właściwości, które mają być używane podczas tworzenia ise:

```json
{
   "id": "/subscriptions/{Azure-subscription-ID/resourceGroups/{Azure-resource-group}/providers/Microsoft.Logic/integrationServiceEnvironments/{ISE-name}",
   "name": "{ISE-name}",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "{Azure-region}",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-1}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-2}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-3}",
            },
            {
               "id": "/subscriptions/{Azure-subscription-ID}/resourceGroups/{Azure-resource-group}/providers/Microsoft.Network/virtualNetworks/{virtual-network-name}/subnets/{subnet-4}",
            }
         ]
      }
   }
}
```

### <a name="request-body-example"></a>Przykład treści żądania

W tej przykładzie treści żądania przedstawiono przykładowe wartości:

```json
{
   "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Logic/integrationServiceEnvironments/Fabrikam-ISE",
   "name": "Fabrikam-ISE",
   "type": "Microsoft.Logic/integrationServiceEnvironments",
   "location": "WestUS2",
   "sku": {
      "name": "Premium",
      "capacity": 1
   },
   "properties": {
      "networkConfiguration": {
         "accessEndpoint": {
            // Your ISE can use the "External" or "Internal" endpoint. This example uses "External".
            "type": "External"
         },
         "subnets": [
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-1",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-2",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-3",
            },
            {
               "id": "/subscriptions/********************/resourceGroups/Fabrikam-RG/providers/Microsoft.Network/virtualNetworks/Fabrikam-VNET/subnets/subnet-4",
            }
         ]
      }
   }
}
```

## <a name="next-steps"></a>Następne kroki

* [Dodawanie zasobów do środowisk usług integracyjnych](../logic-apps/add-artifacts-integration-service-environment-ise.md)
* [Zarządzanie środowiskami usługi integracji](../logic-apps/ise-manage-integration-service-environment.md#check-network-health)

