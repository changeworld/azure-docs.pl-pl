---
title: Wprowadzenie do rejestrowania przepływu dla sieciowych grup zabezpieczeń
titleSuffix: Azure Network Watcher
description: W tym artykule wyjaśniono, jak korzystać z funkcji sieciowej grupy zabezpieczeń Logs dzienników przepływu pracy w usłudze Azure Network Watcher.
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
ms.openlocfilehash: 509545443bc08e8613d5f7a9ba7f33d2a90684b8
ms.sourcegitcommit: 8b37091efe8c575467e56ece4d3f805ea2707a64
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75830517"
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Wprowadzenie do rejestrowania przepływu dla sieciowych grup zabezpieczeń

Dzienniki przepływów sieciowych grup zabezpieczeń to funkcja usługi Network Watcher, która umożliwia wyświetlanie informacji dotyczących ruchu IP przychodzącego i wychodzącego przez sieciową grupę zabezpieczeń. Dzienniki przepływów są zapisywane w formacie JSON i przedstawiają przepływy wychodzące i przychodzące dla każdej reguły, interfejs sieciowy, którego dotyczy przepływ, 5-krotkowe informacje o przepływie (źródłowy/docelowy adres IP, port źródłowy/docelowy i protokół), informację o zezwoleniu na ruch albo jego zablokowaniu, a w wersji 2 również informacje o przepływności.


![Dzienniki przepływów — Omówienie](./media/network-watcher-nsg-flow-logging-overview/figure1.png)

Podczas gdy dzienniki przepływów są docelowe sieciowych grup zabezpieczeń, nie są wyświetlane w taki sam sposób, jak w przypadku innych dzienników. Dzienniki przepływów są przechowywane tylko w ramach konta magazynu i są zgodne ze ścieżką rejestrowania pokazaną w poniższym przykładzie:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```
Można analizować dzienniki przepływu i uzyskiwać wgląd w ruch sieciowy przy użyciu [analizy ruchu](traffic-analytics.md).

Te same zasady przechowywania, które są widoczne dla innych dzienników, mają zastosowanie do dzienników przepływów. Zasady przechowywania dzienników można ustawić od 1 dnia do 365 dni. Jeśli zasady przechowywania nie są ustawione, dzienniki będą obsługiwane przez czas nieokreślony.

> [!NOTE] 
> Korzystanie z funkcji zasad przechowywania z rejestrowaniem przepływu sieciowej grupy zabezpieczeń może skutkować dużą ilością operacji magazynu i powiązanymi kosztami. Jeśli nie jest wymagana funkcja zasad przechowywania, zalecamy ustawienie wartości 0.


## <a name="log-file"></a>Plik dziennika

Dzienniki przepływów obejmują następujące właściwości:

* **czas,** w którym zdarzenie zostało zarejestrowane
* **systemId** — identyfikator zasobu grupy zabezpieczeń sieci.
* **Kategoria** — Kategoria zdarzenia. Kategoria jest zawsze **NetworkSecurityGroupFlowEvent**
* **ResourceID** — identyfikator zasobu sieciowej grupy zabezpieczeń
* **OperationName** — zawsze NetworkSecurityGroupFlowEvents
* **Właściwości** — kolekcja właściwości przepływu
    * Numer **wersji w** schemacie zdarzeń dziennika przepływu
    * **przepływy** — kolekcja przepływów. Ta właściwość ma wiele wpisów dla różnych reguł
        * **reguła** — reguła, dla której są wyświetlane przepływy
            * **przepływy** — kolekcja przepływów
                * **Mac** — adres MAC karty sieciowej dla maszyny wirtualnej, w której zebrano przepływ
                * **flowTuples** — ciąg zawierający wiele właściwości krotki przepływu w formacie rozdzielonym przecinkami
                    * **Sygnatura czasowa** — ta wartość jest sygnaturą czasową, gdy przepływ wystąpił w formacie epoki systemu UNIX
                    * **Źródłowy adres IP** — źródłowy adres IP
                    * **Docelowy adres IP** — docelowy adres IP
                    * **Port źródłowy** — port źródłowy
                    * **Port docelowy** — port docelowy
                    * **Protokół** — protokół przepływu. Prawidłowe wartości to **T** dla protokołów TCP i **U** dla protokołu UDP
                    * **Przepływ ruchu** — kierunek przepływu ruchu. Prawidłowe **wartości to dla** ruchu przychodzącego i **o** dla ruchu wychodzącego.
                    * **Decyzja o ruchu** — czy ruch był dozwolony lub zabroniony. Prawidłowe **wartości to dozwolone** i **D** na odmowa.
                    * **Stan przepływu — tylko wersja 2** — przechwytuje stan przepływu. Możliwe stany to **B**: początek — gdy zostanie utworzony przepływ. Statystyki nie są podawane. **C**: kontynuacja — dotyczy ciągłego przepływu. Statystyki są podawane w 5-minutowych odstępach. **E**: koniec — gdy przepływ zostanie zakończony. Statystyki są podawane.
                    * **Pakiety-tylko do miejsca docelowego — wersja 2** Całkowita liczba pakietów TCP lub UDP wysłanych ze źródła do miejsca docelowego od momentu ostatniej aktualizacji.
                    * **Wysłane bajty — tylko źródło do wersji 2** Całkowita liczba bajtów pakietów TCP lub UDP wysłanych z lokalizacji źródłowej do docelowej od ostatniej aktualizacji. Liczba bajtów pakietu obejmuje nagłówek i ładunek pakietu.
                    * **Pakiety — tylko wersja docelowa do wersji 2** Całkowita liczba pakietów TCP lub UDP wysłanych z lokalizacji docelowej do źródła od momentu ostatniej aktualizacji.
                    * **Bajty wysłane — miejsce docelowe do wersji 2** Całkowita liczba bajtów pakietów TCP i UDP wysłanych z lokalizacji docelowej do źródła od ostatniej aktualizacji. Liczba bajtów pakietu obejmuje nagłówek i ładunek pakietu.

## <a name="nsg-flow-logs-version-2"></a>Dzienniki przepływu sieciowej grupy zabezpieczeń w wersji 2

W wersji 2 dzienników przedstawiono stan przepływu. Można skonfigurować, która wersja odbieranych dzienników przepływu. Aby dowiedzieć się, jak włączyć dzienniki przepływów, zobacz [Włączanie rejestrowania przepływu sieciowej grupy zabezpieczeń](network-watcher-nsg-flow-logging-portal.md).

Stan przepływu *B* jest rejestrowany po zainicjowaniu przepływu. Stan przepływu *C* i stan przepływu *E* są stanami, które oznaczają kontynuację przepływu i zakończenie przepływu. Stany *C* i *E* zawierają informacje o przepustowości ruchu.

**Przykład**: przepływaj kolekcje z konwersacji TCP między 185.170.185.105:35370 i 10.2.0.4:23:

"1493763938, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, B,,,," "1493695838, 185.170.185.105, 10.2.0.4, 35370, 23, T, I, A, C, 1021, 588096, 8005, 4610880" "1493696138, 185.170.185.105, 10.2.0.4,

W przypadku Stanów kontynuacja *C* i End *E* , liczba bajtów i pakietów to liczba zagregowana od czasu poprzedniego rekordu krotki przepływu. Odwołując się do poprzedniej przykładowej konwersacji, Łączna liczba przesłanych pakietów to 1021 + 52 + 8005 + 47 = 9125. Łączna liczba przesłanych bajtów to 588096 + 29952 + 4610880 + 27072 = 5256000.

Poniższy tekst jest przykładem dziennika przepływu. Jak widać, istnieje wiele rekordów, które są zgodne z listą właściwości opisaną w poprzedniej sekcji.

## <a name="nsg-flow-logging-considerations"></a>Zagadnienia dotyczące rejestrowania przepływu sieciowej grupy zabezpieczeń

**Zagadnienia dotyczące konta magazynu**: 

- Lokalizacja: używane konto magazynu musi znajdować się w tym samym regionie co sieciowej grupy zabezpieczeń.
- Samozarządzanie rotacją kluczy: Jeśli zmienisz/przeniesiesz klucze dostępu do konta magazynu, dzienniki przepływu sieciowej grupy zabezpieczeń przestaną działać. Aby rozwiązać ten problem, należy wyłączyć i ponownie włączyć dzienniki przepływu sieciowej grupy zabezpieczeń.

**Włącz rejestrowanie przepływu sieciowej grupy zabezpieczeń na wszystkich sieciowych grup zabezpieczeńach dołączonych do zasobu**: rejestrowanie przepływu na platformie Azure jest konfigurowane na zasobie sieciowej grupy zabezpieczeń. Przepływ zostanie skojarzony tylko z jedną regułą sieciowej grupy zabezpieczeń. W scenariuszach, w których jest używany wiele sieciowych grup zabezpieczeń, zalecamy włączenie rejestrowania przepływów sieciowej grupy zabezpieczeń na wszystkich sieciowych grup zabezpieczeń zastosowały podsieć lub interfejs sieciowy zasobu, aby upewnić się, że cały ruch jest zarejestrowany. Aby uzyskać więcej informacji, zobacz [jak oceniany jest ruch](../virtual-network/security-overview.md#how-traffic-is-evaluated) w sieciowych grupach zabezpieczeń.

**Koszty rejestrowania**w usłudze Flow: w przypadku rejestrowania przepływu sieciowej grupy zabezpieczeń są naliczane opłaty za ilość generowanych dzienników. Duże natężenie ruchu może skutkować dużym woluminem dziennika przepływu i powiązanymi kosztami. Cennik dziennika przepływu sieciowej grupy zabezpieczeń nie obejmuje podstawowych kosztów magazynu. Korzystanie z funkcji zasad przechowywania z rejestrowaniem przepływu sieciowej grupy zabezpieczeń może skutkować dużą ilością operacji magazynu i powiązanymi kosztami. Jeśli nie jest wymagana funkcja zasad przechowywania, zalecamy ustawienie wartości 0. Aby uzyskać więcej informacji, zobacz [cennik Network Watcher](https://azure.microsoft.com/pricing/details/network-watcher/) i [Cennik usługi Azure Storage](https://azure.microsoft.com/pricing/details/storage/) , aby uzyskać dodatkowe informacje.

**Przepływy przychodzące zarejestrowane z Internetu adresów IP na maszynach wirtualnych bez publicznych**adresach IP: maszyny wirtualne, które nie mają publicznego adresu do sieci, są przypisane za pośrednictwem publicznego adresu IP SKOJARZONEGO z kartą sieciową jako publiczny adres IPv4 na poziomie wystąpienia lub które są częścią puli zaplecza usługi równoważenia obciążenia, użyj [domyślnego](../load-balancer/load-balancer-outbound-connections.md#defaultsnat) , a także adresu IP przypisanego przez platformę Azure. W związku z tym mogą pojawić się wpisy dziennika przepływu dla przepływów z internetowych adresów IP, jeśli przepływ jest przeznaczony do portu w zakresie portów przypisanych do tego elementu. Mimo że platforma Azure nie zezwala na te przepływy na maszynę wirtualną, próba zostanie zarejestrowana i zostanie wyświetlona Network Watcher w dzienniku przepływu sieciowej grupy zabezpieczeń przez zaprojektowanie. Zalecamy, aby niepożądane przychodzące ruch internetowy został jawnie zablokowany przy użyciu sieciowej grupy zabezpieczeń.

**Niepoprawna liczba bajtów i pakietów dla przepływów bezstanowych**: [grupy zabezpieczeń sieci (sieciowych grup zabezpieczeń)](https://docs.microsoft.com/azure/virtual-network/security-overview) są implementowane jako [Zapora stanowa](https://en.wikipedia.org/wiki/Stateful_firewall?oldformat=true). Jednak wiele reguł domyślnych/wewnętrznych kontrolujących przepływ ruchu jest implementowane w sposób bezstanowy. Ze względu na ograniczenia platformy liczby bajtów i pakietów nie są rejestrowane dla bezstanowych przepływów (to oznacza, że ruch przechodzi przez reguły bezstanowe), są rejestrowane tylko dla przepływów stanowych. W związku z tym liczba bajtów i pakietów raportowanych w dziennikach przepływu sieciowej grupy zabezpieczeń (i Analiza ruchu) może różnić się od rzeczywistych przepływów. To ograniczenie jest planowane do ustalenia w czerwcu 2020.

## <a name="sample-log-records"></a>Przykładowe rekordy dziennika

Poniższy tekst jest przykładem dziennika przepływu. Jak widać, istnieje wiele rekordów, które są zgodne z listą właściwości opisaną w poprzedniej sekcji.


> [!NOTE]
> Wartości we właściwości **flowTuples* są rozdzielaną przecinkami listą.
 
### <a name="version-1-nsg-flow-log-format-sample"></a>Przykład formatu dziennika przepływu sieciowej grupy zabezpieczeń w wersji 1
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
### <a name="version-2-nsg-flow-log-format-sample"></a>Przykład formatu dziennika przepływu sieciowej grupy zabezpieczeń w wersji 2
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

- Aby dowiedzieć się, jak włączyć dzienniki przepływów, zobacz [Włączanie rejestrowania przepływu sieciowej grupy zabezpieczeń](network-watcher-nsg-flow-logging-portal.md).
- Aby dowiedzieć się, jak czytać dzienniki przepływów, zobacz [odczytywanie dzienników przepływu sieciowej grupy zabezpieczeń](network-watcher-read-nsg-flow-logs.md).
- Aby dowiedzieć się więcej o rejestrowaniu sieciowej grupy zabezpieczeń, zobacz [dzienniki Azure monitor dla sieciowych grup zabezpieczeń (sieciowych grup zabezpieczeń)](../virtual-network/virtual-network-nsg-manage-log.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json).
- Aby określić, czy ruch jest dozwolony, czy odrzucany do lub z maszyny wirtualnej, zobacz [diagnozowanie problemu z filtrem ruchu sieciowego maszyny wirtualnej](diagnose-vm-network-traffic-filtering-problem.md)
