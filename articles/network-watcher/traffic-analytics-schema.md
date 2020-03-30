---
title: Schemat analizy ruchu na platformie Azure | Dokumenty firmy Microsoft
description: Zrozumienie schematu usługi Traffic Analytics do analizowania dzienników przepływu grupy zabezpieczeń sieci platformy Azure.
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
ms.openlocfilehash: ccfbb92c27e4508595f19c2ea6900730cde609b9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74666379"
---
# <a name="schema-and-data-aggregation-in-traffic-analytics"></a>Schemat i agregacja danych w analizie ruchu

Usługa Traffic Analytics to rozwiązanie oparte na chmurze, które zapewnia wgląd w aktywność użytkowników i aplikacji w sieciach w chmurze. Usługa Traffic Analytics analizuje dzienniki przepływu sieciowej grupy zabezpieczeń obserwatora sieci (Network Watcher), aby zapewnić wgląd w przepływ ruchu w chmurze platformy Azure. Dzięki analizie ruchu drogowego możesz:

- Wizualizuj aktywność sieciową w ramach subskrypcji platformy Azure i identyfikuj punkty wyszukiwania.
- Identyfikowanie zagrożeń bezpieczeństwa i zabezpieczanie sieci za pomocą informacji, takich jak otwarte porty, aplikacje próbujące uzyskać dostęp do Internetu i maszyny wirtualne (VM) łączące się z nieautoryzowanymi sieciami.
- Poznaj wzorce przepływu ruchu w regionach platformy Azure i w Internecie, aby zoptymalizować wdrożenie sieci pod kątem wydajności i pojemności.
- Należy wskazać błędy konfiguracji sieci prowadzące do nieudanych połączeń w sieci.
- Poznaj użycie sieci w bajtach, pakietach lub przepływach.

### <a name="data-aggregation"></a>Agregacja danych

1. Wszystkie dzienniki przepływu w nsg między "FlowIntervalStartTime_t" i "FlowIntervalEndTime_t" są przechwytywane w odstępach jednominutowych na koncie magazynu jako obiekty blob przed przetworzeniem przez usługi Traffic Analytics.
2. Domyślny interwał przetwarzania usługi Traffic Analytics wynosi 60 minut. Oznacza to, że co 60 minut usługa Traffic Analytics wybiera obiekty BLOB z magazynu do agregacji. Jeśli wybrany interwał przetwarzania wynosi 10 minut, usługa Traffic Analytics będzie wybierać obiekty BLOB z konta magazynu po co 10 minutach.
3. Przepływy, które mają ten sam źródłowy adres IP, docelowy adres IP, port docelowy, nazwę sieciowej grupy, regułę sieciowej grupy, kierunek przepływu i protokół warstwy transportu (TCP lub UDP) (uwaga: port źródłowy jest wykluczony do agregacji) są łączona w jednym przepływie przez traffic analytics
4. Ten pojedynczy rekord jest dekorowany (Szczegóły w sekcji poniżej) i pochłonięty w usłudze Log Analytics przez Traffic Analytics.Ten proces może potrwać maksymalnie 1 godzinę.
5. FlowStartTime_t pole wskazuje pierwsze wystąpienie takiego zagregowanego przepływu (tej samej czterościowej) w interwale przetwarzania dziennika przepływu między "FlowIntervalStartTime_t" i "FlowIntervalEndTime_t".
6. Dla każdego zasobu w taborze przepływy wskazane w interfejsie użytkownika są całkowitymi przepływami widzianymi przez grupę NSG, ale użytkownik usługi Log Analytics zobaczy tylko pojedynczy, zmniejszony rekord. Aby wyświetlić wszystkie przepływy, użyj pola blob_id, do którego można się odwoływać z magazynu. Całkowita liczba przepływów dla tego rekordu będzie zgodna z poszczególnymi przepływami widzianymi w obiekcie blob.

Poniższa kwerenda pomaga przyjrzeć się wszystkie dzienniki przepływu z lokalnego w ciągu ostatnich 30 dni.
```
AzureNetworkAnalytics_CL
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s  
```
Aby wyświetlić ścieżkę obiektu blob dla przepływów w wyżej wymienionej kwerendzie, użyj poniższej kwerendy:

```
let TableWithBlobId =
(AzureNetworkAnalytics_CL
   | where SubType_s == "Topology" and ResourceType == "NetworkSecurityGroup" and DiscoveryRegion_s == Region_s and IsFlowEnabled_b
   | extend binTime = bin(TimeProcessed_t, 6h),
            nsgId = strcat(Subscription_g, "/", Name_s),
            saNameSplit = split(FlowLogStorageAccount_s, "/")
   | extend saName = iif(arraylength(saNameSplit) == 3, saNameSplit[2], '')
   | distinct nsgId, saName, binTime)
| join kind = rightouter (
   AzureNetworkAnalytics_CL
   | where SubType_s == "FlowLog"  
   | extend binTime = bin(FlowEndTime_t, 6h)
) on binTime, $left.nsgId == $right.NSGList_s  
| extend blobTime = format_datetime(todatetime(FlowIntervalStartTime_t), "yyyy MM dd hh")
| extend nsgComponents = split(toupper(NSGList_s), "/"), dateTimeComponents = split(blobTime, " ")
| extend BlobPath = strcat("https://", saName,
                        "@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/", nsgComponents[0],
                        "/RESOURCEGROUPS/", nsgComponents[1],
                        "/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/", nsgComponents[2],
                        "/y=", dateTimeComponents[0], "/m=", dateTimeComponents[1], "/d=", dateTimeComponents[2], "/h=", dateTimeComponents[3],
                        "/m=00/macAddress=", replace(@"-", "", MACAddress_s),
                        "/PT1H.json")
| project-away nsgId, saName, binTime, blobTime, nsgComponents, dateTimeComponents;

TableWithBlobId
| where SubType_s == "FlowLog" and FlowStartTime_t >= ago(30d) and FlowType_s == "ExternalPublic"
| project Subnet_s , BlobPath
```

Powyższa kwerenda tworzy adres URL, aby uzyskać bezpośredni dostęp do obiektu blob. Adres URL z posiadaczami miejsc znajduje się poniżej:

```
https://{saName}@insights-logs-networksecuritygroupflowevent/resoureId=/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroup}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json

```

### <a name="fields-used-in-traffic-analytics-schema"></a>Pola używane w schemacie analizy ruchu
  > [!IMPORTANT]
  > Schemat analizy ruchu został zaktualizowany 22 sierpnia 2019 r. Nowy schemat zapewnia źródłowe i docelowe adresy IP oddzielnie eliminując konieczność analizowania pola FlowDirection, dzięki czemu kwerendy są prostsze. </br>
  > FASchemaVersion_s zaktualizowany z 1 do 2. </br>
  > Przestarzałe pola: VMIP_s, Subscription_s, Region_s, NSGRules_s, Subnet_s, VM_s, NIC_s, PublicIPs_s, FlowCount_d </br>
  > Nowe pola: SrcPublicIPs_s, DestPublicIPs_s, NSGRule_s </br>
  > Przestarzałe pola będą dostępne do 22 listopada 2019 r.

Usługa Traffic Analytics jest oparta na analizie dzienników, dzięki czemu można uruchamiać niestandardowe zapytania dotyczące danych dekorowanych przez usługi Traffic Analytics i ustawiać alerty na tym samym poziomie.

Poniżej wymieniono pola w schemacie i to, co oznaczają

| Pole | Format | Komentarze |
|:---   |:---    |:---  |
| TableName | AzureNetworkAnalytics_CL | Tabela danych analizy ruchu
| SubType_s | Dziennik przepływu | Podtyp dzienników przepływu. Używaj tylko "FlowLog", inne wartości SubType_s są przeznaczone do wewnętrznego funkcjonowania produktu |
| FASchemaVersion_s |   2   | Wersja schematu. Nie odzwierciedla wersji dziennika przepływu nsg |
| TimeProcessed_t   | Data i godzina w utc  | Czas, w którym usługa Traffic Analytics przetworzyła nieprzetworzone dzienniki przepływu z konta magazynu |
| FlowIntervalStartTime_t | Data i godzina w utc |  Czas rozpoczęcia interwału przetwarzania dziennika przepływu. Jest to czas, od którego mierzony jest interwał przepływu |
| FlowIntervalEndTime_t | Data i godzina w utc | Godzina zakończenia interwału przetwarzania dziennika przepływu |
| FlowStartTime_t | Data i godzina w utc |  Pierwsze wystąpienie przepływu (który zostanie zagregowany) w interwale przetwarzania dziennika przepływu między "FlowIntervalStartTime_t" i "FlowIntervalEndTime_t". Przepływ ten jest agregowany na podstawie logiki agregacji |
| FlowEndTime_t | Data i godzina w utc | Ostatnie wystąpienie przepływu (który zostanie zagregowany) w interwale przetwarzania dziennika przepływu między "FlowIntervalStartTime_t" i "FlowIntervalEndTime_t". Jeśli chodzi o dziennik przepływu v2, to pole zawiera czas, kiedy ostatni przepływ z tej samej czteropletki rozpoczęte (oznaczone jako "B" w rekord przepływu nieprzetworzonego) |
| FlowType_s |  * IntraVNet <br> * InterVNet <br> * S2S <br> * P2S <br> * Platforma AzurePublic <br> * Zewnętrznapubliczna <br> * MaliciousFlow <br> * Nieznany prywatny <br> * Nieznany | Definicja w uwagach poniżej tabeli |
| SrcIP_s | Źródłowy adres IP | Będzie puste w przypadku przepływów publicznych i zewnętrznych azurepublic |
| DestIP_s | Docelowy adres IP | Będzie puste w przypadku przepływów publicznych i zewnętrznych azurepublic |
| VMIP_s | Adres IP maszyny Wirtualnej | Używane dla azurepublic i zewnętrznewypływy publiczne |
| PublicIP_s | Publiczne adresy IP | Używane dla azurepublic i zewnętrznewypływy publiczne |
| DestPort_d | Port docelowy | Port, do którego nasiedmia ruch jest |
| L4Protocol_s  | * T <br> * U  | protokołu transportowego. T = TCP <br> U = UDP |
| L7Protocol_s  | Nazwa protokołu | Pochodzi z portu docelowego |
| FlowDirection_s | * I = przychodzące<br> * O = ruch wychodzący | Kierunek przepływu do/z nsg zgodnie z dziennikiem przepływu |
| FlowStatus_s  | * A = Dozwolone przez regułę NSG <br> * D = Odmowa przez NSG Rule  | Stan przepływu dozwolony/nblokowany przez nsg zgodnie z dziennikiem przepływu |
| NSGList_s | \<>RESOURCEGROUP_NAME \/<RESOURCEGROUP_NAME>\/ RESOURCEGROUP_NAME<NSG_NAME>< | Sieciowa grupa zabezpieczeń (NSG) skojarzona z przepływem |
| NSGRules_s | \<Wartość indeksu 0)>\| \< \| \<NSG_RULENAME>kierunek przepływu>\| \<stan przepływu>\| \<> |  Reguła nsg, która zezwalała lub odmawiała tego przepływu |
| NSGRule_s | NSG_RULENAME |  Reguła nsg, która zezwalała lub odmawiała tego przepływu |
| NSGRuleType_s | * Zdefiniowane przez użytkownika * Domyślne |   Typ reguły nsg używanej przez przepływ |
| MACAddress_s | Adres MAC | Adres MAC karty sieciowej, przy której przepływ został przechwycony |
| Subscription_s | Subskrypcja sieci wirtualnej platformy Azure/ interfejsu sieci/ maszyny wirtualnej jest wypełniona w tym polu | Dotyczy tylko typów przepływu FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow i UnknownPrivate typów przepływu (typy przepływu, gdzie tylko jedna strona jest azure) |
| Subscription1_s | Identyfikator subskrypcji | Identyfikator subskrypcji sieci wirtualnej/ interfejsu sieciowego/ maszyny wirtualnej, do której należy źródłowy adres IP w przepływie |
| Subscription2_s | Identyfikator subskrypcji | Identyfikator subskrypcji sieci wirtualnej/ interfejsu sieciowego/ maszyny wirtualnej, do której należy docelowy adres IP w przepływie |
| Region_s | Region platformy Azure sieci wirtualnej/ interfejsu sieci/ maszyny wirtualnej, do której należy adres IP w przepływie | Dotyczy tylko typów przepływu FlowType = S2S, P2S, AzurePublic, ExternalPublic, MaliciousFlow i UnknownPrivate typów przepływu (typy przepływu, gdzie tylko jedna strona jest azure) |
| Region1_s | Region świadczenia usługi Azure | Region platformy Azure sieci wirtualnej/ interfejsu sieci/ maszyny wirtualnej, do której należy źródłowy adres IP w przepływie |
| Region2_s | Region świadczenia usługi Azure | Region platformy Azure sieci wirtualnej, do którego należy docelowy adres IP w przepływie |
| NIC_s | \<resourcegroup_Name>> \/ \<networkinterfacename resourcegroup_Name |  karta sieciowa skojarzona z maszyną wirtualną wysyłającą lub odbierającą |
| NIC1_s | <> resourcegroup_Name/\<NetworkInterfaceName> | Karta sieciowa skojarzona ze źródłowym adresem IP w przepływie |
| NIC2_s | <> resourcegroup_Name/\<NetworkInterfaceName> | Karta sieciowa skojarzona z docelowym adresem IP w przepływie |
| VM_s | <resourcegroup_Name>\/ \<networkinterfacename> | Maszyna wirtualna skojarzona z interfejsem sieciowym NIC_s |
| VM1_s | <> resourcegroup_Name/\<VirtualMachineName> | Maszyna wirtualna skojarzona ze źródłowym adresem IP w przepływie |
| VM2_s | <> resourcegroup_Name/\<VirtualMachineName> | Maszyna wirtualna skojarzona z docelowym adresem IP w przepływie |
| Subnet_s | <ResourceGroup_Name>/<VNET_Name>/\<Podsieci> | Podsieć skojarzona z NIC_s |
| Subnet1_s | <ResourceGroup_Name>/<VNET_Name>/\<Podsieci> | Podsieć skojarzona z źródłowym adresem IP w przepływie |
| Subnet2_s | <ResourceGroup_Name>/<VNET_Name>/\<Podsieci>    | Podsieć skojarzona z docelowym adresem IP w przepływie |
| ApplicationGateway1_s | \<> subscriptionID/\<ResourceGroupName>/\<ApplicationGatewayName> | Brama aplikacji skojarzona z źródłowym adresem IP w przepływie |
| ApplicationGateway2_s | \<> subscriptionID/\<ResourceGroupName>/\<ApplicationGatewayName> | Brama aplikacji skojarzona z docelowym adresem IP w przepływie |
| LoadBalancer1_s | \<> subscriptionID/\<ResourceGroupName>/\<LoadBalancerName> | Moduł równoważenia obciążenia skojarzony z źródłowym adresem IP w przepływie |
| LoadBalancer2_s | \<> subscriptionID/\<ResourceGroupName>/\<LoadBalancerName> | Moduł równoważenia obciążenia skojarzony z docelowym adresem IP w przepływie |
| LocalNetworkGateway1_s | \<> subscriptionID/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Brama sieci lokalnej skojarzona z źródłowym adresem IP w przepływie |
| LocalNetworkGateway2_s | \<> subscriptionID/\<ResourceGroupName>/\<LocalNetworkGatewayName> | Brama sieci lokalnej skojarzona z docelowym adresem IP w przepływie |
| ConnectionType_s | Możliwe wartości to VNetPeering, VpnGateway i ExpressRoute |    Typ połączenia |
| ConnectionName_s | \<> subscriptionID/\<ResourceGroupName>/\<ConnectionName> | Nazwa połączenia. Dla flowtype P2S, zostanie on <gateway name>sformatowany jako _<VPN Client IP> |
| ConnectingVNets_s | Oddzielona przestrzeń lista nazw sieci wirtualnych | W przypadku topologii piasty i szprych, sieci wirtualne koncentratora będą tutaj wypełniane |
| Country_s | Dwuliterowy kod kraju (ISO 3166-1 alfa-2) | Wypełniony dla typu przepływu ExternalPublic. Wszystkie adresy IP w PublicIPs_s polu będą współużytkować ten sam kod kraju |
| AzureRegion_s | Lokalizacje regionów platformy Azure | Wypełniony dla typu przepływu AzurePublic. Wszystkie adresy IP w PublicIPs_s polu będą współużytkują region platformy Azure |
| AllowedInFlows_d | | Liczba przepływów przychodzących, które były dozwolone. Reprezentuje to liczbę przepływów, które współużytkowały te same przychodzące cztery krotki do interfejsu sieciowego, przy którym przepływ został przechwycony |
| DeniedInFlows_d |  | Liczba przepływów przychodzących, które zostały odrzucone. (Przychodzące do interfejsu sieciowego, przy którym przepływ został przechwycony) |
| AllowedOutFlows_d | | Liczba przepływów wychodzących, które zostały dozwolone (wychodzące do interfejsu sieciowego, przy którym przepływ został przechwycony) |
| DeniedOutFlows_d  | | Liczba przepływów wychodzących, które zostały odrzucone (wychodzące do interfejsu sieciowego, w którym przepływ został przechwycony) |
| FlowCount_d | Przestarzałe. Przepływy całkowite, które odpowiadały tej samej czterotłumowej krocie. W przypadku typów przepływu ExternalPublic i AzurePublic, liczba będzie zawierać przepływy z różnych adresów PublicIP, jak również.
| InboundPackets_d | Pakiety odebrane jako przechwycone w interfejsie sieciowym, w którym zastosowano regułę sieciowej sieciowej | Jest to wypełniane tylko dla wersji 2 schematu dziennika przepływu nsg |
| OutboundPackets_d  | Pakiety wysyłane jako przechwycone w interfejsie sieciowym, w którym zastosowano regułę sieciowej sieciowej | Jest to wypełniane tylko dla wersji 2 schematu dziennika przepływu nsg |
| InboundBytes_d |  Bajty odebrane jako przechwycone w interfejsie sieciowym, w którym zastosowano regułę sieciowej sieciowej | Jest to wypełniane tylko dla wersji 2 schematu dziennika przepływu nsg |
| OutboundBytes_d | Bajty wysyłane jako przechwycone w interfejsie sieciowym, w którym zastosowano regułę sieciowej sieciowej | Jest to wypełniane tylko dla wersji 2 schematu dziennika przepływu nsg |
| CompletedFlows_d  |  | Jest to wypełniane wartością niezerową tylko dla wersji 2 schematu dziennika przepływu nsg |
| PublicIPs_s | <\| \<PUBLIC_IP>>FLOW_STARTED_COUNT>\| \<FLOW_ENDED_COUNT \| \<>>INBOUND_PACKETS \| \< \| \<>OUTBOUND_PACKETS>>\| \<>>> INBOUND_BYTES>>>>OUTBOUND_BYTES | Wpisy oddzielone prętami |
| SrcPublicIPs_s | <\| \<SOURCE_PUBLIC_IP>FLOW_STARTED_COUNT FLOW_STARTED_COUNT>FLOW_ENDED_COUNT \| \<INBOUND_PACKETS>\| \<>\| \< \| \<OUTBOUND_PACKETS INBOUND_PACKETS INBOUND_PACKETS>>\| \<OUTBOUND_BYTES> INBOUND_BYTES>> | Wpisy oddzielone prętami |
| DestPublicIPs_s | <\| \<DESTINATION_PUBLIC_IP>FLOW_STARTED_COUNT>\| \<>FLOW_ENDED_COUNT>\| \<OUTBOUND_PACKETS>\| \<>INBOUND_PACKETS>\| \<OUTBOUND_BYTES \| \<>INBOUND_BYTES> | Wpisy oddzielone prętami |

### <a name="notes"></a>Uwagi

1. W przypadku przepływów usługi AzurePublicpublic i ExternalPublic adres IP należącej do klienta platformy Azure jest wypełniany w VMIP_s polu, podczas gdy publiczne adresy IP są wypełniane w polu PublicIPs_s. W przypadku tych dwóch typów przepływu należy użyć VMIP_s i PublicIPs_s zamiast pól SrcIP_s i DestIP_s. W przypadku adresów AzurePublicpublic i ExternalPublicIP agregujemy dalej, dzięki czemu liczba rekordów przynajmowanych do obszaru roboczego analizy dzienników klientów jest minimalna. (To pole zostanie wkrótce przestarzałe i powinniśmy używać SrcIP_ i DestIP_s w zależności od tego, czy maszyna wirtualna platformy Azure była źródłem, czy miejscem docelowym w przepływie)
1. Szczegóły dotyczące typów przepływu: Na podstawie adresów IP zaangażowanych w przepływ kategoryzujemy przepływy do następujących typów przepływu:
1. IntraVNet — oba adresy IP w przepływie znajdują się w tej samej sieci wirtualnej platformy Azure.
1. InterVNet — adresy IP w przepływie znajdują się w dwóch różnych sieciach wirtualnych platformy Azure.
1. S2S — (Lokacja do lokacji) Jeden z adresów IP należy do usługi Azure Virtual Network, podczas gdy drugi adres IP należy do sieci klienta (lokacji) połączonej z siecią wirtualną platformy Azure za pośrednictwem bramy sieci VPN lub trasy ekspresowej.
1. P2S - (Point To Site) Jeden z adresów IP należy do usługi Azure Virtual Network, podczas gdy drugi adres IP należy do sieci klienta (lokacji) połączonej z siecią wirtualną platformy Azure za pośrednictwem bramy sieci VPN.
1. AzurePublic — jeden z adresów IP należy do usługi Azure Virtual Network, podczas gdy drugi adres IP należy do wewnętrznych publicznych adresów IP platformy Azure należących do firmy Microsoft. Publiczne adresy IP należące do klienta nie będą częścią tego typu przepływu. Na przykład każdy klient własnością maszyny Wirtualnej wysyłania ruchu do usługi Azure (punkt końcowy magazynu) będzie klasyfikowane w ramach tego typu przepływu.
1. ExternalPublic — jeden z adresów IP należy do usługi Azure Virtual Network, podczas gdy drugi adres IP jest publiczny adres IP, który nie jest na platformie Azure, nie jest zgłaszane jako złośliwe w źródłach ASC, które usługa Traffic Analytics zużywa dla interwału przetwarzania między " FlowIntervalStartTime_t" i "FlowIntervalEndTime_t".
1. MaliciousFlow — jeden z adresów IP należą do sieci wirtualnej platformy Azure, podczas gdy drugi adres IP jest publiczny adres IP, który nie jest na platformie Azure i jest zgłaszany jako złośliwy w źródłach ASC, które usługa Traffic Analytics zużywa dla interwału przetwarzania między " FlowIntervalStartTime_t" i "FlowIntervalEndTime_t".
1. UnknownPrivate — jeden z adresów IP należą do usługi Azure Virtual Network, podczas gdy drugi adres IP należy do prywatnego zakresu adresów IP zdefiniowanego w RFC 1918 i nie może być mapowany przez usługę Traffic Analytics do witryny należącej do klienta lub sieci wirtualnej platformy Azure.
1. Nieznany — nie można mapować jednego z adresów IP w przepływach z topologii klienta na platformie Azure, a także lokalnie (lokacja).
1. Niektóre nazwy pól są \_dołączane \_z s lub d. Te nie oznaczają źródła i miejsca docelowego, ale wskazują odpowiednio ciąg typów danych i dziesiętne.

### <a name="next-steps"></a>Następne kroki
Aby uzyskać odpowiedzi na często zadawane pytania, zobacz Często zadawane pytania [dotyczące analizy ruchu,](traffic-analytics-faq.md) aby uzyskać szczegółowe informacje o [funkcjach,](traffic-analytics.md) zobacz Dokumentacja analizy ruchu
