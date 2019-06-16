---
title: Azure IoT Hub IP połączenia filtrów | Dokumentacja firmy Microsoft
description: Jak używać adresu IP filtrowania w celu połączenia bloków z określonych adresów IP do usługi Azure IoT hub. Możesz zablokować możliwość połączenia z indywidualnych lub zakresów adresów IP.
author: rezasherafat
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/23/2017
ms.author: rezas
ms.openlocfilehash: 5e755a528d91d6321f431f256eafdcb01e67d601
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "60399054"
---
# <a name="use-ip-filters"></a>Korzystanie z filtrów adresów IP

Zabezpieczenia są istotnym elementem każde rozwiązanie IoT, w oparciu o usługi Azure IoT Hub. Czasami Musisz jawnie określić adresy IP, z których można połączyć urządzenia jako część konfiguracji zabezpieczeń. *Filtru IP* funkcja umożliwia skonfigurowanie reguł dla odrzuca lub akceptowania ruchu z określonych adresów IPv4.

## <a name="when-to-use"></a>Kiedy stosować

Istnieją dwa szczególne przypadki użycia, gdy jest ona przydatne blokowanie punktów końcowych usługi IoT Hub dla określonych adresów IP:

* Centrum IoT hub powinny odbierać ruch tylko z określonego zakresu adresów IP i odrzucić wszystkie inne elementy. Na przykład używasz usługi IoT hub przy użyciu [Azure Express Route](https://azure.microsoft.com/documentation/articles/expressroute-faqs/#supported-services) na tworzenie prywatnych połączeń między centrum IoT hub i infrastrukturą lokalną.

* Należy odrzucić ruch z adresów IP, które zostały zidentyfikowane jako podejrzane przez administratora usługi IoT hub.

## <a name="how-filter-rules-are-applied"></a>Sposób stosowania reguły filtrowania

Reguły filtrowania adresów IP są stosowane na poziomie usługi IoT Hub. W związku z tym reguł filtrowania adresów IP stosowane do wszystkich połączeń z urządzeniami i aplikacjami zaplecza za pomocą dowolnego obsługiwanego protokołu.

Każda próba połączenia z adresu IP, który jest zgodny z wydzielenia regułą adresów IP w usłudze IoT hub odbiera kod stanu 401 nieautoryzowane i opis. Komunikat odpowiedzi nie mogą zawierać reguły adresów IP.

## <a name="default-setting"></a>Ustawienie domyślne

Domyślnie **filtru IP** siatki w portalu dla usługi IoT hub jest pusty. To ustawienie domyślne oznacza, że Centrum akceptuje połączenia z dowolnego adresu IP. To ustawienie domyślne jest odpowiednikiem regułę, która akceptuje zakres adresów IP 0.0.0.0/0.

![Ustawienia filtru IP domyślnej usługi IoT Hub](./media/iot-hub-ip-filtering/ip-filter-default.png)

## <a name="add-or-edit-an-ip-filter-rule"></a>Dodawanie lub edytowanie reguły filtrowania adresów IP

Po dodaniu regułę filtrowania adresów IP, zostanie wyświetlony monit o następujących wartości:

* **Nazwa reguły filtrowania adresów IP** który musi być unikatowy, bez uwzględniania wielkości liter, alfanumerycznego ciągu maksymalnie 128 znaków. Tylko znaki ASCII 7-bitowe znaki alfanumeryczne oraz `{'-', ':', '/', '\', '.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '''}` są akceptowane.

* Wybierz **Odrzuć** lub **zaakceptować** jako **akcji** reguły filtrowania adresów IP.

* Podaj pojedynczy adres IPv4 lub bloku adresów IP w notacji CIDR. Na przykład w CIDR 192.168.100.0/22 notacji reprezentuje 1024 adresów IPv4 od 192.168.100.0 do 192.168.103.255.

![Dodawanie reguły filtrowania adresów IP do usługi IoT hub](./media/iot-hub-ip-filtering/ip-filter-add-rule.png)

Po zapisaniu reguły zobaczysz alert informujący, że aktualizacja jest w toku.

![Powiadomienie dotyczące zapisywania regułę filtrowania adresów IP](./media/iot-hub-ip-filtering/ip-filter-save-new-rule.png)

**Dodaj** opcja jest wyłączona po przejściu do maksymalnie 10 reguł filtrowania adresów IP.

Możesz edytować istniejącą regułę, klikając dwukrotnie wiersz, który zawiera daną zasadę.

> [!NOTE]
> Odrzuca IP adresów można zapobiec innych usług platformy Azure (np. usługi Azure Stream Analytics, Azure Virtual Machines lub Device Explorer w portalu) interakcji z usługą IoT hub.

> [!WARNING]
> Jeśli używasz usługi Azure Stream Analytics (ASA) odczytywanie komunikatów z usługi IoT hub za pomocą filtrowania adresów IP włączone, użyj nazwy zgodnego z Centrum zdarzeń i punkt końcowy usługi IoT Hub w parametrach połączenia ASA.

## <a name="delete-an-ip-filter-rule"></a>Usuń regułę filtrowania adresów IP

Aby usunąć regułę filtrowania adresów IP, wybierz co najmniej jedną regułę w siatce i kliknij przycisk **Usuń**.

![Usuń regułę filtrowania adresów IP Centrum IoT](./media/iot-hub-ip-filtering/ip-filter-delete-rule.png)

## <a name="retrieve-and-update-ip-filters-using-azure-cli"></a>Pobierania i aktualizowania filtrów IP przy użyciu wiersza polecenia platformy Azure

Można je pobrać i aktualizowane przy użyciu filtrów IP w usłudze IoT Hub [wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest). 

Aby pobrać bieżące filtry IP Centrum IoT Hub, uruchom polecenie:

```azurecli-interactive
az resource show -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs
```

Spowoduje to zwrócenie obiektu JSON, w którym istniejące filtry IP należą `properties.ipFilterRules` klucza:

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

Aby dodać nowy filtr adresów IP dla Centrum IoT, uruchom polecenie:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules "{\"action\":\"Reject\",\"filterName\":\"MaliciousIP\",\"ipMask\":\"6.6.6.6/6\"}"
```

Aby usunąć istniejący filtr adresów IP w usłudze IoT Hub, uruchom polecenie:

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --add properties.ipFilterRules <ipFilterIndexToRemove>
```

Należy pamiętać, że `<ipFilterIndexToRemove>` musi odpowiadać kolejność filtrów IP w usłudze IoT Hub `properties.ipFilterRules`.


## <a name="retrieve-and-update-ip-filters-using-azure-powershell"></a>Pobierania i aktualizowania filtrów IP przy użyciu programu Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Można je pobrać i ustawić przy użyciu filtrów IP w usłudze IoT Hub [programu Azure PowerShell](/powershell/azure/overview). 

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

## <a name="update-ip-filter-rules-using-rest"></a>Aktualizowanie reguł filtrowania adresów IP przy użyciu usługi REST

Możesz również pobrać i zmodyfikować filtr adresów IP usługi IoT Hub przy użyciu punktu końcowego REST dostawcy zasobów platformy Azure. Zobacz `properties.ipFilterRules` w [metodę createorupdate](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate).


## <a name="ip-filter-rule-evaluation"></a>Ocenę reguł filtrowania adresów IP

Reguły filtrowania adresów IP są stosowane w kolejności i pierwszej reguły, który jest zgodny z adresem IP określa akcję Zaakceptuj lub Odrzuć.

Na przykład jeśli chcesz zaakceptować adresów w 192.168.100.0/22 zakresu i odrzucić wszystkie inne elementy, pierwszą regułę w siatce powinna obsługiwać 192.168.100.0/22 zakresu adresów. Następną regułę należy odrzucić wszystkie adresy, używając 0.0.0.0/0 zakresu.

Można zmienić kolejność reguł filtrowania adresów IP w siatce, klikając Wielokropek pionowy na początku wiersza i za pomocą przeciągania i upuszczania.

Aby zapisać nowy kolejność reguł filtrowania adresów IP, kliknij przycisk **Zapisz**.

![Zmień kolejność reguł filtrowania adresów IP Centrum IoT](./media/iot-hub-ip-filtering/ip-filter-rule-order.png)

## <a name="next-steps"></a>Kolejne kroki

Aby bliżej zapoznać się z możliwościami usługi IoT Hub, zobacz:

* [Monitorowanie operacji](iot-hub-operations-monitoring.md)
* [Metryki usługi IoT Hub](iot-hub-metrics.md)
