---
title: Filtry połączeń IP usługi Azure IoT Hub | Dokumenty firmy Microsoft
description: Jak używać filtrowania adresów IP do blokowania połączeń z określonych adresów IP do centrum Usługi Azure IoT Hub. Połączenia można blokować z poszczególnych lub zakresów adresów IP.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/22/2017
ms.author: robinsh
ms.openlocfilehash: a6bd8a766f3205358a65ef2fd0816643e4261cab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68414307"
---
# <a name="use-ip-filters"></a>Korzystanie z filtrów adresów IP

Zabezpieczenia to ważny aspekt każdego rozwiązania IoT opartego na usłudze Azure IoT Hub. Czasami należy jawnie określić adresy IP, z których urządzenia mogą łączyć się w ramach konfiguracji zabezpieczeń. Funkcja *filtru IP* umożliwia skonfigurowanie reguł odrzucania lub akceptowania ruchu z określonych adresów IPv4.

## <a name="when-to-use"></a>Kiedy stosować

Istnieją dwa konkretne przypadki użycia, gdy jest to przydatne do blokowania punktów końcowych Usługi IoT Hub dla niektórych adresów IP:

* Centrum IoT hub powinien odbierać ruch tylko z określonego zakresu adresów IP i odrzucać wszystko inne. Na przykład używasz centrum IoT hub z [marszrutą azure express](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) do tworzenia połączeń prywatnych między centrum IoT hub i infrastruktury lokalnej.

* Należy odrzucić ruch z adresów IP, które zostały zidentyfikowane jako podejrzane przez administratora centrum IoT.

## <a name="how-filter-rules-are-applied"></a>Jak stosowane są reguły filtru

Reguły filtru IP są stosowane na poziomie usługi Usługi Usługi IoT Hub. W związku z tym reguły filtru IP mają zastosowanie do wszystkich połączeń z urządzeń i aplikacji zaplecza przy użyciu dowolnego obsługiwanego protokołu.

Każda próba połączenia z adresu IP, która pasuje do odrzucającej reguły IP w centrum IoT, otrzymuje nieautoryzowany kod stanu 401 i opis. Komunikat odpowiedzi nie wspomina o regule IP.

## <a name="default-setting"></a>Ustawienie domyślne

Domyślnie siatka **filtru IP** w portalu dla centrum IoT jest pusta. To ustawienie domyślne oznacza, że koncentrator akceptuje połączenia z dowolnego adresu IP. To ustawienie domyślne jest równoważne regule, która akceptuje zakres adresów IP 0.0.0.0/0.

![Domyślne ustawienia filtra IP usługi IoT Hub](./media/iot-hub-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Dodawanie lub edytowanie reguły filtru IP

Aby dodać regułę filtru IP, wybierz pozycję **+ Dodaj regułę filtru IP**.

![Dodawanie reguły filtru IP do centrum IoT](./media/iot-hub-ip-filtering/ip-filter-add-rule.png)

Po wybraniu opcji **Dodaj regułę filtru IP**wypełnij pola.

![Po wybraniu opcji Dodaj regułę filtru IP](./media/iot-hub-ip-filtering/ip-filter-after-selecting-add.png)

* Podaj **nazwę** reguły filtru IP. Musi to być unikatowy, niewrażliwy, alfanumeryczny ciąg o długości do 128 znaków. Akceptowane `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` są tylko 7-bitowe znaki alfanumeryczne ASCII plus.

* Podaj pojedynczy adres IPv4 lub blok adresów IP w notacji CIDR. Na przykład w notacji CIDR 192.168.100.0/22 reprezentuje adresy IPv4 1024 od 192.168.100.0 do 192.168.103.255.

* Wybierz **pozycję Zezwalaj** lub **blokuj** jako **akcję** dla reguły filtru IP.

Po wypełnieniu pól wybierz pozycję **Zapisz,** aby zapisać regułę. Zostanie wyświetlony alert informujący, że aktualizacja jest w toku.

![Powiadomienie o zapisywaniu reguły filtru IP](./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png)

Opcja **Dodaj** jest wyłączona po osiągnięciu maksymalnie 10 reguł filtru IP.

Aby edytować istniejącą regułę, zaznacz dane, które chcesz zmienić, wykonuj zmiany, a następnie wybierz pozycję **Zapisz,** aby zapisać edycję.

> [!NOTE]
> Odrzucenie adresów IP może uniemożliwić innym usługom platformy Azure (takim jak usługa Azure Stream Analytics, maszyny wirtualne platformy Azure lub Eksplorator urządzeń w portalu) interakcję z centrum IoT Hub.

> [!WARNING]
> Jeśli używasz usługi Azure Stream Analytics (ASA) do odczytywania wiadomości z centrum IoT z włączoną filtrowania IP, użyj nazwy i punktu końcowego centrum zdarzeń w ciągu połączenia ASA.

## <a name="delete-an-ip-filter-rule"></a>Usuwanie reguły filtru IP

Aby usunąć regułę filtru IP, zaznacz ikonę kosza w tym wierszu, a następnie wybierz pozycję **Zapisz**. Reguła zostanie usunięta, a zmiana zostanie zapisana.

![Usuwanie reguły filtru IP usługi IoT Hub](./media/iot-hub-ip-filtering/ip-filter-delete-rule.png)

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Pobieranie i aktualizowanie filtrów IP przy użyciu interfejsu wiersza polecenia platformy Azure

Filtry IP usługi IoT Hub można pobierać i aktualizować za pośrednictwem [interfejsu wiersza polecenia platformy Azure.](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)

Aby pobrać bieżące filtry IP centrum IoT Hub, uruchom:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Spowoduje to zwrócenie obiektu JSON, w którym istniejące `properties.ipFilterRules` filtry IP są wymienione pod kluczem:

```json
{
...
    "properties": {
        "ipFilterRules": [
        {
            "action": "Reject",
            "filterName": "MaliciousIP",
            "ipMask": "6.6.6.6/6"
        },
        {
            "action": "Allow",
            "filterName": "GoodIP",
            "ipMask": "131.107.160.200"
        },
        ...
        ],
    },
...
}
```

Aby dodać nowy filtr IP dla centrum IoT Hub, uruchom:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Aby usunąć istniejący filtr IP w centrum IoT Hub, uruchom:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Należy `<ipFilterIndexToRemove>` pamiętać, że musi odpowiadać kolejności filtrów IP w `properties.ipFilterRules`centrum IoT Hub .

## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Pobieranie i aktualizowanie filtrów IP przy użyciu programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Filtry IP usługi IoT Hub można pobrać i ustawić za pośrednictwem [programu Azure PowerShell.](/powershell/azure/overview)

```powershell
# Get your IoT Hub resource using its name and its resource group name
$iothubResource = Get-AzResource -ResourceGroupName <resourceGroupNmae> -ResourceName <iotHubName> -ExpandProperties

# Access existing IP filter rules
$iothubResource.Properties.ipFilterRules |% { Write-host $_ }

# Construct a new IP filter
$filter = @{'filterName'='MaliciousIP'; 'action'='Reject'; 'ipMask'='6.6.6.6/6'}

# Add your new IP filter rule
$iothubResource.Properties.ipFilterRules += $filter

# Remove an existing IP filter rule using its name, e.g., 'GoodIP'
$iothubResource.Properties.ipFilterRules = @($iothubResource.Properties.ipFilterRules | Where 'filterName' -ne 'GoodIP')

# Update your IoT Hub resource with your updated IP filters
$iothubResource | Set-AzResource -Force
```

## <a name="update-ip-filter-rules-using-rest"></a>Aktualizowanie reguł filtru IP przy użyciu funkcji REST

Można również pobrać i zmodyfikować filtr IP usługi IoT Hub przy użyciu punktu końcowego REST dostawcy zasobów platformy Azure. Zobacz `properties.ipFilterRules` w [createorupdate metody](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate).

## <a name="ip-filter-rule-evaluation"></a>Ocena reguł filtru IP

Reguły filtru IP są stosowane w kolejności, a pierwsza reguła zgodna z adresem IP określa akcję akceptowania lub odrzucania.

Jeśli na przykład chcesz zaakceptować adresy w zakresie 192.168.100.0/22 i odrzucić wszystko inne, pierwsza reguła w siatce powinna zaakceptować zakres adresów 192.168.100.0/22. Następna reguła powinna odrzucić wszystkie adresy przy użyciu zakresu 0.0.0.0/0.

Kolejność reguł filtrowania IP w siatce można zmienić, klikając trzy pionowe kropki na początku wiersza oraz używając przeciągania i upuszczania.

Aby zapisać nową kolejność reguł filtru IP, kliknij przycisk **Zapisz**.

![Zmienianie kolejności reguł filtrowania IP usługi IoT Hub](./media/iot-hub-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Następne kroki

Aby dokładniej zbadać możliwości usługi IoT Hub, zobacz:

* [Monitorowanie operacji](iot-hub-operations-monitoring.md)
* [Metryki usługi IoT Hub](iot-hub-metrics.md)
