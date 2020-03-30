---
title: Wprowadzenie do rejestrowania przepływu dla nsgs
titleSuffix: Azure Network Watcher
description: W tym artykule wyjaśniono, jak korzystać z funkcji dzienników przepływu sieciowej sieciowej usługi Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: fb4a55b9757748581e26f3d6594f9be2139658cb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228261"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Wprowadzenie do rejestrowania przepływu dla grup zabezpieczeń sieci

Dzienniki przepływów sieciowych grup zabezpieczeń to funkcja usługi Network Watcher, która umożliwia wyświetlanie informacji dotyczących ruchu IP przychodzącego i wychodzącego przez sieciową grupę zabezpieczeń. Dzienniki przepływów są zapisywane w formacie JSON i przedstawiają przepływy wychodzące i przychodzące dla każdej reguły, interfejs sieciowy, którego dotyczy przepływ, 5-krotkowe informacje o przepływie (źródłowy/docelowy adres IP, port źródłowy/docelowy i protokół), informację o zezwoleniu na ruch albo jego zablokowaniu, a w wersji 2 również informacje o przepływności.


![omówienie dzienników przepływu](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Podczas dzienników przepływu docelowe sieciowychg, nie są wyświetlane takie same jak inne dzienniki. Dzienniki przepływu są przechowywane tylko na koncie magazynu i podążają za ścieżką rejestrowania pokazaną w poniższym przykładzie:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
Można analizować dzienniki przepływu i uzyskać wgląd w ruch sieciowy za pomocą [analizy ruchu](traffic-analytics.md).

Te same zasady przechowywania widoczne dla innych dzienników mają zastosowanie do dzienników przepływu. Zasady przechowywania dzienników można ustawić od 1 dnia do 365 dni. Jeśli zasady przechowywania nie jest ustawiona, dzienniki są przechowywane na zawsze.

## <a name="log-file"></a>Plik dziennika

Dzienniki przepływu zawierają następujące właściwości:

* **czas** - Czas, kiedy zdarzenie zostało zarejestrowane
* **systemId** — identyfikator zasobu sieciowej grupy zabezpieczeń.
* **kategoria** - Kategoria wydarzenia. Kategoria jest zawsze **NetworkSecurityGroupFlowEvent**
* **resourceid** — identyfikator zasobu grupy nsg
* **operationName** - Zawsze NetworkSecurityGroupFlowEvents
* **właściwości** — zbiór właściwości przepływu
    * **Wersja** — numer wersji schematu zdarzenia Dziennik przepływu
    * **przepływy** — zbiór przepływów. Ta właściwość ma wiele wpisów dla różnych reguł
        * **reguła** - Reguła, dla której przepływy są wymienione
            * **przepływy** - zbiór przepływów
                * **mac** — adres MAC karty sieciowej dla maszyny Wirtualnej, na której zebrano przepływ
                * **flowTuples** - Ciąg zawierający wiele właściwości krotki przepływu w formacie oddzielonym przecinkami
                    * **Sygnatura czasowa** — ta wartość jest sygnaturą czasową czasu wystąpienia przepływu w formacie epoki systemu UNIX
                    * **Źródłowy adres IP** — źródłowy adres IP
                    * **Docelowy adres IP** — docelowy adres IP
                    * **Port źródłowy** — port źródłowy
                    * **Port docelowy** — port docelowy
                    * **Protokół** — protokół przepływu. Prawidłowe wartości to **T** dla TCP i **U** dla UDP
                    * **Przepływ ruchu** — kierunek przepływu ruchu. Prawidłowe wartości to **I** dla ruchu przychodzącego i **O** dla ruchu wychodzącego.
                    * **Decyzja o ruchu drogowym** — czy ruch był dozwolony, czy zabroniony. Prawidłowe wartości są **A** dla dozwolone i **D** dla odmowy.
                    * **Stan przepływu — tylko w wersji 2** — przechwytuje stan przepływu. Możliwe stany to **B**: początek — gdy zostanie utworzony przepływ. Statystyki nie są podawane. **C**: kontynuacja — dotyczy ciągłego przepływu. Statystyki są podawane w 5-minutowych odstępach. **E**: koniec — gdy przepływ zostanie zakończony. Statystyki są podawane.
                    * **Pakiety — od źródła do miejsca docelowego — tylko w wersji 2** Całkowita liczba pakietów TCP lub UDP wysłanych ze źródła do miejsca docelowego od ostatniej aktualizacji.
                    * **Bajty wysłane — źródło do miejsca docelowego — tylko wersja 2** Całkowita liczba bajtów pakietów TCP lub UDP wysłanych ze źródła do miejsca docelowego od ostatniej aktualizacji. Liczba bajtów pakietu obejmuje nagłówek i ładunek pakietu.
                    * **Pakiety — miejsce docelowe do źródła — tylko wersja 2** Całkowita liczba pakietów TCP lub UDP wysłanych z miejsca docelowego do źródła od ostatniej aktualizacji.
                    * **Bajty wysłane - Miejsce docelowe do źródła - Tylko wersja 2** Całkowita liczba bajtów pakietów TCP i UDP wysłanych z miejsca docelowego do źródła od ostatniej aktualizacji. Liczba bajtów pakietu obejmuje nagłówek i ładunek pakietu.

## <a name="nsg-flow-logs-version-2"></a>Dzienniki przepływu NSG w wersji 2

Wersja 2 dzienników wprowadza stan przepływu. Można skonfigurować, która wersja dzienników przepływu, które otrzymujesz. Aby dowiedzieć się, jak włączyć dzienniki przepływu, zobacz [Włączanie rejestrowania przepływu nsg](network-watcher-nsg-flow-logging-portal.md).

Stan przepływu *B* jest rejestrowany podczas inicjowania przepływu. Stan przepływu *C* i stan przepływu *E* są stanami, które oznaczają kontynuację zakończenia przepływu i przepływu, odpowiednio. Zarówno *stany C,* jak i *E* zawierają informacje o przepustowości ruchu.

**Przykład:** Krotki przepływu z konwersacji TCP między 185.170.185.105:35370 i 10.2.0.4:23:

"1493763938,185.170.185.105,10.2.0.4,35370,23,T,I,A,B,,,," "1493695838,185.170.185.105,10,2.0.4,35370,23,23,T,I,A,C,1021,588096,8005,4610880" "1493696138,185.170.18 5.105,10.2.0.4,35370,23,T,I,A,E,52,29952,47,27072"

W przypadku stanów przepływu *C* i końca *E* liczba bajtów i pakietów jest zliczaniem agregacji od czasu poprzedniego rekordu krotki przepływu. Odwołując się do poprzedniej przykładowej konwersacji, całkowita liczba przesłanych pakietów to 1021+52+8005+47 = 9125. Całkowita liczba bajtów przeniesionych jest 588096+29952+4610880+27072 = 5256000.

Poniższy tekst jest przykładem dziennika przepływu. Jak widać, istnieje wiele rekordów, które są zgodne z listą właściwości opisaną w poprzedniej sekcji.

## <a name="nsg-flow-logging-considerations"></a>Zagadnienia dotyczące rejestrowania przepływu nsg

**Zagadnienia dotyczące konta magazynu:** 

- Lokalizacja: używane konto magazynu musi znajdować się w tym samym regionie co grupa celu nd.
- Samodzielne zarządzanie rotacją kluczy: Jeśli zmienisz/obrócisz klucze dostępu do konta magazynu, dzienniki przepływu sieciowej przestaną działać. Aby rozwiązać ten problem, należy wyłączyć, a następnie ponownie włączyć dzienniki przepływu nsg.

**Włącz rejestrowanie przepływu zabezpieczeń systemu nsg na wszystkich grupach zabezpieczeń podłączonych do zasobu:** rejestrowanie przepływu na platformie Azure jest skonfigurowane w zasobie nsg. Przepływ będzie skojarzony tylko z jedną regułą nsg. W scenariuszach, w których wiele sieciowych grup zabezpieczeń są wykorzystywane, zaleca się, że rejestrowanie przepływu sieciowej grupy zabezpieczeń jest włączona na wszystkich sieciowych grup zabezpieczeń zastosowano podsieci lub interfejs sieciowy zasobu, aby upewnić się, że cały ruch jest rejestrowany. Aby uzyskać więcej informacji, [zobacz, jak ruch jest oceniany](../virtual-network/security-overview.md#how-traffic-is-evaluated) w sieciowych grupach zabezpieczeń.

**Koszty rejestrowania przepływu:** Rejestrowanie przepływu nsg jest rozliczane na woluminie wyprodukowanych dzienników. Duża głośność ruchu może spowodować duży wolumen dziennika przepływu i związane z tym koszty. Ceny dziennika przepływu nsg nie obejmuje podstawowych kosztów magazynu. Korzystanie z funkcji zasad przechowywania z rejestrowaniem przepływu nsg oznacza poniesienie oddzielnych kosztów magazynowania przez dłuższy czas. Jeśli nie potrzebujesz funkcji zasad przechowywania, zaleca się ustawienie tej wartości na 0. Aby uzyskać więcej informacji, zobacz [Cennik obserwatora sieci](https://azure.microsoft.com/pricing/details/network-watcher/) i [Cennik usługi Azure Storage, aby](https://azure.microsoft.com/pricing/details/storage/) uzyskać dodatkowe informacje.

**Przepływy przychodzące rejestrowane z internetowych adresów IP do maszyn wirtualnych bez publicznych adresów IP:** maszyny wirtualne, które nie mają publicznego adresu IP przypisanego za pośrednictwem publicznego adresu IP skojarzonego z kartą sieciową jako publiczny adres IP na poziomie wystąpienia lub które są częścią podstawowej puli modułów równoważenia obciążenia, używają [domyślnego identyfikatora SNAT](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) i mają adres IP przypisany przez platformę Azure w celu ułatwienia łączności wychodzącej. W rezultacie mogą być widoczne wpisy dziennika przepływu dla przepływów z internetowych adresów IP, jeśli przepływ jest przeznaczony do portu w zakresie portów przypisanych do SNAT. Chociaż platforma Azure nie zezwala na te przepływy do maszyny Wirtualnej, próba jest rejestrowana i pojawia się w dzienniku przepływu sieciowej sieciowej sieciowej czujki sieciowej obserwatora sieciowego według projektu. Firma Microsoft zaleca, aby niechciane przychodzące ruchu internetowego być jawnie zablokowane z siecią sieciowych.

**Niepoprawna liczba bajtów i pakietów dla przepływów bezstanowych:** [Sieciowe grupy zabezpieczeń (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) są implementowane jako [zapora stanowa](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true). Jednak wiele reguł domyślnych/wewnętrznych, które kontrolują przepływ ruchu są implementowane w sposób bezstanowy. Ze względu na ograniczenia platformy liczby bajtów i pakietów nie są rejestrowane dla przepływów bezstanowych (czyli przepływów ruchu przechodzących przez reguły bezstanowe), są rejestrowane tylko dla przepływów stanowych. W związku z tym liczba bajtów i pakietów zgłoszonych w dziennikach przepływu nsg (i analizy ruchu) może się różnić od rzeczywistych przepływów. Ograniczenie to ma zostać ustalone do czerwca 2020 r.

## <a name="sample-log-records"></a>Przykładowe rekordy dziennika

Poniższy tekst jest przykładem dziennika przepływu. Jak widać, istnieje wiele rekordów, które są zgodne z listą właściwości opisaną w poprzedniej sekcji.


> [!NOTE]
> Wartości we właściwości **flowTuples* są listą oddzieloną przecinkami.
 
### <a name="version-1-nsg-flow-log-format-sample"></a>Przykład formatu dziennika przepływu nsg w wersji 1
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
### <a name="version-2-nsg-flow-log-format-sample"></a>Przykład formatu dziennika przepływu nsg w wersji 2
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

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak włączyć dzienniki przepływu, zobacz [Włączanie rejestrowania przepływu nsg](network-watcher-nsg-flow-logging-portal.md).
- Aby dowiedzieć się, jak odczytać dzienniki przepływu, zobacz [Odczyt dzienników przepływu nsg](network-watcher-read-nsg-flow-logs.md).
- Aby dowiedzieć się więcej o rejestrowaniu sieci NSG, zobacz [Dzienniki usługi Azure Monitor dla sieciowych grup zabezpieczeń .](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json)
- Aby ustalić, czy ruch jest dozwolony, czy zabroniony do maszyny wirtualnej, zobacz Diagnozowanie problemu z [filtrem ruchu sieciowego maszyny Wirtualnej](diagnose-vm-network-traffic-filtering-problem.md)
