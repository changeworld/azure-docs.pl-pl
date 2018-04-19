---
title: "Omówienie dostawcy zasobów sieci | Dokumentacja firmy Microsoft"
description: "Więcej informacji na temat nowego dostawcy zasobów sieciowych w systemie Azure Resource Manager"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: tysonn
ms.assetid: 79bf09da-4809-45cb-8d21-705616ef24dc
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.openlocfilehash: 2428c707ddeed281fddd1e57bc5574603f0b9b1c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/11/2017
---
# <a name="network-resource-provider"></a>Dostawca zasobów sieciowych
Podstawa w bieżącej sukcesu firmy, jest możliwość tworzenia i zarządzania aplikacjami pamiętać sieci dużą skalę w sposób agile, elastyczne, bezpieczne i repeatable. Usługa Azure Resource Manager umożliwia tworzenie takich aplikacji, jako pojedynczą kolekcję zasobów w grupach zasobów. Takie zasoby są zarządzane przez różnych dostawców zasobów w obszarze usługi Resource Manager.

Usługa Azure Resource Manager zależy od dostawców zasobów różnych w celu zapewnienia dostępu do zasobów. Istnieją trzy dostawców zasobów głównego: sieci, magazynu i zasobów obliczeniowych. W tym dokumencie omówiono charakterystyki i korzyści wynikające z dostawcy zasobów sieciowych, w tym:

* **Metadane** — informacje można dodać do zasobów przy użyciu tagów. Znaczniki mogą służyć do śledzenia wykorzystania zasobów między grupami zasobów i subskrypcje.
* **Większą kontrolę nad siecią** — są luźno powiązane z zasobów sieciowych i sterować nimi w sposób bardziej szczegółowego. Oznacza to, że masz większą elastyczność w zarządzaniu zasobów sieciowych.
* **Szybsze konfiguracji** — ponieważ są luźno powiązane z zasobów sieciowych, można tworzyć i organizowania zasobów sieciowych równolegle. Ma to znaczne zmniejszoną ilość czasu konfiguracji.
* **Kontroli dostępu opartej na rolach** -RBAC zawiera domyślne role, z określonego zakresu zabezpieczeń, oprócz umożliwienia Tworzenie niestandardowych ról dla bezpiecznego zarządzania.
* **Łatwiejsze zarządzanie i wdrażanie** — ułatwia wdrażanie i zarządzanie aplikacjami, ponieważ można całego stosu aplikacji można utworzyć jako pojedynczą kolekcję zasobów w grupie zasobów. I szybsze do wdrożenia, ponieważ po prostu zapewniając ładunek JSON szablonu można wdrożyć.
* **Dostosowywanie szybkie** — szablony deklaratywne stylu można użyć do włączenia powtarzalnych i szybkiego dostosowywania wdrożeń.
* **Dostosowywanie powtarzalne** — szablony deklaratywne stylu można użyć do włączenia powtarzalnych i szybkiego dostosowywania wdrożeń.
* **Interfejsy zarządzania** — można użyć dowolnego z poniższych interfejsów do zarządzania zasobami:
  * REST API oparty na
  * PowerShell
  * Zestaw SDK .NET
  * Zestaw Node.JS SDK
  * Zestaw SDK Java
  * Interfejs wiersza polecenia platformy Azure
  * Portal w wersji zapoznawczej
  * Język szablonu usługi Resource Manager

## <a name="network-resources"></a>Zasoby sieciowe
Możesz teraz zarządzać zasobów sieciowych niezależnie, zamiast je zarządzanych za pomocą obliczeń jednego zasobu (maszyny wirtualnej). Dzięki temu wyższego poziomu bezpieczeństwa i elastyczność oraz elastyczność w tworzenie infrastruktury złożone i dużej skali, w grupie zasobów.

Widok koncepcyjny Przykładowe wdrożenie obejmujące wielowarstwowych aplikacji znajduje się poniżej. Każdy zasób, który zostanie wyświetlony, takie jak karty sieciowe, publicznych adresów IP i maszyn wirtualnych, można zarządzać niezależnie.

![Model zasobów sieciowych](./media/resource-groups-networking/Figure2.png)

Każdy zasób zawiera zbiór wspólnych właściwości i ich indywidualnych właściwości. Wspólne właściwości są:

| Właściwość | Opis | Przykładowe wartości |
| --- | --- | --- |
| **Nazwa** |Nazwa zasobu unikatowy. Każdy typ zasobu ma własną ograniczenia nazewnictwa. |NIC01 PIP01, VM01, |
| **location** |Region platformy Azure, w której znajduje się zasób |westus, eastus |
| **Identyfikator** |Unikatowy identyfikator URI na podstawie |/Subscriptions/<subGUID>/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP |

Możesz sprawdzić poszczególnych właściwości zasobów w poniższych sekcjach.

[!INCLUDE [virtual-networks-nrp-pip-include](../../includes/virtual-networks-nrp-pip-include.md)]

[!INCLUDE [virtual-networks-nrp-nic-include](../../includes/virtual-networks-nrp-nic-include.md)]

[!INCLUDE [virtual-networks-nrp-nsg-include](../../includes/virtual-networks-nrp-nsg-include.md)]

[!INCLUDE [virtual-networks-nrp-udr-include](../../includes/virtual-networks-nrp-udr-include.md)]

[!INCLUDE [virtual-networks-nrp-vnet-include](../../includes/virtual-networks-nrp-vnet-include.md)]

[!INCLUDE [virtual-networks-nrp-dns-include](../../includes/virtual-networks-nrp-dns-include.md)]

[!INCLUDE [virtual-networks-nrp-lb-include](../../includes/virtual-networks-nrp-lb-include.md)]

[!INCLUDE [virtual-networks-nrp-appgw-include](../../includes/virtual-networks-nrp-appgw-include.md)]

[!INCLUDE [virtual-networks-nrp-vpn-include](../../includes/virtual-networks-nrp-vpn-include.md)]

[!INCLUDE [virtual-networks-nrp-tm-include](../../includes/virtual-networks-nrp-tm-include.md)]

## <a name="management-interfaces"></a>Interfejsy zarządzania
Możesz zarządzać zasobami sieci Azure przy użyciu różnych interfejsów. W tym dokumencie firma Microsoft będzie skupić się na tow te interfejsy: interfejs API REST i szablony.

### <a name="rest-api"></a>Interfejs API REST
Jak wspomniano wcześniej, można zarządzać za pośrednictwem różnych interfejsy, w tym interfejsu API REST, zestawu SDK programu .NET, Node.JS SDK, zestaw SDK Java, programu PowerShell, interfejsu wiersza polecenia, portalu Azure i szablony zasobów sieciowych.

Interfejs API Rest jest zgodny ze specyfikacją protokołu HTTP 1.1. Identyfikator URI ogólną strukturę interfejsu API znajduje się poniżej:

    https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

I parametrów w nawiasach klamrowych reprezentują następujące elementy:

* **Identyfikator subskrypcji** — identyfikator subskrypcji platformy Azure.
* **przestrzeń nazw dostawcy zasobów** — przestrzeń nazw dla używanego dostawcy. Wartość dostawcy zasobów sieciowych jest *Microsoft.Network*.
* **Nazwa regionu** — nazwa regionu Azure

W wywołaniach interfejsu API REST, obsługiwane są następujące metody HTTP:

* **Umieść** — umożliwia tworzenie zasobu danego typu, modyfikować właściwości zasobu lub zmiana skojarzenia między zasobami.
* **Pobierz** — używane do pobierania informacji o elastycznie zasobu.
* **Usuń** — używane do usuwania istniejącego zasobu.

Żądanie i odpowiedź jest zgodna z formatem ładunek JSON. Aby uzyskać więcej informacji, zobacz [interfejsów API usługi Azure Resource Management](https://msdn.microsoft.com/library/azure/dn948464.aspx).

### <a name="resource-manager-template-language"></a>Język szablonu usługi Resource Manager
Oprócz zarządzania zasobami imperatively (za pośrednictwem interfejsów API lub zestawu SDK), można również używać deklaratywne stylu programowania do tworzenia i zarządzania zasobami sieci przy użyciu języka szablonu usługi Resource Manager.

Poniższe przykładowe reprezentację szablonu —

    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "<version-number-of-template>",
      "parameters": { <parameter-definitions-of-template> },
      "variables": { <variable-definitions-of-template> },
      "resources": [ { <definition-of-resource-to-deploy> } ],
      "outputs": { <output-of-template> }    
    }

Szablon jest głównie opisu JSON zasobów i wartości wystąpienia wprowadzonym za pomocą parametrów. W poniższym przykładzie może służyć do tworzenia sieci wirtualnej 2 podsieci.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
        "contentVersion": "1.0.0.0",
        "parameters" : {
          "location": {
            "type": "String",
            "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
            "metadata" : {
              "Description" : "Deployment location"
            }
          },
          "virtualNetworkName":{
            "type" : "string",
            "defaultValue":"myVNET",
            "metadata" : {
              "Description" : "VNET name"
            }
          },
          "addressPrefix":{
            "type" : "string",
            "defaultValue" : "10.0.0.0/16",
            "metadata" : {
              "Description" : "Address prefix"
            }

          },
          "subnet1Name": {
            "type" : "string",
            "defaultValue" : "Subnet-1",
            "metadata" : {
              "Description" : "Subnet 1 Name"
            }
          },
          "subnet2Name": {
            "type" : "string",
            "defaultValue" : "Subnet-2",
            "metadata" : {
              "Description" : "Subnet 2 name"
            }
          },
          "subnet1Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.0.0/24",
            "metadata" : {
              "Description" : "Subnet 1 Prefix"
            }
          },
          "subnet2Prefix" : {
            "type" : "string",
            "defaultValue" : "10.0.1.0/24",
            "metadata" : {
              "Description" : "Subnet 2 Prefix"
            }
          }
        },
        "resources": [
        {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('virtualNetworkName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[parameters('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[parameters('subnet1Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet1Prefix')]"
                }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties" : {
                  "addressPrefix": "[parameters('subnet2Prefix')]"
                }
              }
            ]
          }
        }
        ]
    }

Masz ręcznie podać wartości parametrów przy użyciu szablonu, lub można użyć pliku parametrów. W poniższym przykładzie pokazano zestaw możliwych wartości parametrów do użycia na podstawie tego szablonu:

    {
      "location": {
          "value": "East US"
      },
      "virtualNetworkName": {
          "value": "VNET1"
      },
      "subnet1Name": {
          "value": "Subnet1"
      },
      "subnet2Name": {
          "value": "Subnet2"
      },
      "addressPrefix": {
          "value": "192.168.0.0/16"
      },
      "subnet1Prefix": {
          "value": "192.168.1.0/24"
      },
      "subnet2Prefix": {
          "value": "192.168.2.0/24"
      }
    }


Główne zalety za pomocą szablonów są następujące:

* Można tworzyć złożone infrastruktury w grupie zasobów w stylu deklaratywne. Orchestration tworzenia zasobów, w tym zarządzanie zależności jest obsługiwany przez Menedżera zasobów.
* Infrastruktura można tworzyć w sposób powtarzalny w różnych regionach i w obrębie regionu, zmieniając po prostu parametrów.
* Styl deklaratywne prowadzi do krótszy czas realizacji w tworzeniu szablonów i wdrażania infrastruktury.

Przykładowe szablonów, zobacz [szablonów Szybki Start Azure](https://github.com/Azure/azure-quickstart-templates).

Aby uzyskać więcej informacji o języku szablonu usługi Resource Manager, zobacz [język szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

Przykładowy szablon powyżej korzysta z sieci wirtualnej i zasoby podsieci. Brak innych zasobów sieciowych, których można używać wymienione poniżej:

### <a name="using-a-template"></a>Przy użyciu szablonu
Można wdrożyć usługi Azure z szablonu, za pomocą programu PowerShell, AzureCLI, lub wykonując kliknij, aby wdrożyć z usługi GitHub. Do wdrożenia usług z szablonu w usłudze GitHub, wykonaj następujące czynności:

1. Otwórz plik template3 z usługi GitHub. Na przykład otwórz [sieci wirtualnej z dwoma podsieciami](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network).
2. Polecenie **wdrażanie na platformie Azure**, a następnie zaloguj się portalu Azure przy użyciu poświadczeń.
3. Sprawdź szablon, a następnie kliknij przycisk **zapisać**.
4. Kliknij przycisk **Edytuj parametry** i wybierz lokalizację, takich jak *zachodnie stany USA*dla sieci wirtualnej i podsieci.
5. W razie potrzeby zmień **prefiks adresu** i **SUBNETPREFIX** parametry, a następnie kliknij przycisk **OK**.
6. Kliknij przycisk **wybierz grupę zasobów** a następnie kliknij polecenie grupy zasobów, aby dodać sieć wirtualną i podsieci z. Alternatywnie można utworzyć nową grupę zasobów, klikając **lub utworzyć nowy**.
7. Kliknij przycisk **Utwórz**. Zwróć uwagę, wyświetlanie kafelka **wdrażania szablonu inicjowania obsługi administracyjnej**. Po zakończeniu wdrożenia zostanie wyświetlony ekran podobny do poniżej.

![Przykładowe wdrożenie szablonu](./media/resource-groups-networking/Figure6.png)

## <a name="next-steps"></a>Następne kroki
[Język szablonu usługi Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)

[Sieć platformy Azure — często używane szablony](https://github.com/Azure/azure-quickstart-templates)

[Usługa Azure Resource Manager, a wdrożenie klasyczne](../azure-resource-manager/resource-manager-deployment-model.md)

[Omówienie usługi Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)

