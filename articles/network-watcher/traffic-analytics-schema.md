---
title: Schemat analizy ruchu platformy Azure | Dokumentacja firmy Microsoft
description: Informacje o schemacie analiza ruchu w celu analizowania dzienników przepływu grupy zabezpieczeń sieci platformy Azure.
services: network-watcher
documentationcenter: na
author: vinynigam
manager: agummadi
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/26/2019
ms.author: vinigam
ms.openlocfilehash: 491f19abfd87c28ede45e98a24f31fe7e599b18b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2019
ms.locfileid: "64691413"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Agregacja schematu i danych w analizy ruchu

Analiza ruchu jest oparta na chmurze rozwiązania, który zapewnia wgląd w aktywność użytkownika i aplikacji w sieciach w chmurze. Analiza ruchu analizuje usługi Network Watcher dzienniki sieciowych grup zabezpieczeń (NSG) przepływu udostępnienie szczegółowych informacji na przepływ ruchu w Twojej chmurze platformy Azure. Za pomocą analizy ruchu możesz wykonywać następujące czynności:

- Wizualizowanie działań sieciowych wszystkich subskrypcji platformy Azure i identyfikacji punktów aktywnych.
- Identyfikuj zagrożenia bezpieczeństwa dla i zabezpieczyć swoją sieć, za pomocą informacje, takie jak otwierać porty, aplikacji, próba dostępu do Internetu i maszyn wirtualnych (VM) nawiązywania połączenia z sieciami nieautoryzowane.
- Zrozumieć wzorce przepływu ruchu między regionami platformy Azure a Internetem, aby zoptymalizować wdrożenie sieci, wydajność i pojemność.
- Wykrywanie błędów konfiguracji sieci, co prowadzi do połączenia zakończone niepowodzeniem w sieci.
- Wiadomo, użycie sieci, w bajtach, pakietów lub przepływy.

### <a name="data-aggregation"></a>Agregacja danych

1. Wszystkie dzienniki przepływu w lokalizacji sieciowej grupy zabezpieczeń między "FlowIntervalStartTime_t" i "FlowIntervalEndTime_t" są przechwytywane w odstępach jednej minuty na koncie magazynu jako obiekty BLOB, mogła zostać przetworzona przez analizę ruchu. 
2. Przetwarzanie analizy ruchu odbywa się domyślnie 60 minut. Oznacza to, że co 60 minut, w których analiza ruchu wybiera obiekty BLOB z magazynu na potrzeby agregacji.
3. Przepływy, które mają ten sam źródłowy adres IP, docelowy adres IP, port docelowy, nazwa sieciowej grupy zabezpieczeń, reguła sieciowej grupy zabezpieczeń, kierunek przepływu i Transport layer protocol (TCP lub UDP) (Uwaga: Port źródłowy jest wyłączone dla agregacji) jest uwzględniona w jednej usłudze flow za analizę ruchu
4. Ten pojedynczy rekord jest ozdobione (szczegóły w dalszej części tego artykułu) i odebrane w dzienniku analizy ruchu Analytics.This proces może potrwać maksymalnie 1 godzinę max.
5. Pole FlowStartTime_t wskazuje pierwszego wystąpienia takich zagregowane przepływu (tego samego czterech podwójny) w dzienniku przepływ przetwarzania interwał między "FlowIntervalStartTime_t" i "FlowIntervalEndTime_t". 
6. Dla dowolnego zasobu w TA przepływów wskazane w interfejsie użytkownika są łączna liczba przepływów widzianych przez sieciową grupę zabezpieczeń, ale w dzienniku Anlaytics użytkownik będzie widział tylko jednego, zmniejszone rekord. Aby wyświetlić wszystkie przepływy, użyj pola blob_id, które mogą być przywoływane z magazynu. Ogólny przepływ uznawane za, że rekord będzie odnosić się do poszczególnych przepływów, które występuje w obiekcie blob.


### <a name="fields-used-in-traffic-analytics-schema"></a>Pola używane w schemacie analizy ruchu

Analiza ruchu bazuje na usłudze Log Analytics, aby można było uruchomić niestandardowe zapytania na danych dekorowane za analizę ruchu i Ustawiaj alerty na tym samym.

Poniżej wymieniono pól w schemacie i ich oznaczającego

| Pole | Format | Komentarze | 
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Tabela danych Anlaytics ruchu
| SubType_s | FlowLog | Podtyp dzienników przepływu |
| FASchemaVersion_s |   1   | Wersja Scehma. Nie będzie odzwierciedlał wersji dzienników przepływu sieciowych grup zabezpieczeń |
| TimeProcessed_t   | Data i godzina w formacie UTC  | Czas przetwarzania analizy ruchu i dzienników przepływów pierwotnych z konta magazynu |
| FlowIntervalStartTime_t | Data i godzina w formacie UTC |  Godzina rozpoczęcia interwału przepływ przetwarzania dziennika. Jest to czas, z której jest mierzona interwał przepływu |
| FlowIntervalEndTime_t | Data i godzina w formacie UTC | Interwał przetwarzania dziennika przepływu godzina zakończenia |
| FlowStartTime_t | Data i godzina w formacie UTC |  Pierwsze wystąpienie przepływu, (które zostaną Pobierz zagregowane) w zakresie przetwarzania dziennika przepływ między "FlowIntervalStartTime_t" i "FlowIntervalEndTime_t". Ten przepływ pobiera agregowane w oparciu o logikę agregacji |
| FlowEndTime_t | Data i godzina w formacie UTC | Ostatnie wystąpienie przepływu, (które zostaną Pobierz zagregowane) w zakresie przetwarzania dziennika przepływ między "FlowIntervalStartTime_t" i "FlowIntervalEndTime_t". Pod względem v2 dziennika przepływu to pole zawiera godzina rozpoczęcia (oznaczone jako "B" w rekordzie pierwotne przepływu) ostatniego przepływu przy użyciu tego samego krotki czterech |
| FlowType_s |  * IntraVNet <br> * Między sieciami wirtualnymi <br> * S2S <br> * P2S <br> * AzurePublic <br> * ExternalPublic <br> * MaliciousFlow <br> * Nieznany prywatne <br> * Nieznany | Definicja w uwagi pod tabelą |
| SrcIP_s | Źródłowy adres IP | Jest puste w przypadku AzurePublic i ExternalPublic przepływów |
| DestIP_s | Docelowy adres IP | Jest puste w przypadku AzurePublic i ExternalPublic przepływów |
| VMIP_s | Adresu IP maszyny Wirtualnej | Używany do AzurePublic i ExternalPublic przepływów |
| PublicIP_s | Publiczne adresy IP | Używany do AzurePublic i ExternalPublic przepływów |
| DestPort_d | Port docelowy | Port ruchu przychodzącego | 
| L4Protocol_s  | * T <br> * U  | Protokół transportu. T = TCP <br> U = UDP | 
| L7Protocol_s  | Nazwa protokołu | Pochodną port docelowy |
| FlowDirection_s | * I = dla ruchu przychodzącego<br> * O = ruchu wychodzącego | Kierunek przepływu/poza sieciowej grupy zabezpieczeń, zgodnie z dzienników przepływu | 
| FlowStatus_s  | * = Dozwolone przez reguły sieciowej grupy zabezpieczeń <br> * D = odrzucany przez regułę sieciowej grupy zabezpieczeń  | Stan przepływu dozwolone/nblocked przez sieciową grupę zabezpieczeń dla każdego dziennika przepływu |
| NSGList_s | \<SUBSCRIPTIONID>\/<RESOURCEGROUP_NAME>\/<NSG_NAME> | Sieciową grupę zabezpieczeń (NSG) skojarzoną z przepływem |
| NSGRules_s | \<Indeks wartości 0) >< NSG_RULENAME >\<kierunek przepływu >\<stan przepływu >\<FlowCount ProcessedByRule > |  Reguła sieciowej grupy zabezpieczeń, która może wchodzić lub wychodzić ten przepływ |
| NSGRuleType_s | * Zdefiniowane przez użytkownika * domyślne |   Typ reguły sieciowej grupy zabezpieczeń, używane przez przepływ |
| MACAddress_s | Adres MAC | Adres MAC karty sieciowej, w którym przepływ został przechwycony |
| Subscription_s | Subskrypcja sieci wirtualnej platformy Azure / interfejs sieciowy / maszyna wirtualna jest wypełniana w tym polu | Dotyczy tylko dla przepływu = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow i UnknownPrivate typy przepływów (typy przepływów tylko po jednej stronie w przypadku platformy azure) |
| Subscription1_s | Identyfikator subskrypcji | Identyfikator subskrypcji sieci wirtualnej / interfejs sieciowy / maszyny wirtualnej, do której należy źródłowy adres IP w usłudze flow |
| Subscription2_s | Identyfikator subskrypcji | Identyfikator subskrypcji sieci wirtualnej / interfejs sieciowy / maszyny wirtualnej, do którego docelowy adres IP w przepływie należy do |
| Region_s | Region platformy Azure w sieci wirtualnej / interfejs sieciowy / maszyny wirtualnej, do której należy adres IP w przepływie | Dotyczy tylko dla przepływu = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow i UnknownPrivate typy przepływów (typy przepływów tylko po jednej stronie w przypadku platformy azure) |
| Region1_s | Region Azure | Region platformy Azure w sieci wirtualnej / interfejs sieciowy / maszyny wirtualnej, do której należy źródłowy adres IP w usłudze flow |
| Region2_s | Region Azure | Sieć wirtualna, do którego docelowy adres IP w przepływie należy do regionu platformy Azure |
| NIC_s | \<resourcegroup_Name>\/\<NetworkInterfaceName> |  Karta sieciowa skojarzona z maszyną Wirtualną, wysyłania i odbierania ruchu |
| NIC1_s | <resourcegroup_Name>/\<NetworkInterfaceName> | Karta sieciowa skojarzona źródłowy adres IP w usłudze flow |
| NIC2_s | <resourcegroup_Name>/\<NetworkInterfaceName> | Karta sieciowa skojarzona docelowy adres IP w usłudze flow |
| VM_s | <resourcegroup_Name>\/\<NetworkInterfaceName> | Maszyna wirtualna skojarzona z interfejsem sieciowym NIC_s |
| VM1_s | < resourcegroup_Name > /\<VirtualMachineName > | Maszyna wirtualna skojarzona z źródłowy adres IP w usłudze flow |
| VM2_s | < resourcegroup_Name > /\<VirtualMachineName > | Maszyna wirtualna skojarzona z docelowy adres IP w usłudze flow |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Podsieć skojarzona z NIC_s |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName> | Podsieć skojarzona z źródłowy adres IP w usłudze flow |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<SubnetName>    | Podsieć skojarzona z docelowy adres IP w usłudze flow |
| ApplicationGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Usługa Application gateway skojarzone z źródłowy adres IP w usłudze flow | 
| ApplicationGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<ApplicationGatewayName> | Usługa Application gateway skojarzone z docelowy adres IP w usłudze flow |
| LoadBalancer1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Źródłowy adres IP w przepływie skojarzonego modułu równoważenia obciążenia |
| LoadBalancer2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LoadBalancerName> | Docelowy adres IP w przepływie skojarzonego modułu równoważenia obciążenia |
| LocalNetworkGateway1_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Brama sieci lokalnej, skojarzone z źródłowy adres IP w usłudze flow |
| LocalNetworkGateway2_s | \<SubscriptionID>/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Brama sieci lokalnej, skojarzone z docelowy adres IP w usłudze flow |
| ConnectionType_s | Możliwe wartości to VNetPeering, bramy VpnGateway i ExpressRoute |    Typ połączenia |
| ConnectionName_s | \<SubscriptionID>/\<ResourceGroupName>/\<ConnectionName> | Nazwa połączenia |
| ConnectingVNets_s | Rozdzieloną spacjami listę nazw sieci wirtualnej | W przypadku topologii gwiazdy sieci wirtualne centrum zostanie wypełniony w tym miejscu |
| Country_s | Dwuliterowa numer kierunkowy kraju (ISO, alfa 3166-1-2) | Wypełnione dla typu przepływu ExternalPublic. Wszystkie adresy IP w polu PublicIPs_s współużytkują ten sam kod kraju |
| AzureRegion_s | Lokalizacje w regionie platformy Azure | Wypełnione dla typu przepływu AzurePublic. Wszystkie adresy IP w polu PublicIPs_s współużytkują region platformy Azure |
| AllowedInFlows_d | | Liczba przepływów ruchu przychodzącego, które były dozwolone. Jest to liczba przepływów, udostępnione w tym samym krotki czterech dla ruchu przychodzącego do interfejsu netweork przechwyconych przepływu | 
| DeniedInFlows_d |  | Liczba przepływów ruchu przychodzącego, które zostały odrzucone. (Przychodzące z interfejsem sieciowym, w którym przepływ został przechwycony) |
| AllowedOutFlows_d | | Liczba przepływów wychodzących, które były dozwolone (ruchu wychodzącego z interfejsem sieciowym, w którym przepływ został przechwycony) |
| DeniedOutFlows_d  | | Liczba przepływów ruchu wychodzącego, które zostały odrzucone (ruchu wychodzącego z interfejsem sieciowym, w którym przepływ został przechwycony) |
| FlowCount_d | Przestarzałe. Łączna liczba przepływów, pasujących do tego samego krotki cztery. W przypadku typów przepływu ExternalPublic i AzurePublic liczba będzie zawierać przepływy z także różne adresy publicznego adresu IP.
| InboundPackets_d | Odebrane pakiety określonymi w interfejsie sieciowym, w którym została zastosowana reguła sieciowej grupy zabezpieczeń | To jest wypełniana tylko w przypadku schemat dziennika przepływu w wersji 2 z sieciowych grup zabezpieczeń |
| OutboundPackets_d  | Pakiety wysyłane, gdy przechwyconych w interfejsie sieciowym, w której została zastosowana reguła sieciowej grupy zabezpieczeń | To jest wypełniana tylko w przypadku schemat dziennika przepływu w wersji 2 z sieciowych grup zabezpieczeń |
| InboundBytes_d |  Bajtów odebranych w trakcie przechwyconych w interfejsie sieciowym, w której została zastosowana reguła sieciowej grupy zabezpieczeń | To jest wypełniana tylko w przypadku schemat dziennika przepływu w wersji 2 z sieciowych grup zabezpieczeń |
| OutboundBytes_d | Bajty wysłane jako przechwyconych w interfejsie sieciowym, w której została zastosowana reguła sieciowej grupy zabezpieczeń | To jest wypełniana tylko w przypadku schemat dziennika przepływu w wersji 2 z sieciowych grup zabezpieczeń |
| CompletedFlows_d  |  | Wypełniony wartość różna od zera tylko dla schematu dziennika przepływu w wersji 2 z sieciowych grup zabezpieczeń |
| PublicIPs_s | <PUBLIC_IP>\|\<FLOW_STARTED_COUNT>\|\<FLOW_ENDED_COUNT>\|\<OUTBOUND_PACKETS>\|\<INBOUND_PACKETS>\|\<OUTBOUND_BYTES>\|\<INBOUND_BYTES> | Wpisy oddzielone paski |
    
### <a name="notes"></a>Uwagi
    
1. W przypadku przepływów AzurePublic i ExternalPublic klient należące do adresów IP maszyn wirtualnych platformy Azure jest wypełniana w polu VMIP_s, gdy trwa wypełnianie listy publicznych adresów IP w polu PublicIPs_s. Dla tych typów dwóch przepływ powinien używamy VMIP_s i PublicIPs_s zamiast pól SrcIP_s i DestIP_s. Dla adresów AzurePublic i ExternalPublicIP firma Microsoft agregacji dodatkowo tak, aby liczba rekordów pozyskane do obszaru roboczego analizy dzienników klienta jest minimalny. (W tym polu wkrótce staną się przestarzałe i powinny być używamy SrcIP_ i DestIP_s w zależności od tego, czy maszyna wirtualna platformy azure był źródło lub miejsce docelowe w przepływie) 
1. Szczegóły typów przepływu: Oparte na adresach IP zaangażowane w przepływ, firma Microsoft kategoryzowanie przepływów w następujących przepływów: 
1. IntraVNet — adresy IP w usłudze flow znajdują się w tej samej sieci wirtualnej platformy Azure. 
1. Między sieciami wirtualnymi — adresy IP w usłudze flow znajdują się w dwóch różnych sieciach wirtualnych platformy Azure. 
1. S2S — (lokacja) jeden z adresów IP należy do sieci wirtualnej platformy Azure, gdy adres IP należy do sieci klienta (witryna) podłączone do sieci wirtualnej platformy Azure za pośrednictwem bramy sieci VPN lub usługi Expressroute. 
1. P2S - (punkt lokacja) jeden z adresów IP należy do usługi Azure Virtual Network, gdy adres IP należy do sieci klienta (witryna) podłączone do sieci wirtualnej platformy Azure za pośrednictwem bramy sieci VPN.
1. AzurePublic — jeden adresów IP nie należy do sieci wirtualnej platformy Azure podczas, gdy adres IP należy do usługi Azure wewnętrznego publiczne adresy IP należące do firmy Microsoft. Należące do klientów, publiczne adresy IP nie będzie należeć do tego typu przepływu. Na przykład maszyny Wirtualnej, ruch wysyłany do usługi platformy Azure (punkt końcowy magazynu) będzie dzielony na kategorie w ramach tego typu przepływu należące do każdego klienta. 
1. ExternalPublic — jeden z adresów IP nie należy do usługi Azure Virtual Network adres IP jest publiczny adres IP, który nie jest na platformie Azure, nie został zgłoszony jako złośliwe w kanałach informacyjnych ASC, które Traffic Analytics wykorzystuje interwał przetwarzania między " FlowIntervalStartTime_t"i"FlowIntervalEndTime_t". 
1. MaliciousFlow — która jeden z adresów IP należą do sieci wirtualnej platformy azure w adres IP jest publiczny adres IP, który nie znajduje się w usłudze Azure i jest zgłaszana jako złośliwe w kanałach informacyjnych ASC, które Traffic Analytics wykorzystuje interwał przetwarzania między" FlowIntervalStartTime_t"i"FlowIntervalEndTime_t". 
1. UnknownPrivate — która jeden z adresów IP należą do sieci wirtualnej platformy Azure w adres IP należy do zakresu prywatnych adresów IP, zgodnie z definicją w dokumencie RFC 1918 i nie można zamapować za analizę ruchu należących do klienta lokacji lub w usłudze Azure Virtual Network.
1. Nieznany — nie można zamapować albo adresu IP adresów w przepływy z topologią klienta na platformie Azure oraz w środowisku lokalnym (lokacja).
1. Niektóre nazwy pól są dołączane za pomocą _s lub _d. Te nie oznaczają, źródłowe i docelowe.

### <a name="next-steps"></a>Następne kroki
Aby uzyskać odpowiedzi na często zadawane pytania, zobacz [Traffic analytics — często zadawane pytania](traffic-analytics-faq.md) Aby wyświetlić szczegółowe informacje o funkcjach, zobacz [dokumentacji analizy ruchu](traffic-analytics.md)
    


    


