---
title: Bezpieczny dostęp do konta usługi Azure Cosmos DB przy użyciu punktu końcowego usługi Azure Virtual Network
description: W tym dokumencie opisano dotyczące sieci wirtualnej i podsieci kontroli dostępu dla konta usługi Azure Cosmos.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: 672c62c440708f8e949d67d545bee2179c6066b2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2019
ms.locfileid: "60765520"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>Dostęp do usługi Azure Cosmos DB z sieciami wirtualnymi (VNet)

Można skonfigurować konto usługi Azure Cosmos, aby zezwolić na dostęp tylko z określonej podsieci sieci wirtualnej (VNet). Po włączeniu [punktu końcowego usługi](../virtual-network/virtual-network-service-endpoints-overview.md) dostęp do usługi Azure Cosmos DB w podsieci sieci wirtualnej, ruch z tej podsieci są wysyłane do usługi Azure Cosmos DB przy użyciu tożsamości, podsieci i sieci wirtualnej. Po włączeniu punktu końcowego usługi Azure Cosmos DB można ograniczyć dostęp do podsieci, dodając ją do swojego konta usługi Azure Cosmos.

Domyślnie konta usługi Azure Cosmos jest dostępny z dowolnego źródła, jeśli żądania dołączono prawidłowy token autoryzacji. Podczas dodawania co najmniej jednej podsieci w ramach sieci wirtualnych tylko żądania pochodzące z tych podsieci otrzyma prawidłowej odpowiedzi. Żądania pochodzące z dowolnego innego źródła otrzyma odpowiedź 403 (zabronione). 

## <a name="frequently-asked-questions"></a>Często zadawane pytania

Poniżej przedstawiono niektóre często zadawane pytania dotyczące konfigurowania dostępu z sieci wirtualnych:

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Konta usługi Azure Cosmos można określić zarówno punkt końcowy usługi sieci wirtualnej, jak i zasady kontroli dostępu IP? 

Punkt końcowy usługi sieci wirtualnej i zasady kontroli dostępu IP (zwane również zapory) można włączyć na koncie usługi Azure Cosmos. Te dwie funkcje uzupełniają się, a zbiorczo zapewnienia izolacji i zabezpieczeń konta usługi Azure Cosmos. Przy użyciu protokołu IP zapory gwarantuje, że statyczne adresy IP można uzyskać dostępu do konta. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Jak ograniczyć dostęp do podsieci w sieci wirtualnej? 

Istnieją dwa kroki wymagane do ograniczania dostępu do konta usługi Azure Cosmos z podsieci. Po pierwsze musisz zezwolić na ruch z podsieci, aby wykonać jego podsieci i tożsamość sieci wirtualnej do usługi Azure Cosmos DB. Odbywa się przez włączenie punktu końcowego usługi dla usługi Azure Cosmos DB w podsieci. Następnie jest dodanie reguły w ramach konta usługi Azure Cosmos określenie tej podsieci jako źródło, z którego konta można uzyskać dostęp.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Listy ACL sieci wirtualnej i zapory adresów IP odrzucają żądania lub połączenia? 

Gdy zapory adresów IP lub sieci wirtualnej dostęp do reguły są dodawane, tylko żądania z dozwolonych źródeł get prawidłowe odpowiedzi. Inne żądania są odrzucane 403 (zabronione). Jest ważne odróżnienia zapory konto usługi Cosmos Azure zapory na poziomie połączenia. Źródło wciąż mogą się łączyć z usługą i połączenia się nie są odrzucane.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Moje żądania pracę blokowany włączenie punkt końcowy usługi Azure Cosmos DB w podsieci. Co się stało?

Po włączeniu punktu końcowego usługi dla usługi Azure Cosmos DB w podsieci źródła ruchu, osiągając konta zmienia się z publicznym adresem IP na sieć wirtualną i podsieć. Jeśli konto usługi Azure Cosmos ma zapory oparte na adresie IP tylko ruch z podsieci włączona usługa nie będzie odpowiadać reguły zapory IP i dlatego odrzucony. Przechodzi przez kroki bezproblemową migrację z opartego na protokole IP zapory do kontroli dostępu opartych na sieci wirtualnej.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Wirtualne sieci równorzędne również mają dostęp do konta usługi Azure Cosmos? 
Tylko sieć wirtualną i ich podsieci dodane do konta usługi Azure Cosmos mają dostęp. Ich wirtualne sieci równorzędne nie może uzyskać dostępu do konta, dopóki podsieci w sieciach wirtualnych są dodawane do konta.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Co to jest maksymalna liczba podsieci dozwolony dostęp do jednego konta usługi Cosmos? 
Obecnie usługa może mieć co najwyżej 64 podsieci dozwoloną dla konta usługi Azure Cosmos.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Można włączyć dostęp z sieci VPN i Expressroute? 
Do uzyskania dostępu do konta usługi Azure Cosmos za pośrednictwem expressroute z lokalnie, należy włączyć komunikację równorzędną firmy Microsoft. Po wprowadzeniu zapory adresów IP lub reguły dostępu do sieci wirtualnej można dodać publiczne adresy IP, używany do komunikacji równorzędnej firmy Microsoft na zaporze IP konta usługi Azure Cosmos w celu umożliwienia lokalnej usługi dostępu do konta usługi Azure Cosmos. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Należy zaktualizować zasady grupy zabezpieczeń sieci (NSG)? 
Reguły sieciowej grupy zabezpieczeń są używane do ograniczenia łączności do i z podsieci z sieci wirtualnej. Po dodaniu punkt końcowy usługi dla usługi Azure Cosmos DB do podsieci, nie ma potrzeby można otworzyć połączenia wychodzącego w sieciowej grupie zabezpieczeń dla konta usługi Azure Cosmos. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Punkty końcowe usługi są dostępne dla wszystkich sieci wirtualnych?
Nie, sieciami wirtualnymi tylko usługi Azure Resource Manager może mieć włączonego punktu końcowego usługi. Klasyczne sieci wirtualne nie obsługują punkty końcowe usługi.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Można "Akceptuj połączenia z w ramach publicznych centrów danych platformy Azure" I po włączeniu dostępu do punktu końcowego usługi dla usługi Azure Cosmos DB?  
Jest to wymagane tylko, aby Twoje konto usługi Azure Cosmos DB można uzyskać dostęp przez inne platformy Azure do pierwszej strony usług takich jak usługi Azure Data factory, usługa Azure Search lub dowolnej usługi, która jest wdrażana w danym regionie platformy Azure.


## <a name="next-steps"></a>Kolejne kroki

* [Sposób ograniczania dostępu do konta usługi Azure Cosmos do podsieci w sieciach wirtualnych](how-to-configure-vnet-service-endpoint.md)
* [Jak skonfigurować zapory adresów IP dla konta usługi Azure Cosmos](how-to-configure-firewall.md)

