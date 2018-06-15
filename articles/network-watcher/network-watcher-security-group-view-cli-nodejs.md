---
title: Analizowanie zabezpieczeń sieci z widokiem grupy obserwatora zabezpieczeń Azure sieci - Azure CLI 1.0 | Dokumentacja firmy Microsoft
description: W tym artykule opisano sposób użycia interfejsu wiersza polecenia platformy Azure w wersji 1.0 do analizy zabezpieczeń maszyn wirtualnych z widokiem grupy zabezpieczeń.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: a986ff4f-7e0c-4994-95e1-4ac824986500
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: f27157129bea4e47a2e0e6cc1169b9e4887bdd78
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/21/2017
ms.locfileid: "23862831"
---
# <a name="analyze-your-virtual-machine-security-with-security-group-view-using-azure-cli-10"></a>Analizowanie zabezpieczeń maszyny wirtualnej z widoku grupy zabezpieczeń przy użyciu interfejsu wiersza polecenia platformy Azure w wersji 1.0

> [!div class="op_single_selector"]
> - [Program PowerShell](network-watcher-security-group-view-powershell.md)
> - [Interfejs wiersza polecenia 1.0](network-watcher-security-group-view-cli-nodejs.md)
> - [Interfejs wiersza polecenia 2.0](network-watcher-security-group-view-cli.md)
> - [Interfejs API REST](network-watcher-security-group-view-rest.md)

Widok grupy zabezpieczeń zwraca reguły zabezpieczeń sieci skonfigurowane i skuteczne, które są stosowane do maszyny wirtualnej. Ta funkcja jest przydatna do inspekcji i diagnostyki sieciowych grup zabezpieczeń i reguł, które są skonfigurowane na maszynie Wirtualnej dla zapewnienia ruchu jest poprawnie dozwolony lub niedozwolony. W tym artykule firma Microsoft opisano, jak pobrać zasady zabezpieczeń skonfigurowane i skuteczne z maszyną wirtualną za pomocą wiersza polecenia platformy Azure

W tym artykule wykorzystano 1.0 interfejsu wiersza polecenia platformy Azure i platform, która jest dostępna dla systemu Windows, Mac i Linux. Obserwatora sieciowego aktualnie używa interfejsu wiersza polecenia platformy Azure w wersji 1.0 do obsługi interfejsu wiersza polecenia.

## <a name="before-you-begin"></a>Przed rozpoczęciem

W tym scenariuszu przyjęto zostały już wykonane kroki przedstawione w [utworzyć obserwatora sieciowego](network-watcher-create.md) utworzyć obserwatora sieciowego.

## <a name="scenario"></a>Scenariusz

Scenariusz omówione w tym artykule pobiera reguły zabezpieczeń skonfigurowane i efektywny dla podanej maszyny wirtualnej.

## <a name="get-a-vm"></a>Uzyskiwanie maszyny Wirtualnej

Maszyna wirtualna jest wymagana do uruchamiania `vm list` polecenia cmdlet. Następujące polecenie wyświetla listę wirtualnych machinese w grupie zasobów:

```azurecli
azure vm list -g resourceGroupName
```

Jeśli znasz już maszyny wirtualnej, można użyć `vm show` polecenia cmdlet, aby pobrać jego identyfikator zasobu:

```azurecli
azure vm show -g resourceGroupName -n virtualMachineName
```

## <a name="retrieve-security-group-view"></a>Pobierz widok grupy zabezpieczeń

Następnym krokiem jest do pobierania wyników widoku grupy zabezpieczeń. Dodawanie "--json" flagi sformatuje wyniki w formacie json.

```azurecli
azure network watcher security-group-view -g resourceGroupName -n networkWatcherName -t targetResourceId --json
```

## <a name="viewing-the-results"></a>Wyświetlanie wyników

Poniższy przykład jest skróconą odpowiedzi wyników zwróconych. Wyniki Pokaż wszystkie reguły zabezpieczeń skuteczne i zastosowane na maszynie wirtualnej podziale grupy **NetworkInterfaceSecurityRules**, **DefaultSecurityRules**, i **EffectiveSecurityRules**.

```json
{
  "networkInterfaces": [
    {
      "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic",
      "securityRuleAssociations": {
        "networkInterfaceAssociation": {
          "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testvm",
          "securityRules": [
            {
              "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/test-nsg/securityRules/default-allow-rdp",
              "protocol": "TCP",
              "sourcePortRange": "*",
              "destinationPortRange": "3389",
              "sourceAddressPrefix": "*",
              "destinationAddressPrefix": "*",
              "access": "Allow",
              "priority": 1000,
              "direction": "Inbound",
              "provisioningState": "Succeeded",
              "name": "default-allow-rdp",
              "etag": "W/\"00000000-0000-0000-0000-000000000000\""
            }
          ]
        },
        "defaultSecurityRules": [
          {
            "id": "/subscriptions//resourceGroups//providers/Microsoft.Network/networkSecurityGroups//defaultSecurityRules/",
            "description": "Allow inbound traffic from all VMs in VNET",
            "protocol": "*",
            "sourcePortRange": "*",
            "destinationPortRange": "*",
            "sourceAddressPrefix": "VirtualNetwork",
            "destinationAddressPrefix": "VirtualNetwork",
            "access": "Allow",
            "priority": 65000,
            "direction": "Inbound",
            "provisioningState": "Succeeded",
            "name": "AllowVnetInBound"
          }
        ]
      }
    }
  ]
}
```

## <a name="next-steps"></a>Kolejne kroki

Odwiedź stronę [inspekcji sieci zabezpieczeń grupy (NSG) z obserwatora sieciowego](network-watcher-nsg-auditing-powershell.md) sposób automatyzacji sprawdzania poprawności grup zabezpieczeń sieci.

Dowiedz się więcej na temat reguł zabezpieczeń, które są stosowane do zasobów sieciowych, odwiedzając [Omówienie widoku grupy zabezpieczeń](network-watcher-security-group-view-overview.md)
