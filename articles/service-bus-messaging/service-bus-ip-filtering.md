---
title: Konfigurowanie reguł zapory IP dla usługi Azure Service Bus
description: Jak używać reguł zapory, aby zezwolić na połączenia z określonych adresów IP do usługi Azure Service Bus.
services: service-bus
documentationcenter: ''
author: axisc
manager: timlt
editor: spelluru
ms.service: service-bus
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 24591c20ed707d9541eece0698ecd6e6b5ddee35
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878191"
---
# <a name="configure-ip-firewall-rules-for-azure-service-bus"></a>Konfigurowanie reguł zapory IP dla usługi Azure Service Bus
Domyślnie obszary nazw usługi Service Bus są dostępne z Internetu, o ile żądanie jest zawiera prawidłowe uwierzytelnianie i autoryzację. Zaporą IP można ją ograniczyć do tylko zestawu adresów IPv4 lub zakresów adresów IPv4 w notacji [CIDR (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Ta funkcja jest przydatna w scenariuszach, w których usługa Azure Service Bus powinna być dostępna tylko z niektórych dobrze znanych witryn. Reguły zapory umożliwiają konfigurowanie reguł do akceptowania ruchu pochodzącego z określonych adresów IPv4. Na przykład jeśli używasz usługi Service Bus z [trasą Azure Express,][express-route]można utworzyć **regułę zapory,** aby zezwolić na ruch tylko z adresów IP infrastruktury lokalnej lub adresów głównej bramy TRANSLATORa. 

> [!IMPORTANT]
> Zapory i sieci wirtualne są obsługiwane tylko w warstwie **premium** usługi Service Bus. Jeśli uaktualnienie do warstwy **premier** nie jest opcją, zaleca się, aby zachować token sygnatury dostępu współdzielonego (SAS) bezpieczne i udostępniać tylko autoryzowanym użytkownikom. Aby uzyskać informacje na temat uwierzytelniania [sygnatury dostępu Współdzielonego, zobacz Uwierzytelnianie i autoryzacja](service-bus-authentication-and-authorization.md#shared-access-signature).

## <a name="ip-firewall-rules"></a>Reguły zapory IP
Reguły zapory IP są stosowane na poziomie obszaru nazw usługi Service Bus. W związku z tym reguły mają zastosowanie do wszystkich połączeń z klientami przy użyciu dowolnego obsługiwanego protokołu. Każda próba połączenia z adresu IP, która nie jest zgodna z dozwoloną regułą IP w obszarze nazw usługi Service Bus, jest odrzucana jako nieautoryzowana. W odpowiedzi nie wspomina się o regule IP. Reguły filtru IP są stosowane w kolejności, a pierwsza reguła zgodna z adresem IP określa akcję akceptowania lub odrzucania.

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
W tej sekcji pokazano, jak używać witryny Azure Portal do tworzenia reguł zapory IP dla obszaru nazw usługi Service Bus. 

1. Przejdź do **obszaru nazw usługi Service Bus** w [witrynie Azure portal](https://portal.azure.com).
2. W menu po lewej stronie wybierz opcję **Sieć.** Domyślnie zaznaczona jest opcja **Wszystkie sieci.** Obszar nazw usługi Service Bus akceptuje połączenia z dowolnego adresu IP. To ustawienie domyślne jest równoważne regule, która akceptuje zakres adresów IP 0.0.0.0/0. 

    ![Zapora sieciowa — wybrano opcję Wszystkie sieci](./media/service-bus-ip-filtering/firewall-all-networks-selected.png)
1. Wybierz opcję **Wybrane sieci** u góry strony. W sekcji **Zapora postępuj** zgodnie z tymi czynnościami:
    1. Wybierz **opcję Dodaj adres IP klienta,** aby nadać bieżącemu adresowi IP klienta dostęp do obszaru nazw. 
    2. W przypadku **zakresu adresów**wprowadź określony adres IPv4 lub zakres adresu IPv4 w notacji CIDR. 
    3. Określ, czy chcesz **zezwolić zaufanym usługom firmy Microsoft na ominięcie tej zapory**. 

        ![Zapora sieciowa — wybrano opcję Wszystkie sieci](./media/service-bus-ip-filtering/firewall-selected-networks-trusted-access-disabled.png)
3. Wybierz **pozycję Zapisz** na pasku narzędzi, aby zapisać ustawienia. Poczekaj kilka minut na wyświetlenie potwierdzenia w powiadomieniach portalu.

## <a name="use-resource-manager-template"></a>Używanie szablonu usługi Resource Manager
W tej sekcji jest przykładowy szablon usługi Azure Resource Manager, który tworzy sieć wirtualną i regułę zapory.


Poniższy szablon Menedżera zasobów umożliwia dodanie reguły sieci wirtualnej do istniejącego obszaru nazw usługi Service Bus.

Parametry szablonu:

- **ipMask** jest pojedynczym adresem IPv4 lub blokiem adresów IP w notacji CIDR. Na przykład w notacji CIDR 70.37.104.0/24 reprezentuje 256 adresów IPv4 z 70.37.104.0 do 70.37.104.255, z 24 wskazując liczbę znaczących bitów prefiks dla zakresu.

> [!NOTE]
> Chociaż nie ma reguł odmowy możliwe, szablon usługi Azure Resource Manager ma domyślną akcję ustawioną na **"Zezwalaj",** która nie ogranicza połączeń.
> Podczas tworzenia reguł sieci wirtualnej lub zapór sieciowych musimy zmienić ***"defaultAction"***
> 
> Z
> ```json
> "defaultAction": "Allow"
> ```
> na
> ```json
> "defaultAction": "Deny"
> ```
>

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "servicebusNamespaceName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Service Bus namespace"
        }
      },
      "location": {
        "type": "string",
        "metadata": {
          "description": "Location for Namespace"
        }
      }
    },
    "variables": {
      "namespaceNetworkRuleSetName": "[concat(parameters('servicebusNamespaceName'), concat('/', 'default'))]",
    },
    "resources": [
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[parameters('servicebusNamespaceName')]",
        "type": "Microsoft.ServiceBus/namespaces",
        "location": "[parameters('location')]",
        "sku": {
          "name": "Premium",
          "tier": "Premium"
        },
        "properties": { }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": [<YOUR EXISTING VIRTUAL NETWORK RULES>],
          "ipRules": 
          [
            {
                "ipMask":"10.1.1.1",
                "action":"Allow"
            },
            {
                "ipMask":"11.0.0.0/24",
                "action":"Allow"
            }
          ],
          "trustedServiceAccessEnabled": false,          
          "defaultAction": "Deny"
        }
      }
    ],
    "outputs": { }
  }
```

Aby wdrożyć szablon, postępuj zgodnie z instrukcjami dotyczącymi [usługi Azure Resource Manager][lnk-deploy].

## <a name="next-steps"></a>Następne kroki

Aby ograniczyć dostęp do usługi Service Bus do sieci wirtualnych platformy Azure, zobacz następujące łącze:

- [Punkty końcowe usługi sieci wirtualnej dla magistrali usług][lnk-vnet]

<!-- Links -->

[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[lnk-vnet]: service-bus-service-endpoints.md
[express-route]:  /azure/expressroute/expressroute-faqs#supported-services
