---
title: Konfigurowanie punktów końcowych usługi sieci wirtualnej dla usługi Azure Service Bus
description: Ten artykuł zawiera informacje dotyczące sposobu dodawania punktu końcowego usługi Microsoft.ServiceBus do sieci wirtualnej.
services: service-bus
documentationcenter: ''
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: aschhab
ms.openlocfilehash: 9dbf65522d5c85e1054ed3f1f6ca9f86180e7f7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79454985"
---
# <a name="configure-virtual-network-service-endpoints-for-azure-service-bus"></a>Konfigurowanie punktów końcowych usługi sieci wirtualnej dla usługi Azure Service Bus

Integracja usługi Service Bus z [punktami końcowymi usługi sieci wirtualnej (VNet)][vnet-sep] umożliwia bezpieczny dostęp do funkcji obsługi wiadomości z obciążeń, takich jak maszyny wirtualne, które są powiązane z sieciami wirtualnymi, przy czym ścieżka ruchu sieciowego jest zabezpieczona na obu końcach.

Po skonfigurowaniu do wiązanych z co najmniej jednym punktem końcowym usługi podsieci sieci wirtualnej odpowiedni obszar nazw usługi Service Bus nie będzie już akceptował ruchu z dowolnego miejsca, ale autoryzowanych sieci wirtualnych. Z punktu widzenia sieci wirtualnej powiązanie obszaru nazw usługi Service Bus z punktem końcowym usługi konfiguruje tunel sieci izolowanej z podsieci sieci wirtualnej do usługi obsługi wiadomości.

Wynikiem jest prywatna i izolowana relacja między obciążeniami powiązanymi z podsiecią i odpowiednim obszarem nazw usługi Service Bus, pomimo obserwowalnego adresu sieciowego punktu końcowego usługi obsługi wiadomości w zakresie publicznego adresu IP.

> [!IMPORTANT]
> Sieci wirtualne są obsługiwane tylko w przestrzeniach nazw usługi Service Bus [warstwy Premium.](service-bus-premium-messaging.md)
> 
> Korzystając z punktów końcowych usługi sieci wirtualnej z usługą Service Bus, nie należy włączać tych punktów końcowych w aplikacjach, które łączą standardowe i premium warstwy obszarów nazw usługi Service Bus. Ponieważ warstwa standardowa nie obsługuje sieci wirtualnych. Punkt końcowy jest ograniczony tylko do obszarów nazw warstwy Premium.

## <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Zaawansowane scenariusze zabezpieczeń włączone przez integrację sieci wirtualnej 

Rozwiązania, które wymagają ścisłego i podzielonego zabezpieczeń i w których podsieci sieci wirtualnej zapewniają segmentację między usługami podzielonymi na przedziały, zazwyczaj nadal potrzebują ścieżek komunikacji między usługami zamieszkałymi w tych przedziałach.

Każda bezpośrednia trasa IP między przedziałami, w tym przewożącym HTTPS przez protokół TCP/IP, niesie ze sobą ryzyko wykorzystania luk w zabezpieczeniach z warstwy sieciowej. Usługi obsługi wiadomości zapewniają całkowicie izolowane ścieżki komunikacji, gdzie wiadomości są nawet zapisywane na dysku podczas przechodzenia między stronami. Obciążenia w dwóch różnych sieciach wirtualnych, które są powiązane z tym samym wystąpieniem usługi Service Bus, mogą komunikować się wydajnie i niezawodnie za pośrednictwem wiadomości, podczas gdy integralność granic izolacji sieci jest zachowywana.
 
Oznacza to, że rozwiązania chmurowe z uwzględnieniem zabezpieczeń nie tylko uzyskują dostęp do wiodących w branży niezawodnych i skalowalnych asynchronicznych funkcji obsługi wiadomości asynchronicznych platformy Azure, ale teraz mogą używać wiadomości do tworzenia ścieżek komunikacji między bezpiecznymi przedziałami rozwiązań, które są z natury bezpieczniejsze niż to, co jest osiągalne w dowolnym trybie komunikacji typu peer-to-peer, w tym HTTPS i innych protokołów gnieździe zabezpieczonych protokołami TLS.

## <a name="binding-service-bus-to-virtual-networks"></a>Powiązanie usługi Service Bus z sieciami wirtualnymi

*Reguły sieci wirtualnej* to funkcja zabezpieczeń zapory, która określa, czy serwer usługi Azure Service Bus akceptuje połączenia z określonej podsieci sieci wirtualnej.

Powiązanie obszaru nazw usługi Service Bus z siecią wirtualną jest procesem dwuetapowym. Najpierw należy utworzyć **punkt końcowy usługi sieci wirtualnej** w podsieci sieci wirtualnej i włączyć go dla **microsoft.ServiceBus,** jak wyjaśniono w [omówieniu punktu końcowego usługi][vnet-sep]. Po dodaniu punktu końcowego usługi należy powiązać obszar nazw usługi Service Bus z **regułą sieci wirtualnej**.

Reguła sieci wirtualnej jest skojarzeniem obszaru nazw usługi Service Bus z podsiecią sieci wirtualnej. Gdy reguła istnieje, wszystkie obciążenia powiązane z podsiecią mają dostęp do obszaru nazw usługi Service Bus. Sama usługa Service Bus nigdy nie ustanawia połączeń wychodzących, nie musi uzyskiwać dostępu i dlatego nigdy nie uzyskała dostępu do podsieci, włączając tę regułę.

## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
W tej sekcji pokazano, jak za pomocą witryny Azure Portal, aby dodać punkt końcowy usługi sieci wirtualnej. Aby ograniczyć dostęp, należy zintegrować punkt końcowy usługi sieci wirtualnej dla tej przestrzeni nazw centrum zdarzeń.

1. Przejdź do **obszaru nazw usługi Service Bus** w [witrynie Azure portal](https://portal.azure.com).
2. W menu po lewej stronie wybierz opcję **Sieć.** Domyślnie zaznaczona jest opcja **Wszystkie sieci.** Obszar nazw akceptuje połączenia z dowolnego adresu IP. To ustawienie domyślne jest równoważne regule, która akceptuje zakres adresów IP 0.0.0.0/0. 

    ![Zapora sieciowa — wybrano opcję Wszystkie sieci](./media/service-endpoints/firewall-all-networks-selected.png)
1. Wybierz opcję **Wybrane sieci** u góry strony.
2. W sekcji **Sieć wirtualna** na stronie wybierz pozycję **+Dodaj istniejącą sieć wirtualną**. 

    ![dodawanie istniejącej sieci wirtualnej](./media/service-endpoints/add-vnet-menu.png)
3. Wybierz sieć wirtualną z listy sieci wirtualnych, a następnie wybierz **podsieć**. Należy włączyć punkt końcowy usługi przed dodaniem sieci wirtualnej do listy. Jeśli punkt końcowy usługi nie jest włączony, portal wyświetli monit o jego włączenie.
   
   ![wybieranie podsieci](./media/service-endpoints/select-subnet.png)

4. Po włączeniu punktu końcowego usługi dla podsieci dla firmy **Microsoft.ServiceBus**powinien zostać wyświetlony następujący komunikat pomyślny. Wybierz **pozycję Dodaj** u dołu strony, aby dodać sieć. 

    ![wybieranie podsieci i włączanie punktu końcowego](./media/service-endpoints/subnet-service-endpoint-enabled.png)

    > [!NOTE]
    > Jeśli nie można włączyć punktu końcowego usługi, można zignorować brakujący punkt końcowy usługi sieci wirtualnej przy użyciu szablonu Menedżera zasobów. Ta funkcja nie jest dostępna w portalu.
6. Wybierz **pozycję Zapisz** na pasku narzędzi, aby zapisać ustawienia. Poczekaj kilka minut na wyświetlenie potwierdzenia w powiadomieniach portalu. Przycisk **Zapisz** powinien być wyłączony. 

    ![Zapisz sieć](./media/service-endpoints/save-vnet.png)

## <a name="use-resource-manager-template"></a>Używanie szablonu usługi Resource Manager
Poniższy szablon Menedżera zasobów umożliwia dodanie reguły sieci wirtualnej do istniejącego obszaru nazw usługi Service Bus.

Parametry szablonu:

* **namespaceName**: Obszar nazw magistrali usług.
* **virtualNetworkingSubnetId:** W pełni kwalifikowana ścieżka Menedżera zasobów dla podsieci sieci wirtualnej; na przykład `/subscriptions/{id}/resourceGroups/{rg}/providers/Microsoft.Network/virtualNetworks/{vnet}/subnets/default` dla domyślnej podsieci sieci wirtualnej.

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

Szablon:

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
      "virtualNetworkName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Rule"
        }
      },
      "subnetName": {
        "type": "string",
        "metadata": {
          "description": "Name of the Virtual Network Sub Net"
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
      "subNetId": "[resourceId('Microsoft.Network/virtualNetworks/subnets/', parameters('virtualNetworkName'), parameters('subnetName'))]"
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
        "apiVersion": "2017-09-01",
        "name": "[parameters('virtualNetworkName')]",
        "location": "[parameters('location')]",
        "type": "Microsoft.Network/virtualNetworks",
        "properties": {
          "addressSpace": {
            "addressPrefixes": [
              "10.0.0.0/23"
            ]
          },
          "subnets": [
            {
              "name": "[parameters('subnetName')]",
              "properties": {
                "addressPrefix": "10.0.0.0/23",
                "serviceEndpoints": [
                  {
                    "service": "Microsoft.ServiceBus"
                  }
                ]
              }
            }
          ]
        }
      },
      {
        "apiVersion": "2018-01-01-preview",
        "name": "[variables('namespaceNetworkRuleSetName')]",
        "type": "Microsoft.ServiceBus/namespaces/networkruleset",
        "dependsOn": [
          "[concat('Microsoft.ServiceBus/namespaces/', parameters('servicebusNamespaceName'))]"
        ],
        "properties": {
          "virtualNetworkRules": 
          [
            {
              "subnet": {
                "id": "[variables('subNetId')]"
              },
              "ignoreMissingVnetServiceEndpoint": false
            }
          ],
          "ipRules":[<YOUR EXISTING IP RULES>],
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

Aby uzyskać więcej informacji na temat sieci wirtualnych, zobacz następujące łącza:

- [Punkty końcowe usługi dla sieci wirtualnej platformy Azure][vnet-sep]
- [Filtrowanie adresów IP usługi Azure Service Bus][ip-filtering]

[vnet-sep]: ../virtual-network/virtual-network-service-endpoints-overview.md
[lnk-deploy]: ../azure-resource-manager/templates/deploy-powershell.md
[ip-filtering]: service-bus-ip-filtering.md
