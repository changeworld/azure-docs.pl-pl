---
title: Bezpieczny dostęp do kont usługi Azure Cosmos DB przy użyciu punktu końcowego usługi sieci wirtualnej
description: W tym dokumencie opisano informacje o kontroli dostępu do sieci wirtualnej i podsieci dla konta usługi Azure Cosmos.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: c1c5bdd1d210a1933699cad52dbf123b50048e01
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421330"
---
# <a name="access-azure-cosmos-db-from-virtual-networks-vnet"></a>Dostęp do usługi Azure Cosmos DB z sieci wirtualnych

Konto usługi Azure Cosmos można skonfigurować tak, aby zezwalało na dostęp tylko z określonej podsieci sieci wirtualnej (sieci wirtualnej). Dzięki umożliwieniu [punktu końcowego usługi](../virtual-network/virtual-network-service-endpoints-overview.md) dostęp do usługi Azure Cosmos DB w podsieci w sieci wirtualnej, ruch z tej podsieci jest wysyłany do usługi Azure Cosmos DB z tożsamością podsieci i sieci wirtualnej. Po włączeniu punktu końcowego usługi Usługi Azure Cosmos DB można ograniczyć dostęp do podsieci, dodając ją do konta usługi Azure Cosmos.

Domyślnie konto usługi Azure Cosmos jest dostępne z dowolnego źródła, jeśli żądaniu towarzyszy prawidłowy token autoryzacji. Po dodaniu co najmniej jednej podsieci w sieciach wirtualnych tylko żądania pochodzące z tych podsieci otrzymają prawidłową odpowiedź. Żądania pochodzące z dowolnego innego źródła otrzymają odpowiedź 403 (zabronione). 

## <a name="frequently-asked-questions"></a>Często zadawane pytania

Oto kilka często zadawanych pytań dotyczących konfigurowania dostępu z sieci wirtualnych:

### <a name="can-i-specify-both-virtual-network-service-endpoint-and-ip-access-control-policy-on-an-azure-cosmos-account"></a>Czy można określić zarówno punkt końcowy usługi sieci wirtualnej, jak i zasady kontroli dostępu do adresu IP na koncie usługi Azure Cosmos? 

Można włączyć zarówno punkt końcowy usługi sieci wirtualnej, jak i zasady kontroli dostępu do adresów IP (aka firewall) na koncie usługi Azure Cosmos. Te dwie funkcje są komplementarne i wspólnie zapewniają izolację i bezpieczeństwo konta usługi Azure Cosmos. Korzystanie z zapory IP zapewnia, że statyczne adresy IP mogą uzyskać dostęp do twojego konta. 

### <a name="how-do-i-limit-access-to-subnet-within-a-virtual-network"></a>Jak ograniczyć dostęp do podsieci w sieci wirtualnej? 

Istnieją dwa kroki wymagane, aby ograniczyć dostęp do konta usługi Azure Cosmos z podsieci. Najpierw zezwalasz ruchowi z podsieci na przenoszenie jego tożsamości podsieci i wirtualnej sieci do usługi Azure Cosmos DB. Odbywa się to przez włączenie punktu końcowego usługi dla usługi Azure Cosmos DB w podsieci. Dalej jest dodanie reguły na koncie usługi Azure Cosmos określającej tę podsieć jako źródło, z którego można uzyskać dostęp do konta.

### <a name="will-virtual-network-acls-and-ip-firewall-reject-requests-or-connections"></a>Czy listy ACL sieci wirtualnej i Zapora IP odrzucą żądania lub połączenia? 

Po dodaniu zapory IP lub reguł dostępu do sieci wirtualnej tylko żądania z dozwolonych źródeł otrzymują prawidłowe odpowiedzi. Inne wnioski są odrzucane z 403 (Zabronione). Ważne jest, aby odróżnić zaporę konta usługi Azure Cosmos od zapory na poziomie połączenia. Źródło nadal może łączyć się z usługą, a same połączenia nie są odrzucane.

### <a name="my-requests-started-getting-blocked-when-i-enabled-service-endpoint-to-azure-cosmos-db-on-the-subnet-what-happened"></a>Moje żądania zaczęły być blokowane po włączeniu punktu końcowego usługi do usługi Azure Cosmos DB w podsieci. Co się stało?

Gdy punkt końcowy usługi dla usługi Azure Cosmos DB jest włączony w podsieci, źródło ruchu docierającego do konta przełącza się z publicznego adresu IP do sieci wirtualnej i podsieci. Jeśli twoje konto usługi Azure Cosmos ma tylko zaporę opartą na protokoiści IP, ruch z podsieci z włączoną usługą nie będzie już zgodny z regułami zapory IP i w związku z tym zostanie odrzucony. Przejdź do kroków, aby bezproblemowo przeprowadzić migrację z zapory opartej na protokoi ip do kontroli dostępu opartej na sieci wirtualnej.

### <a name="are-additional-rbac-permissions-needed-for-azure-cosmos-accounts-with-vnet-service-endpoints"></a>Czy dodatkowe uprawnienia RBAC są potrzebne dla kont usługi Azure Cosmos z punktami końcowymi usługi VNET?

Po dodaniu punktów końcowych usługi sieci wirtualnej do konta usługi Azure Cosmos, aby wprowadzić `Microsoft.Network/virtualNetworks/subnets/joinViaServiceEndpoint/action` wszelkie zmiany w ustawieniach konta, należy uzyskać dostęp do akcji dla wszystkich sieci wirtualnych skonfigurowanych na koncie usługi Azure Cosmos. To uprawnienie jest wymagane, ponieważ proces autoryzacji sprawdza poprawność dostępu do zasobów (takich jak zasoby bazy danych i sieci wirtualnej) przed oceną jakichkolwiek właściwości.
 
Autoryzacja sprawdza poprawność uprawnień do akcji zasobu sieci wirtualnej, nawet jeśli użytkownik nie określa list ACL sieci wirtualnej przy użyciu interfejsu wiersza polecenia platformy Azure. Obecnie płaszczyzna sterowania konta usługi Azure Cosmos obsługuje ustawienie pełnego stanu konta usługi Azure Cosmos. Jednym z parametrów wywołań `virtualNetworkRules`płaszczyzny sterowania jest . Jeśli ten parametr nie jest określony, interfejsu wiersza polecenia `virtualNetworkRules` platformy Azure sprawia, że wywołanie bazy danych pobrania, aby pobrać i używa tej wartości w wywołaniu aktualizacji.

### <a name="do-the-peered-virtual-networks-also-have-access-to-azure-cosmos-account"></a>Czy sieci wirtualne równorzędne mają również dostęp do konta usługi Azure Cosmos? 
Tylko sieć wirtualna i ich podsieci dodane do konta usługi Azure Cosmos mają dostęp. Ich równorzędne sieci wirtualne nie mogą uzyskać dostępu do konta, dopóki podsieci w równorzędnych sieciach wirtualnych nie zostaną dodane do konta.

### <a name="what-is-the-maximum-number-of-subnets-allowed-to-access-a-single-cosmos-account"></a>Jaka jest maksymalna liczba podsieci, które mogą uzyskać dostęp do jednego konta usługi Cosmos? 
Obecnie można mieć co najwyżej 256 podsieci dozwolone dla konta usługi Azure Cosmos.

### <a name="can-i-enable-access-from-vpn-and-express-route"></a>Czy mogę włączyć dostęp z sieci VPN i trasy ekspresowej? 
Aby uzyskać dostęp do konta usługi Azure Cosmos za pomocą trasy Express z lokalnego, należy włączyć komunikację równorzędną firmy Microsoft. Po umieszczeniu zapory IP lub reguł dostępu do sieci wirtualnej można dodać publiczne adresy IP używane do komunikacji równorzędnej firmy Microsoft w zaporze IP konta usługi Azure Cosmos, aby umożliwić usługom lokalnym dostęp do konta usługi Azure Cosmos. 

### <a name="do-i-need-to-update-the-network-security-groups-nsg-rules"></a>Czy muszę zaktualizować reguły sieciowych grup zabezpieczeń? 
Reguły sieciowej sieciowej sieciowej są używane do ograniczania łączności do i z podsieci z siecią wirtualną. Po dodaniu punktu końcowego usługi dla usługi Azure Cosmos DB do podsieci nie ma potrzeby otwierania łączności wychodzącej w sieci NSG dla konta usługi Azure Cosmos. 

### <a name="are-service-endpoints-available-for-all-vnets"></a>Czy punkty końcowe usługi są dostępne dla wszystkich sieci wirtualnych?
Nie, tylko sieci wirtualne usługi Azure Resource Manager mogą mieć włączony punkt końcowy usługi. Klasyczne sieci wirtualne nie obsługują punktów końcowych usługi.

### <a name="can-i-accept-connections-from-within-public-azure-datacenters-when-service-endpoint-access-is-enabled-for-azure-cosmos-db"></a>Czy mogę "Akceptować połączenia z publicznych centrów danych platformy Azure", gdy dostęp do punktu końcowego usługi jest włączony dla usługi Azure Cosmos DB?  
Jest to wymagane tylko wtedy, gdy chcesz, aby twoje konto usługi Azure Cosmos DB było dostępne dla innych usług pierwszej firmy platformy Azure, takich jak fabryka danych platformy Azure, usługa Azure Cognitive Search lub dowolna usługa wdrożona w danym regionie platformy Azure.


## <a name="next-steps"></a>Następne kroki

* [Jak ograniczyć dostęp do kont usługi Azure Cosmos do podsieci w sieciach wirtualnych](how-to-configure-vnet-service-endpoint.md)
* [Jak skonfigurować zaporę IP dla konta usługi Azure Cosmos](how-to-configure-firewall.md)

