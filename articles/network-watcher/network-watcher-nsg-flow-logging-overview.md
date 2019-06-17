---
title: Wprowadzenie do rejestrowania przepływu zabezpieczeń sieciowych grup przy użyciu usługi Azure Network Watcher | Dokumentacja firmy Microsoft
description: W tym artykule wyjaśniono, jak korzystać z funkcji Dzienniki przepływu sieciowej grupy zabezpieczeń usługi Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 1ec7fd4116aa848a9c431df386997cb23f405f1b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/13/2019
ms.locfileid: "64925409"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Wprowadzenie do rejestrowanie przepływu dla sieciowych grup zabezpieczeń

Dzienniki przepływu Sieciowej grupy zabezpieczeń sieci są funkcją usługi Network Watcher, który służy do wyświetlania informacji na temat przychodzący i wychodzący ruch IP sieciowej grupy zabezpieczeń. Dzienniki przepływów są zapisywane w formacie JSON i Pokaż ruchu wychodzącego i przychodzącego przepływów na podstawie poszczególnych reguł, interfejsu sieciowego (NIC) przepływ dotyczy, 5-elementowych informacji na temat przepływu (źródłowego i docelowego adresu IP, źródłowy i docelowy port i protokół) Jeśli był ruch dozwolone lub odmowa i w wersji 2, informacji o przepływności (bajtów i pakietów).


![Przegląd dzienników przepływu](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Gdy dzienników przepływów sieciowych grup zabezpieczeń docelowej, nie są wyświetlane takie same jak inne dzienniki. Dzienniki przepływów są przechowywane tylko w ramach konta magazynu, a następnie wykonaj podana ścieżka pokazano w poniższym przykładzie:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
Można analizować dzienniki przepływu i Uzyskaj szczegółowe informacje dotyczące ruchu w sieci przy użyciu [traffic analytics](traffic-analytics.md).

Te same zasady przechowywania widoczne dla innych dzienników dotyczą dzienników przepływów. Możesz ustawić zasady przechowywania dziennika od dnia 1 do 2147483647 dni. Jeśli zasady przechowywania nie są ustawione, dzienniki będą obsługiwane przez czas nieokreślony.

> [!NOTE] 
> Funkcja zasad przechowywania użyciu rejestrowanie przepływu sieciowych grup zabezpieczeń może skutkować dużą liczbę operacji magazynu i powiązanych kosztów. Jeśli funkcja zasad przechowywania nie jest wymagane, zaleca się Ustaw tę wartość na 0.


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
                    * **Ruch decyzji** — czy zezwolenie lub odrzucenie ruchu. Prawidłowe wartości to **A** dla dozwolone i **D** dla odmowa.
                    * **Stan usługi Flow — tylko w wersji 2** -przechwytuje stan przepływu. Możliwe stany to **B**: początek — gdy zostanie utworzony przepływ. Statystyki nie są podawane. **C**: kontynuacja — dotyczy trwającego przepływu. Statystyki są podawane w 5-minutowych odstępach. **E**: koniec — gdy przepływ zostanie zakończony. Statystyki są podawane.
                    * **Pakiety - źródła do miejsca docelowego — w wersji 2 tylko** całkowita liczba pakietów TCP lub UDP, które są odsyłane ze źródła do miejsca docelowego od czasu ostatniej aktualizacji.
                    * **Liczba wysłanych bajtów - źródła do miejsca docelowego — w wersji 2 tylko** całkowita liczba bajtów pakiet TCP lub UDP, odsyłane ze źródła do miejsca docelowego od czasu ostatniej aktualizacji. Liczba bajtów pakietu obejmuje nagłówek i ładunek pakietu.
                    * **Pakiety - docelowe źródło — tylko w wersji 2** całkowita liczba pakietów TCP lub UDP, które są wysyłane z docelowym źródłem od czasu ostatniej aktualizacji.
                    * **Liczba wysłanych bajtów - docelowe źródło — tylko w wersji 2** całkowita liczba bajtów pakiet TCP i UDP, wysyłane z docelowym źródłem od czasu ostatniej aktualizacji. Liczba bajtów pakietu obejmuje nagłówek i ładunek pakietu.

## <a name="nsg-flow-logs-version-2"></a>W wersji 2 dzienników przepływów sieciowych grup zabezpieczeń

Dzienniki w wersji 2 wprowadzono stan przepływu. Można skonfigurować, która wersja dzienników przepływu pojawi się. Aby dowiedzieć się, jak włączyć dzienniki przepływu, zobacz [rejestrowanie przepływu sieciowej grupy zabezpieczeń z włączeniem](network-watcher-nsg-flow-logging-portal.md).

Stan przepływu *B* jest rejestrowane, gdy przepływ jest inicjowana. Stan przepływu *C* i stan przepływu *E* są Stany, oznaczające kontynuacji przepływ i zakończenia przepływu. Zarówno *C* i *E* stany zawierają informacje o przepustowości ruchu.

Dla kontynuacji *C* i na końcu *E* Stany przepływu wygasają po, liczby bajtów i pakietów są łącznej liczby od czasu poprzedniego rekordu przepływu w spójnej kolekcji. Całkowita liczba pakietów przesyłanych odwołujące się do konwersacji poprzedniego przykładu, jest 1021 + 52 + 8005 + 47 = 9125. Całkowita liczba bajtów przesłanych jest 588096 + 29952 + 4610880 + 27072 = 5256000.

**Przykład**: Przepływ krotkami z konwersacji między 185.170.185.105:35370 i 10.2.0.4:23 TCP:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,," "1493695838,185.170.185.105,10.2.0.4,35370,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.185.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

Dla kontynuacji *C* i na końcu *E* Stany przepływu wygasają po, liczby bajtów i pakietów są łącznej liczby od czasu poprzedniego rekordu przepływu w spójnej kolekcji. Całkowita liczba pakietów przesyłanych odwołujące się do konwersacji poprzedniego przykładu, jest 1021 + 52 + 8005 + 47 = 9125. Całkowita liczba bajtów przesłanych jest 588096 + 29952 + 4610880 + 27072 = 5256000.

Tekst, który następuje jest przykładem dziennika przepływu. Jak widać, istnieje wiele rekordów, które należy wykonać na liście właściwości opisanych w poprzedniej sekcji.

## <a name="nsg-flow-logging-considerations"></a>Zagadnienia dotyczące rejestrowanie przepływu sieciowych grup zabezpieczeń

**Włączyć NSG Flow rejestrowanie na wszystkie sieciowe grupy zabezpieczeń, które są dołączone do zasobu**: Przepływ rejestrowania na platformie Azure jest konfigurowany na zasób sieciowej grupy zabezpieczeń. Przepływ zostanie być skojarzony tylko z jedną regułę sieciowej grupy zabezpieczeń. W scenariuszach, gdzie są wykorzystywane wiele sieciowych grup zabezpieczeń, zaleca się, czy rejestrowanie przepływu sieciowych grup zabezpieczeń jest włączona na wszystkie sieciowe grupy zabezpieczeń stosowane zasobu podsiecią lub interfejsem sieciowym aby upewnić się, że cały ruch jest rejestrowane. Zobacz [jak ruch jest oceniany](../virtual-network/security-overview.md#how-traffic-is-evaluated) Aby uzyskać więcej informacji na temat sieciowych grup zabezpieczeń. 

**Przepływ koszty rejestrowania**: Rejestrowanie przepływu sieciowych grup zabezpieczeń jest rozliczana na woluminie dzienników generowanych. Duży ruch woluminu może spowodować przepływ dużą woluminu dziennika i powiązanych kosztów. Cennik dzienników przepływów sieciowych grup zabezpieczeń nie obejmuje podstawowych kosztów magazynu. Funkcja zasad przechowywania użyciu rejestrowanie przepływu sieciowych grup zabezpieczeń może skutkować dużą liczbę operacji magazynu i powiązanych kosztów. Jeśli funkcja zasad przechowywania nie jest wymagane, zaleca się Ustaw tę wartość na 0. Zobacz [cennik usługi Network Watcher](https://azure.microsoft.com/pricing/details/network-watcher/) i [cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/) dodatkowe szczegóły.

**Dla ruchu przychodzącego przepływów rejestrowane z Internetu adresy IP do maszyn wirtualnych bez publiczne adresy IP**: Maszyny wirtualne nie mają przypisany za pośrednictwem publicznego adresu IP skojarzone z kartą Sieciową jako publiczny adres IP na poziomie wystąpienia publiczny adres IP lub które są częścią puli zaplecza modułu równoważenia obciążenia podstawowe, użyj [domyślne SNAT](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) i adres IP przypisany przez Azure w celu ułatwienia łączności wychodzącej. W rezultacie, zostanie wyświetlony przepływ wpisów dziennika dla przepływów z Internetu adresy IP, jeśli przepływ jest kierowany do portu z zakresu portów przypisanych do SNAT. Gdy platforma Azure nie zezwala na te przepływy do maszyny Wirtualnej, próba jest rejestrowane i pojawi się dzienników przepływu sieciowych grup zabezpieczeń usługi Network Watcher zgodnie z projektem. Zaleca się, że niechcianego przychodzącego ruchu internetowego można jawnie zablokowane za pomocą sieciowej grupy zabezpieczeń.

## <a name="sample-log-records"></a>Przykładowy rekord dziennika

Tekst, który następuje jest przykładem dziennika przepływu. Jak widać, istnieje wiele rekordów, które należy wykonać na liście właściwości opisanych w poprzedniej sekcji.


> [!NOTE]
> Wartości w **flowTuples* właściwość to lista rozdzielonych przecinkami.
 
### <a name="version-1-nsg-flow-log-format-sample"></a>Próbki format dziennika, wersja 1 sieciowej grupy zabezpieczeń przepływu
```json
{
    "records": [
        {
            "time": "2017-02-16T22:00:32.8950000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A",
                                    "1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A",
                                    "1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A",
                                    "1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2017-02-16T22:01:32.8960000Z",
            "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 1,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "UserRule_default-allow-rdp",
                        "flows": [
                            {
                                "mac": "000D3AF8801A",
                                "flowTuples": [
                                    "1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A",
                                    "1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A",
                                    "1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
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
### <a name="version-2-nsg-flow-log-format-sample"></a>Próbki format dziennika, przepływu sieciowej grupy zabezpieczeń w wersji 2
```json
 {
    "records": [
        {
            "time": "2018-11-13T12:00:35.3899262Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110402,94.102.49.190,10.5.16.4,28746,443,U,I,D,B,,,,",
                                    "1542110424,176.119.4.10,10.5.16.4,56509,59336,T,I,D,B,,,,",
                                    "1542110432,167.99.86.8,10.5.16.4,48495,8088,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    },
                    {
                        "rule": "DefaultRule_AllowInternetOutBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110377,10.5.16.4,13.67.143.118,59831,443,T,O,A,B,,,,",
                                    "1542110379,10.5.16.4,13.67.143.117,59932,443,T,O,A,E,1,66,1,66",
                                    "1542110379,10.5.16.4,13.67.143.115,44931,443,T,O,A,C,30,16978,24,14008",
                                    "1542110406,10.5.16.4,40.71.12.225,59929,443,T,O,A,E,15,8489,12,7054"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        {
            "time": "2018-11-13T12:01:35.3918317Z",
            "systemId": "a0fca5ce-022c-47b1-9735-89943b42f2fa",
            "category": "NetworkSecurityGroupFlowEvent",
            "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
            "operationName": "NetworkSecurityGroupFlowEvents",
            "properties": {
                "Version": 2,
                "flows": [
                    {
                        "rule": "DefaultRule_DenyAllInBound",
                        "flows": [
                            {
                                "mac": "000D3AF87856",
                                "flowTuples": [
                                    "1542110437,125.64.94.197,10.5.16.4,59752,18264,T,I,D,B,,,,",
                                    "1542110475,80.211.72.221,10.5.16.4,37433,8088,T,I,D,B,,,,",
                                    "1542110487,46.101.199.124,10.5.16.4,60577,8088,T,I,D,B,,,,",
                                    "1542110490,176.119.4.30,10.5.16.4,57067,52801,T,I,D,B,,,,"
                                ]
                            }
                        ]
                    }
                ]
            }
        },
        ...
```

## <a name="next-steps"></a>Kolejne kroki

- Aby dowiedzieć się, jak włączyć dzienniki przepływu, zobacz [rejestrowanie przepływu sieciowej grupy zabezpieczeń z włączeniem](network-watcher-nsg-flow-logging-portal.md).
- Aby dowiedzieć się, jak odczytywać dzienniki przepływu, zobacz [dzienników przepływu sieciowej grupy zabezpieczeń odczytu](network-watcher-read-nsg-flow-logs.md).
- Aby dowiedzieć się więcej na temat rejestrowania sieciowej grupy zabezpieczeń, zobacz [usługi Azure Monitor dzienników dla sieciowych grup zabezpieczeń (NSG)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Aby określić, czy ruch jest dozwolony lub wychodzić do / z maszyny Wirtualnej, zobacz [diagnozowanie problemu z filtrowaniem ruchu maszyny Wirtualnej sieci](diagnose-vm-network-traffic-filtering-problem.md)
