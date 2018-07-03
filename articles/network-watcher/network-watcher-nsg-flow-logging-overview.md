---
title: Wprowadzenie do rejestrowania przepływu zabezpieczeń sieciowych grup przy użyciu usługi Azure Network Watcher | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak korzystać z funkcji Dzienniki przepływu sieciowej grupy zabezpieczeń usługi Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: ae4edb82fa5e192a30d297dae82199bb7efca0c2
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344975"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Wprowadzenie do rejestrowanie przepływu dla sieciowych grup zabezpieczeń

Dzienniki przepływu Sieciowej grupy zabezpieczeń sieci są funkcją usługi Network Watcher, który służy do wyświetlania informacji na temat przychodzący i wychodzący ruch IP sieciowej grupy zabezpieczeń. Dzienniki przepływów są zapisywane w formacie json i wyświetlić przepływy wychodzące i przychodzące na poszczególnych reguł dla interfejsu sieciowego (NIC) dotyczy przepływu, 5-elementowych informacji na temat przepływu (źródłowego i docelowego adresu IP, źródłowy i docelowy port i protokół), a ruch był dozwolony lub blokowany.

![Przegląd dzienników przepływu](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Gdy dzienników przepływów sieciowych grup zabezpieczeń docelowej, nie są wyświetlane takie same jak inne dzienniki. Dzienniki przepływów są przechowywane tylko w ramach konta magazynu, a następnie wykonaj podana ścieżka pokazano w poniższym przykładzie:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Te same zasady przechowywania widoczne dla innych dzienników dotyczą dzienników przepływów. Możesz ustawić zasady przechowywania dziennika od dnia 1 do 2147483647 dni. Jeśli zasady przechowywania nie są ustawione, dzienniki będą obsługiwane przez czas nieokreślony.

## <a name="log-file"></a>Plik dziennika

Dzienniki przepływów obejmują następujące właściwości:

* **czas** — godzina zarejestrowania zdarzenia
* **systemId** -identyfikatorem zasobu sieciowej grupy zabezpieczeń.
* **Kategoria** -kategorii zdarzenia. Kategoria jest zawsze **NetworkSecurityGroupFlowEvent**
* **RESOURCEID** — identyfikator sieciowej grupy zabezpieczeń
* **operationName** — zawsze NetworkSecurityGroupFlowEvents
* **właściwości** — zbiór właściwości przepływu
    * **Wersja** — numer wersji schemat zdarzeń dziennika przepływu
    * **przepływy** — zbierania przepływów. Ta właściwość ma wiele wpisów dla różnych zasad
        * **Reguła** -reguły są wyświetlane przepływów
            * **przepływy** — zbierania przepływów
                * **Mac** -adres MAC karty Sieciowej dla maszyny Wirtualnej, w którym przepływ został zebrany
                * **flowTuples** — ciąg, który zawiera wiele właściwości dla krotki przepływu w formacie rozdzielonych przecinkami
                    * **Sygnatury czasu** — ta wartość jest sygnaturę czasową, gdy przepływ występującym w formacie EPOKI systemu UNIX
                    * **Źródłowy adres IP** -źródłowy adres IP
                    * **Docelowego adresu IP** -docelowy adres IP
                    * **Port źródłowy** — port źródłowy
                    * **Port docelowy** — docelowy Port
                    * **Protokół** — protokół przepływ. Prawidłowe wartości to **T** dla protokołu TCP i **U** protokołu UDP
                    * **Traffic Flow** -kierunek przepływu ruchu. Prawidłowe wartości to **I** dla ruchu przychodzącego i **O** dla ruchu wychodzącego.
                    * **Ruch** — czy zezwolenie lub odrzucenie ruchu. Prawidłowe wartości to **A** dla dozwolone i **D** dla odmowa.

Tekst, który następuje jest przykładem dziennika przepływu. Jak widać, istnieje wiele rekordów, które należy wykonać na liście właściwości opisanych w poprzedniej sekcji.

> [!NOTE]
> Wartości w **flowTuples* właściwość to lista rozdzielonych przecinkami.
 
```json
{
    "records":
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        ,
        ...
```

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak włączyć dzienniki przepływu, zobacz [rejestrowanie przepływu sieciowej grupy zabezpieczeń z włączeniem](network-watcher-nsg-flow-logging-portal.md).
- Aby dowiedzieć się więcej na temat rejestrowania sieciowej grupy zabezpieczeń, zobacz [Log analytics dla sieciowych grup zabezpieczeń (NSG)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Aby określić, czy ruch jest dozwolony lub wychodzić do / z maszyny Wirtualnej, zobacz [diagnozowanie problemu z filtrowaniem ruchu maszyny Wirtualnej sieci](diagnose-vm-network-traffic-filtering-problem.md)
