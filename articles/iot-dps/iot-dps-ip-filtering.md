---
title: Filtry połączeń IP usługi Azure IoT DPS | Dokumenty firmy Microsoft
description: Jak używać filtrowania adresów IP do blokowania połączeń z określonych adresów IP do wystąpienia dps usługi Azure IoT. Połączenia można blokować z poszczególnych lub zakresów adresów IP.
author: wesmc7777
ms.author: wesmc
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 0b13040b39ed491ec4fee4d6922d41f086edeeb2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79284917"
---
# <a name="use-ip-filters"></a>Korzystanie z filtrów adresów IP

Bezpieczeństwo jest ważnym aspektem każdego rozwiązania IoT. Czasami należy jawnie określić adresy IP, z których urządzenia mogą łączyć się w ramach konfiguracji zabezpieczeń. Funkcja *filtrowania adresów IP* usługi aprowizacji urządzeń usługi Azure IoT Hub (DPS) umożliwia skonfigurowanie reguł odrzucania lub akceptowania ruchu z określonych adresów IPv4.

## <a name="when-to-use"></a>Kiedy stosować

Istnieją dwa konkretne przypadki użycia, w których przydatne jest blokowanie połączeń z punktem końcowym DPS z niektórych adresów IP:

* Dps powinien odbierać ruch tylko z określonego zakresu adresów IP i odrzucać wszystko inne. Na przykład używasz dps z [usługi Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) do tworzenia połączeń prywatnych między DPS i urządzeń.

* Należy odrzucić ruch z adresów IP, które zostały zidentyfikowane jako podejrzane przez administratora DPS.

## <a name="how-filter-rules-are-applied"></a>Jak stosowane są reguły filtru

Reguły filtru IP są stosowane na poziomie wystąpienia DPS. W związku z tym reguły filtru IP mają zastosowanie do wszystkich połączeń z urządzeń i aplikacji zaplecza przy użyciu dowolnego obsługiwanego protokołu.

Każda próba połączenia z adresu IP zgodna z odrzucającą regułą IP w wystąpieniu DPS otrzymuje nieautoryzowany kod stanu 401 i opis. Komunikat odpowiedzi nie wspomina o regule IP.

## <a name="default-setting"></a>Ustawienie domyślne

Domyślnie siatka **filtru IP** w portalu dla DPS jest pusta. To ustawienie domyślne oznacza, że dps akceptuje połączenia z dowolnego adresu IP. To ustawienie domyślne jest równoważne regule, która akceptuje zakres adresów IP 0.0.0.0/0.

![Domyślne ustawienia filtra IP usługi IoT DPS](./media/iot-dps-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Dodawanie lub edytowanie reguły filtru IP

Aby dodać regułę filtru IP, wybierz pozycję **+ Dodaj regułę filtru IP**.

![Dodawanie reguły filtru IP do dps IoT](./media/iot-dps-ip-filtering/ip-filter-add-rule.png)

Po wybraniu opcji **Dodaj regułę filtru IP**wypełnij pola.

![Po wybraniu opcji Dodaj regułę filtru IP](./media/iot-dps-ip-filtering/ip-filter-after-selecting-add.png)

* Podaj **nazwę** reguły filtru IP. Musi to być unikatowy, niewrażliwy, alfanumeryczny ciąg o długości do 128 znaków. Akceptowane `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` są tylko 7-bitowe znaki alfanumeryczne ASCII plus.

* Podaj pojedynczy adres IPv4 lub blok adresów IP w notacji CIDR. Na przykład w notacji CIDR 192.168.100.0/22 reprezentuje adresy IPv4 1024 od 192.168.100.0 do 192.168.103.255.

* Wybierz **pozycję Zezwalaj** lub **blokuj** jako **akcję** dla reguły filtru IP.

Po wypełnieniu pól wybierz pozycję **Zapisz,** aby zapisać regułę. Zostanie wyświetlony alert informujący, że aktualizacja jest w toku.

![Powiadomienie o zapisywaniu reguły filtru IP](./media/iot-dps-ip-filtering/ip-filter-save-new-rule.png)

Opcja **Dodaj** jest wyłączona po osiągnięciu maksymalnie 10 reguł filtru IP.

Aby edytować istniejącą regułę, zaznacz dane, które chcesz zmienić, wykonuj zmiany, a następnie wybierz pozycję **Zapisz,** aby zapisać edycję.

> [!NOTE]
> Odrzucenie adresów IP może uniemożliwić innym usługom platformy Azure interakcję z wystąpieniem DPS.

## <a name="delete-an-ip-filter-rule"></a>Usuwanie reguły filtru IP

Aby usunąć regułę filtru IP, zaznacz ikonę kosza w tym wierszu, a następnie wybierz pozycję **Zapisz**. Reguła zostanie usunięta, a zmiana zostanie zapisana.

![Usuwanie reguły filtru IP usługi IoT DPS](./media/iot-dps-ip-filtering/ip-filter-delete-rule.png)


## <a name="update-ip-filter-rules-in-code"></a>Aktualizowanie reguł filtru IP w kodzie

Możesz pobrać i zmodyfikować filtr IP DPS przy użyciu punktu końcowego REST dostawcy zasobów platformy Azure. Zobacz `properties.ipFilterRules` w [createorupdate metody](https://docs.microsoft.com/rest/api/iot-dps/iotdpsresource/createorupdate).

Aktualizowanie reguł filtru IP DPS nie jest obecnie obsługiwane w usłudze Azure CLI lub Azure PowerShell, ale można je wykonać za pomocą szablonów usługi Azure Resource Manager. Zobacz [szablony usługi Azure Resource Manager, aby](../azure-resource-manager/templates/overview.md) uzyskać wskazówki dotyczące korzystania z szablonów Menedżera zasobów. Przykłady szablonów, które należy wykonać, pokazują, jak tworzyć, edytować i usuwać reguły filtru IP DPS.

### <a name="add-an-ip-filter-rule"></a>Dodawanie reguły filtru IP

Poniższy przykład szablonu tworzy nową regułę filtru IP o nazwie "AllowAll", która akceptuje cały ruch.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#", 
    "contentVersion": "1.0.0.0", 
    "parameters": {
        "iotDpsName": {
            "type": "string",
            "defaultValue": "[resourceGroup().name]",
            "minLength": 3,
            "metadata": {
                "description": "Specifies the name of the IoT DPS service."
            }
        }, 
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for Iot DPS resource."
            }
        }        
    }, 
    "variables": {
        "iotDpsApiVersion": "2020-01-01"
    }, 
    "resources": [
        {
            "type": "Microsoft.Devices/provisioningServices",
            "apiVersion": "[variables('iotDpsApiVersion')]",
            "name": "[parameters('iotDpsName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "S1",
                "tier": "Standard",
                "capacity": 1
            },
            "properties": {
                "IpFilterRules": [
                    {
                        "FilterName": "AllowAll",
                        "Action": "Accept",
                        "ipMask": "0.0.0.0/0"
                    }
                ]
            }            
        }
    ]
}
```

Zaktualizuj atrybuty reguły filtru IP szablonu na podstawie wymagań.

| Atrybut                | Opis |
| ------------------------ | ----------- |
| **Filtername**           | Podaj nazwę reguły filtru IP. Musi to być unikatowy, niewrażliwy, alfanumeryczny ciąg o długości do 128 znaków. Akceptowane są tylko 7-bitowe znaki alfanumeryczne ASCII oraz {'-', ':', '/', '/',\''', '+', '%', '_', '#', '*', '?', '?', '(',',', ',', '=', '@', ';', ''''}} |
| **Akcja**               | Zaakceptowane wartości to **Accept** lub **Reject** as the action for the IP filter rule. |
| **maska ip**               | Podaj pojedynczy adres IPv4 lub blok adresów IP w notacji CIDR. Na przykład w notacji CIDR 192.168.100.0/22 reprezentuje adresy IPv4 1024 od 192.168.100.0 do 192.168.103.255. |


### <a name="update-an-ip-filter-rule"></a>Aktualizowanie reguły filtru IP

Poniższy przykład szablonu aktualizuje regułę filtru IP o nazwie "AllowAll", pokazaną wcześniej, aby odrzucić cały ruch.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
                "IpFilterRules": [ 
                    { 
                        "FilterName": "AllowAll", 
                        "Action": "Reject", 
                        "ipMask": "0.0.0.0/0" 
                    } 
                ] 
            }             
        } 
    ] 
}
```

### <a name="delete-an-ip-filter-rule"></a>Usuwanie reguły filtru IP

Poniższy przykład szablonu usuwa wszystkie reguły filtru IP dla wystąpienia DPS.

```json
{ 
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",  
    "contentVersion": "1.0.0.0",  
    "parameters": { 
        "iotDpsName": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().name]", 
            "minLength": 3, 
            "metadata": { 
                "description": "Specifies the name of the IoT DPS service." 
            } 
        },  
        "location": { 
            "type": "string", 
            "defaultValue": "[resourceGroup().location]", 
            "metadata": { 
                "description": "Location for Iot DPS resource." 
            } 
        }        
    },  
    "variables": { 
        "iotDpsApiVersion": "2020-01-01" 
    },  
    "resources": [ 
        { 
            "type": "Microsoft.Devices/provisioningServices", 
            "apiVersion": "[variables('iotDpsApiVersion')]", 
            "name": "[parameters('iotDpsName')]", 
            "location": "[parameters('location')]", 
            "sku": { 
                "name": "S1", 
                "tier": "Standard", 
                "capacity": 1 
            }, 
            "properties": { 
            }             
        } 
    ] 
}
```



## <a name="ip-filter-rule-evaluation"></a>Ocena reguł filtru IP

Reguły filtru IP są stosowane w kolejności, a pierwsza reguła zgodna z adresem IP określa akcję akceptowania lub odrzucania.

Jeśli na przykład chcesz zaakceptować adresy w zakresie 192.168.100.0/22 i odrzucić wszystko inne, pierwsza reguła w siatce powinna zaakceptować zakres adresów 192.168.100.0/22. Następna reguła powinna odrzucić wszystkie adresy przy użyciu zakresu 0.0.0.0/0.

Kolejność reguł filtrowania IP w siatce można zmienić, klikając trzy pionowe kropki na początku wiersza oraz używając przeciągania i upuszczania.

Aby zapisać nową kolejność reguł filtru IP, kliknij przycisk **Zapisz**.

![Zmienianie kolejności reguł filtrowania adresów IP DPS](./media/iot-dps-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Następne kroki

Aby dokładniej zbadać zarządzanie DPS, zobacz:

* [Opis adresów IP dps IoT](iot-dps-understand-ip-address.md)
* [Konfigurowanie dps przy użyciu interfejsu wiersza polecenia platformy Azure](how-to-manage-dps-with-cli.md)
* [Kontrola dostępu do DPS](how-to-control-access.md)
